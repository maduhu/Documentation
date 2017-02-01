## I.  OVERVIEW  ##

Summary

![](./diagrams/getSenderDetails.png)

Assumptions

## II.  GET CLIENT INFORMATION  ##

Summary

![](./diagrams/getSenderDetails.png)

**Title**
----
  <_Additional information about your API call. Try to use verbs that match both request type (fetching vs modifying) and plurality (one vs multiple)._>

* **URL**

  `/invoices/client/{userNumber}`

* **Method:**

  `GET`

*  **URL Params**

   `userNumber - the number of the user`

* **Data Params**

  **Required:**

   `accountNumber [string] - Merchant's account number`

   **Optional:**

   `accountNumber [string] - Merchant's account number`

* **Sample Call:**

  ```
    {
      "accountNumber": "merchant",
      "amount": 123,
      "userNumber": "78956562"
    }
  ```

* **Success Response:**

  * **Code:** 200 <br />
    **Content:**
      `id [string] - some id`

* **Sample Response:**

  ```
    {
      "id" : 12
    }
  ```


## III.  CREATE INVOICE  ##

Summary

![](./diagrams/getSenderDetails.png)

Api description

## IV.  LIST PENDING INVOICES  ##

Summary

![](./diagrams/getSenderDetails.png)

Api description

## V.  GET INVOICE DETAILS  ##

Summary

![](./diagrams/getSenderDetails.png)

Api description

## VI.  APPROVE INVOICE  ##

Summary

![](./diagrams/getSenderDetails.png)

Api description

## VII.  REJECT INVOICE  ##

Summary

![](./diagrams/getSenderDetails.png)

Api description
