# Quote API

This document proposes an approach for propagading, reserving and applying fees for the payee. It tries to cover all gaps in 'retail' fees between the current state of the system and the requirement document created by Leseley-Ann @ https://www.dropbox.com/s/ixnch73wc34cz4l/Interoperability%20Fee%20Scenarios.docx?dl=0. 
Currenlty the DFSPs are capable for calculating and applying fees both for incoming and outgoing transfers based on amount, transaction type and other criterias. The gap that we have identified is that the payer cannot get the information about the fee that is going to be paid by the payee. The document below describes how to handle that gap. In brief the proposal is:

1. Extend getSourceAmount and getDestinationAmount SPSP APIs to get addtional paramteres (tranaction type, destination account, etc.)
2. Those APIs will be integrated with the DFSP-API which will provide for each transaction the fee information for the payee, so there will be only one fee engine per DFSP. 
3. Besides the transaction information the payee DFSP will 'reserve' the quote and retun unique identification of the quote reservation


## I. Communication flow

***1. dfsp-api -> spsp-client-proxy***

* **URL**

  /v1/quoteSourceAmount

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
        "currencyCode": "USD",
        "currencySymbol": "$",
        "sourceAmount": "12.50",
        "account": "http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/accounts/bob",
        "identifier": "30754016",
        "identifierType": "eur",
        "transferType": "p2p"
    }
    ```

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      {
          "destinationAmount": "13.75",
          "expires": "2017-05-11T11:30:03.561Z",
          "paymentId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1"
      }
    ```

* **Sample Call:**

  ```curl
    curl -X POST --header 'Accept: application/json' --header 'Authorization: Basic dGVzdDoxMjM=' -d
    '{
        "currencyCode": "USD",
        "currencySymbol": "$",
        "sourceAmount": "12.50",
        "account": "http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/accounts/bob",
        "identifier": "30754016",
        "identifierType": "eur",
        "transferType": "p2p"
    }'
    'http://spsp-client-proxy/v1/quoteSourceAmount'
  ```

***2. spsp-client-proxy -> spsp-client***

* **Notes**
    * The spsp-client-proxy should use the 'identifer' and 'identifierType' parameters to obtain information from the central directory about which the recipient's default DFSP is.
    * In order to be able to authenticate its request to the cenral directory the proxy should use the credentials passed through the authorization     header.
    * The proxy should use the information returned by the central directory to correctly construct the submissionUrl

* **URL**

  /v1/quoteSourceAmount

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
        "currencyCode": "USD",
        "currencySymbol": "$",
        "sourceAmount": "12.50",
        "account": "http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/accounts/bob",
        "identifier": "30754016",
        "identifierType": "eur",
        "transferType": "p2p",
        "submissionUrl": "http://spsp-server/v1/quoteSourceAmount"
    }
    ```

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      {
          "destinationAmount": "13.75",
          "expires": "2017-05-11T11:30:03.561Z",
          "paymentId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1"
      }
    ```

* **Sample Call:**

  ```curl
    curl -X POST --header 'Accept: application/json' --header 'Authorization: Basic dGVzdDoxMjM=' -d
    '{
        "currencyCode": "USD",
        "currencySymbol": "$",
        "sourceAmount": "12.50",
        "account": "http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/accounts/bob",
        "identifier": "30754016",
        "identifierType": "eur",
        "transferType": "p2p",
        "submissionUrl": "http://spsp-server/v1/quoteSourceAmount"
    }'
    'http://spsp-client/v1/quoteSourceAmount'
  ```


***3. spsp-client -> spsp-server***

* **Notes**
    * The spsp client should submit the request to the submissionUrl passed by the spsp-client-proxy

* **URL**

  /v1/quoteSourceAmount

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
        "currencyCode": "USD",
        "currencySymbol": "$",
        "sourceAmount": "12.50",
        "account": "http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/accounts/bob",
        "identifier": "30754016",
        "identifierType": "eur",
        "transferType": "p2p"
    }
    ```

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      {
          "destinationAmount": "13.75",
          "expires": "2017-05-11T11:30:03.561Z",
          "paymentId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1"
      }
    ```

* **Sample Call:**

  ```curl
    curl -X POST --header 'Accept: application/json' --header 'Authorization: Basic dGVzdDoxMjM=' -d
    '{
        "currencyCode": "USD",
        "currencySymbol": "$",
        "sourceAmount": "12.50",
        "account": "http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/accounts/bob",
        "identifier": "30754016",
        "identifierType": "eur",
        "transferType": "p2p"
    }'
    'http://spsp-server/v1/quoteSourceAmount'
  ```

***4. spsp-server -> spsp-server-backend***

* **URL**

  /v1/quoteSourceAmount

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
        "currencyCode": "USD",
        "currencySymbol": "$",
        "sourceAmount": "12.50",
        "account": "http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/accounts/bob",
        "identifier": "30754016",
        "identifierType": "eur",
        "transferType": "p2p"
    }
    ```

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      {
          "destinationAmount": "13.75",
          "expires": "2017-05-11T11:30:03.561Z",
          "paymentId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1"
      }
    ```

* **Sample Call:**

  ```curl
    curl -X POST --header 'Accept: application/json' --header 'Authorization: Basic dGVzdDoxMjM=' -d
    '{
        "currencyCode": "USD",
        "currencySymbol": "$",
        "sourceAmount": "12.50",
        "account": "http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/accounts/bob",
        "identifier": "30754016",
        "identifierType": "eur",
        "transferType": "p2p"
    }'
    'http://spsp-server-backend/v1/quoteSourceAmount'
  ```

***5. spsp-server-backend -> dfsp-api***

* **URL**

  /v1/quoteSourceAmount

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
        "currencyCode": "USD",
        "currencySymbol": "$",
        "sourceAmount": "12.50",
        "account": "http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/accounts/bob",
        "identifier": "30754016",
        "identifierType": "eur",
        "transferType": "p2p"
    }
    ```

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      {
          "destinationAmount": "13.75",
          "expires": "2017-05-11T11:30:03.561Z",
          "paymentId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1"
      }
    ```

* **Sample Call:**

  ```curl
    curl -X POST --header 'Accept: application/json' --header 'Authorization: Basic dGVzdDoxMjM=' -d
    '{
        "currencyCode": "USD",
        "currencySymbol": "$",
        "sourceAmount": "12.50",
        "account": "http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/accounts/bob",
        "identifier": "30754016",
        "identifierType": "eur",
        "transferType": "p2p"
    }'
    'http://dfsp-api/v1/quoteSourceAmount'
  ```

## II. Additional Information

* The receiver DFSP should calculate the amount based on the parameters it receives
     ```json
    {
        "currencyCode": "USD",
        "currencySymbol": "$",
        "sourceAmount": "12.50",
        "account": "http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/accounts/bob",
        "identifier": "30754016",
        "identifierType": "eur",
        "transferType": "p2p"
    }
    ```

* The receiver DFSP should generate a unique Id `paymentId` and expiration date `expires` and should persist those values along with the request parameters and the calculated amount before returning the following request to the spsp-server:
    ```json
      {
          "destinationAmount": "13.75",
          "expires": "2017-05-11T11:30:03.561Z",
          "paymentId": "110ec58a-a0f2-4ac4-8393-c866d813b8d1"
      }
    ```
* The generated `paymentId` should be used by the sender DFSP when executing a transfer.
* The actual fee amount is the difference between the amount that has been passed(sourceAmount) and the amount that has been returned(destinationAmount).

## III. Error cases

The receiver DFSP should throw an error in either of the following cases:

* It receives a transfer request with paymentId which is not associated with a locally reserved quote.
* It receives a transfer request with paymentId associated with an already expired quote.
* It receives a transfer request with correct paymentId but different parameters than those associated with the already reserved quote (amount, currency, receiver account, transfer type, etc...)

## IV. Notes

* The same exact flow described in (I.) can be used for `/v1/quoteDestinationAmount` where the initial amount should include the fee amount.
* Additional method `(GET /v1/quote/{paymentId})` could be implemented in case there's a need for already reserved quotes to be obtainable.
