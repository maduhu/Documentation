


#  Pending Transaction Use Case




#### Assumptions




1. The invoice will be created in the merchant's DFSP. It will be associated with an account.
2. After the invoice is created in the merchant's DFSP a notification with the invoice reference will be send to the default client DFSP.
3. The client DFSP will stored the reference (full URL) to the merchant's invoice.
4. The invoice reference in the client DFSP will not be associated with any clients account thus the client can choose an account from which he is going to pay the invoice.
5. As a consequence of the above, in case the client has accounts in more than one DFSP he will receive the invoice notification only in his default DFSP and from the USSD interface he will be able to pay the invoice only from his default DFSP


I would like to propose the following new APIs and changes to the existing APIs defined to support the pending transaction use case.


In the notes below I will refer to '**dfsp1**' as client DFSP (paying the invoice) and '**dfsp2**' as the merchant DFSP (issuing the invoice)



## I. LOOKUP  RECEIVER ##

![](./QueryReceiver.jpg)
      
####  A. User Lookup Request from [ DFSP API ](https://github.com/LevelOneProject/dfsp-api) to [ Central Directory ](https://github.com/LevelOneProject/central-directory) ###

**Endpoint**

DFSP API calls [GET /user] endpoint in Central Directory


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}


####  B. User Lookup Request from  [ Central Directory ](https://github.com/LevelOneProject/central-directory) to [ Central Registry ](https://github.com/LevelOneProject/central-end-user-registry)###

**Endpoint**

Central Directory [GET /user] endpoint in Central End User Registry


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

####  C. Query Receiver Request from  [ DFSP API ](https://github.com/LevelOneProject/dfsp-api) to [ SPSP CLIENT PROXY ](https://github.com/LevelOneProject/interop-spsp-client-proxy) ###

**Endpoint**

DFSP API calls [GET /query] endpoint in SPSP Client Proxy


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

####  D. Query Receiver Request from  [ SPSP CLIENT PROXY ](https://github.com/LevelOneProject/interop-spsp-client-proxy) to [ SPSP CLIENT ](https://github.com/LevelOneProject/ilp-spsp-client-rest) ###

**Endpoint**

SPSP Client Proxy calls [GET /query] endpoint in SPSP Client


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}


####  E. Query Receiver Request from  [ SPSP CLIENT ](https://github.com/LevelOneProject/ilp-spsp-client-rest) to [ SPSP SERVER ](https://github.com/LevelOneProject/ilp-spsp-server) ###

**Endpoint**

SPSP Client calls [GET /query] endpoint in SPSP Server


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

####  F. Query Receiver Request from  [ SPSP SERVER ](https://github.com/LevelOneProject/ilp-spsp-server) to [ SPSP Server Backend ](https://github.com/LevelOneProject/interop-spsp-server-backend)###

**Endpoint**

SPSP Server calls [GET /query] endpoint in SPSP Server Backend


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

####  G. Query Receiver Request from  [ SPSP Server Backend ](https://github.com/LevelOneProject/interop-spsp-server-backend) to [ DFSP API ](https://github.com/LevelOneProject/dfsp-api)###

**Endpoint**

SPSP Server Backend calls [GET /query] endpoint in DFSP API


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

## II. CREATE INVOICE  ##


TO BE FILLED BY SG

## III. POST INVOICE NOTIFICATION  ##

![](./PostInvoiceNotification.jpg)

####  A. Post Invoice Notification from [ DFSP API ](https://github.com/LevelOneProject/dfsp-api) to [ SPSP CLIENT PROXY ](https://github.com/LevelOneProject/interop-spsp-client-proxy) ###

**Endpoint**

DFSP API calls [POST /v1/invoices] endpoint in SPSP Client Proxy


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}


####  B. Post Invoice Notification from [ SPSP CLIENT PROXY ](https://github.com/LevelOneProject/interop-spsp-client-proxy) to [ SPSP CLIENT ](https://github.com/LevelOneProject/ilp-spsp-client-rest) ###

**Endpoint**

SPSP Client Proxy calls [POST /v1/invoices] endpoint in SPSP Client


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

####  C. Post Invoice Notification from  [ SPSP CLIENT ](https://github.com/LevelOneProject/ilp-spsp-client-rest) to [ SPSP Server ](https://github.com/LevelOneProject/ilp-spsp-server) ###

**Endpoint**

SPSP Client calls [POST /v1/invoices] endpoint in SPSP Server


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

####  D. Post Invoice Notification from  [ SPSP Server ](https://github.com/LevelOneProject/ilp-spsp-server) to [ SPSP Server Backend ](https://github.com/LevelOneProject/interop-spsp-server-backend)###

**Endpoint**

SPSP Server calls [POST /v1/invoices] endpoint in SPSP Server Backend


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

####  E. Post Invoice Notification from  [ SPSP Server Backend ](https://github.com/LevelOneProject/interop-spsp-server-backend)  to [DFSP API](https://github.com/LevelOneProject/dfsp-api) ###

**Endpoint**

SPSP Server Backend calls [POST /v1/invoices] endpoint in DFSP API


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}



## IV. SETUP PAYMENT ##


![](./SetupInvoicePayment.jpg)

###  A. setup request from [ DFSP API ](https://github.com/LevelOneProject/dfsp-api) to [ SPSP CLIENT PROXY ](https://github.com/LevelOneProject/interop-spsp-client-proxy) ###

**Endpoint**

DFSP API calls [POST /v1/setup](https://github.com/LevelOneProject/ilp-spsp-client-rest#post-v1setup) endpoint in SPSP Client Proxy


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}


###  B. setup request from [ SPSP CLIENT PROXY ](https://github.com/LevelOneProject/interop-spsp-client-proxy) to [ ILP SPSP CLIENT ](https://github.com/LevelOneProject/ilp-spsp-client-rest)###

**Endpoint**

SPSP Client Proxy calls [POST /v1/setup](https://github.com/LevelOneProject/ilp-spsp-client-rest#post-v1setup) endpoint in ILP SPSP Client


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

###  C. setup request from [ ILP SPSP CLIENT ](https://github.com/LevelOneProject/ilp-spsp-client-rest) to [ ILP SPSP SERVER ](https://github.com/LevelOneProject/ilp-spsp-server) on Client side DFSP###

**Endpoint**

ILP SPSP CLIENT calls [POST /v1/setup] endpoint in SPSP Server


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

###  D. setup request from [ SPSP SERVER ](https://github.com/LevelOneProject/ilp-spsp-client-server) to [ SPSP SERVER BACKEND ](https://github.com/LevelOneProject/interop-spsp-backend-services)###

**Endpoint**

SPSP SERVER calls [POST /v1/setup] endpoint in SPSP Server Backend


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

###  E. setup request from [ SPSP SERVER BACKEND](https://github.com/LevelOneProject/interop-spsp-server-backend) to [ DFSP API ](https://github.com/LevelOneProject/dfsp-api)###

**Endpoint**

SPSP SERVER Backend calls [POST /v1/setup] endpoint in DFSP API


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

###  F. setup request from [ SPSP SERVER BACKEND](https://github.com/LevelOneProject/interop-spsp-server-backend) to [ DFSP API ](https://github.com/LevelOneProject/dfsp-api)###

**Endpoint**

SPSP SERVER Backend calls [POST /v1/setup] endpoint in DFSP API


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}


## IV. PREPARE PAYMENT ##

![](./PrepareInvoicePayment.jpg)

###  A. Prepare payment request from [ DFSP API ](https://github.com/LevelOneProject/dfsp-api) to [ SPSP CLIENT PROXY ](https://github.com/LevelOneProject/interop-spsp-client-proxy) ###

**Endpoint**

DFSP API calls [POST /v1/transfers/{id}](https://github.com/LevelOneProject/ilp-spsp-client-rest#post-v1setup) endpoint in SPSP Client Proxy


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

###  B. Prepare payment request from [ SPSP CLIENT PROXY ](https://github.com/LevelOneProject/interop-spsp-client-proxy) ###

**Endpoint**

DFSP API calls [POST /v1/transfers/{id}](https://github.com/LevelOneProject/ilp-spsp-client-rest#post-v1setup) endpoint in SPSP Client Proxy


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

###  C. Quote request from [ SPSP CLIENT ](https://github.com/LevelOneProject/ilp-spsp-client-rest) to [ILP Connector] ###

**Endpoint**

SPSP Client calls [GET /quote]endpoint in ILP Connector


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

###  D. Prepare payment request from [ SPSP CLIENT ](https://github.com/LevelOneProject/ilp-spsp-client-rest) to [ ILP LEDGER ADAPTER](https://github.com/LevelOneProject/interop-ilp-ledger) ###

**Endpoint**

SPSP Client calls [GET /quote]endpoint in ILP Ledger Adapter


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

###  E. Prepare payment request from [ ILP LEDGER ADAPTER](https://github.com/LevelOneProject/interop-ilp-ledger) to  [DFSP Ledger](https://github.com/LevelOneProject/dfsp-ledger) ###

**Endpoint**

ILP Ledger Adapter calls [GET /quote] endpoint in DFSP Ledger


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


*Response:*

	201 Created

	{
		"id": "b9c4ceba-51e4-4a80-b1a7-2972383e98af",
		"name":"Bob Dilan",
		"destinationAccount": "dfsp2.bob_dylan.account",
	  	"destinationAmount": "10.40",
	  	"sourceAmount": "9.00",
	  	"sourceAccount": "dfsp1.alice.account",
	  	"expiresAt": "2016-08-16T12:00:00Z",
	  	"data": {
		    "senderIdentifier": "9809890190934023"
	  	},
	  	"additionalHeaders": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
	  	"execution_condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
	  	"cancelation_condition": "dd:0:5:eey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
	}

###  F. Prepare payment notification from [ ILP LEDGER ADAPTER](https://github.com/LevelOneProject/interop-ilp-ledger) to  [ ILP Connector](https://github.com/LevelOneProject/central-ledger) ###

**Endpoint**

ILP Ledger Adapter posts notification at the below websocket endpoint:


*Notification Message:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}



###  G. Prepare payment request from [ ILP CONNECTOR ](https://github.com/LevelOneProject/interop-ilp-ledger) to  [Central Ledger](https://github.com/LevelOneProject/dfsp-ledger) ###

**Endpoint**

ILP Ledger Adapter calls [GET /quote] endpoint in DFSP Ledger


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}

###  G. Prepare payment request from [ ILP CONNECTOR ](https://github.com/LevelOneProject/interop-ilp-ledger) to  [Central Ledger](https://github.com/LevelOneProject/dfsp-ledger) ###

**Endpoint**

ILP Ledger Adapter calls [GET /quote] endpoint in DFSP Ledger


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


  *Response*

  	Comes back as a Websocket notification

 ###  H. Prepare payment request from [ ILP CONNECTOR ](https://github.com/LevelOneProject/interop-ilp-ledger) to  [ Ledger Adapter](https://github.com/LevelOneProject/ilp-ledger-adapter) on the Client DFSP###

**Endpoint**

ILP Connector calls [POST /invoices] endpoint in ILP Ledger Adapter


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


  *Response*

  	Comes back as a Websocket notification

  ###  I. Prepare payment request from [ ILP LEDGER ADAPTER ](https://github.com/LevelOneProject/interop-ilp-ledger) to  [ DFSP Ledger ](https://github.com/LevelOneProject/dfsp-ledger) on the Client DFSP###

**Endpoint**

ILP Ledger Adapter calls [POST /invoices] endpoint in DFSP Ledger


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


  *Response*

  	Comes back as a Websocket notification

 ###  J. Send Websocket Notification from [ ILP LEDGER ADAPTER ](https://github.com/LevelOneProject/interop-ilp-ledger) to  [ ILP Connector ] and SPSP Server###


*Notification Object:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


  *Response*

  	Comes back as a Websocket notification


## V. EXECUTE PAYMENT ##

![](./ExecuteInvoicePayment.jpg)

 ###  A. Execute payment request from [ SPSP Server](https://github.com/LevelOneProject/ilp-spsp-server) to [ ILP LEDGER ADAPTER ](https://github.com/LevelOneProject/ilp-ledger-adapter) ###


**Endpoint**

SPSP Server calls [POST /transfer/{id}/fulfillment] endpoint in ILP Ledger Adapter


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


  *Response*

  	Comes back as a Websocket notification

  ###  B. Execute payment request from [ ILP LEDGER ADAPTER ](https://github.com/LevelOneProject/ilp-ledger-adapter) to [ DFSP Ledger](https://github.com/LevelOneProject/dfsp-ledger) ###


**Endpoint**

ILP Ledger Adapter calls [POST /transfer/{id}/fulfillment] endpoint in DFSP Ledger


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


  *Response*

###  C. Execute complete notification from [ ILP LEDGER ADAPTER ](https://github.com/LevelOneProject/ilp-ledger-adapter) to [ SPSP SERVER ](https://github.com/LevelOneProject/ilp-spsp-server) and  [ ILP CONNECTOR ]###


**Endpoint**

ILP Ledger Adapter sends Wensocket notification to subscribing accounts in SPSP Server and ILP Connector


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


 *Response*

###  D.  Send Execute complete request from [ ILP CONNECTOR ]to [ CENTRAL LEDGER ](https://github.com/LevelOneProject/central-ledger) ###


**Endpoint**

ILP Connector sends request to end point /POST in Central Ledger


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


 *Response*

###  E.  Send Execute complete notification from [ CENTRAL LEDGER ](https://github.com/LevelOneProject/central-ledger) to [ILP Connector] on both the DFSP ###


**Endpoint**

Central Ledger posts Execute Complete Notification 


*Response*

###  F.  Send Execute complete request from [ ILP CONNECTOR ]to [ ILP LEDGER ADAPTER ](https://github.com/LevelOneProject/central-ledger) in DFSP on merchant side ###


**Endpoint**

ILP Connector sends request to end point /POST in Central Ledger


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


 *Response*

###  G.  Send Execute complete request from [ INTEROP LEDGER ADAPTER](https://github.com/LevelOneProject/interop-ilp-ledger) to [ DFSP LEDGER ](https://github.com/LevelOneProject/dfsp-ledger) ###


**Endpoint**

ILP Connector sends request to end point /POST in Central Ledger


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


 *Response*

###  H.  Send Execute complete notification from [ ILP LEDGER ADAPTER ](https://github.com/LevelOneProject/central-ledger) to [ILP CONNECTOR] and [ SPSP CLIENT PROXY ](https://github.com/LevelOneProject/interop-spsp-client-proxy) ###


**Endpoint**

ILP Ledger Adapter sends websocket notification to end point /POST in ILP Connecotor and SPSP Client Proxy


*Request:*

	POST http://dfsp1.spsp-client/v1/setup

	{
  		"receiver": "http://dfsp2.spsp-server/invoice/12345",
  		"sourceAccount": "dfsp1.alice.account",
  		"sourceIdentifier": "9809890190934023"
	}


 *Response*
