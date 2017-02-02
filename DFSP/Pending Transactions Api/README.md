## I.  OVERVIEW  ##
 
Current document will cover the flow of creating and approving/rejecting pending transactions using 
DFSP Over the Top API. The API exposes the DFSP functionalities for transactions processing, customer and account management, etc. to third party applications such as Android/IPhone smart application. There are additional set of APIs which are used for communication between DFSP to DFSP. Those APIs will not be analyzed in the current document. 

API Principles  

  * Restful approach to API design
  * Based on JSON, no other content types are supported

Assumptions  

   * Merchant is the party sending the invoices.
   * Client is the party who is receiving the ivoices and he is able to approve/reject them.
   * Merchant and the client are in different systems.
   * Merchant and the client are loggen in their systems.


![](./diagrams/pendingTransactions.png)


## II.  GET CLIENT INFORMATION  ##

It will check if the client is in the same system and if he is then it will return information about him. If the client is not in the same system it will ask the central directory and return information about him if there are any matching results. Client information is described bellow.

### Api description

----
  

* **URL**

  `/v1/client/{userNumber}`

* **Method:**

  `GET`

*  **URL Params**
   * `userNumber - The number of the user`

* **Sample Call:**

  ```
    curl -X GET --header 'Accept: application/json' 'http://host/v1/client/78956562'
  ```

* **Success Response:**

  * **Code:** 200 <br />
    **Content:**  

     * `firstName [string] - Client's first name`  
     * `lastName [string] - Client's last name`  
     * `imageUrl [string] - Link to the client's image`
      
* **Sample Response:**

  ```
    {
      "firstName": "Bob",
      "lastName": "Smith",
      "imageUrl": "https://red.ilpdemo.org/api/receivers/bob/profile_pic.jpg"
    }
  ```
* **<a href="http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8010/documentation?tags=getClient" target="_blank">Try it out here</a>**

## III.  CREATE INVOICE  ##

The invoice will be created in the merchant's DFSP. It will be associated with an account. After the invoice is created in the merchant's DFSP a notification with the invoice reference will be send to the default client DFSP. The invoice reference in the client DFSP will not be associated with any clients account thus the client can choose an account from which he is going to pay the invoice.

### Api description
----
  

* **URL**

  `/v1/invoices`

* **Method:**

  `POST`

* **Data Params**

  **Required:**  

   * `account [string] - Invoice merchant's account`  
   * `amount [number] - Invoice amount`  
   * `userNumber [string] - Client's user number`

   **Optional:**

   * `info [string] - Additional invoice information`

* **Sample Call:**

  ```
    curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' -d 
    '{
      "account": "merchant",
      "amount": 123,
      "userNumber": "78956562",
      "info": "Invoice from merchant to Alice"
    }' 
    'http://host/v1/invoices'
  ```

* **Success Response:**

  * **Code:** 200 <br />
    **Content:**
       * `type [string] - Invoice type`
       * `invoiceNotificationId [number] - Invoice notification id`
       * `account [string] - Invoice merchant's account`  
       * `firstName [string] - Merchant's first name`
       * `lastName [string] - Merchant's last name`
       * `currencyCode [string] - Invoice merchant's currency code`
       * `currencySymbol [string] - Invoice merchant's currency symbol`
       * `amount [string] - Invoice amount`
       * `status [string] - Invoice status`
       * `userNumber [string] - Invoice client's user number`
       * `info [string] - Invoice additional information`

* **Sample Response:**

  ```
    {
      "type": "invoice",
      "invoiceNotificationId": 1,
      "account": "merchant",
      "firstName": "John",
      "lastName": "Smith",
      "currencyCode": "USD",
      "currencySymbol": "$",
      "amount": "130.34",
      "status": "pending",
      "userNumber": "78956562",
      "info": "Invoice from merchant for 130.34 USD"
    }
  ```
* **<a href="http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8010/documentation?tags=postInvoice" target="_blank">Try it out here</a>**

## IV.  LIST PENDING INVOICES - CLIENT ##

Client will be able to check all the invoices associated with him.

### Api description

----
  

* **URL**

  `/v1/invoiceNotifications/pending/{userNumber}`

* **Method:**

  `GET`

*  **URL Params**

   * `userNumber - Client's user number`

* **Sample Call:**

  ```
    curl -X GET --header 'Accept: application/json' 'http://host/v1/invoiceNotifications/pending/78956562'
  ```

* **Success Response:**

  * **Code:** 200 <br />
    **Content:**
       * `invoiceNotificationId [number] - Invoice notification id`  
       * `status [string] - Invoice notification status`  
       * `info [string] - Additional invoice notification information`  

* **Sample Response:**

  ```
    {
      "invoices": [
        {
          "invoiceNotificationId": 2,
          "status": "pending",
          "info": "Invoice from merchant for 130.34 USD"
        }
      ]
    }
  ```  
* **<a href="http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8010/documentation?tags=getInvoiceNotificationList" target="_blank">Try it out here</a>**

## V.  GET INVOICE DETAILS  ##

It will return all the data related to the posted invoice by passing the invoice notification ID.

### Api description

----
  

* **URL**

  `/v1/invoicesNotifications/{invoiceNotificationId}`

* **Method:**

  `GET`

*  **URL Params**

   * `invoiceNotificationId - Invoice notification id`

* **Sample Call:**

  ```
    curl -X GET --header 'Accept: application/json' 'http://host/v1/invoiceNotifications/2'
  ```

* **Success Response:**

  * **Code:** 200 <br />
    **Content:**
       * `firstName [string] - Merchant's first name`  
       * `lastName [string] - Merchant's last name`  
       * `amount [number] - Invoice amount`  
       * `currencyCode [string] - Currency code`  
       * `currencySymbol [string] - Currency symbol`  
       * `fee [number] - Invoice fee`  

* **Sample Response:**

  ```
    {
      "firstName": "Ben",
      "lastName": "Smith",
      "amount": 123,
      "currencyCode": "USD",
      "currencySymbol": "$",
      "fee": 1.23,
    }
  ```  
* **<a href="http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8010/documentation?tags=getInvoiceInfo" target="_blank">Try it out here</a>**

## VI.  APPROVE INVOICE  ##

Client will be able to approve invoices. Whit this action invoice notification will be approved, merchant's DFSP will be notified and it will approve the merchant's invoice. After all merchant will receive invoice payment notification. 

### Api description
----
  

* **URL**

  `/v1/invoiceNotifications/approve`

* **Method:**

  `PUT`

* **Data Params**

  **Required:**

   * `account [string] - Invoice sender's account number`    
   * `invoiceNotificationId [string] - Invoice notification id`    

* **Sample Call:**

  ```
    curl -X PUT --header 'Content-Type: application/json' --header 'Accept: application/json' -d 
    '{
      "account": "merchant",
      "invoiceNotificationId": "6"
    }' 
    'http://host/v1/invoiceNotifications/approve'
  ```

* **Success Response:**

  * **Code:** 200 <br />
    **Content:**
      * `invoiceNotificationId [string] - Invoice notification id`  
      * `status [string] - Invoice notification status`  

* **Sample Response:**

  ```
    {
      "invoiceNotificationId": "3",
      "status": "approved",
    }
  ```  
* **<a href="http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8010/documentation?tags=approveInvoiceNotification" target="_blank">Try it out here</a>**

## VII.  REJECT INVOICE  ##

Client also will be able to reject invoices. Whit this action invoice notification will be rejected,merchant's DFSP will be notified and it will reject the merchant's invoice. After all merchant will receive invoice rejection notification.

### Api description
----
  

* **URL**

  `/v1/invoiceNotifications/reject`

* **Method:**

  `PUT`

* **Data Params**

  **Required:**

   * `invoiceNotificationId [string] - Invoice notification id`

* **Sample Call:**

  ```
    curl -X PUT --header 'Content-Type: application/json' --header 'Accept: application/json' -d 
   '{
       "invoiceNotificationId": "2"
    }' 
    'http://host/v1/invoiceNotifications/reject'
  ```

* **Success Response:**

  * **Code:** 200 <br />
    **Content:**
      * `invoiceNotificationId [string] - Invoice notification id`  
      * `status [string] - Invoice notification status`  


* **Sample Response:**

  ```
    {
      "invoiceNotificationId": "2",
      "status": "rejected",
    }
  ```
* **<a href="http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8010/documentation?tags=rejectInvoiceNotification" target="_blank">Try it out here</a>**