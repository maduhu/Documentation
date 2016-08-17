# Interledger Docs
> This folder includes the documentation for the Interledger components

## ILP/SPSP Client REST API

See [ILP/SPSP Client REST API](./ilp-spsp-client-rest-api.md)

## ILP Component Architecture

![ILP Component Block Diagram](./block-diagram.png)

| Component | Function | Interaction w/ Other Components | Owner | Language(s)
|---|---|---|---|---|
| SPSP Client | Main entry point for sending interledger transfers | Query receiving DFSP SPSP Server. Includes ILP Client | Ripple | JS |
| SPSP Server | Generates payment request (ILP Packet) based on SPSP Client query | Responds to SPSP Client query. Includes ILP Client | Ripple | JS |
| ILP Client | Get quotes, send interledger transfers, receive notifications of incoming transfers, fulfill transfer conditions | Included in SPSP Client and Server Get quotes from ILP Connector and authorizes transfers/holds on ILP Ledger Adapter and Central Clearing Ledger | Ripple | JS, later Java |
| ILP Connector | Route interledger payments, respond to quote requests, broadcast rates and routes to other connectors, listen for notifications of incoming transfers, authorize outgoing transfers | Responds to Client quote requests. Listens for notifications from ILP Ledger Adapter and Central Clearing Ledger | Ripple | JS |
| ILP Ledger Adapter | Implement functionality necessary to turn a DFSP's basic ledger into an ILP-compatible one: Crypto Condition validation, transfer holds, notifications | Called by ILP Client and Connectors | ModusBox (with Ripple guidance on API) | Java |
| Central Clearing Ledger | Hold/execute transfers, validate Crypto Conditions, send notifications | Called by ILP Connectors and sends notifications to Connectors | Dwolla | ? |
| DFSP Core System | Already existing accounting system. Implements basic ledger functionality (simple transfers without holds) | Called by ILP Ledger Adapter | Existing / Software Group | ? |  

## ILP Flow

![ILP Flow Diagram](./flow-diagram.png)

Notes:
* Ripple may reimplement the SPSP/ILP Client in Java so that it can run on top of MuleSoft and DFSPs can interact with it using any APIs provided by MuleSoft
* If the DFSP Core Systems are not performant enough, we may need to remove them from the flow of the ILP transfer

Open Questions:

- [ ] Is it okay that SPSP involves one DFSP-to-DFSP request? Can we assume that there is a way for DFSPs to communicate (even if the requests are proxied by the IST)?
- [ ] Which requests should go through a proxy layer (hosted by the DFSP or IST)?

