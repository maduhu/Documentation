
#Architectural Simplifications

-----


The motivation behind all the points proposed here is that the proxies and the connectors should be developed once in a way that they could be plugged in various DFSP instances, while each DFSP instance will have its own internal logic. So therefore common functionalists like those should be provided by the l1p platform out of the box without the need to be replicated across different custom implementations.


##1. When adding a user we should pass no arguments to POST /user-registration/users

**Changes Required by: Softwaregroup, Dwolla, ModusBox**

At the moment the dfsp logic must provide the base url to the spsp server when registering a new user

i.e. It is now:

    POST /user-registration/users
    {url: 'http://spsp-server:3043/v1'}

Our proposal is that when a new user is register we do not need to pass SPSP server url address.

The required changes from Central Directory Service is described in [Manage Account Account](https://github.com/LevelOneProject/Docs/tree/master/DFSP/ManageAcccoutHolders) document.

  
##2. When obtaining information about a payee the dfsp logic should just provide a user identifier

**Changes Required by: Softwaregroup, ModusBox**

From the user identifier the proxy(mule) could call the central directory with that identifier to obtain the spsp server url associated with the user and perform a GET  {spsp_server}/receivers/{identifier} to obtain information about the payee.


At the moment the dfsp must make 2 consequent calls in order obtain information about a payee.

    request1:
    GET http://central-directory/recources?identifier=78956562&identifierType=eur
    response1:
    {spspReceiver: 'http://spsp-server:3043/v1'}

then it builds the receiver url as spspReceiver/receivers/identifier before providing it to the spsp-client-proxy

    request2:
    GET http://spsp-client-proxy/query?receiver=http://spsp-server:3043/v1/receivers/78956562


Our proposal is that the DFSP logic should call the spsp-client-proxy with only just the identifier and from there on the proxy should do the rest on its own (obtain the SPSP server base URL from the central directory and make a get request as needed to the respective SPSP server endpoint).

    GET http://spsp-client-proxy/query?identifier=78956562


 
##3.Calls  /quoteSourceAmount and /quoteDestinationAmount shall expect {identifier}


**Changes Required by: Softwaregroup, ModusBox**

For the calls /quoteSourceAmount and /quoteDestinationAmount instead of expecting a "receiver" parameter which must represent {spsp_server}/receivers/{identifier} it could just wait for "identifier" and do the rest on its own similarly to as described in the second point.

Now as in point 2) the DFSP logic should perform 2 consequent calls. (on for obtaining spspReceiver URL from central directory in order to build the receiver parameter which should be passed to the proxy)
so instead of:

    http://spsp-client-proxy/quoteSourceAmount?receiver=http://spsp-server:3043/v1/receivers/38439659&sourceAmount=21

The call could be just:  


    http://spsp-client-proxy/quoteSourceAmount?identifier=78956562&sourceAmount=21

 
#4) /setup method in spsp client proxy should expect accountNumber

**Changes Required by: Softwaregroup, ModusBox**

Same applies for /setup method in spsp client proxy. Instead of "receiver" URL it could expect "identifier" and instead of "sourceAccount" it could expect "accountNumber" and that way to build the correct urls to the spsp server and the ledger adapter before further proceeding.

instead of:

    {
      "receiver": "http://receivingdfsp/v1/receivers/78956562",
      "sourceAccount": "http://dfsp1:8014/ledger/accounts/alice",
      "destinationAmount": "10.40",
      "memo": "Hi Bobb!",
      "sourceIdentifier": "9809890190934023"
    }
    

it could be:

    {
      "destinationIdentifier": "78956562",
      "sourceAccountNumber": "alice",
      "destinationAmount": "10.40",
      "memo": "Hi Bobb!",
      "sourceIdentifier": "29201743"
    }
    

#5. When posting on /invoice the submissionUrl could be constructed by the proxy on the fly.

**Changes Required by: Softwaregroup, ModusBox**

It has the "senderIdentifier" so calling the central directory and building the submissionUrl as {spsp_server}/invoices should not be a problem.

Instead of:

    {
	    "invoiceId":"12345",
	    "submissionUrl": "dfsp1.spsp-server/v1/invoices",
	    "senderIdentifier": "client.user.number",
	    "memo":"Invoice from merchant for 100 USD" 
    }

it could be:

    {
	    "invoiceId":"12345",
	    "senderIdentifier": "client.user.number",
	    "memo":"Invoice from merchant for 100 USD" 
    } 
    
submission URL can be omitted as by using the senderIdentifier the proxy could obtain the URL to the SPSP server of the server and dynamically determine the submissionUrl as it is spsp-server/invoices

We now have a similar case when instructing a transfer hold operation - the sender account should point to the ledger adapter but not to the actual ledger.


##6. Removing 'transfer execute' message from SPSP Client Proxy. 

**Changes Required by: Softwaregroup, ModusBox and  possibly Ripple**

From the DFSP perspective the transfer setup and transfer execute methods are called one after another without any logic between them. 

SPSP Client Proxy Setup Method:

	POST http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/setup
	{
		receiver: "http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:3043/v1/receivers/26547070",
		sourceAccount: "http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8014/ledger/accounts/alice",
		destinationAmount: 33,
		memo: {
			fee: 1,
			transferCode: 'p2p',
			debitName: 'alice',
			creditName: 'bob'
		},
		sourceIdentifier: '85555384'
	}

Response:

	{
	  "id": "",
	  "address": "",
	  "destinationAmount": "",
	  "sourceAmount": "",
	  "sourceAccount": "",
	  "expiresAt": "",
	  "data": {
	    "senderIdentifier": ""
	  },
	  "additionalHeaders": "",
	  "condition": ""
	}

SPSP Client Proxy Setup Method:


    http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/payments/b51ec534-ee48-4575-b6a9-ead2955b8069

As a body of the request the dfsp-api passes the object returned from call the setup method

##7. Service Discovery

**Changes Required by: Softwaregroup, ModusBox, Ripple, Dwolla**

I believe there should be a centralized point of configuration somewhere within the core of the L1P project. It should know about where the different internal services reside and respectively be able to dynamically build different kind of internal and external URLs. 
