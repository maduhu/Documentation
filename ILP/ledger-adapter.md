# ILP Ledger Adapter API

The ILP Ledger Adapter API is a RESTful API served by a ledger adapter, or directly by a ledger. ILP Client applications use the ILP Ledger Adapter API to conduct business in a ledger.

## Overview

The ILP Ledger Adapter API defines the following data types and structures:

- [Transfer object][]
- [Notification object][]
- [Crypto-Condition][] and [Crypto-Condition Fulfillment][]
- [Error Codes](#api-error-codes)

The core operations of the API are:

- [Prepare a transfer](#prepare-tranfser)
    - If the transfer is unconditional, it executes immediately.
    - If the transfer is conditional, it waits for a matching fulfillment.
- [Execute a prepared transfer](#execute-prepared-transfer)
- [Check a transfer's status](#get-transfer-by-id) and [fulfillment](#get-transfer-fulfillment)
- [Subscribe to transfers affecting a specific account](#subscribe-to-account-transfers)
- [Report metadata about the ledger](#get-server-metadata)

### Currency Denominations

The ILP Ledger Adapter operates on a single ledger containing a single currency. The [Server Metadata method](#get-server-metadata) reports the currency and precision used.

For a DFSP that supports multiple currencies, we can run multiple instances of the ILP Ledger Adapter API. For each method described in this document, there would be a version of it prefixed by a different currency code. For example, the [Prepare Transfer method](#prepare-tranfser) could be available for different currencies at the following locations:

* `PUT https://ledger.dfsa.example/TZS/transfers/:id`
* `PUT https://ledger.dfsa.example/KES/transfers/:id`

These instances could be served by the same underlying software and share infrastructure, but it would not be possible to transfer directly from one to the other, since the currency denominations are different. (An [ILP connector](https://github.com/LevelOneProject/Docs/tree/master/ILP) could facilitate cross-currency payments.)

### Scope

The ILP Ledger Adapter API does not define the full range of functionality needed by a functional ledger. (For a full-featured ILP reference ledger, see the [five-bells-ledger project](https://github.com/interledger/five-bells-ledger).) This API only covers the needs of the ILP Client. Some important elements not defined by this API:

- Format for account identifiers. This spec treats them as opaque strings.
- Authentication. The API should ensure that client apps are authorized to perform the requested operations, but this spec does not define how authorization should take place or the details of what auth levels should exist.
- Account management. This API does not include a definition of an account object or CRUD operations on account objects.
- Withdrawals or deposits. This API does not include a way to add or remove money from the ledger. The API methods defined here cannot change the total sum of account balances in the ledger.


## Data Types

### Transfer Object
[Transfer object]: #transfer-object

A transfer represents money being moved around _within a single ledger_. For Level One Project version 1, a transfer always has exactly one debit and exactly one credit.

It can be conditional upon a supplied crypto-condition, in which case it executes automatically when presented with the fulfillment for the condition. (Assuming the transfer has not expired or been canceled first.) If no crypto-condition is specified, the transfer is unconditional, and executes as soon as it is prepared.

Some fields are *Read-only*, meaning they are set by the API and cannot be modified by clients. A transfer object can have the following fields:

| Name | Type | Description |
| ---- | ---- | ----------- |
| `additional_info` | Object | *Optional* Arbitrary fields attached to this transfer. (For example, the IDs of related transfers in other systems.) |
| `cancellation_condition` | [Crypto-Condition][] | *Optional* The condition for executing the transfer |
| `credits` | Array | Array of objects defining who receives how much. For version 1, there is always exactly 1 member object. |
| `credits`[] | Object | Amount of currency and account holder to receive money from this transfer. |
| *`credits`[].* `account` | [URI][] | The full account identifier of a beneficiary. |
| *`credits`[].* `amount` | String | Positive decimal amount of money to credit to a beneficiary. This is a string so that no precision is lost in JSON encoding/decoding. |
| *`credits`[].* `invoice` | [URI][] | *Optional* Unique invoice URI. The ledger must only allow at most one transfer referencing a given invoice ID. |
| *`credits`[].* `memo` | Object | *Optional* Additional information related to the credit |
| `debits` | Array | Array of objects defining who sends how much. For version 1, there is always exactly 1 member object.  |
| `debits`[] | Object | Amount of currency and account holder to send money in this transfer. |
| *`debits`[].* `account` | [URI][] | Account holding the funds |
| *`debits`[].* `amount` | String | Positive decimal amount of money to debit from an originator. This is a string so that no precision is lost in JSON encoding/decoding. |
| *`debits`[].* `invoice` | [URI][] | *Optional* Unique invoice URI. The ledger must only allow at most one transfer referencing a given invoice ID. |
| *`debits`[].* `memo` | Object | *Optional* Additional information related to the debit |
| `execution_condition` | [Crypto-Condition][] | *Optional* The condition for executing the transfer |
| `expires_at` | [Date-Time][] | *Optional* Time when the transfer expires. If the transfer has not executed by this time, the transfer is canceled. |
| `id` | [UUID][] | *Optional* Resource identifier |
| `ledger` | [URI][] | *Optional* The ledger where the transfer will take place |
| `rejection_reason` | String | *Optional, Read-only* Why the transfer was rejected. Valid values are `cancelled` and `expired`. |
| `state` | String | *Optional, Read-only* The current state of the transfer. Valid values are `proposed`, `prepared`, `executed`, and `rejected`. |
| `timeline` | Object | *Optional, Read-only* Timeline of the transfer's state transitions |
| *`timeline`.* `executed_at` | [Date-Time][] | *Optional* When the transfer was originally executed |
| *`timeline`.* `prepared_at` | [Date-Time][] | *Optional* When the transfer was originally prepared |
| *`timeline`.* `rejected_at` | [Date-Time][] | *Optional* When the transfer was originally rejected |

[UUID]: http://en.wikipedia.org/wiki/Universally_unique_identifier

### Notification Object
[Notification object]: #notification-object

The Ledger pushes a notification object to WebSocket clients when a transfer changes state. This notification is sent _at most once_ for each state change. If a transfer advances through multiple steps as part of a single operation, the notification only describes the final state of the transfer. (For example, if an unconditional transfer is proposed, prepared, and executed by one request, there is only a notification that the transfer has reached the "executed" state.)

A notification object can have the following fields:

| Name | Type | Description |
| ---- | ---- | ----------- |
| id | [URI][] | *Optional* Unique identifier for this notification |
| resource | [Transfer object][] | The transfer that is the subject of the notification |
| related_resources | Object | *Optional* Additional resources relevant to the event |
| *related_resources.* cancellation_condition_fulfillment | [Crypto-Condition Fulfillment][] | *Optional* Proof of condition completion |
| *related_resources.* execution_condition_fulfillment | [Crypto-Condition Fulfillment][] | *Optional* Proof of condition completion |


### Crypto-Conditions
[Crypto-Condition]: #crypto-conditions
[Crypto-Condition Fulfillment]: #crypto-conditions

The [Crypto-Conditions spec](https://github.com/interledger/rfcs/tree/master/0002-crypto-conditions) defines standard formats for _conditions_ and _fulfillments_.

Conditions are distributable event descriptions, and fulfillments are cryptographically verifiable messages that prove an event occurred. If you transmit a fulfillment, then everyone who has the corresponding condition can agree that the condition has been met.

Crypto-conditions control the execution or cancellation of conditional transfers. The Ledger Adapter API supports conditions and fulfillments in text format.

The Crypto-Conditions specification anticipates that it will need to expand to keep up with changes in the field of cryptography, so conditions always define which rules and algorithms are necessary to verify the fulfillment. Implementations can use the condition's feature list to determine if they can properly process the fulfillment, without having seen the fulfillment itself.

Example condition in string format:

    cc:0:3:dB-8fb14MdO75Brp_Pvh4d7ganckilrRl13RS_UmrXA:66

Example fulfillment in string format:

    cf:0:VGhlIG9ubHkgYmFzaXMgZm9yIGdvb2QgU29jaWV0eSBpcyB1bmxpbWl0ZWQgY3JlZGl0LuKAlE9zY2FyIFdpbGRl

### URIs
[URI]: #uris

Accounts and some other entities must be identified by unique identifiers. Those identifiers should be formatted as valid [Uniform Resource Identifiers](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier) in accordance with [RFC3986](https://www.ietf.org/rfc/rfc3986.txt).

We _suggest_ that the URIs used should use the `http:` or `https:` schemes in such a way that they can be dereferenced to access the resources they identify through a RESTful API. However, this is not strictly required.


### Date-Time
[Date-Time]: #date-time

All dates and times should be expressed in [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) date-time format with precision to the second or millisecond. The time zone offset should always be `Z`, for no offset. In other words, the date-time should be a **string** matching the one of the following formats:

| Precision   | Example                    |
|:------------|:---------------------------|
| Second      | `YYYY-MM-DDTHH:mm:ss.sssZ` |
| Millisecond | `YYYY-MM-DDTHH:mm:ssZ`     |


## API Methods

### Prepare Transfer

Prepares a new transfer (conditional or unconditional) in the ledger. When a transfer becomes prepared, it executes immediately if there is no condition. If you specify `execution_condition`, the funds are held until the beneficiary [submits the matching fulfillment](#execute-prepared-transfer) or the `expires_at` time is reached.

**Note:** Only the sender (the account being debited) should have the authority to propose a conditional transfer. Only an administrator should have the ability to propose an unconditional transfer.

#### Request Format

```
PUT /transfers/:id
```

##### URL Parameters

| Field | Value | Description |
|-------|-------|-------------|
| `id`  | UUID | A new [UUID][] to identify this Transfer. |

##### Body Parameters

The message body should be a JSON [Transfer object][].

##### Response Format

A successful result uses the HTTP response code **201 Created** and contains a JSON body with the [Transfer object][] as saved.

#### Example

Request:

```
PUT /transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204
Content-Type: application/json

{
  "id": "http://usd-ledger.example/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204",
  "ledger": "http://usd-ledger.example",
  "debits": [{
    "account": "http://usd-ledger.example/accounts/alice",
    "amount": "50",
  }],
  "credits": [{
    "account": "http://usd-ledger.example/accounts/bob",
    "amount": "50"
  }],
  "execution_condition": "cc:0:3:8ZdpKBDUV-KX_OnFZTsCWB_5mlCFI3DynX5f5H2dN-Y:2",
  "expires_at": "2015-06-16T00:00:01.000Z"
}
```

Response:

```json
HTTP/1.1 201 Created

{
  "id": "http://usd-ledger.example/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204",
  "ledger": "http://usd-ledger.example",
  "debits": [{
    "account": "http://usd-ledger.example/accounts/alice",
    "amount": "50"
  }],
  "credits": [{
    "account": "http://usd-ledger.example/accounts/bob",
    "amount": "50"
  }],
  "execution_condition": "cc:0:3:8ZdpKBDUV-KX_OnFZTsCWB_5mlCFI3DynX5f5H2dN-Y:2",
  "expires_at": "2015-06-16T00:00:01.000Z",
  "state": "proposed"
}
```

#### Errors

- [InsufficientFundsError][]
- [UnprocessableEntityError][]
- [AlreadyExistsError][]
- [InvalidUriParameterError][]
- [InvalidBodyError][]


### Execute Prepared Transfer

Execute or cancel a transfer that has already been prepared, by submitting a matching cryto-condition fulfillment. If the prepared transfer has an `execution_condition`, you can submit the fulfillment of that condition to execute the transfer. If the prepared transfer has a `cancellation_condition`, you can submit the fulfillment of that condition to cancel the transfer.

**Note:** Only the recipient of a transfer (the account being credited) should have the authority to use this method to execute the transfer.

#### Request Format

```
PUT /transfers/:id/fulfillment
Content-Type: text/plain
```

**Caution:** This method, specifically, requires the request to specify the header `Content-Type: text/plain` because it accepts fulfillments in text format. In the future, the API might also accept fulfillments in binary fulfillment.

##### URL Parameters

| Field | Value | Description |
|-------|-------|-------------|
| `id`  | UUID | The [UUID][] of the Transfer to execute. |

##### Body Parameters

The message body should be a [Crypto-Condition Fulfillment][] in text format.

##### Response Format

A successful result uses the HTTP response code **200 OK** and contains a JSON body with the submitted fulfillment.

#### Example

Request:

```
PUT /transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204/fulfillment
Content-Type: text/plain

cf:0:_v8
```

Response:

```
HTTP/1.1 200 OK

cf:0:_v8
```

#### Errors

- [UnmetConditionError][]
- [UnprocessableEntityError][]
- [InvalidUriParameterError][]
- [InvalidBodyError][]


### Get Transfer by ID

Check the details or status of a local transfer.

#### Request Format

```
GET /transfers/:id
```

##### URL Parameters

| Field | Value | Description |
|-------|-------|-------------|
| `id`  | UUID | The [UUID][] of the Transfer to retrieve. |

##### Response Format

A successful result uses the HTTP response code **200 OK** and contains a JSON body with the requested [Transfer object][].

#### Example

Request:

```
GET /transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204
```

Response:

```
HTTP/1.1 200 OK

{
  "id": "http://usd-ledger.example/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204",
  "ledger": "http://usd-ledger.example",
  "debits": [{
    "account": "http://usd-ledger.example/accounts/alice",
    "amount": "50"
  }],
  "credits": [{
    "account": "http://usd-ledger.example/accounts/bob",
    "amount": "50"
  }],
  "execution_condition": "cc:0:3:8ZdpKBDUV-KX_OnFZTsCWB_5mlCFI3DynX5f5H2dN-Y:2",
  "expires_at": "2015-06-16T00:00:01.000Z",
  "state": "executed",
  "timeline": {
    "proposed_at": "2015-06-16T00:00:00.000Z",
    "prepared_at": "2015-06-16T00:00:00.500Z",
    "executed_at": "2015-06-16T00:00:00.999Z"
  }
}
```

#### Errors

- [NotFoundError][]
- [InvalidUriParameterError][]


### Get Transfer Fulfillment

Retrieve the fulfillment for a transfer that has been executed or cancelled. This is separate from the [Transfer object][] because it can be very large.

#### Request Format

```
GET /transfers/:id/fulfillment
```

##### URL Parameters

| Field | Value | Description |
|-------|-------|-------------|
| `id`  | UUID | The [UUID][] of the Transfer whose fulfillment to retrieve. |

##### Response Format

A successful result uses the HTTP response code **200 OK** and the header `Content-Type: text/plain`. The body contains the Transfer's [Crypto-Condition Fulfillment][] in text format.

#### Example

Request:

```
GET /transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204/fulfillment
```

Response:

```
HTTP/1.1 200 OK
Content-Type: text/plain

cf:0:_v8
```

#### Errors

- [NotFoundError][]
- [InvalidUriParameterError][]


### Subscribe to Account Transfers
[WebSocket]: https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API

Subscribe to an account's transfers and receive real-time notifications via [WebSocket][].

#### Request Format

```
ws://<host>/accounts/:name/transfers
```

##### URL Parameters

| Field  | Value  | Description |
|--------|--------|-------------|
| `host` | String | The hostname or IP address of the ILP Ledger Adapter server. |
| `name` | String | A unique name of the account to subscribe to. |

##### Response Format

A successful result uses the HTTP response code **101 Switching Protocols** and opens a [WebSocket][] connection to the Ledger Adapter API server.

After establishing the connection, the server sends a JSON [Notification object][] whenever there is a change of state in a transfer affecting the subscribed account. (This includes transfers that credit or debit the account.)

The server ignores messages from the client in the WebSocket connection.

#### Example

Request:

```
GET /accounts/santiago/transfers HTTP/1.1
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
```

Response:

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```

Notification Message in WebSocket Connection:

```
{
    "resource": {
        "id": "http://usd-ledger.example/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204",
        "ledger": "http://usd-ledger.example",
        "debits": [{
            "account": "http://usd-ledger.example/accounts/santiago",
            "amount": "50"
        }],
        "credits": [{
            "account": "http://usd-ledger.example/accounts/rosa",
            "amount": "50"
        }],
        "execution_condition": "cc:0:3:8ZdpKBDUV-KX_OnFZTsCWB_5mlCFI3DynX5f5H2dN-Y:2",
        "expires_at": "2015-06-16T00:00:01.000Z",
        "state": "executed"
    },
    "related_resources" {
        "execution_condition_fulfillment": "cf:0:_v8"
    }
}
```

#### Errors

- [NotFoundError][]
- [InvalidUriParameterError][]
- [UnauthorizedError][]


### Get Server Metadata

Receive information about the ILP Ledger Adapter

#### Request Format

```
GET /
```

##### Response Format

A successful result uses the HTTP response code **200 OK** and contains a JSON object with the following fields:

| Field             | Value     | Description    |
|:------------------|:----------|:---------------|
| `currency_code`   | String    | Three-letter ([ISO 4217](http://www.xe.com/iso4217.php)) code of the currency this ledger tracks. |
| `currency_symbol` | String    | Currency symbol to use in user interfaces for the currency represented in this ledger. For example, "$". |
| `precision`       | Integer   | How many total decimal digits of precision this ledger uses to represent currency amounts. |
| `scale`           | Integer   | How many digits after the decimal place this ledger supports in currency amounts. |
| `urls`            | Object    | (Optional) Paths to other methods exposed by this ledger. Each field name is short name for a method and the value is the path to that method. |
| ...               | (Various) | (Optional) Additional arbitrary values as desired. |

#### Example

Request:

```
GET /
```

Response:

```
HTTP/1.1 200 OK

{
    "currency_code": null,
    "currency_symbol": null,
    "condition_sign_public_key": "YNDefwo4LB+AjkCRzuCSGuAlDLvSCWUxPRX7lXLhV1I=",
    "notification_sign_public_key": "-----BEGIN RSA PUBLIC KEY-----\nMIIBCgKCAQEAnR0o5RIONZy8zwKNxt8ibQtuIu+VDgcZB5MFzFywEvhNFAMXJZyq2ZgER2fb\nXJGfT0CAOMLa3TNcPHvhdHCOnkHSqs7SRLnjnGJuxv/+WyNaFuzrgUT4ymBdtK2LT5j1p7uw\nllxUv9uAjWRz96LUQewjXl38QxE56rp5ov+O+frF2TDN+qFLqgRX1N6kbY6roQRDJ3BFKKqN\nS3mVqMqokeQ5UmYwqAcgmdysoFZFcCkuRdZ1Han/CMDfnhL0mtQmwOhUdOZ4a6dfWNgozycI\nyQOS59ckDp31dRjMZddaSQki/yDIAxmtZHzE4z+U4ZMxEbirwCZbA9QZed2Tu35yQwIDAQAB\n-----END RSA PUBLIC KEY-----\n",
    "urls": {
        "health": "http://usd-ledger.example/health",
        "transfer": "http://usd-ledger.example/transfers/:id",
        "transfer_fulfillment": "http://usd-ledger.example/transfers/:id/fulfillment",
        "transfer_state": "http://usd-ledger.example/transfers/:id/state",
        "connectors": "http://usd-ledger.example/connectors",
        "accounts": "http://usd-ledger.example/accounts",
        "account": "http://usd-ledger.example/accounts/:name",
        "subscription": "http://usd-ledger.example/subscriptions/:id"
    },
    "precision": 10,
    "scale": 2
}
```


#### Errors

- This method does not return any errors under normal circumstances.




## API Error Codes

The Ledger Adapter API may return errors using HTTP codes in the range 400-599, depending on the type of error. The message body of the error response is a JSON object with additional information about the nature of the error.

Every error response contains at least the following fields:

| Field     | Type   | Description |
|:----------|:-------|:------------|
| `id`      | String | A unique error code for this specific type of error, such as `UnmetConditionError`. |
| `message` | String | A longer, human-readable description for the cause of the error. |

Errors may also have additional arbitrary fields describing the cause or context of the error.

**Note:** The error examples in this spec use some additional fields without specifying their format. These are suggestions, not requirements.

### InvalidUriParameterError
[InvalidUriParameterError]: #invaliduriparametererror

At least one provided URI or UUID parameter was invalid.

**HTTP Status Code:** 400 Bad Request

    HTTP/1.1 400 Bad Request
    {
    	"id": "InvalidUriParameterError",
    	"message": "id is not a valid Uuid",
    	"validationErrors": [{
    		"message": "String does not match pattern: ^[a-fA-F0-9]{8}-[a-fA-F0-9]{4}-[a-fA-F0-9]{4}-[a-fA-F0-9]{4}-[a-fA-F0-9]{12}$",
    		"params": {
    			"pattern": "^[a-fA-F0-9]{8}-[a-fA-F0-9]{4}-[a-fA-F0-9]{4}-[a-fA-F0-9]{4}-[a-fA-F0-9]{12}$"
    		},
    		"code": 202,
    		"dataPath": "",
    		"schemaPath": "/pattern",
    		"subErrors": null,
            "stack": "..."
        }]
    }


### InvalidBodyError
[InvalidBodyError]: #invalidbodyerror

The submitted JSON entity does not match the required schema.

**HTTP Status Code:** 400 Bad Request

    HTTP/1.1 400 Bad Request
    {
    	"id": "InvalidBodyError",
    	"message": "Body did not match schema Transfer",
    	"validationErrors": [{
    		"message": "Missing required property: id",
    		"params": {
    			"key": "id"
    		},
    		"code": 302,
    		"dataPath": "",
    		"schemaPath": "/required/0",
    		"subErrors": null,
    		"stack": "..."
    	}]
    }

### NotFoundError
[NotFoundError]: #notfounderror

The requested resource could not be found.

**HTTP Status Code:** 404 Not Found

    HTTP/1.1 404 Not Found
    {
      "id": "NotFoundError",
      "message": "Unknown transfer."
    }

### UnprocessableEntityError
[UnprocessableEntityError]: #unprocessableentityerror

The provided entity is syntactically correct, but there is a generic semantic problem with it.

**HTTP Status Code:** 422 Unprocessable Entity

    HTTP/1.1 422 Unprocessable Entity
    {
      "id": "UnprocessableEntityError",
      "message": "Debits and credits are not equal"
    }

### InsufficientFundsError
[InsufficientFundsError]: #insufficientfundserror

The source account does not have sufficient funds to satisfy the request.

**HTTP Status Code:** 422 Unprocessable Entity

    HTTP/1.1 422 Unprocessable Entity
    {
      "id": "InsufficientFundsError",
      "message": "Sender has insufficient funds.",
      "owner": "santiago"
    }

### AlreadyExistsError
[AlreadyExistsError]: #alreadyexistserror

The specified entity already exists and may not be modified.

**HTTP Status Code:** 422 Unprocessable Entity

    HTTP/1.1 422 Unprocessable Entity
    {
      "id": "AlreadyExistsError",
      "message": "Can't modify transfer after execution."
    }

### UnauthorizedError
[UnauthorizedError]: #unauthorizederror

You do not have permissions to access or modify this resource in the requested way.

**HTTP Status Code:** 403 Forbidden

    HTTP/1.1 403 Forbidden
    {
      "id": "UnauthorizedError",
      "message": "Only the beneficiary can post the fulfillment."
    }

### UnmetConditionError
[UnmetConditionError]: #unmetconditionerror

The submitted fulfillment does not meet the specified condition.

**HTTP Status Code:** 422 Unprocessable Entity

    HTTP/1.1 422 Unprocessable Entity
    {
      "id": "UnmetConditionError",
      "message": "Fulfillment does not match condition.",
      "condition": "cc:2:2b:mJUaGKCuF5n-3tfXM2U81VYtHbX-N8MP6kz8R-ASwNQ:146",
      "fulfillment": "cf:1:DUhlbGxvIFdvcmxkISAABGDsFyuTrV5WO_STLHDhJFA0w1Rn7y79TWTr-BloNGfiv7YikfrZQy-PKYucSkiV2-KT9v_aGmja3wzN719HoMchKl_qPNqXo_TAPqny6Kwc7IalHUUhJ6vboJ0bbzMcBwo"
    }
