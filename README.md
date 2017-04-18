# Level One Project Documentation
"The Docs repo documents the architecture, component design, message flow, and overview of the Level One Project (L1P) software. The details of source, APIs, and implementation are located in other repos of the [Level One Project GitHub organization](https://github.com/LevelOneProject).

## What is the Level One Project?
> A level playing field for everyone by building one digital financial system in every country around the world. That's The Level One Project.

To participate in the formal, global economy, everyone needs access to digital financial services so they can transact quickly and safely, across distances long and short. The Level One Project is an initiative by the Bill & Melinda Gates Foundation to make it easier for developing countries to provide useful digital financial services to the people who live there. It started with a model for a financial system that could be implemented in any country. Now we're taking that model and making it a fully-functional prototype, with all the details in place.

For more Level One information see the [Level One Project site](https://leveloneproject.org/).
New developers see [Onboarding](https://github.com/LevelOneProject/Docs/wiki/Onboarding).

## Level One Services
The following architecture diagram shows Level One services:

![Level One Services](./Wiki/Basic%20Overview.png)

See [here](./AWS/Infrastructure/machines.md) for [physical architecture and machines topology](./AWS/Infrastructure/machines.md).

The basic idea behind the L1P model is that we need to connect multiple Digital Financial Services Providers (DFSPs) together into a competitive but interoperable network. We don't want a single monopoly power in control of all payments in a country, or a system that shuts out new players. It also doesn't help if there are too many isolated subnetworks. Our model solves this problem with several key elements:

- A set of central services provides a hub through which money can flow from one DFSP to each other. This is similar to how money moves through a central bank or clearing house in developed countries. Besides a central ledger, central services can provide identity lookup, fraud management, and other such rules.
- A standard set of interfaces a DFSP can implement to connect to the system, and example code that shows how to use the system. A DFSP that wants to connect up can adapt our example code or implement the standard interfaces into their own software. The goal is for it to be as straightforward as possible for a DFSP to connect to the interoperable network.
- Complete working open-source implementations of both sides of the interfaces - an example DFSP that can send and receive payments and the client that an existing DFSP could host to connect to the network.

### DFSP Service
The DFSP code is an example implementation of a mobile money provider. Customers connect to it from their mobile feature phones (via USSD) and it allows them to create accounts, send money, and receive money.  USSD (Unstructured Supplementary Service Data) is a Global System for Mobile (GSM) communication technology that is used to send text between a mobile phone and an application program in the network.

[DFSP Documentation](./DFSP)

### Level One Client Service
The client service connects a DFSP to other other DFSPs and the central services. It has a few simple interfaces to connect to a DFSP for account holder lookup, payment setup, and ledger operations. The level one client can be hosted locally by the DFSP or in a remote data center such as Amazon.

[Level One Client Documentation](./LevelOneClient)

### Central Services
The central services are a collection of separate services that help the DFSPs perform operations on the network.

- The [Central Directory Service](./CentralDirectory) finds which DFSP handles a user's accounts.
- The [Central Ledger Service](./CentralLedger) handles clearing and settlement.
- The [Central Rules Service](./CentralRules) sets policy across the system.
- The **Fraud service** aids DFPS in identifying suspicious behavior.

## End-to-End Scenarios
The individual services listed above can't easily describe how key scenarios work across the system. For each of the [Level One Scenarios](https://github.com/LevelOneProject/Docs/wiki/L1P-Scenarios) we provide a technical walk through.

1. Send Money to Anyone: [scenario](https://github.com/LevelOneProject/Docs/wiki/L1P-Scenarios#send-money-to-anyone),  [walkthrough](./LevelOneClient/scenarios/Send%20Payment.md)
2. Invoices [scenario](https://github.com/LevelOneProject/Docs/wiki/L1P-Scenarios#buy-goods---pending-transactions), [message flow](./DFSP/PendingTransactions/README.md)
3. Bulk Payment [scenario](https://github.com/LevelOneProject/Docs/wiki/L1P-Scenarios#bulk-payments), [message flow](./DFSP/BulkPayment/README.md)

## System-wide Testing
Individual services have their own tests, but there are system-wide tests as per the [testing strategy](https://github.com/LevelOneProject/Docs/wiki/Manual-and-automated-testing-strategy).

- [End-to-end functional testing](https://github.com/LevelOneProject/interop-functional-tests)
- [Performance testing](./JMeter)
- Resilience testing
- Threat Model Overview

## Related Projects
The [Interledger Protocol Suite](https://interledger.org/) (ILP) is an open and secure standard that lets DFSPs settle payments with minimal counterparty risk. (That's the risk you take when someone else is holding your money.) With ILP, you can transact across different systems with no chance that someone in the middle disappears with your money. The Level One Project uses the Interledger Protocol Suite for the clearing layer. For an overview of how it works, see the [Clearing Architecture Documentation](./ILP).

## About This Document

This document is a work in progress; not all sections are updated to the latest developments in the project. Sections that are known to be out of date are marked as follows:

> ***OUT OF DATE STARTS HERE***

Any text in this area is considered "out of date." It may reflect earlier versions of the technology, outdated terminology use, or sections that are poorly phrased and edited.

> ***OUT OF DATE ENDS HERE***
