**Blacklist API**
----

This document describes a proposal for REST Api which to be used for fetching/adding/removing blacklisted identifiers within the scope of a given DFSP

***1. Get all blacklisted identifiers or a list of such by type***

  *Get all blacklisted identifiers or a list of such by type*

* **URL**

  /v1/blacklist

* **Headers**

  * Content-Type: application/json
  * Accept: application/json
  * Authorization: Basic dGVzdDoxMjM=

* **Method:**

  `GET`

* **URL Params:**

  identifierType [optional]

* **Data Params:**

  None

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      [
          {
              "identifier": "123456789",
              "identifierType": "eur"
          },
          {
              "identifier": "345678910",
              "identifierType": "eur"
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
    curl -X GET --header 'Accept: application/json' --header 'Authorization: Basic dGVzdDoxMjM=' 'http://host:port/v1/blacklist?identifierType=eur'
  ```


***2. Mark identifiers as blacklisted***

  *Mark identifiers as blacklisted*

* **URL**

  /v1/blacklist

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
    [
        {
            "identifier": "123456789",
            "identifierType": "eur"
        },
        {
            "identifier": "345678910",
            "identifierType": "eur"
        }
    ]
  ```

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      {
          "success": true
      }
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
    curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --header 'Authorization: Basic dGVzdDoxMjM=' -d
    '[
        {
            "identifier": "123456789",
            "identifierType": "eur"
        },
        {
            "identifier": "345678910",
            "identifierType": "eur"
        }
    ]'
    'http://host:port/v1/blacklist'
  ```

***3. Remove identifiers from the blacklist***

  *Remove identifiers from the blacklist*

* **URL**

  /v1/blacklist

* **Headers**

  * Content-Type: application/json
  * Accept: application/json
  * Authorization: Basic dGVzdDoxMjM=


* **Method:**

  `PUT`

* **URL Params:**

  None

* **Data Params:**

  ```json
    [
        {
            "identifier": "123456789",
            "identifierType": "eur"
        },
        {
            "identifier": "345678910",
            "identifierType": "eur"
        }
    ]
  ```

* **Success Response:**

  * **Code:** 200 OK<br />
    **Content:**
    ```json
      {
          "success": true
      }
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
    curl -X PUT --header 'Content-Type: application/json' --header 'Accept: application/json' --header 'Authorization: Basic dGVzdDoxMjM=' -d
    '[
        {
            "identifier": "123456789",
            "identifierType": "eur"
        },
        {
            "identifier": "345678910",
            "identifierType": "eur"
        }
    ]'
    'http://host:port/v1/blacklist'
  ```
