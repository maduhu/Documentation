# Level One Project Documentation
> A level playing field for everyone by building one digital financial system in every country around the world. That’s The Level One Project.

This repository is the hub for architecture documentation, component designs, and overall information about Level One Project (L1P) software. You can find the packages and implementations in the [Level One Project GitHub organization](https://github.com/LevelOneProject).

See also: [Onboarding](https://github.com/LevelOneProject/Docs/wiki/Onboarding)

## What is L1P?

To participate in the formal, global economy, everyone needs access to digital financial services so they can transact quickly and safely, across distances long and short. The Level One Project is an initiative by the Bill & Melinda Gates Foundation to make it easier for developing countries to provide useful digital financial services to the people who live there. It started with a model for a financial system that could be implemented in any country. Now we're taking that model and making it a fully-functional prototype, with all the details in place.

## Model Architecture

The basic idea behind the L1P model is that we need to connect multiple Digital Financial Services Providers (DFSPs) together into a competitive but interoperable network. We don't want a single monopoly power in control of all payments in a country, but it also doesn't help if there are too many isolated subnetworks. Our model solves this problem with several key elements:

- A set of central services provides a hub through which money can flow from one DFSP to each other. This is similar to how money moves through a central bank or clearing house in developed countries. Besides a central ledger, central services can provide identity lookup, fraud management, and other such rules.
- A standard set of interfaces a DFSP can implement to connect to the system, and example code that shows how to use the system. A DFSP that wants to connect up can adapt our example code or implement the standard interfaces into their own software. The goal is for it to be as straightforward as possible for a DFSP to connect to the interoperable network.
- The [Interledger Protocol](https://interledger.org/) (ILP), an open standard that lets DFSPs settle payments with minimal counterparty risk. (That's the risk you take when someone else is holding your money.) With ILP, you can transact across different systems with no chance that someone in the middle disappears with your money.

The following architecture diagram shows interactions between pieces of the Level One Project model:

![Top Level Architecture](Wiki/Demo%20Service%20Interactions.png)

### DFSP Components

Each Digital Financial Services Provider runs these components to provide payments to its customers. For an overview, see [DFSP Documentation](https://github.com/LevelOneProject/Docs/tree/master/DFSP).

- **Wallet UI** - The user interface used by individuals
- **Bulk Payment Interface** - An alternate entry point to the system.
- **Transfer Service**
    - [Spec](https://github.com/LevelOneProject/Docs/blob/master/DFSP/transfer.md) | [Code](https://github.com/LevelOneProject/dfsp-transfer)
- **Directory Service** - Service for looking up people, URLs, locations, etc.
    - [Spec](https://github.com/LevelOneProject/Docs/blob/master/DFSP/directory.md) | [Code](https://github.com/LevelOneProject/dfsp-directory)
- **Rule Service** - Policies around fees, limits, and other business rules. Possibly also home of Anti-Money Laundering (AML) functionality.
    - [Spec](https://github.com/LevelOneProject/Docs/blob/master/DFSP/rule.md) | [Code](https://github.com/LevelOneProject/dfsp-rule)
- **DFSP Interledger Service** - ??? (Code and spec missing)
- **DFSP Ledger** - The DFSP's core database of accounts and balances.
    - [Code](https://github.com/LevelOneProject/dfsp-ledger)

### Gateways

These gateways sit between core systems and outside networks. They can provide a variety of benefits like logging and metrics, input sanitization, automatic retries, and other best practices for any internet-facing software.

- **Directory Gateway** - Connects a DFSP's Directory Service and the central Directory Service.
- **Fraud Gateway** - Connects a DFSP's Rule Service with the central Fraud service.
- **SPSP Client (Proxy)** - Connects a DFSP's Interledger Service with that DFSP's SPSP Client and Server.
- **ILP Ledger Adapter Gateway** - Connects the central ledger's ILP Ledger Adapter with a DFSP's ILP Connector.

### Interledger Components

The Interledger Protocol (ILP) is a proposed standard that uses cryptography to make (mostly) atomic transfers across ledgers. The Level One Project uses ILP with the Simple Payment Setup Protocol (SPSP) for the clearing layer. For an overview of how it works, see the [Clearing Architecture Documentation](https://github.com/LevelOneProject/Docs/tree/master/ILP).

- **Crypto-conditions** - A fundamental data type used by the Interledger Protocol. We prepare a transfer in a ledger and hold the funds with a crypto-condition; the transfer automatically executes when the ledger sees a "fulfillment" message that matches the condition.
    - [Spec](https://github.com/interledger/rfcs/blob/master/0002-crypto-conditions/0002-crypto-conditions.md) | [Code](https://github.com/interledger/five-bells-condition)
- **SPSP Client** - The Simple Payment Setup Protocol (SPSP) Client orchestrates the preparation and execution of a payment, calling the SPSP Server on the receiving end of the transaction for information. Rather than using the client library directly, a DFSP interfaces to it through the "ILP Butler."
    - [Spec](https://github.com/LevelOneProject/ilp-spsp-client/blob/master/README.md) | [Code](https://github.com/LevelOneProject/ilp-spsp-client)
- **SPSP Server** - Sets up and executes a payment at the request of the SPSP Client.
    - [Spec](https://github.com/LevelOneProject/ilp-spsp-server/blob/master/README.md) | [Code](https://github.com/LevelOneProject/ilp-spsp-server)
- **ILP Butler** - AKA the SPSP Client RESTful API. A stable RESTful interface to the SPSP Client, to abstract away changes in the evolving standard.
    - [Spec](https://github.com/LevelOneProject/ilp-spsp-client-rest/blob/master/README.md) | [Code](https://github.com/LevelOneProject/ilp-spsp-client-rest)
- **ILP Client** - A JavaScript library used by other Interledger components, including creating and verifying Crypto-conditions.
    - [Spec](https://github.com/interledger/js-ilp/blob/master/README.md) | [Code](https://github.com/interledger/js-ilp)
- **ILP Connector** - A service that owns balances in two or more ILP-enabled ledgers, facilitating payments across those ledgers by receiving money in one and sending in the other.
    - [Spec](https://interledger.org/js-ilp-connector/apidoc/) | [Code](https://github.com/interledger/js-ilp-connector)
- **ILP Ledger Adapter** - A RESTful API to a ledger that provides functionality needed for ILP payments to and from that ledger, such as transfers that are held until a crypto-condition is fulfilled. This can be built directly into a ledger, or added as a separate component. The DFSP Ledger and the Central Ledger use the same spec for their ILP Ledger Adapter.
    - [Spec](https://github.com/LevelOneProject/Docs/blob/master/ILP/ledger-adapter.md) | [Code (Central Ledger)](https://github.com/LevelOneProject/central-ledger/tree/master/src/api) | [Code (DFSP Ledger)](https://github.com/LevelOneProject/dfsp-ledger/tree/master/service/ledger) | [Code (Interop)](https://github.com/LevelOneProject/interop-ilp-ledger) | [Schemas](https://github.com/LevelOneProject/ilp-schemas)

### Central Services Components

- **Directory** - Provides user lookup. We've chosen to use numerical identifiers for users because they are easier for users who cannot read.
    - [Spec](https://github.com/LevelOneProject/Docs/tree/master/CentralDirectory) | [Code](https://github.com/LevelOneProject/central-directory)
- **Fraud Sharing** - Central fraud management service.
- **Rules** - Central rules service.
    - [Spec](https://github.com/LevelOneProject/Docs/tree/master/CentralRules)
- **Central Ledger** - Holds balances for each connected DSFP's connector, not individual users.
    - [Spec](https://github.com/LevelOneProject/Docs/tree/master/CentralLedger) | [Code](https://github.com/LevelOneProject/central-ledger)
