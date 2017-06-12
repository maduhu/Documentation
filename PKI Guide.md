# PKI Guide
***

## Introduction
In this guide, we will introduce some features of CloudFlare PKI -- [cfssl.](#https://github.com/cloudflare/cfssl)
* [**Install cfssl**](#install-cfssl)
* [**CA Config**](#ca-config)
* [**Client Config**](#client-config)
* [**Key Suggestions**](#key-recommendations)
* [**Integrating the Certificates with Service**](#integrating-the-certificates-with-service)
***

### Install cfssl
To install cfssl tool, please follow the instructions in [cfssl.](#https://github.com/cloudflare/cfssl)

### CA Config
#### Initialize a certificate authority
First, you need to configure the certificate signing request (csr), named ```ca.json```. For the key algorithm, rsa and ecdsa are supported by cfssl, but you need to avoid using small-size key.
```
{
  "hosts": [
    "root.com",
    "www.root.com"
  ],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
    "C": "US",
    "L": "Des Moines",
    "O": "Dwolla, Inc.",
    "OU": "Level One Project",
    "ST": "Iowa"
    }
  ]
}
 ```
 Then you need to generate cert and related private key for the CA.
 ```
 cfssl gencert -initca ca.json | cfssljson -bare ca -
 ```
You will receive the following files:
```
ca-key.pem
ca.csr
ca.pem
```
* ```ca.pem``` is your cert.
* ```ca-key.pem``` is your related private key, so you need to keep it safe. It will allow you to sign any cert.

#### Run a CA server
To run a CA server, you need the ```ca-key.pem, ca.pem``` from the first step, and a config file ```config_ca.json``` for the server.
```
{
   "signing": {
     "default": {
       "auth_key": "central_ledger",
       "expiry": "8760h",
       "usages": [
          "signing",
          "key encipherment",
          "server auth",
          "client auth"
        ],
       "name_whitelist": "\\.central-ledger\\.com$"
      }
   },
   "auth_keys": {
     "central_ledger": {
       "key": "0123456789abcdef",
       "type": "standard"
     }
   }
 }
 ```
 * ```auth_key``` is the token used for authenticate the client's CSR.
 * ```expiry``` is the valid time period for the cert. A year is around 8760 hours.
 * ```name_whitelist``` is the regular expression for the domain name can be signed by the CA.
To run the server:
```
cfssl serve -ca=ca.pem -ca-key=ca-key.pem -config=config_ca.json -port=6666
```
The default IP and port number is: 127.0.0.1:8888.

### Client Config
To generate a certificate for the client, you will need a config file -- ```config_clients.json ``` for cfssl;
```
{
    "auth_keys" : {
       "central_ledger" : {
          "type" : "standard",
          "key" : "0123456789abcdef"
       }
    },
    "signing" : {
       "default" : {
          "auth_remote" : {
             "remote" : "ca_server",
             "auth_key" : "central_ledger"
          }
       }
    },
    "remotes" : {
       "ca_server" : "localhost:6666"
    }
 }
 ```
* The authentication token in ```auth_keys``` must match the one in the server.
* The server address in ```remotes``` must match the real server address.
You will also need another config file -- ```central_ledger.json``` for the service.
```
{
    "hosts": [
         "www.central-ledger.com"
     ],
     "key": {
         "algo": "ecdsa",
         "size": 256
     },
     "names": [
         {
             "C": "US",
             "L": "Des Moines",
             "O": "Level 1 Project",
             "OU": "l1p-central-services",
             "ST": "Iowa"
         }
     ]
 }
 ```
 * The domain name in ```hosts``` must match the whitelist in ```config_ca.json```.
 * You should avoid using small size key in ```key```.
To generate a certificate for the service:
```
cfssl gencert -config=config_clients.json central_ledger.json | cfssljson -bare central_ledger
```
You will receive the following files:
```
central_ledger-key.pem
central_ledger.csr
central_ledger.pem
```
``` central_ledger.pem``` will be your service's cert, and ```central_ledger-key.pem``` will be your private key.

### Key Suggestions
During the certificate signing requests, we suggest to avoid using small keys. The minium requirement is as the following table:
| Signature Key |     RSA     |      ECC      |
| ------------- | ----------- | ------------- |
|    AES-256    |    >=2048   | PCurves >= 256|

### Integrating the certificates with service
Once the certificates have been created, you will need to integrate them with your service. We will use central-ledger as an example here.

#### Server
On the server side, you'll want to set it up so that every incoming request is checked against the cert. In the projects so far, our team has used Hapi, but it's just as simple just using Node libraries.
Both methods are shown to make the process as easy as possible.

##### Setting up with Hapi
On the server side, we simply added the key and cert to a tls object. When the server connection is initialized, the tls object is added as an option.
The fs library is used to read the key and cert files.

```
var fs = require('fs') 
.
.
.
const tls = {
  key: fs.readFileSync('./src/ssl/central-ledger-key.pem'),
  cert: fs.readFileSync('./src/ssl/central-ledger-cert.pem')
}
const server = new Hapi.Server()
server.connection({
  tls
})
```


##### Setting up with Node
This step doesn't change much. The key and cert along with the client cert are added to an options object. When the server is created, they are added as options.
The fs library is used to read the key and cert files.

```
var fs = require('fs') 
var https = require('https') 
.
.
.
var options = { 
    key: fs.readFileSync('server-key.pem'), 
    cert: fs.readFileSync('server-crt.pem'), 
    ca: fs.readFileSync('ca-cert.pem'), 
}

https.createServer(options, function (req, res) { 
    res.writeHead(200) 
}).listen(3000)
```    

#### Client
For client requests to the server, we use many of the same libraries. fs is used to read the client cert and https is used to make the requests. 
The cert simply needs to be added as part of the options object under the name ca.

```
var fs = require('fs') 
var https = require('https') 
var options = { 
    hostname: 'localhost', 
    port: 3000, 
    path: '/', 
    method: 'GET', 
    ca: fs.readFileSync('ca-cert.pem') 
}

var req = https.request(options, function(res) { 
    res.on('data', function(data) { 
    }) 
}) 
req.end()
```