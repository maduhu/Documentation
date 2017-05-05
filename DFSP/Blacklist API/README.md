**Blacklist API**
----

This document describes a proposal for REST Api which to be used for fetching a list of users which are blacklisted by a given DFSP

***1. Get all blacklisted users***

  *Get all blacklisted users*

* **URL**

  /v1/blacklist

* **Headers**

  * Content-Type: application/json
  * Accept: application/json
  * Authorization: Basic dGVzdDoxMjM=

* **Method:**

  `GET`

* **URL Params:**

  None

* **Data Params:**

  None

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      [
          {
              "identifier": "123456789",
              "identifierType": "eur",
              "firstName": "Bob",
              "lastName": "Dylan",
              "dob": "1999-12-10",
              "nationalId": "123456789"
          },
          {
              "identifier": "321321321",
              "identifierType": "eur",
              "firstName": "Alice",
              "lastName": "Cooper",
              "dob": "1989-03-22",
              "nationalId": "987654321"
          }
      ]
    ```

* **Error Response:**

  * **Code:** 404 (Not Found)<br />
    **Content:**
    ```json
    {
      "statusCode":404,
      "error":"Not Found"
    }
    ```

  OR

  * **Code:** 401 (Unauthorized) <br />
    **Content:**
    ```
    {
      "statusCode": 401,
      "error": "Invalid credentials"
    }
    ```

* **Sample Call:**

  ```curl
    curl -X GET --header 'Accept: application/json' --header 'Authorization: Basic dGVzdDoxMjM=' 'http://host:port/v1/blacklist'
  ```
