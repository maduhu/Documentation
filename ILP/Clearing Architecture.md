# Clearing Architecture

The Interledger Protocol suite (ILP) is the standard by which the clearing layer of the Level One Project will be built. For some components, the Level One Project will use the Interledger reference implementations. Some other components expose APIs that are part of the Interledger protocol suite.

## Interledger Summary

Interledger is a suite of protocols and standards for coordinating multiple _book transfers_ that each occur within a single ledger, to facilitate an overall _payment_ that crosses ledgers. The overall means of conducting the transfers is similar to how cross-ledger payments occur today, except that ILP uses cryptography to provide stronger guarantees about where system failures can occur. It also allows the execution of all the transfers to be automated, which lets them be near-simultaneous. By providing a _ledger_ that is compatible with ILP, a Digital Financial Services Provider (DFSP) can ensure compatibility with other DFSPs who do the same.

### Why Interledger

Digital Financial Services Providers are subject to network effects, because the value of the financial services increases with the number of goods and services that are available to users of the DFSP. This frequently means that in a market with too many DFSPs, none of the DFSPs is very useful because the market is so fragmented. Meanwhile, if the market becomes too consolidated, the largest DFSP can use its monopoly power to stifle innovative competitors and take advantage of customers. We would like to foster a competitive ecosystem of interoperable DFSPs. We can do this by encouraging and assisting companies in developing nations to provide ledgers and services that are compatible with the Interledger Protocol. In such an ecosystem, the digital financial services are useful because all compatible providers have access to the same shared network, which removes the market forces that would drive consolidation.
In context of the overall project architecture, the clearing architecture is highlighted in the following diagram:

### Clearing Architecture Summary

![L1P Clearing Architecture Diagram](../LevelOneClient/scenarios/Send%20Payment%20Via%20SPSP.png)

In short, the proposed clearing architecture for the Level One Project is:

* There is one ILP-compatible, centrally-hosted clearing ledger.
* Each DFSP has an "ILP Ledger Adapter" that acts as an ILP-compatible sub-ledger within the DFSP's core ledger.
* Each DFSP has an "ILP Connector" that holds balances in both the DFSP's ILP-compatible (sub-)ledger and the central clearing ledger.
* Each DFSP uses the Simple Payment Services Protocol (SPSP) to plan and coordinate payments. The sender's SPSP Client connects to the receiver's SPSP Server.


## Related GitHub Information

* [Epic #5: Design for ILP and Connector services](https://github.com/LevelOneProject/Docs/issues/5)
* [Epic #4: Design for SPSP service](https://github.com/LevelOneProject/Docs/issues/4)


## Detailed Information

The components are summarized as follows:

| Component Name  | Summary |
|-----------------|---------|
| [DFSP Ledger][] | The internal system of record used by the DFSP for tracking customer balances. |
| [SPSP Client (Proxy)][] | Intermediary between SPSP Client and other services. |
| [SPSP Client][] | This plans a payment on the sending side, and calls the SPSP Server. |
| [SPSP Server][] | This plans a payment on the receiving side, and responds to the SPSP Client. |
| [ILP Client][] | A library in the SPSP Client. |
| [ILP Ledger Adapter][] | Provides a standard interface for the ILP Connector and ILP Client to interact with the ledgers. |
| [ILP Connector][] | Holds money in the DFSP ledger and the Central Ledger, and sets the rates of exchange between them. |
| [ILP Ledger Adapter Gateway][] | Intermediary between ILP Connector and Central Ledger's ILP Ledger Adapter |

Other useful information:

* [Crypto-Conditions][]
* [Payment Flow][]


### DFSP Ledger
[DFSP Ledger]: #dfsp-ledger

The DFSP Ledger is the core system of record that the Digital Financial Services Provider uses to track the balances of all its customers. We expect that existing DFSPs already have a ledger and do not want to change it, so we use an [ILP Ledger Adapter][] to provide the necessary APIs for ILP clearing. The DFSP Ledger contains one or more accounts whose funds are set aside for the ILP Ledger Adapter to manage.


### SPSP Client (Proxy)
[SPSP Client (Proxy)]: #spsp-client-proxy

The SPSP Client Proxy separates the SPSP Client and ILP Ledger Adapter from the non-clearing portions of the stack.


### SPSP Client
[SPSP Client]: #spsp-client

The SPSP Client is an application that plans outgoing payments. (This is not an end-user-facing application. It is used internally by the sending Digital Financial Services Provider.) The SPSP Client:

* Contacts the receiving DFSP's [SPSP Server][] to set up the ILP packet and [Crypto-Conditions][] that will be used to release the funds when the payment is executed.
* Contacts the sending DFSP's [ILP Connector][] (indirectly) to find the rate of exchange (including fees) between the ledgers in question.
* Contacts the sending DFSP's [ILP Ledger Adapter][] to prepare the transfer in the sending DFSP's ledger.
* Contacts the ILP Ledger Adapter again to send the fulfillment that releases and executes the transfer.

Reference Implementation: https://github.com/interledger/js-ilp-spsp

SPSP Specification: <https://github.com/interledger/rfcs/blob/master/0009-simple-payment-setup-protocol/0009-simple-payment-setup-protocol.md>

### SPSP Server
[SPSP Server]: #spsp-server

The SPSP Server is a service that plans incoming payments. (It is used internally by the receiving Digital Financial Services Provider.) The SPSP Server:

* Waits for requests from the sending DFSP's [SPSP Client][]. It sets up the ILP packet and [Crypto-Conditions][] to match the request of the SPSP Client.
* Contacts the receiving DFSP's [ILP Connector][] (indirectly) to find the rate of exchange (including fees) between the ledgers in question.
* Contacts the receiving DFSP's [ILP Ledger Adapter][] to prepare the transfer in the receiving DFSP's ledger.
* Contacts the ILP Ledger Adapter again to send the fulfillment that releases and executes the transfer.

Reference Implementation: <https://github.com/interledger/js-ilp-spsp>

SPSP Specification: <https://github.com/interledger/rfcs/blob/master/0009-simple-payment-setup-protocol/0009-simple-payment-setup-protocol.md>


### ILP Client
[ILP Client]: #ilp-client

This is a standard library used by both the SPSP Client and the SPSP Server. It handles things like validating and verifying [Crypto-Conditions][]. It interfaces with the ILP Ledger Adapter and the ILP Connector using the Interledger Protocol and the Interledger Quoting Protocol.

We expect to use the Interledger Protocol in Universal Mode. This mode has holds and timeouts to incentivize participants to act in the best interests of the system, with emphasis on making sure the initial sender and the final receiver do not lose money.

The ILP Client can have different _Ledger Plugins_ to connect to ledgers or ledger adapters with different APIs.

In the future, we could expand to Atomic Mode, which uses one or more "Notary" services run by third parties to coordinate the transfers.

Reference implementation:
* <https://github.com/interledger/js-ilp> (ILP Client)
* <https://github.com/interledger/js-ilp-plugin-bells> (Ledger Plugin)



### ILP Ledger Adapter
[ILP Ledger Adapter]: #ilp-ledger-adapter

The Interledger Protocol requires a ledger that implements and exposes certain functionality (the "ILP Ledger API"). In particular, the ledger needs to allow "preparing" transfers (by holding funds) that will be automatically executed upon the receipt of a [cryptographic fulfillment message][Crypto-Conditions].

Since most existing ledgers do not have this functionality exactly, the ILP Ledger Adapter is a minimal piece of software that acts as a ledger with those qualities. It conducts book transfers in the "traditional" DFSP ledger and manages a small set of accounts as a "sub-ledger" using money allocated to it in an account on the traditional DFSP ledger.

If we are providing a DFSP ledger from scratch, it can expose the ILP Ledger API directly without needing an adapter. The same is true for the Central Ledger.

Reference Implementation: <https://github.com/interledger/five-bells-ledger>


### ILP Connector
[ILP Connector]: #ilp-connector

The ILP Connector is a piece of software that connects one DFSP's Ledger to another ledger. For now, a DFSP's connector always connects the DFSP to the Central Ledger. In the future, it is possible that ILP Connectors could connect two DFSP ledgers directly, and there could even be a competitive marketplace of ILP Connectors between pairs of ledgers.

The ILP Connector has accounts holding money with the ILP Ledger Adapters of each of the two ledgers it connects. (It can also connect directly to ledgers that implement ILP natively.) The connector defines the exchange rates between balances on the two ledgers.

Reference Implementation: <https://github.com/interledger/five-bells-connector>


### ILP Ledger Adapter Gateway
[ILP Ledger Adapter Gateway]: #ilp-ledger-adapter-gateway

The ILP Ledger Adapter Gateway is a service that proxies requests to and from the ILP Ledger Adapter of the Central Ledger. It may provide any of the following features, or more (to be decided):

* Authentication
* Connection security/encryption
* Logging/telemetry


### Payment Flow
[Payment Flow]: #payment-flow

The typical payment involves three ledgers and two ILP Connectors. This means it has at least 3 book transfers:

1. Sender's account to Sending Connector's account in the sending DFSP's ledger
2. Sending Connector's account to Receiving Connector's account in the Central ledger
3. Receiving Connector's account to Receiver's account in the receiving DFSP's ledger

In Universal Mode, these three payments are prepared in order, but released in reverse order.


### Crypto-Conditions
[Crypto-Conditions]: #crypto-conditions

Crypto-Conditions are a message type used in the Interledger Protocol to provide irrefutable proof that an event has occurred. The Crypto-Conditions specification defines two categories of message, _conditions_ and _fulfillments_. A condition describes a message that will tell you an event has occurred, without giving you enough information to create the message directly. A fulfillment is that message: it tells you an event has occurred, and matches the parameters described in the condition. Generally, a condition is a cryptographic hash of some data from one or more fulfillments.

There are many types of Crypto-Conditions, but the Simple Payment Services Protocol (SPSP) only uses the PREIMAGE-SHA-256 type of Crypto-Condition.

Specification: <https://github.com/rfcs/crypto-conditions/>
