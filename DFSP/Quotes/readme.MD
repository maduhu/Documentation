# Quote API

This document proposes a redesign of the current approach of reserving quotes before transfers are initiated within the DFSP network. Currenlty the DFSPs are capable for calculating and applying fees both for incoming and outgoing transfers based on amount, transaction type and other criterias. The gap that we have identified is that the payee DFSP should not persist the quotes it generates but instead encode them into the ipr it signs which later must be used for initiationg a transfer.

## I. Communication flow

***1. dfsp-api(payer) -> scheme-adapter***

* **URL**

  scheme/adapter/v1/quotes

* **Headers**

  * Content-Type: application/json
  * Accept: application/json

* **Method:**

  `POST`

* **URL Params:**

  None

* **Data Params:**
     ```json
    {
        "paymentId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
        "payer": {
          "identifier": "92806391",
          "identifierType": "eur"
        },
        "payee": {
          "url": "payee-dfsp-scheme-adaptor-baseUrl",
          "identifier": "30754016",
          "identifierType": "eur",
          "account":"http://payee-account"
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": {
          "amount": "0.25",
          "currency": "USD"
        }
    }
    ```

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      {
          "paymentId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
          "receiveAmount": {
            "amount": "9.25",
            "currency": "USD"
          },
          "payeeFee": {
            "amount": "1",
            "currency": "USD"
          },
          "payeeCommission": {
            "amount": "1",
            "currency": "USD"
          },
          "connectorAccount":"http://host:port/scheme/adapter/v1/ilp/ledger/v1/accounts/dfsp1-testconnector",
          "ipr": "c29tZSBpcHIgaGVyZQ==",
          "sourceExpiryDuration": 10
      }
    ```

* **Sample Call:**

  ```curl
    curl -X POST --header 'Accept: application/json'  -d
    '{
        "paymentId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
        "payer": {
          "identifier": "92806391",
          "identifierType": "eur"
        },
        "payee": {
          "url": "<host>:8088/scheme/adapter/v1",
          "identifier": "30754016",
          "identifierType": "eur",
          "account":"http://host:port/ledger/v1/accounts/alice"
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": {
          "amount": "0.25",
          "currency": "USD"
        }
    }'
    'http://spsp-client-proxy/v1/quotes'
  ```

***2. scheme-adapter -> dfsp-api(payee)***

* **URL**

  /quotes

* **Headers**

  * Content-Type: application/json
  * Accept: application/json
  * Authorization: Basic dGVzdDoxMjM=

* **Method:**

  `POST`

* **URL Params:**

  None

* **Data Params:**
     ```json
    {
        "paymentId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
        "payer": {
          "identifier": "92806391",
          "identifierType": "eur"
        },
        "payee": {
          "identifier": "30754016",
          "identifierType": "eur",
          "account":"dfsp-ledger-account"
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": {
          "amount": "0.25",
          "currency": "USD"
        }
    }
    ```

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      {
          "paymentId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
          "receiveAmount": {
            "amount": "9.25",
            "currency": "USD"
          },
          "payeeFee": {
            "amount": "1",
            "currency": "USD"
          },
          "payeeCommission": {
            "amount": "1",
            "currency": "USD"
          },
          "expiresAt": "2017-06-14T00:00:01.000Z",
          "data": {
            // anything can be put here
          }
      }
    ```

* **Sample Call:**

  ```curl
    curl -X POST --header 'Accept: application/json' --header 'Authorization: Basic dGVzdDoxMjM=' -d
    '{
        "transferId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
        "payer": {
          "identifier": "92806391",
          "identifierType": "eur"
        },
        "payee": {
          "account":"dfsp-ledger-account",
          "identifier": "30754016",
          "identifierType": "eur",
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": {
          "amount": "0.25",
          "currency": "USD"
        }
    }'
    'http://dfsp-api/quotes'
  ```

## II. Important clarifications

* When called on POST /quote The payee dfsp will return a "data" object which will include information about the quote it had reserved.
* That "data" object should be encoded into the ipr which will be later used for initiating a transfer. This way the necessity for the quote to be persisted on payee's side gets eliminated.
* The generated `paymentId`, `ipr` and `connectorAccount` should be used by the Payer DFSP when executing the transfer and passed along with `sourceAmount`, `sourceAccount` and `sourceExpiryDuration` properties.
i.e.

```
POST http://payer-scheme-adapter-host:8088/scheme/adapter/v1/payment HTTP/1.1
Content-Type: application/json
{
   "paymentId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
   "ipr": "c29tZSBpcHIgaGVyZQ==",
   "connectorAccount":"http://host:port/scheme/adapter/v1/ilp/ledger/v1/accounts/dfsp1-testconnector",
   "sourceAmount":"10",
   "sourceAccount":"payer-dfsp-ledger-account",
   "sourceExpiryDuration":"10"
}
```
* the data object that is encoded into the ipr should be passed in the credit memo when preparing the transfer in payee's dfsp-ledger.
* In the request for preparing the transfer in payer's dfsp-ledger there should not be credit memo as the dfsp should not be able to decode the ipr encoded by another dfsp (unless both participants are in the same DFSP).
