# ILP/SPSP Client RESTful API

This is the main entry point for clients to use the Interledger Protocol (ILP) and Simple Payment Setup Protocol (SPSP).

## Configuration

The client server is configured with account credentials so that it can communicate with the ledger on the client's behalf.

The client server is stateless, so no database is needed.

## GET /v1/query

Retrieve information about the receiver.

### Request

Query Parameters:

| Name | Type | Description |
|---|---|---|
| `receiver` | URI | Receiver endpoint |

See the [Simple Payment Setup Protocol](https://github.com/interledger/rfcs/blob/master/0009-simple-payment-setup-protocol/0009-simple-payment-setup-protocol.md) for more information about the receiver endpoint.

### Response

There are two types of receivers, with two types of responses:

#### Payee

Payee information consists of basic account details.

Example Receiver:
``` json
{
  "type": "payee",
  "account": "ilpdemo.red.bob",
  "currency_code": "USD",
  "currency_symbol": "$",
  "name": "Bob Dylan",
  "image_url": "https://red.ilpdemo.org/api/receivers/bob/profile_pic.jpg"
}
```

| Name | Type | Description |
|---|---|---|
| `type` | `"payee"` | Receiver type |
| `account` | ILP Address | ILP Address of the recipient's account |
| `currency_code` | String | Currency code to identify the receiver's currency. Currencies that have [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) codes should use those. Sender UIs SHOULD be able to render non-standard codes |
| `currency_symbol` | String | Symbol for the receiver's currency intended for display in the sender's UI (e.g. `"$"` or `"shares"`). Sender UIs SHOULD be able to render non-standard symbols |
| `name` | String | Full name of the individual, company or organization the receiver represents |
| `image_url` | HTTPS URL | URL that a picture of the recipient can be fetched from. The image MUST be square and SHOULD be 128x128 pixels. |

#### Invoice

Invoice information includes an exact amount as well as the status of the invoice. (Invoices can only be paid once.)

Example Receiver:
``` json
{
  "type": "invoice",
  "account": "ilpdemo.red.amazon.111-7777777-1111111",
  "currency_code": "USD",
  "currency_symbol": "$",
  "amount": "10.40",
  "status": "unpaid",
  "invoice_info": "https://www.amazon.com/gp/your-account/order-details?ie=UTF8&orderID=111-7777777-1111111"
}
```

| Name | Type | Description |
|---|---|---|
| `type` | `"invoice"` | Receiver type |
| `account` | ILP Address | ILP Address of the recipient's account |
| `currency_code` | String | Currency code to identify the receiver's currency. Currencies that have [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) codes should use those. Sender UIs SHOULD be able to render non-standard codes |
| `currency_symbol` | String | Symbol for the receiver's currency intended for display in the sender's UI (e.g. `"$"` or `"shares"`). Sender UIs SHOULD be able to render non-standard symbols |
| `amount` | Decimal String | Value of the invoice in the recipient's currency |
| `status` | Enum: `"paid"`, `"unpaid"`, `"cancelled"` | State of the invoice |
| `invoice_info` | URI | URI where additional information about the invoice can be found. |


## GET /v1/quote

OPTIONAL: This is an informational endpoint to get a quote for either a fixed source amount or fixed destination amount. The Setup command may be used without getting a quote first.

The amount returned includes exchange rates, when applicable, and fees from all parties in the payment path.

### Request

Query Parameters:

| Name | Type | Description |
|---|---|---|
| `destination_address` | ILP Address | Recipient address (obtained from Query) |
| `destination_amount` | Decimal String | For fixed destination amount quotes |
| `source_amount` | Decimal String | For fixed source amount quotes |

Either `destination_amount` or `source_amount` is required.

### Response

Example quote:
```json
{
  "destination_address": "ilpdemo.red.bob",
  "destination_amount": "11",
  "source_amount": "10"
}
```

## POST /v1/setup

Set up a payment and get the exact parameters, including the execution condition, from the receiver. This step also allows receivers to refuse payments (for example, if they would push the recipient over a daily transaction limit).

### Request

#### Payee

Example setup request:
```json
{
  "receiver": "https://dfsp.example/api/receivers/bob",
  "destination_amount": "10.40",
  "memo": "Hi Bob!",
  "source_identifier": "9809890190934023"
}
```

| Name | Type | Description |
|---|---|---|
| `receiver` | URI | Receiver endpoint |
| `destination_amount` | Decimal String | Amount the recipient will receive, denoted in the receiver's currency |
| `memo` | String | Message for the recipient |
| `source_identifier` | String | ID for the sender |

#### Invoice

Example setup request:
```json
{
  "receiver": "https://dfsp.example/api/receivers/invoices/109820394812309-2345",
  "source_identifier": "9809890190934023"
}
```

| Name | Type | Description |
|---|---|---|
| `receiver` | URI | Receiver endpoint |
| `source_identifier` | String | ID for the sender |

### Response:

Example setup response:
```json
{
  "address": "ilpdemo.red.bob.b9c4ceba-51e4-4a80-b1a7-2972383e98af",
  "destination_amount": "10.40",
  "source_amount": "9.00",
  "expires_at": "2016-08-16T12:00:00Z",
  "data": {
    "sender_identifier": "9809890190934023"
  },
  "additional_headers": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
  "condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
}
```

| Name | Type | Description |
|---|---|---|
| `address` | ILP Address | Address the payment will be routed to |
| `destination_amount` | Decimal String | Amount the receiver will receive, denoted in the receiver's currency |
| `source_amount` | Decimal String | Amount the sender will send, denoted in the sender's currency |
| `expires_at` | ISO 8601 Timestamp | Expiry of the request. After this time the receiver will no longer fulfill the condition. |
| `data` | Object | Data that will be included in the payment |
| `additional_headers` | Base64-Encoded String | Headers used for routing the payment that the sender should treat as opaque |
| `condition` | Crypto Condition | Execution condition for the payment |

## PUT /v1/payments/:uuid

Execute a payment. This endpoint is idempotent.

### Request

This is the response from the Setup endpoint. Note the `uuid` is chosen by the client.

Example payment request:
```json
{
  "address": "ilpdemo.red.bob.b9c4ceba-51e4-4a80-b1a7-2972383e98af",
  "destination_amount": "10.40",
  "source_amount": "9.00",
  "expires_at": "2016-08-16T12:00:00Z",
  "data": {
    "sender_identifier": "9809890190934023"
  },
  "additional_headers": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
  "condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32"
}
```

### Response

Example payment response:
```json
{
  "address": "ilpdemo.red.bob.b9c4ceba-51e4-4a80-b1a7-2972383e98af",
  "destination_amount": "10.40",
  "source_amount": "9.00",
  "expires_at": "2016-08-16T12:00:00Z",
  "data": {
    "sender_identifier": "9809890190934023"
  },
  "additional_headers": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
  "condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
  "fulfillment": "cf:0:qUAo3BNo49adBtbYTab2L5jAWLpAhnrkNQamsMYjWvM",
  "status": "executed"
}
```

| Name | Type | Description
|---|---|---|
|  |  | (same as above) |
| `fulfillment` | Crypto Condition Fulfillment | Proof that the payment has been executed. This field will be added when the payment is completed successfully. |
| `status` | `executed`, `cancelled`, `rejected`, `pending` | State of the transfer |

OR

```json
{
  "status": "cancelled",
  "message": "Transfer timed out"
}
```

## GET /v1/payments/:uuid

### Response

Example executed payment:

```json
{
  "address": "ilpdemo.red.bob.b9c4ceba-51e4-4a80-b1a7-2972383e98af",
  "destination_amount": "10.40",
  "source_amount": "9.00",
  "expires_at": "2016-08-16T12:00:00Z",
  "data": {
    "sender_identifier": "9809890190934023"
  },
  "additional_headers": "asdf98zxcvlknannasdpfi09qwoijasdfk09xcv009as7zxcv",
  "condition": "cc:0:3:wey2IMPk-3MsBpbOcObIbtgIMs0f7uBMGwebg1qUeyw:32",
  "fulfillment": "cf:0:qUAo3BNo49adBtbYTab2L5jAWLpAhnrkNQamsMYjWvM",
  "status": "executed"
}
```

## Notifications

**TODO**: Should notifications be done using Websockets, RESThooks, a message queue, or something else?
