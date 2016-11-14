# ILP Ledger Adapter API

The ILP Ledger Adapter API is a RESTful API served by a ledger adapter, or directly by a ledger. ILP Client applications use the ILP Ledger Adapter API to conduct business in a ledger.

## Overview

The ILP Ledger Adapter API defines the following data types and structures:

- [Transfer object][]
- [Message object][]
- [Crypto-Condition][] and [Crypto-Condition Fulfillment][]
- [Error Codes](#api-error-codes)

The core operations of the API are:

- [Prepare a transfer](#prepare-transfer)
    - If the transfer is unconditional, it executes immediately.
    - If the transfer is conditional, it waits for a matching fulfillment.
- [Execute a prepared transfer](#execute-prepared-transfer)
- [Reject Transfer][]
- [Check a transfer's status](#get-transfer-by-id) and [fulfillment](#get-transfer-fulfillment)
- [Send Message](#send-message) to another account
- [Get Auth Token](#get-auth-token) for WebSocket API.
- [Report metadata about the ledger](#get-server-metadata)
- [WebSocket Connection](#websocket-connection) for subscribing to account activity

### Currency Denominations

The ILP Ledger Adapter operates on a single ledger containing a single currency. The [Server Metadata method](#get-server-metadata) reports the currency and precision used.

For a DFSP that supports multiple currencies, we can run multiple instances of the ILP Ledger Adapter API. For each method described in this document, there would be a version of it prefixed by a different currency code. For example, the [Prepare Transfer method](#prepare-transfer) could be available for different currencies at the following locations:

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

Some fields are *Read-only*, meaning they are set by the API and cannot be modified by clients. Fields that are marked *Optional* are optional for clients to provide when submitting transfers, but MUST still be implemented. A transfer object can have the following fields:

| Name | Type | Description |
| ---- | ---- | ----------- |
| `additional_info` | Object | *Optional* Arbitrary fields attached to this transfer. (For example, the IDs of related transfers in other systems.) |
| `cancellation_condition` | [Crypto-Condition][] | *Optional* The condition for executing the transfer |
| `credits` | Array | Array of objects defining who receives how much. For version 1, there is always exactly 1 member object. |
| `credits`[] | Object | Amount of currency and account holder to receive money from this transfer. |
| *`credits`[].* `account` | [URI][] | The full account identifier of a beneficiary. |
| *`credits`[].* `amount` | String | Positive decimal amount of money to credit to a beneficiary. This is a string so that no precision is lost in JSON encoding/decoding. |
| *`credits`[].* `memo` | Object | *Optional* Additional information related to the credit |
| `debits` | Array | Array of objects defining who sends how much. For version 1, there is always exactly 1 member object.  |
| `debits`[] | Object | Amount of currency and account holder to send money in this transfer. |
| *`debits`[].* `account` | [URI][] | Account holding the funds |
| *`debits`[].* `amount` | String | Positive decimal amount of money to debit from an originator. This is a string so that no precision is lost in JSON encoding/decoding. |
| *`debits`[].* `authorized` | Boolean | Marks that this debit has been approved by its account owner. Must be set to `true`. (The Five Bells Ledger sets transfers to `proposed` until this is set to true.) |
| *`debits`[].* `memo` | Object | *Optional* Additional information related to the debit |
| `execution_condition` | [Crypto-Condition][] | *Optional* The condition for executing the transfer |
| `expires_at` | [Date-Time][] | *Optional* Time when the transfer expires. If the transfer has not executed by this time, the transfer is canceled. |
| `id` | [UUID][] | *Optional* Resource identifier |
| `ledger` | [URI][] | *Optional* The ledger where the transfer will take place |
| `rejection_reason` | String | *Optional, Read-only* Why the transfer was rejected. Arbitrary string set in the [Reject Transfer][] method. |
| `state` | String | *Optional, Read-only* The current state of the transfer. Valid values are `proposed`, `prepared`, `executed`, and `rejected`. (The state `rejected` includes transfers that were manually rejected by a credit account and transfers that expired.) |
| `timeline` | Object | *Optional, Read-only* Timeline of the transfer's state transitions |
| *`timeline`.* `prepared_at` | [Date-Time][] | When the transfer was originally prepared. Always present, even on unconditional transfers. MUST be before or equal to the `rejected_at` or `executed_at` date. |
| *`timeline`.* `executed_at` | [Date-Time][] | *Optional* When the transfer was originally executed. Must be present if and only if the transfer's `state` is `executed`. |
| *`timeline`.* `rejected_at` | [Date-Time][] | *Optional* When the transfer expired or was manually rejected. Must be present if and only if the transfer's `state` is `rejected`. |

[UUID]: http://en.wikipedia.org/wiki/Universally_unique_identifier


### Message Resource
[Message object]: #message-resource

Messages are sent through the ledger's [Send Message][] method and received in a [WebSocket][] subscription. All fields of the message are required:

| Field    | Value   | Description                                             |
|:---------|:--------|:--------------------------------------------------------|
| `ledger` | [URI][] | The [URI][] of this ledger. MUST be an HTTP(S) URL where the client can [Get Server Metadata](#get-server-metadata). |
| `from`   | [URI][] | Resource identifier of the account sending the message. |
| `to`     | [URI][] | Resource identifier of the account receiving the message. |
| `data`   | Object  | The message to send, containing arbitrary data. A ledger MAY set a maximum length on messages, but that limit MUST NOT be less than 510 UTF-8 characters or 2,048 bytes. |


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

**Note:** Only the sender (the account being debited) or an admin should have the authority to prepare a transfer.

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

```json
PUT /transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204
Content-Type: application/json

{
  "id": "http://usd-ledger.example/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204",
  "ledger": "http://usd-ledger.example",
  "debits": [{
    "account": "http://usd-ledger.example/accounts/alice",
    "amount": "50",
    "authorized": true
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
    "amount": "50",
    "authorized": true
  }],
  "credits": [{
    "account": "http://usd-ledger.example/accounts/bob",
    "amount": "50"
  }],
  "execution_condition": "cc:0:3:8ZdpKBDUV-KX_OnFZTsCWB_5mlCFI3DynX5f5H2dN-Y:2",
  "expires_at": "2015-06-16T00:00:01.000Z",
  "state": "prepared"
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

```json
HTTP/1.1 200 OK

{
  "id": "http://usd-ledger.example/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204",
  "ledger": "http://usd-ledger.example",
  "debits": [{
    "account": "http://usd-ledger.example/accounts/alice",
    "amount": "50",
    "authorized": true
  }],
  "credits": [{
    "account": "http://usd-ledger.example/accounts/bob",
    "amount": "50"
  }],
  "execution_condition": "cc:0:3:8ZdpKBDUV-KX_OnFZTsCWB_5mlCFI3DynX5f5H2dN-Y:2",
  "expires_at": "2015-06-16T00:00:01.000Z",
  "state": "executed",
  "timeline": {
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


### Send Message
[Send Message]: #send-message

Try to send a notification to another account. The message is only delivered if the other account is subscribed to [account notifications](#subscribe-to-account) on a WebSocket connection. ILP Connectors use this method to share quote information.

#### Request Format

```
POST /messages
```

The request contains a [Message object][] as JSON.

#### Response Format

A successful response uses the HTTP response code **201 Created** and contains no message body or an empty message body.

#### Example

Request:

```json
POST /messages
Content-Type: application/json

{
  "ledger": "http://usd-ledger.example",
  "from": "http://usd-ledger.example/accounts/bob",
  "to": "http://usd-ledger.example/accounts/alice",
  "data": {
    "foo": "bar"
  }
}
```

Response:

```
HTTP/1.1 201 Created
```

**Note:** We expect this to be used for [Interledger Quoting Protocol](https://github.com/interledger/rfcs/blob/master/0008-interledger-quoting-protocol/0008-interledger-quoting-protocol.md) quote request messages. However, the `data` field may contain arbitrary objects, so the ledger MUST NOT require a specific format for such data.

#### Errors

- [InvalidBodyError][]
- [InvalidUriParameterError][]
- [UnprocessableEntityError][]


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
| `connectors`      | Array     | Array of objects describing the ledger's _recommended_ ILP Connectors. Each ILP Connector MUST have an account in the ledger. |
| `connectors[].name` | String | The unique `name` of this connector's account in the ledger. |
| `connectors[].id` | [URI][] | The `id` of this connector's account in the ledger. This MUST be an HTTP(S) URL where a client can [Get Account Info][] on the connector's account. This `id` is used for [messaging](#send-message) when getting a quote from the connector. |
| `precision`       | Integer   | How many total decimal digits of precision this ledger uses to represent currency amounts. |
| `scale`           | Integer   | How many digits after the decimal place this ledger supports in currency amounts. |
| `urls`            | Object    | Paths to other methods exposed by this ledger. Each field name is short name for a method and the value is the path to that method. |
| ...               | (Various) | (Optional) Additional arbitrary values as desired. |

#### Example

Request:

```
GET /
```

Response:

```json
HTTP/1.1 200 OK

{
    "currency_code": null,
    "currency_symbol": null,
    "connectors": [{
      "id": "https://red.ilpdemo.org/ledger/accounts/connie",
      "name": "connie"
    }],
    "urls": {
        "health": "https://usd-ledger.example/health",
        "transfer": "https://usd-ledger.example/transfers/:id",
        "transfer_fulfillment": "http://usd-ledger.example/transfers/:id/fulfillment",
        "accounts": "https://usd-ledger.example/accounts",
        "account": "https://usd-ledger.example/accounts/:name",
        "websocket": "wss://usd-ledger.example/"
    },
    "precision": 10,
    "scale": 2
}
```

#### Errors

- This method does not return any errors under normal circumstances.


### Reject Transfer
[Reject Transfer]: #reject-transfer

Reject a prepared transfer. A transfer can be rejected if and only if that transfer is in the `prepared` state. Doing so transitions the transfer to the `rejected` state.

**Authorization:** Accounts from the `credits` field of a transfer MUST be able to reject the transfer. Others, _especially_ accounts from the `debits` field of a transfer, MUST NOT be able to reject the transfer.

#### Request Format

```
PUT /transfers/:id/rejection
Content-Type: text/plain

your rejection reason here
```

Request MUST use the header `Content-Type: text/plain`. The message body contains the rejection reason as plain text. The rejection reason is an arbitrary string that is intended to be a machine-readable identifier indicating the reason for the transfer's rejection. This string should be no more than 2KB or 512 UTF-8 characters.

#### Response Format

A successful result uses the HTTP response code **200 OK** and contains a JSON object containing the updated [Transfer object](#transfer-object) containing the rejection reason in the `rejection_reason` field.

#### Example

Request:

```
PUT /transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204/rejection
Content-Type: text/plain

BlacklistedSender
```

Response:

```json
HTTP/1.1 200 OK

{
  "id": "http://usd-ledger.example/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204",
  "ledger": "http://usd-ledger.example",
  "debits": [{
    "account": "http://usd-ledger.example/accounts/alice",
    "amount": "50",
    "authorized": true
  }],
  "credits": [{
    "account": "http://usd-ledger.example/accounts/bob",
    "amount": "50"
  }],
  "execution_condition": "cc:0:3:8ZdpKBDUV-KX_OnFZTsCWB_5mlCFI3DynX5f5H2dN-Y:2",
  "expires_at": "2015-06-16T00:00:01.000Z",
  "state": "rejected",
  "timeline": {
    "prepared_at": "2015-06-16T00:00:00.500Z",
    "rejected": "2015-06-16T02:34:02.140Z"
  }
}
```

#### Errors

- [NotFoundError][] - The transfer does not exist.
- [InvalidBodyError][] - The request's message body was too long or the request did not use the proper Content-Type header.


### Get Auth Token
[Get Auth Token]: #get-auth-token

Get a token that can be used to authenticate future requests.

**Note:** This method is REQUIRED for ledgers to authenticate [WebSocket][] connections. If the ledger does not authenticate WebSocket connections, this method is OPTIONAL. The ledger MAY allow clients to authenticate for any other methods of the API using the tokens returned by this method.

#### Request Format

```
GET /auth_token
```

Depending on the authentication mechanism used by this ledger, the ledger MAY require the HTTP `Authorization` header with a valid username and password, or the ledger may require a different method of authentication.

**Metadata URL Name:** `auth_token`.

#### Response Format

A successful response uses the HTTP response code **200 OK** and contains a JSON object with a token that can be used for subsequent requests. The ledger can generate the token with [JWT](https://jwt.io/) or any similar system.

#### Example

Request:

```
GET /auth_token
Authorization: Basic myUsername:securePassphrase
```

Response:

```
HTTP/1.1 200 OK
Content-Type: application/json

{"token": "9AtVZPN3t49Kx07stO813UHXv6pcES"}
```

#### Errors

- [Unauthorized][]


## WebSocket
[WebSocket]: #websocket

Clients can subscribe to live, read-only notifications of ledger activity by opening a [WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) connection to the `/websocket` path and sending a subscription request.

### WebSocket Authentication

Clients MUST authenticate themselves using a token in the `token` query parameter. The authentication you use when opening the connection applies to all subscriptions made in the connection. Non-admin clients can only subscribe to changes to the account they own.

A ledger MUST support multiple independent WebSocket connections with the same authentication. (This provides connection redundancy for notifications, which is important for ILP connectors.)

### WebSocket Messages

After the connection is established, the client and ledger communicate by passing messages back and forth in [JSON-RPC 2.0](http://www.jsonrpc.org/specification) format. Both the client and ledger can take the roles of "client" and "server" in JSON-RPC terms. The client can submit requests to subscribe or unsubscribe from specific categories of message. The ledger responds directly to the client's requests with confirmation messages, and also sends "notification" requests to the client. (Notifications are identified by the `id` value `null`.)

The only subscription type defined at this time is "Subscribe to Account."

#### Subscribe to Account

This request replaces any existing account subscriptions on this WebSocket connection. The client sends a JSON object to the ledger with the following fields:

| Field              | Value            | Description                          |
|:-------------------|:-----------------|:-------------------------------------|
| `id`               | String or Number | An arbitrary identifier for this request. MUST NOT be `null`. The immediate response to this request identifies itself using the same `id`. |
| `jsonrpc`          | String           | MUST have the value `"2.0"` to indicate this is a JSON-RPC 2.0 request. |
| `method`           | String           | MUST be the value `"subscribe_account"`. |
| `params`           | Object           | Information on what subscriptions to open. |
| `params.accounts`  | Array            | Each member of this array must be the [URI][] of an account to subscribe to, as a string. This replaces existing subscriptions; if the array length is zero, the client is unsubscribed from all account notifications. |

##### Response
The ledger acknowledges the request immediately by sending a JSON object with the following fields:

| Field     | Value            | Description                                   |
|:----------|:-----------------|:----------------------------------------------|
| `id`      | String or Number | The `id` value from the request. This helps distinguish responses from other messages. |
| `jsonrpc` | String           | MUST have the value `"2.0"` to indicate this is a JSON-RPC 2.0 message. |
| `result`  | Number           | Updated number of active account subscriptions on this WebSocket connection. In practice, this is usually the length of the `params` array from the request. |

Later, the ledger responds with [notifications](#websocket-notifications) whenever any of the following occurs:

- A transfer affecting the account is prepared (`event` type: `transfer.create`)
- A transfer affecting the account changes state. For example, from prepared to executed or to expired. (`event` type: `transfer.update`)
- Someone [sends a message](#send-message) to the account. (`event` type: `message.send`)

Example:

```
--> {
      "jsonrpc": "2.0",
      "method": "subscribe_account",
      "params": {
        "accounts": ["https://ledger.example/accounts/alice"]
      },
      "id": 1
    }
<-- {
      "jsonrpc": "2.0",
      "result": 1,
      "id": 1
    }
```

#### WebSocket Notifications

The ledger sends notifications to connected clients when certain events occur, according to the current subscriptions of the clients. Every notification is sent at most once per WebSocket connection. If a client has multiple connections open, the ledger MUST attempt to send a message on every open connection.

All event notifications from the ledger are in the format of JSON objects with the following fields:

| Field                      | Value   | Description                           |
|:---------------------------|:--------|:--------------------------------------|
| `jsonrpc`                  | String  | MUST have the value `"2.0"` to indicate this is a JSON-RPC 2.0 notification. |
| `id`                       | Null    | MUST be `null` to indicate a notification. |
| `method`                   | String  | MUST be the value `"notify"`          |
| `params`                   | Object  | Nested object with information about the notification. |
| `params.event`             | String  | The type of event that prompted this notification. Valid types include `transfer.create`, `transfer.update`, and others. See [Event Types](#event-types) for more information. |
| `params.id`                | [UUID][] | _(Optional)_ A [UUID][] to uniquely identify this notification. |
| `params.resource`          | Object  | An object related to `event` that occurred. This is either a [Transfer object][] or a [Message object][]. |
| `params.related_resources` | Object  | _(Not present in all responses)_ A map of additional resources related to this notification in named sub-fields, depending on the `event` type. In particular, this MUST contain the fulfillment (in the field `fulfillment`) when a transfer is updated to the `executed` state. |

##### Event Types
[Event Types]: #event-types

The ledger MUST support the following event types:

| Value             | Resource              | Description                      |
|:------------------|:----------------------|:---------------------------------|
| `transfer.create` | [Transfer object][] | Occurs when a new transfer is prepared. Sent to clients subscribed to the accounts in the `debits` and/or the `credits` fields. If the transfer is unconditional, this notification indicates the state of the transaction after execution. The `related_resources` field is omitted. |
| `transfer.update` | [Transfer object][] | Occurs when a transfer changes state from `prepared` to `executed` or `rejected`. If the transfer was executed, the `execution_condition_fulfillment` field of `related_resources` MUST contain the fulfillment. If the transfer was rejected, the `related_resources` field is empty. |
| `message.send`    | [Message object][]  | Occurs when someone else sends a message. |




## API Error Codes

The Ledger Adapter API may return errors using HTTP codes in the range 400-599, depending on the type of error. The message body of the error response is a JSON object with additional information about the nature of the error.

Every error response contains at least the following fields:

| Field     | Type   | Description |
|:----------|:-------|:------------|
| `id`      | String | A unique error code for this specific type of error, such as `UnmetConditionError`. |
| `message` | String | A longer, human-readable description for the cause of the error. |

Errors may also have additional arbitrary fields describing the cause or context of the error.

**Note:** The error examples in this spec use some additional fields without specifying their format. These are suggestions, not requirements.


### Unauthorized
[Unauthorized]: #unauthorized

The authentication information supplied to this request was insufficient for one of the following reasons:

- This method requires authentication but none was provided
- The credentials were provided using a system not supported by this method (for example, not providing a token when connecting to [WebSocket][])
- The credentials were malformed or don't match the known account information

A ledger MAY return any message body using any content type with this error. (This makes it easier to use proxy servers and stock server configuration to handle authorization.)

**HTTP Status Code:** 401 Unauthorized

    HTTP/1.1 401 Unauthorized
    {
      "error_id": "Unauthorized",
      "message": "Client certificate doesn't match known fingerprint"
    }


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

The submitted message body does not match the required schema or Content-Type for this method.

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
