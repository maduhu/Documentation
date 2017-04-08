
# Bulk Payments

## Bulk payment initiation

In the DFSP there should be a web interface available where every user (with certain user rights) of the DFSP can login with user number and PIN and initiate a bulk payment.

When initiation a bulk payment the user should be able to select an account from which the bulk payments will be send.

L1P shall support upload of the bulk file with payments from a web interface. The file format should be .csv. The fields in the file will be:

- Sequence Number (row)
- User Number or Account address
- Client Name
- Amount

**Note** Currently we do not have nationalID in the system, that's why we didn't put it in the file format

**Question:** Are we going to allow sending funds by recipient address? Currently in the system identification of the recipient is done only by user number?



## Maker/Checker

There should be a maker/checker concept implemented for bulk payments. Maker and Checker will be roles that can be assigned to different users. Maker role is going to upload the file with the bulk payments and checker role is going the bulk payment.

**Question:** Currently we do not have enabled users / roles / permission management in our web interface. Shall we implement as a part of this PI?

**Question:** if we implement users/roles/permissions shall implement as well ability each user of the system to be able to add other users for (maker/checkers)?


## Bulk file verification

All the fields in the bulk file shall be mandatory.


In case the user number or account address are not valid the funds will not be disbursed to this user.

**Question:** Do we have to validate recipient names in the bulk file against the one recorded in DFSP and reject the transaction if those do not match? All of the real lift implementations that we did so far,  do not require additional check whether the names match to the account/user in DFSP


## Handling fees

The discriminatory fees should be recorded in the same way as it is done for a regular transaction - different row in the ledger for each payment. There is possibility to configure different fees per different transaction type, so there could be special set of fee configured for the bulk payments.
The rational behind having the fees recorded as a separate line for each transaction is:

- Calculation and record of the discriminatory fees are done within the DFSP and we do not foresee any performance issues to come from that approach.

- In the real world the fees are recorded as a separate row in the ledger (conversation with Lesley-Ann)

**Question:** How will handle non-discriminatory fees?

**Question:** Do we need to verify if payers account have enough funds counting as well the discriminatory fees which may be applied on the receiver's side?



## Re-sending of funds in case a temporary error is detected

Upon sending bulk payments the system should detect temporary errors and retry sending of funds again to those user.

Temporary errors could be technical such as missing connectivity or some service is down or non technical such as not enough funds in the account, tier limit is hit, etc.

The system should detect a special case - a user has already a user number but for a service different from mWallet/bank account. In this case the system should notify the user to open a mWallet/bank account.

**Question:** Shall implement SMS notifications or leave them for the next PI?

When initiating a bulk payment the system shall allow entering of the end date/time after which the bulk report will be finalized and no more retries to recover from temporary errors will be done.

Central Directory should be a able to register and report back user numbers for which there are different then mwallet/bank accounts. For those user numbers a notification URL should be kept instead of SPSP Server URL.

The L1P should have a minimum retry time. (e.g., no less than 15 minutes) for bulk payments. This should be a configuration on DFSP level

**Question:** Do you see any reason this minimum retry time to be set by the payer?

The L1P should enable a system administrator to manually retry or trigger distribution.

**Question:** Do you see a reason this functionality to be a part of the operation hub?

## Out-of Scope Scenarios

The L1P shall reject component transactions where named participants are ineligible to transact.

Rationale: Payments must be subject to fraud and AML/CFT checks, and blocked where the participant is deemed an unacceptable recipient. www.gatesfoundation.org Requirements for a Pro-Poor Interoperability Service for Transfers

*Currently we don’t have such components in the system to support this scenario*


## Handling bulk payments transactions


Bulk payment transactions should be processed in batches, not one by one. The sender's DFSP first should query the central directory to get the default DFSP by user number for each payments in the batch.
The central directory will also return information about the users that have a subscription to other services within a DFSP but does not have mWallet/bank account.

The following changes have to be implemented:

** Central directory get user API**

Current Request:

    GET http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/directory/v1/resources?identifierType=eur&identifier=26547070

Current Response:

    {
      "spspReceiver": "http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:3043/v1"
    }

- To be able to query for many users we should enable posting of multiple 'identifierTypes' and 'identifiers'
- To support posting to a user number which does not have mWallet/bank account opened, we need to define another 'identifierType' e.g. phone subscribers and to be able to do a query of if. The response shouldn't be a spspServer, but a 'notificationURL'


** SPSP Protocol**


- Get Payee Details (changes in this API is needed only if we have to compare names in DFSP against names from the uploaded bulk file)

Current Request:

    GET http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/query?receiver=http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:3043/v1/receivers/26547070

Current Response:

	{
	  "type": "payee",
	  "name": "bob",
	  "account": "levelone.dfsp1.bob",
	  "currencyCode": "USD",
	  "currencySymbol": "$",
	  "imageUrl": "https://red.ilpdemo.org/api/receivers/bob/profile_pic.jpg"
	}

We have to enable API to be able to query for multiple receivers

** Quote Destination Amount **
This API is needed only if we have to get connector fees.

Current Request:

    GET http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/quoteDestinationAmount?receiver=http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:3043/v1/receivers/26547070&destinationAmount=32


Current Response:

    {
      "sourceAmount": "32"
    }

This API should be changed to support multiple receivers/amounts and get back the information for them

** Setup/Payment **

Current Request:

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

Current Response:

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

This API should be changed to support multiple receivers/amounts and get back the information for them




** ILP Protocol to support multiple payments (Ripple to advice)**
