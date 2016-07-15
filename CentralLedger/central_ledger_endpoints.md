# Central Ledger

The central ledger is a system to record transfers between DFSPs, and to calculate net positions for DFSPs and issue settlement instructions.

## Endpoints

### Propose and prepare a transfer

This endpoint is used to create and authorize transfers.

``` http
PUT https://central-ledger/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204 HTTP/1.1
Accept: application/json
{
  "id": "https://central-ledger/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204",
  "ledger": "http://usd-ledger.example/USD",
  "debits": [
    {
      "account": "http://usd-ledger.example/USD/accounts/alice",
      "amount": "50"
    }
  ],
  "credits": [
    {
      "account": "http://usd-ledger.example/USD/accounts/bob",
      "amount": "50"
    }
  ],
  "execution_condition": "cc:0:3:8ZdpKBDUV-KX_OnFZTsCWB_5mlCFI3DynX5f5H2dN-Y:2",
  "expires_at": "2015-06-16T00:00:01.000Z"
}
```

``` http
HTTP/1.1 201 CREATED
Content-Type: application/json
{
  "id": "https://central-ledger/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204",
  "ledger": "http://usd-ledger.example/USD",
  "debits": [
    {
      "account": "http://usd-ledger.example/USD/accounts/alice",
      "amount": "50"
    }
  ],
  "credits": [
    {
      "account": "http://usd-ledger.example/USD/accounts/bob",
      "amount": "50"
    }
  ],
  "execution_condition": "cc:0:3:8ZdpKBDUV-KX_OnFZTsCWB_5mlCFI3DynX5f5H2dN-Y:2",
  "expires_at": "2015-06-16T00:00:01.000Z",
  "state": "proposed"
}
```

### Execute a prepared transfer

This endpoint is used to execute or cancel a transfer that has already been prepared.

``` http
PUT https://central-ledger/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204/fulfillment HTTP/1.1
Content-Type: text/plain
cf:0:_v8
```

``` http
HTTP/1.1 200 OK
cf:0:_v8
```

### Get a transfer object

This endpoint is used to query about the details or status of a local transfer.

``` http
GET https://central-ledger/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204 HTTP/1.1
```

``` http
HTTP/1.1 200 OK
{
  "id": "http://central-ledger/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204",
  "ledger": "http://usd-ledger.example/USD",
  "debits": [
    {
      "account": "http://usd-ledger.example/USD/accounts/alice",
      "amount": "50"
    }
  ],
  "credits": [
    {
      "account": "http://usd-ledger.example/USD/accounts/bob",
      "amount": "50"
    }
  ],
  "execution_condition": "cc:0:3:8ZdpKBDUV-KX_OnFZTsCWB_5mlCFI3DynX5f5H2dN-Y:2",
  "expires_at": "2015-06-16T00:00:01.000Z",
  "state": "executed",
  "timeline": {
    "proposed_at": "2015-06-16T00:00:00.000Z",
    "prepared_at": "2015-06-16T00:00:00.500Z",
    "executed_at": "2015-06-16T00:00:00.999Z"
  }
}
```

### Get a transfer’s fulfillment

This endpoint is used to retrieve the fulfillment for a transfer that has been executed or cancelled.

``` http
GET https://central-ledger/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204/fulfillment HTTP/1.1
```

``` http
HTTP/1.1 200 OK
cf:0:_v8
```

### Get the state of a transfer

This endpoint is used to get a signed receipt containing only the id of transfer and its state. It functions even if the transfer doesn't exist yet.

``` http
GET https://central-ledger/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204/state HTTP/1.1
```

``` http
HTTP/1.1 200 OK
{
  "message": {
    "id": "http://localhost/transfers/03b7c787-e104-4390-934e-693072c6eda2",
    "state": "nonexistent"
  },
  "type": "ed25519-sha512",
  "signer": "http://localhost",
  "public_key": "9PAqTUEptSeQCOp/0FQTm3rkFnUFaYEUEwCcyyySQP0=",
  "signature": "DPHsnt3/5gskzs+tF8LNne/3p9ZqFFWNO+mvUlol8geh3VeErLE3o3bKkiSLg890/SFIeUDtvHL3ruiZRcOFAQ=="
}
```

### Retrieve transfers

This endpoint is used to retrieve transfers based off a variety of query parameters.

Query parameters:

* from
* to
* amount

``` http
GET https://central-ledger/transfers?from=2016-01-01 HTTP/1.1
```

``` http
HTTP/1.1 200 OK
[
  {
    "id": "http://central-ledger/transfers/3a2a1d9e-8640-4d2d-b06c-84f2cd613204",
    "ledger": "http://usd-ledger.example/USD",
    "debits": [
      {
        "account": "http://usd-ledger.example/USD/accounts/alice",
        "amount": "50"
      }
    ],
    "credits": [
      {
        "account": "http://usd-ledger.example/USD/accounts/bob",
        "amount": "50"
      }
    ],
    "execution_condition": "cc:0:3:8ZdpKBDUV-KX_OnFZTsCWB_5mlCFI3DynX5f5H2dN-Y:2",
    "expires_at": "2015-06-16T00:00:01.000Z",
    "state": "executed",
    "timeline": {
      "proposed_at": "2015-06-16T00:00:00.000Z",
      "prepared_at": "2015-06-16T00:00:00.500Z",
      "executed_at": "2015-06-16T00:00:00.999Z"
    }
  }
]
```

### Subscribe to rebalance events and transfer events

This endpoint is used to subscribe for webhook notifications about rebalance and transfer events.

``` http
PUT https://central-ledger/subscriptions/7ba9f960-0f4a-e611-80e2-02c4cfdff3c0 HTTP/1.1
Accept: application/json
{
  "id": "7ba9f960-0f4a-e611-80e2-02c4cfdff3c0",
  "event": "position.rebalanced",
  "target": "http://dfsp1.com/rebalance"
}
```

``` http
HTTP/1.1 201 CREATED
{
  "id": "7ba9f960-0f4a-e611-80e2-02c4cfdff3c0",
  "event": "position.rebalanced",
  "target": "http://dfsp1.com/rebalance"
}
```