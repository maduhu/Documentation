## DFSP1

| Property | Description |
| ---- | ----------- |
| Instance Name | integrate-dfsp1 |
| Instance ID | i-0e209e2ea190edf8c |
| Public DNS | ec2-35-163-231-111.us-west-2.compute.amazonaws.com |
| Public IP | 35.163.231.111 |
| Instance type | m4.large |
| Private DNS | ip-172-31-23-143.us-west-2.compute.internal |
| Private IPs | 172.31.23.143 |

### Service Info

| Service | Port | Full Service URL |
| ------- | ---- | ----------- |
| dfsp1-interop-directory | 8088 |  [http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/directory/v1](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/directory/v1/console) |
| dfsp1-interop-spsp-client-proxy | 8088 |  [http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/console) |
| dfsp1-ilp-spsp-client-rest | 3042 | http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3042 |
| dfsp1-ilp-spsp-server | 3043 | http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3043 |
| dfsp1-interop-ledger-adapter | 8088 |  http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/ledger |
| dfsp1-ledger | 8014 | http://dfsp1:8014/ledger |
| dfsp1-ilp-connector | 3044 | http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3044 |
|     |     |     |
| dfsp2-interop-spsp-backend | 8090 |  [http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8090/spsp/backend/v1](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8090/spsp/backend/v1/console) |
| dfsp2-ilp-spsp-client-rest | 3045 | http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3045 |
| dfsp2-ilp-spsp-server | 3046 | http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3046 |
| dfsp2-ilp-connector | 3047 | http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3047 |
| dfsp2-interop-ledger-adapter | 8090 |  http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8090/ledger |
| dfsp2-ledger | 8114 | http://dfsp1:8114/ledger |
|     |     |     |
| ist-ledger | 3075 | http://dfsp1:3075 |

### SSH

on a mac:  `ssh -i "interop-dev1.pem" ec2-user@ec2-35-163-231-111.us-west-2.compute.amazonaws.com`

on windows:  use putty, and you will need to use the .ppk file linked above.  under the `Connection/SSH/Auth` section you will need to select this .ppk file for `Private key file for authentication`

### Services

| Name | External Ports | Description |
| ---- | -------------- | ----------- |
| dfsp-interop-services | 8081 | mule services for sending dfsp (directory / spsp-client-proxy / ilp-ledger-adapter / spsp-backend ) |
| dfsp-mock-services | 9081 | temporary mule mocks to support integration |
| interop-demo-ledger | 3000 | five bells docker image admin / foo |

### Dev

DFSP Directory Gateway

| path | Description |
| ---- | ----------- |
| /directory/v1 | directory gateway service root
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/directory/v1/console/) | api demo console |
| [user/get](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/directory/v1/user/get) | /directory/user/get |
| [user/add](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/directory/v1/user/add) | for testing only - add accounts to directory |

Data Structure for /user/add - this method takes a list of maps of account information which is then returned via calls to /user/get.  This data should map to accounts that exist in test ledgers.  For PI 2 demo purposes only the URI contained in the account field is valuable.  The other information should be taken from the result of calling query on the spsp-client.  The directory service and the whole topic of resolving user identifiers back to account information  will be discussed and refactored as part of the next convening and will be implemented in PI 3.1.

note the uri field below is the key for queries into /user/get.  the result will be the matching user json and the value in its account field shoudl be passed forward into the spsp-client

```json
{
  "users": [
    {
      "uri": "http://centraldirectory.com/alice",
      "name": "Alice Somebody",
      "account": "http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3000/alice",
      "currency": "USD"
    },
    {
      "uri": "http://centraldirectory.com/bob",
      "name": "Bob Nobody",
      "account": "http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3000/bob",
      "currency": "USD"
    }
  ]
}
```

DFSP SPSP Client Proxy

| path | Description |
| ---- | ----------- |
| /spsp/client/v1 | spsp client proxy service root |
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/console/) | api demo console |
| ... | see api documentation or console for additional functions |

ILP Ledger Adapter

This deployment of the ILP Ledger Adapter is connected to an instance of the five bells ledger running on interop-dev1.

| path | Description |
| ---- | ----------- |
| /ilp/ledger/v1 | spsp client proxy service root |
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/ledger/console/) | api demo console |
| ... | see api documentation or console for additional functions |

SPSP Server Backend

| path | Description |
| ---- | ----------- |
| /spsp/backend/v1 | spsp server backend service root |
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8090/spsp/backend/v1/console/) | api demo console |
| ... | see api documentation or console for additional functions |



## DFSP2

| Property | Description |
| ---- | ----------- |
| Instance Name | integrate-dfsp2 |
| Instance ID | i-08489438f19024a4a |
| Public DNS | ec2-35-163-249-3.us-west-2.compute.amazonaws.com |
| Public IP | 35.163.249.3 |
| Instance type | m4.large |
| Private DNS | ip-172-31-22-112.us-west-2.compute.internal |
| Private IPs | 172.31.22.112 |

### Service Info

| Service | Port | Full Service URL |
| ------- | ---- | ----------- |
| dfsp1-interop-directory | 8088 |  [http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/directory/v1](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/directory/v1/console) |
| dfsp1-interop-spsp-client-proxy | 8088 |  [http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/console) |
| dfsp1-ilp-spsp-client-rest | 3042 | http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3042 |
| dfsp1-ilp-spsp-server | 3043 | http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3043 |
| dfsp1-interop-ledger-adapter | 8088 |  http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/ledger |
| dfsp1-ledger | 8014 | http://dfsp1:8014/ledger |
| dfsp1-ilp-connector | 3044 | http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3044 |
|     |     |     |
| dfsp2-interop-spsp-backend | 8090 |  [http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8090/spsp/backend/v1](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8090/spsp/backend/v1/console) |
| dfsp2-ilp-spsp-client-rest | 3045 | http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3045 |
| dfsp2-ilp-spsp-server | 3046 | http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3046 |
| dfsp2-ilp-connector | 3047 | http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3047 |
| dfsp2-interop-ledger-adapter | 8090 |  http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8090/ledger |
| dfsp2-ledger | 8114 | http://dfsp1:8114/ledger |
|     |     |     |
| ist-ledger | 3075 | http://dfsp1:3075 |

### SSH

on a mac:  `ssh -i "interop-dev1.pem" ec2-user@ec2-35-163-249-3.us-west-2.compute.amazonaws.com`

on windows:  Same as above except for hostname

### Services

| Name | External Ports | Description |
| ---- | -------------- | ----------- |
| dfsp-interop-services | 8081 | mule services for sending dfsp (directory / spsp-client-proxy / ilp-ledger-adapter / spsp-backend ) |
| dfsp-mock-services | 9081 | temporary mule mocks to support integration |
| interop-demo-ledger | 3000 | five bells docker image admin / foo |

### Dev

DFSP Directory Gateway

| path | Description |
| ---- | ----------- |
| /directory/v1 | directory gateway service root
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/directory/v1/console/) | api demo console |
| [user/get](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/directory/v1/user/get) | /directory/user/get |
| [user/add](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/directory/v1/user/add) | for testing only - add accounts to directory |

Data Structure for /user/add - this method takes a list of maps of account information which is then returned via calls to /user/get.  This data should map to accounts that exist in test ledgers.  For PI 2 demo purposes only the URI contained in the account field is valuable.  The other information should be taken from the result of calling query on the spsp-client.  The directory service and the whole topic of resolving user identifiers back to account information  will be discussed and refactored as part of the next convening and will be implemented in PI 3.1.

note the uri field below is the key for queries into /user/get.  the result will be the matching user json and the value in its account field shoudl be passed forward into the spsp-client

```json
{
  "users": [
    {
      "uri": "http://centraldirectory.com/alice",
      "name": "Alice Somebody",
      "account": "http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3000/alice",
      "currency": "USD"
    },
    {
      "uri": "http://centraldirectory.com/bob",
      "name": "Bob Nobody",
      "account": "http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3000/bob",
      "currency": "USD"
    }
  ]
}
```

DFSP SPSP Client Proxy

| path | Description |
| ---- | ----------- |
| /spsp/client/v1 | spsp client proxy service root |
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/console/) | api demo console |
| ... | see api documentation or console for additional functions |

ILP Ledger Adapter

This deployment of the ILP Ledger Adapter is connected to an instance of the five bells ledger running on interop-dev1.

| path | Description |
| ---- | ----------- |
| /ilp/ledger/v1 | spsp client proxy service root |
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8088/ledger/console/) | api demo console |
| ... | see api documentation or console for additional functions |

SPSP Server Backend

| path | Description |
| ---- | ----------- |
| /spsp/backend/v1 | spsp server backend service root |
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8090/spsp/backend/v1/console/) | api demo console |
| ... | see api documentation or console for additional functions |
