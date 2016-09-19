| Property | Description |
| ---- | ----------- |
| Instance ID | i-0826afc008e9c45a8 |
| Public DNS | ec2-52-37-54-209.us-west-2.compute.amazonaws.com |
| Public IP | 52.37.54.209 |
| Instance type | m4.large |
| Private DNS | ip-172-31-24-6.us-west-2.compute.internal |
| Private IPs | 172.31.24.6 |

### Services

| Name | External Ports | Description |
| ---- | -------------- | ----------- |
| dfsp-interop-services | 8081 | mule services for sending dfsp (directory / spsp-client-proxy / ilp-ledger-adapter / spsp-backend ) |
| dfsp-mock-services | 9081 | temporary mule mocks to support integration |
| interop-demo-ledger | 3000 | five bells docker image admin / foo |

### Dev

DFSP Directory Gateway

| path | Description |
| ---- | ----------- |
| /directory/v1 | directory gateway service root
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8081/directory/v1/console/) | api demo console |
| [user/get](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8081/directory/v1/user/get) | /directory/user/get |
| [user/add](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8081/directory/v1/user/add) | for testing only - add accounts to directory |

Data Structure for /user/add - this method takes a list of maps of account information which is then returned via calls to /user/get.  This data should map to accounts that exist in test ledgers.  For PI 2 demo purposes only the URI contained in the account field is valuable.  The other information should be taken from the result of calling query on the spsp-client.  The directory service and the whole topic of resolving user identifiers back to account information  will be discussed and refactored as part of the next convening and will be implemented in PI 3.1.

note the uri field below is the key for queries into /user/get.  the result will be the matching user json and the value in its account field shoudl be passed forward into the spsp-client

```json
{
  "users": [
    {
      "uri": "http://centraldirectory.com/alice",
      "name": "Alice Somebody",
      "account": "http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3000/alice",
      "currency": "USD"
    },
    {
      "uri": "http://centraldirectory.com/bob",
      "name": "Bob Nobody",
      "account": "http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:3000/bob",
      "currency": "USD"
    }
  ]
}
```

DFSP SPSP Client Proxy

| path | Description |
| ---- | ----------- |
| /spsp/client/v1 | spsp client proxy service root |
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8081/spsp/client/v1/console/) | api demo console |
| ... | see api documentation or console for additional functions |

ILP Ledger Adapter

This deployment of the ILP Ledger Adapter is connected to an instance of the five bells ledger running on interop-dev1.

| path | Description |
| ---- | ----------- |
| /ilp/ledger/v1 | spsp client proxy service root |
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8081/ilp/ledger/v1/console/) | api demo console |
| ... | see api documentation or console for additional functions |

SPSP Server Backend

| path | Description |
| ---- | ----------- |
| /spsp/backend/v1 | spsp server backend service root |
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8081/spsp/backend/v1/console/) | api demo console |
| ... | see api documentation or console for additional functions |
