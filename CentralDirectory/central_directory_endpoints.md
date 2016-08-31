# Central Directory

The Central Directory API is a group of loosely coupled microservices that allow DFSPs to store and retrieve End User information.

## Endpoints

### Retrieve End User information by User Number

This endpoint is used to retrieve information about an End User by User Number. The response will include name and account for the End User.

``` http
GET https://central-directory/users/11122233333333 HTTP/1.1
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json
{
  "user_number": 11122233333333,
  "name": "Bob Smith",
  "account": {
  	"currency": "USD",
  	"address": "http://ledger.com/USD/accounts/bob"
  },
  "status": "Active"
}
```

### Retrieve End User information by account

This endpoint is used to retrieve information about an End User by account. This could be used by a receiving DFSP if they did not have the User Number for the sender. The response will include name and account for the End User.

``` http
GET https://central-directory/users?account=http%3A%2F%2Fledger.com%2FUSD%2Faccounts%2Fbob HTTP/1.1
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json
{
  "user_number": 11122233333333,
  "name": "Bob Smith",
  "account": {
  	"currency": "USD",
  	"address": "http://ledger.com/USD/accounts/bob"
  },
  "status": "Active"
}
```

### Add End User information

This endpoint is used to add a new End User to the Central Directory. All fields are required.

``` http
POST https://central-directory/users HTTP/1.1
Accept: application/json
{
  "name": "Bob Smith",
  "account": {
  	"currency": "USD",
  	"address": "http://ledger.com/USD/accounts/bob"
  }
}
```

``` http
HTTP/1.1 201 CREATED
Content-Type: application/json
{
  "user_number": 11122233333333,
  "name": "Robert Smith",
  "account": {
  	"currency": "USD",
  	"address": "http://ledger.com/USD/accounts/bob"
  },
  "status": "Active"
}
```

### Update End User information

This endpoint is used to update End User information. All fields are required, even if they are not being updated.

``` http
PUT https://central-directory/users/11122233333333 HTTP/1.1
Accept: application/json
{
  "name": "Robert Smith",
  "account": {
  	"currency": "USD",
  	"address": "http://ledger.com/USD/accounts/bob"
  }
}
```

``` http
HTTP/1.1 200 OK
Content-Type: application/json
{
  "user_number": 11122233333333,
  "name": "Robert Smith",
  "account": {
  	"currency": "USD",
  	"address": "http://ledger.com/USD/accounts/bob"
  },
  "status": "Active"
}
```

### Error information

This section identifies the potential errors retruned and the structure of the response.


``` http
HTTP/1.1 404 Not Found
Content-Type: application/json
{
  "error": {
  	"id": "UserNotFound",
  	"message": "End user was not found"
  }
}
```
