# Quote API

This document proposes an approach for propagating, reserving and applying fees for the payee. It tries to cover all gaps in 'retail' fees between the current state of the system and the requirement document created by Leseley-Ann @ https://www.dropbox.com/s/ixnch73wc34cz4l/Interoperability%20Fee%20Scenarios.docx?dl=0.
Currenlty the DFSPs are capable for calculating and applying fees both for incoming and outgoing transfers based on amount, transaction type and other criterias. The gap that we have identified is that the payer cannot get the information about the fee that is going to be paid by the payee. The document below describes how to handle that gap. In brief the proposal is:

1. Besides the transaction information the payee DFSP will 'reserve' the quote and retun unique identification of the quote reservation


## I. Communication flow

***1. dfsp-api -> scheme-adapter***

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

  /v1/quotes

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
          "url": "payee-dfsp-scheme-adaptor-baseUrl",
          "identifier": "30754016",
          "identifierType": "eur",
          "account":"http://ledger-account-url"
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
          "expiresAt": "2017-06-14T00:00:01.000Z"
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
          "url": "http://payee-dfsp-scheme-adaptor-baseUrl",
          "identifier": "30754016",
          "identifierType": "eur",
        },
        "transferType": "PROXY",
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
    'http://dfsp-api/v1/quotes'
  ```

## II. Additional Information

* The Payee DFSP should calculate the `receiveAmount`,  `payeeFee` and `payeeCommission` based on the parameters it receives
    ```json
    {
        "paymentId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
        "payer": {
          "identifier": "92806391",
          "identifierType": "eur"
        },
        "payee": {
          "url": "host:8088/scheme/adapter/v1",
          "identifier": "30754016",
          "identifierType": "eur",
          "account":"ledger-account-url"
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

* The Payee DFSP should calculate the `expiration`, fees and should persist those values along with the request parameters and the calculated `receiveAmount`,  `payeeFee` and `payeeCommission` before returning the following request to the spsp-server:
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
          }
      }
    ```
* The generated `paymentId`, `ipr` should be used by the Payer DFSP when executing the transfer.

## III. Error cases

The receiver DFSP should throw an error in either of the following cases:

* It receives a transfer request with paymentId which is not associated with a locally reserved quote.
* It receives a transfer request with paymentId associated with an already expired quote.
* It receives a transfer request with correct paymentId but different parameters than those associated with the already reserved quote (amount, currency, receiver account, transfer type, etc...)

## IV. Notes

* Additional method `(GET /v1/quotes/{paymentId})` could be implemented in case there's a need for already reserved quotes to be obtainable.