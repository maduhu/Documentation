# ILP/SPSP Client RESTful API

The ILP/SPSP Client RESTful API (colloquially called the "ILP Butler") is a RESTful API that components of the Level One Project can use to access the Interledger Protocol (ILP) Client and Simple Payment Setup Protocol (SPSP) Client. This extra layer of abstraction is useful since the ILP Client and SPSP Client are not yet stable, and the reference implementations are being developed in JavaScript, but the components that need to act as a client to the ILP Client and SPSP Client are not JavaScript. Those components can call the ILP/SPSP Client RESTful API, which should provide a more stable interface than the in-development ILP Client and SPSP Client.

## Configuration

The ILP/SPSP Client RESTful API needs to be configured with account credentials for the DFSP Ledger, so that it can interact with the ledger. (The ILP/SPSP Client RESTful API connects indirectly through the **ILP Ledger Adapter**.)

The client server is stateless, so no database is needed.

# Methods

* [Query Receiver - `GET /v1/query`](#get-v1query)
* [Get Quote - `GET /v1/quote/`](#get-v1quote)
* [Prepare Payment - `POST /v1/setup`](#post-v1setup)
* [Execute Payment - `PUT /v1/payments/:uuid`](#put-v1paymentsuuid)

## GET /v1/query

Retrieve information about the receiver. This method instructs the ILP/SPSP Client RESTful API to use the [Simple Payment Setup Protocol](https://github.com/interledger/rfcs/blob/master/0009-simple-payment-setup-protocol/0009-simple-payment-setup-protocol.md) to look up a particular user at a particular address.

### Request

Query Parameters:

| Name       | Type | Description                                              |
|:-----------|:-----|:---------------------------------------------------------|
| `receiver` | URL  | The SPSP Receiver Endpoint to use, including the unique ID of the receiver. |

> **Tip:** The design of this method may seem silly, because it is a request to a URL with another URL as its only parameter. The main purpose of the ILP/SPSP Client RESTful API is to format the response from the given URL into a consistent format even if the other side changes.

### Response

A successful response uses the HTTP response code 200 OK and contains a JSON object describing the receiver. There are two types of receivers, a Payee and an Invoice, with separate formats for each:

#### Payee

A payee represents a person, company, or organization that can receive payments. The response contains the ILP account address of the Payee, and other information that can be used to confirm the Payee's identity.

Example Payee Receiver:

```json
{
  "type": "payee",
  "account": "ilpdemo.red.bob",
  "currency_code": "USD",
  "currency_symbol": "$",
  "name": "Bob Dylan",
  "image_url": "https://red.ilpdemo.org/api/receivers/bob/profile_pic.jpg"
}
```

| Name              | Type        | Description                                |
|:------------------|:------------|:-------------------------------------------|
| `type`            | String      | The value `"payee"` indicates this is a Payee-type Receiver. |
| `account`         | ILP Address | ILP Address of the receiver's account.     |
| `currency_code`   | String      | Currency code of the receiver's currency. Currencies that have [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) codes should use those. Sender UIs SHOULD be able to render non-standard codes. |
| `currency_symbol` | String      | Symbol for the receiver's currency intended for display in the sender's user interface. For example, `"$"` for dollars, or `"shares"` for shares of a security. Sender UIs SHOULD be able to render non-standard symbols. |
| `name`            | String      | Full name of the individual, company, or organization the receiver represents. |
| `image_url`       | HTTPS URL   | (May be omitted) URL that a picture of the receiver can be fetched from. The image MUST be square and SHOULD be 128x128 pixels. |

#### Invoice

An Invoice is a type of receiver for a payment with a specific purpose. Invoices can be paid at most once. In addition to the ILP address and other information that can be used to confirm the Invoice's purpose, the Invoice information contains an exact amount of currency needed to pay the invoice and the current status of the invoice.

Example Invoice Receiver:

```json
{
  "type": "invoice",
  "account": "ilpdemo.red.amazon.111-7777777-1111111",
  "currency_code": "USD",
  "currency_symbol": "$",
  "amount": "10.40",
  "status": "unpaid",
  "invoice_info": "https://www.example.com/gp/your-account/order-details?ie=UTF8&orderID=111-7777777-1111111"
}
```

| Name              | Type           | Description                             |
|:------------------|:---------------|:----------------------------------------|
| `type`            | String         | The value `"invoice"` indicates this is an Invoice-type Receiver. |
| `account`         | ILP Address    | ILP Address of the receiver's account.  |
| `currency_code`   | String         | Currency code of the receiver's currency. Currencies that have [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) codes should use those. Sender UIs SHOULD be able to render non-standard codes. |
| `currency_symbol` | String         | Symbol for the receiver's currency intended for display in the sender's user interface. For example, `"$"` for dollars or `"shares"` for shares of a security. Sender UIs SHOULD be able to render non-standard symbols. |
| `amount`          | Decimal String | Value of the invoice in the receiver's currency. |
| `status`          | String         | The current state of the invoice. Valid states are `"paid"`, `"unpaid"`, or `"cancelled"`. |
| `invoice_info`    | HTTPS URL      | (May be omitted) URL where additional information about the invoice can be found. |


## GET /v1/quote

OPTIONAL: This is an informational endpoint to get a quote for either a fixed source amount or fixed destination amount. You may use the [Setup](#post-v1setup) method without getting a quote first.

The amount returned includes exchange rates, when applicable, and fees from all parties in the payment path.

> **Note:** The quotes returned by this method are non-binding. The connectors involved might change their rates between this and the [Setup](#post-v1setup) step.

### Request

Query Parameters:

| Name                  | Type           | Description                         |
|:----------------------|:---------------|:------------------------------------|
| `destination_address` | ILP Address    | Receiver address (obtained from the [Query method](#get-v1query).) |
| `destination_amount`  | Decimal String | Amount to deliver (for fixed destination amount quotes). |
| `source_amount`       | Decimal String | Amount to send (for fixed source amount quotes). |

You must specify either `destination_amount` or `source_amount`, but not both.

Example request:

```
GET /v1/quote?destination_address=ilpdemo.red.amazon.111-7777777-1111111&destination_amount=11
```

### Response

The response uses the HTTP status code 200 OK and contains a JSON object with the quote. The fields are the same as the request, except that both `source_amount` and `destination_amount` are provided.

Example response:

```json
200 OK

{
  "destination_address": "ilpdemo.red.bob",
  "destination_amount": "11",
  "source_amount": "10"
}
```



## POST /v1/setup

Prepare a payment and get the exact parameters, including the execution crypto-condition, from the receiver. The receiver MAY refuse the payment. (For example, a receiver may refuse a payment that would push the receiver over a daily transaction limit.)

### Request

The request contains a JSON object with fields describing the payment to prepare. The required fields depend on whether the receiver is a Payee or an Invoice.

#### Payee

For a Payee-type receiver, the request contains the following fields:

| Name                 | Type           | Description                          |
|:---------------------|:---------------|:-------------------------------------|
| `receiver`           | URL            | The SPSP Receiver Endpoint to use to set up the payment. This is unique to the receiver. |
| `destination_amount` | Decimal String | Amount the receiver should get, denoted in the receiver's currency. |
| `memo`               | String         | (Optional) Message for the recipient. |
| `source_identifier`  | String         | An identifier of the sender. This SHOULD be in a format the receiver's user interface can understand. |

Example setup request with a Payee as receiver:

```json
POST /v1/setup

{
  "receiver": "https://dfsp.example/api/receivers/bob",
  "destination_amount": "10.40",
  "memo": "Hi Bob!",
  "source_identifier": "9809890190934023"
}
```

#### Invoice

For an Invoice-type receiver, the request contains the following fields:

| Name                | Type   | Description                                   |
|:--------------------|:-------|:----------------------------------------------|
| `receiver`          | URL    | The SPSP Receiver Endpoint to use to set up the payment. This is unique to the receiver. |
| `source_identifier` | String | An identifier of the sender. This SHOULD be in a format the receiver's user interface can understand. |

Example setup request with an Invoice as receiver:

```json
POST /v1/setup

{
  "receiver": "https://dfsp.example/api/receivers/invoices/109820394812309-2345",
  "source_identifier": "9809890190934023"
}
```


### Response

A successful response from this method uses the HTTP code **201 Created** and describes the ILP Payment that has been set up. The format is the same regardless of whether the receiver is an Invoice or Payee.

Example setup response:

```json
201 Created

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

| Name                 | Type                  | Description                   |
|:---------------------|:----------------------|:------------------------------|
| `address`            | ILP Address           | Address the payment will be routed to |
| `destination_amount` | Decimal String        | Amount the receiver will receive, denoted in the receiver's currency |
| `source_amount`      | Decimal String        | Amount the sender will send, denoted in the sender's currency |
| `expires_at`         | ISO 8601 Timestamp    | Expiration time of the request. After this time the receiver will no longer fulfill the condition. |
| `data`               | Object                | Data that will be included in the payment |
| `additional_headers` | Base64-Encoded String | Headers used for routing the payment that the sender should treat as opaque |
| `condition`          | Crypto Condition      | Execution condition for the payment, in string format. |



## PUT /v1/payments/:uuid

Execute a prepared payment. This endpoint is idempotent.

### Request

For the request, the sender creates a new UUID for the payment to use in the method URL. The message body is a JSON object in the same format as the response from the Setup endpoint.

Example payment request:

```json
PUT /v1/payments/b51ec534-ee48-4575-b6a9-ead2955b8069

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

Fields in the request body:

| Name                       | Type                  | Description             |
|:---------------------------|:----------------------|:------------------------|
| `address`                  | ILP Address           | Address the payment will be routed to |
| `destination_amount`       | Decimal String        | Amount the receiver will receive, denoted in the receiver's currency |
| `source_amount`            | Decimal String        | Amount the sender will send, denoted in the sender's currency |
| `expires_at`               | ISO 8601 Timestamp    | Expiration time of the request. After this time the receiver will no longer fulfill the condition. |
| `data`                     | Object                | Arbitrary data that will be included in the payment. |
| `data`.`sender_identifier` | String                | The unique identifier of the sender. |
| `additional_headers`       | Base64-Encoded String | Headers used for routing the payment that the sender should treat as opaque |
| `condition`                | Crypto Condition      | Execution condition for the payment, in string format. |

### Response

A successful response uses the HTTP response code **200 OK** and contains a JSON object describing the payment and its current status. For a canceled or expired payment, the response uses the HTTP response code **422 Unprocessable Entity** and contains a standardized error message.

The fields of the response are the same as the request, plus the following:

| Name          | Type                         | Description                   |
|:--------------|:-----------------------------|:------------------------------|
| `fulfillment` | Crypto-Condition Fulfillment | Proof that the payment has been executed. This field only appears after the payment has completed successfully. |
| `status`      | String                       | State of the transfer. Valid states are `executed`, `cancelled`, `rejected`, or `pending`. |

Example payment response (successfully executed):

```json
200 OK

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

Example payment response (failed payment):

```json
422 Unprocessable Entity

//Note: this example is a placeholder
{
  "errorId": "FulfillmentFailedTimeout",
  "message": "Transfer timed out",
  "debug": {
    "message": "fulfillment for payment b51ec534-ee48-4575-b6a9-ead2955b8069... failed because the current time (2016-09-08T23:18:09Z) is past the expiration for this payment object (2016-08-16T12:00:00Z)."
  }
}
```

> **TODO:** Update the error format when the [standard error type](https://github.com/LevelOneProject/Docs/issues/23) is finalized.


## GET /v1/payments/:uuid

### Request

The request uses the UUID created for this payment in the [execute payment step](#put-v1paymentsuuid).

### Response

The response uses the HTTP response code **200 OK** and contains a JSON object describing the current status of the payment. Example response for an executed payment:

```json
200 OK

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

> **TODO:** Should notifications be done using Websockets, RESThooks, a message queue, or something else?
