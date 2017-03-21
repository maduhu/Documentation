#L1 Service Portal

![L1 Service Portal](../Wiki/L1%20Portal%20Service.png)
[Note that the Setup step, "C" above, has since been removed]

## Microservices
- **SPSP Client** - The Simple Payment Setup Protocol (SPSP) Client orchestrates the preparation and execution of a payment, calling the SPSP Server on the receiving end of the transaction for information. Rather than using the client library directly, a DFSP interfaces to it through the "ILP Butler."
    - [Spec](https://github.com/LevelOneProject/ilp-spsp-client/blob/master/README.md) | [Code](https://github.com/LevelOneProject/ilp-spsp-client)
- **SPSP Server** - Sets up and executes a payment at the request of the SPSP Client.
    - [Spec](https://github.com/LevelOneProject/ilp-spsp-server/blob/master/README.md) | [Code](https://github.com/LevelOneProject/ilp-spsp-server)
- **SPSP Client RESTful API**. A stable RESTful interface to the SPSP Client, to abstract away changes in the evolving standard.
    - [Spec](https://github.com/LevelOneProject/ilp-spsp-client-rest/blob/master/README.md) | [Code](https://github.com/LevelOneProject/ilp-spsp-client-rest)
- **ILP Client** - A JavaScript library used by other Interledger components, including creating and verifying Crypto-conditions.
    - [Spec](https://github.com/interledger/js-ilp/blob/master/README.md) | [Code](https://github.com/interledger/js-ilp)
- **ILP Connector** - A service that owns balances in two or more ILP-enabled ledgers, facilitating payments across those ledgers by receiving money in one and sending in the other.
    - [Spec](https://interledger.org/js-ilp-connector/apidoc/) | [Code](https://github.com/interledger/js-ilp-connector)
- **ILP Ledger Adapter** - A RESTful API to a ledger that provides functionality needed for ILP payments to and from that ledger, such as transfers that are held until a [crypto-condition](https://github.com/interledger/rfcs/blob/master/0002-crypto-conditions/0002-crypto-conditions.md) is fulfilled. This can be built directly into a ledger, or added as a separate component. The DFSP Ledger and the Central Ledger use the same spec for their ILP Ledger Adapter.
    - [Spec](./ILP/ledger-adapter.md) | [Code (Central Ledger)](https://github.com/LevelOneProject/central-ledger/tree/master/src/api) | [Code (DFSP Ledger)](https://github.com/LevelOneProject/dfsp-ledger/tree/master/service/ledger) | [Code (Interop)](https://github.com/LevelOneProject/interop-ilp-ledger) | [Schemas](https://github.com/LevelOneProject/ilp-schemas)


## External DFSP Interfaces:
- [Ledger Adapter API](./ILP/ledger-adapter.md)
- [Simple Payment Setup Protocol API](https://github.com/LevelOneProject/ilp-spsp-client-rest) 
- [DFSP Identity Lookup](./CentralDirectory/central_directory_endpoints.md)
