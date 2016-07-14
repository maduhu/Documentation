# Interledger Docs
> This folder includes the documentation for the Interledger components

## ILP Component Architecture

![ILP Component Block Diagram](./block-diagram.png)

## ILP Flow

![ILP Flow Diagramt](./flow-diagram.png)

Notes:
* Ripple may reimplement the SPSP/ILP Client in Java so that it can run on top of MuleSoft and DFSPs can interact with it using any APIs provided by MuleSoft
* If the DFSP Core Systems are not performant enough, we may need to remove them from the flow of the ILP transfer

Open Questions:

- [ ] Is it okay that SPSP involves one DFSP-to-DFSP request? Can we assume that there is a way for DFSPs to communicate (even if the requests are proxied by the IST)?
- [ ] Which requests should go through a proxy layer (hosted by the DFSP or IST)?

