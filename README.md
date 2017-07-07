# Level One Project Overivew
The Level One Project is about financial inclusion for everyone.  Today more than 2 billion adults do not have a bank account or access to a formal financial institution.  The Level One Project is aimed at changing this denominator by making banking accessible by everyone via a standard flip phone.  Our project is aimed at building a national digital financial solution that is open to everyone.  Specifically, any person in any location with a phone can open a back account, send money, receive money or get paid for services.  This open system will help the poorest people in the most remote locations to ensure a safe and reliable solution.  

For more information on the Level One Project, see the [Level One Project site](https://leveloneproject.org/).

## Why should I contribute to the Level One Project?
The Level One Project is an initiative by the Bill and Melinda Gates Foundation to make it easier for developing countries to provide useful digital financial services to the people who live there. To participate in the formal, global economy, everyone needs access to digital financial services so they can transact quickly and safely, across distances long and short.  

This project started with a model and prototype for a financial system that could be implemented in any country.  In addition, this code takes that a step further by implementing a strong reliable messaging using the [Interledger](http://interledger.org) protocol as well as a functioning central hub that financial providers can connect to facilitate common settlement and regulatory compliance. 

> In order to expand this service and ensure that we a level playing field for everyone we need your help to enhance this project and help to realize the vision of having one digital financial system in every country around the world. 

## Getting Started
The Level One Project in github is broken down into microservices.  As such, the team has created over twenty different repositories in github that align to the different Level One Services.  The "Docs" repository documents the overall architecture, component design, message flow, and an overview of the Level One Project (L1P) software. Individual repositories in the [Level One Project GitHub organization](https://github.com/LevelOneProject) each describe their component details including source and APIs.

New developers see [contibutors guide] (./Docs/contributors.md) for onboarding materials.

## Level One Services
The following architecture diagram shows the Level One services:

![Level One Services](./Wiki/Basic%20Overview.png)

See the [physical machines](./AWS/Infrastructure/machines.md) for info on the test and demo implementations in AWS.

The basic idea behind the L1P model is that we need to connect multiple Digital Financial Services Providers (DFSPs) together into a competitive and interoperable network in order to provide that most opportunity for poor people to get access to financial services with low or no fees. We don't want a single monopoly power in control of all payments in a country, or a system that shuts out new players. It also doesn't help if there are too many isolated subnetworks. Our model solves this problem with several key elements:

- A set of central services provides a hub through which money can flow from one DFSP to each other. This is similar to how money moves through a central bank or clearing house in developed countries. Besides a central ledger, central services can provide identity lookup, fraud management, and enforce scheme rules.
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
- The [Fraud service](https://github.com/LevelOneProject/central-fraud-sharing) aids DFPS in identifying suspicious behavior.

## End-to-End Scenarios
The individual services listed above can't easily describe how key scenarios work across the system. For each of the [Level One Scenarios](https://github.com/LevelOneProject/Docs/wiki/L1P-Scenarios) we provide a technical walk through.

1. Send Money to Anyone: [scenario](https://github.com/LevelOneProject/Docs/blob/master/scenarios.md#send-money-to-anyone),  [walkthrough](./LevelOneClient/scenarios/Send%20Payment.md)
2. Invoices [scenario](https://github.com/LevelOneProject/Docs/blob/master/scenarios.md#buy-goods---pending-transactions), [message flow](./DFSP/PendingTransactions/README.md)
3. Bulk Payment [scenario](https://github.com/LevelOneProject/Docs/blob/master/scenarios.md#bulk-payments), [message flow](./DFSP/BulkPayment/README.md)

## System-wide Testing
Individual services have their own tests, but there are system-wide tests as per the [testing strategy](https://github.com/LevelOneProject/Docs/wiki/Manual-and-automated-testing-strategy).

- [Scenario testing](https://github.com/LevelOneProject/Docs/blob/master/JMeter/scenarioTests/readme.md)
- [End-to-end functional testing](https://github.com/LevelOneProject/interop-functional-tests)
- [Performance testing](./JMeter)
- [Resilience Modeling and Anaylysis (RMA)](./RMD.md)
- Threat Modeling

## Related Projects
The [Interledger Protocol Suite](https://interledger.org/) (ILP) is an open and secure standard that lets DFSPs settle payments with minimal counterparty risk. (That's the risk you take when someone else is holding your money.) With ILP, you can transact across different systems with no chance that someone in the middle disappears with your money. The Level One Project uses the Interledger Protocol Suite for the clearing layer. For an overview of how it works, see the [Clearing Architecture Documentation](./ILP).

## About This Document

This document is a work in progress; not all sections are updated to the latest developments in the project. Sections that are known to be out of date are marked as follows:

> ***OUT OF DATE STARTS HERE***

Any text in this area is considered "out of date." It may reflect earlier versions of the technology, outdated terminology use, or sections that are poorly phrased and edited.

> ***OUT OF DATE ENDS HERE***
