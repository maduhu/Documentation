# Quote API

This document proposes an approach for propagading, reserving and applying fees for the payee. It tries to cover all gaps in 'retail' fees between the current state of the system and the requirement document created by Leseley-Ann @ https://www.dropbox.com/s/ixnch73wc34cz4l/Interoperability%20Fee%20Scenarios.docx?dl=0.
Currenlty the DFSPs are capable for calculating and applying fees both for incoming and outgoing transfers based on amount, transaction type and other criterias. The gap that we have identified is that the payer cannot get the information about the fee that is going to be paid by the payee. The document below describes how to handle that gap. In brief the proposal is:

1. Extend getSourceAmount and getDestinationAmount SPSP APIs to get addtional paramteres (tranaction type, destination account, etc.)
2. Those APIs will be integrated with the DFSP-API which will provide for each transaction the fee information for the payee, so there will be only one fee engine per DFSP.
3. Besides the transaction information the payee DFSP will 'reserve' the quote and retun unique identification of the quote reservation


## I. Communication flow

***1. dfsp-api -> spsp-client-proxy***

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
        "transferId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
        "payer": {
          "identifier": "92806391",
          "identifierType": "eur"
        },
        "payee": {
          "identifier": "30754016",
          "identifierType": "eur",
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": [
          {
            "amount": "0.25",
            "currency": "USD"
          }
        ]
    }
    ```

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      {
          "transferId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
          "receiveAmount": {
            "amount": "9.25",
            "currency": "USD"
          },
          "payeeFee": [
            {
              "amount": "1",
              "currency": "USD"
            }
          ],
          "payeeCommission": [
            {
              "amount": "1",
              "currency": "USD"
            }
          ],
          "ilpPacket": "AYGxAAAAAAAAAGQLZXhhbXBsZS5ib2KBmlBTSy8xLjAKTm9uY2U6IHFBdHg1Nk9PUXFyM192dEpvX1JoWkEKRW5jcnlwdGlvbjogbm9uZQpQYXltZW50LUlkOiA1NjM4YmJhYi03ZjJlLTQ4NDAtYWI4Ny1lZjBjMDAxZGFjMGIKCkV4cGlyZXMtQXQ6IDIwMTctMDQtMTFUMTA6MjQ6MTguNzQxWgoKaGVsbG8gd29ybGQA",
          "condition": "ni:///sha-256;47DEQpj8HBSa-_TImW-5JCeuQeRkm5NMpJWZG3hSuFU?fpt=preimage-sha-256&cost=0"
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
          "identifier": "30754016",
          "identifierType": "eur",
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": [
          {
            "amount": "0.25",
            "currency": "USD"
          }
        ]
    }'
    'http://spsp-client-proxy/v1/quotes'
  ```

***2. spsp-client-proxy -> spsp-client***

* **Notes**
    * The spsp-client-proxy should use the 'identifer' and 'identifierType' properties of the payee object to obtain information from the central directory about which the recipient's default DFSP is.
    * In order to be able to authenticate its request to the cenral directory the proxy should use the credentials passed through the authorization header.
    * The proxy should use the information returned by the central directory to correctly construct the submissionUrl

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
        "transferId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
        "payer": {
          "identifier": "92806391",
          "identifierType": "eur"
        },
        "payee": {
          "identifier": "30754016",
          "identifierType": "eur",
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": [
          {
            "amount": "0.25",
            "currency": "USD"
          }
        ],
        "submissionUrl": "http://spsp-server/v1/quotes"
    }
    ```

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
     ```json
      {
          "transferId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
          "receiveAmount": {
            "amount": "9.25",
            "currency": "USD"
          },
          "payeeFee": [
            {
              "amount": "1",
              "currency": "USD"
            }
          ],
          "payeeCommission": [
            {
              "amount": "1",
              "currency": "USD"
            }
          ],
          "ilpPacket": "AYGxAAAAAAAAAGQLZXhhbXBsZS5ib2KBmlBTSy8xLjAKTm9uY2U6IHFBdHg1Nk9PUXFyM192dEpvX1JoWkEKRW5jcnlwdGlvbjogbm9uZQpQYXltZW50LUlkOiA1NjM4YmJhYi03ZjJlLTQ4NDAtYWI4Ny1lZjBjMDAxZGFjMGIKCkV4cGlyZXMtQXQ6IDIwMTctMDQtMTFUMTA6MjQ6MTguNzQxWgoKaGVsbG8gd29ybGQA",
          "condition": "ni:///sha-256;47DEQpj8HBSa-_TImW-5JCeuQeRkm5NMpJWZG3hSuFU?fpt=preimage-sha-256&cost=0"
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
          "identifier": "30754016",
          "identifierType": "eur",
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": [
          {
            "amount": "0.25",
            "currency": "USD"
          }
        ],
        "submissionUrl": "http://spsp-server/v1/quotes"
    }'
    'http://spsp-client/v1/quotes'
  ```


***3. spsp-client -> spsp-server***

* **Notes**
    * The spsp client should submit the request to the submissionUrl passed by the spsp-client-proxy

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
        "transferId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
        "payer": {
          "identifier": "92806391",
          "identifierType": "eur"
        },
        "payee": {
          "identifier": "30754016",
          "identifierType": "eur",
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": [
          {
            "amount": "0.25",
            "currency": "USD"
          }
        ]
    }
    ```

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      {
          "transferId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
          "receiveAmount": {
            "amount": "9.25",
            "currency": "USD"
          },
          "payeeFee": [
            {
              "amount": "1",
              "currency": "USD"
            }
          ],
          "payeeCommission": [
            {
              "amount": "1",
              "currency": "USD"
            }
          ],
          "ilpPacket": "AYGxAAAAAAAAAGQLZXhhbXBsZS5ib2KBmlBTSy8xLjAKTm9uY2U6IHFBdHg1Nk9PUXFyM192dEpvX1JoWkEKRW5jcnlwdGlvbjogbm9uZQpQYXltZW50LUlkOiA1NjM4YmJhYi03ZjJlLTQ4NDAtYWI4Ny1lZjBjMDAxZGFjMGIKCkV4cGlyZXMtQXQ6IDIwMTctMDQtMTFUMTA6MjQ6MTguNzQxWgoKaGVsbG8gd29ybGQA",
          "condition": "ni:///sha-256;47DEQpj8HBSa-_TImW-5JCeuQeRkm5NMpJWZG3hSuFU?fpt=preimage-sha-256&cost=0"
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
          "identifier": "30754016",
          "identifierType": "eur",
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": [
          {
            "amount": "0.25",
            "currency": "USD"
          }
        ]
    }'
    'http://spsp-server/v1/quotes'
  ```

***4. spsp-server -> spsp-server-backend***

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
        "transferId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
        "payer": {
          "identifier": "92806391",
          "identifierType": "eur"
        },
        "payee": {
          "identifier": "30754016",
          "identifierType": "eur",
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": [
          {
            "amount": "0.25",
            "currency": "USD"
          }
        ]
    }
    ```

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      {
          "transferId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
          "receiveAmount": {
            "amount": "9.25",
            "currency": "USD"
          },
          "payeeFee": [
            {
              "amount": "1",
              "currency": "USD"
            }
          ],
          "payeeCommission": [
            {
              "amount": "1",
              "currency": "USD"
            }
          ],
          "ilpPacket": "AYGxAAAAAAAAAGQLZXhhbXBsZS5ib2KBmlBTSy8xLjAKTm9uY2U6IHFBdHg1Nk9PUXFyM192dEpvX1JoWkEKRW5jcnlwdGlvbjogbm9uZQpQYXltZW50LUlkOiA1NjM4YmJhYi03ZjJlLTQ4NDAtYWI4Ny1lZjBjMDAxZGFjMGIKCkV4cGlyZXMtQXQ6IDIwMTctMDQtMTFUMTA6MjQ6MTguNzQxWgoKaGVsbG8gd29ybGQA",
          "condition": "ni:///sha-256;47DEQpj8HBSa-_TImW-5JCeuQeRkm5NMpJWZG3hSuFU?fpt=preimage-sha-256&cost=0"
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
          "identifier": "30754016",
          "identifierType": "eur",
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": [
          {
            "amount": "0.25",
            "currency": "USD"
          }
        ]
    }'
    'http://spsp-server-backend/v1/quotes'
  ```

***5. spsp-server-backend -> dfsp-api***

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
        "transferId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
        "payer": {
          "identifier": "92806391",
          "identifierType": "eur"
        },
        "payee": {
          "identifier": "30754016",
          "identifierType": "eur",
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": [
          {
            "amount": "0.25",
            "currency": "USD"
          }
        ]
    }
    ```

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      {
          "transferId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
          "receiveAmount": {
            "amount": "9.25",
            "currency": "USD"
          },
          "payeeFee": [
            {
              "amount": "1",
              "currency": "USD"
            }
          ],
          "payeeCommission": [
            {
              "amount": "1",
              "currency": "USD"
            }
          ],
          "ilpPacket": "AYGxAAAAAAAAAGQLZXhhbXBsZS5ib2KBmlBTSy8xLjAKTm9uY2U6IHFBdHg1Nk9PUXFyM192dEpvX1JoWkEKRW5jcnlwdGlvbjogbm9uZQpQYXltZW50LUlkOiA1NjM4YmJhYi03ZjJlLTQ4NDAtYWI4Ny1lZjBjMDAxZGFjMGIKCkV4cGlyZXMtQXQ6IDIwMTctMDQtMTFUMTA6MjQ6MTguNzQxWgoKaGVsbG8gd29ybGQA",
          "condition": "ni:///sha-256;47DEQpj8HBSa-_TImW-5JCeuQeRkm5NMpJWZG3hSuFU?fpt=preimage-sha-256&cost=0"
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
          "identifier": "30754016",
          "identifierType": "eur",
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": [
          {
            "amount": "0.25",
            "currency": "USD"
          }
        ]
    }'
    'http://dfsp-api/v1/quotes'
  ```

## II. Additional Information

* The Payee DFSP should calculate the `receiveAmount`,  `payeeFee` and `payeeCommission` based on the parameters it receives
    ```json
    {
        "transferId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
        "payer": {
          "identifier": "92806391",
          "identifierType": "eur"
        },
        "payee": {
          "identifier": "30754016",
          "identifierType": "eur",
        },
        "transferType": "p2p",
        "amountType": "SEND",
        "amount": {
          "amount": "10",
          "currency": "USD"
        },
        "fees": [
          {
            "amount": "0.25",
            "currency": "USD"
          }
        ]
    }
    ```

* The Payee DFSP should calculate the `expiration`, fees and should persist those values along with the request parameters and the calculated `receiveAmount`,  `payeeFee` and `payeeCommission` before returning the following request to the spsp-server:
    ```json
      {
          "transferId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1",
          "receiveAmount": {
            "amount": "9.25",
            "currency": "USD"
          },
          "payeeFee": [
            {
              "amount": "1",
              "currency": "USD"
            }
          ],
          "payeeCommission": [
            {
              "amount": "1",
              "currency": "USD"
            }
          ]
      }
    ```
* The generated `transferId` should be used by the Payer DFSP when executing the transfer.

## III. Error cases

The receiver DFSP should throw an error in either of the following cases:

* It receives a transfer request with transferId which is not associated with a locally reserved quote.
* It receives a transfer request with transferId associated with an already expired quote.
* It receives a transfer request with correct transferId but different parameters than those associated with the already reserved quote (amount, currency, receiver account, transfer type, etc...)

## IV. Notes

* Additional method `(GET /v1/quotes/{transferId})` could be implemented in case there's a need for already reserved quotes to be obtainable.
