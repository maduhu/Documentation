# Send Money Tests

As part of the Level One principles, the customer must be able to see at least the name of the person or business they are sending their money to and the full cost of the transfer, broken out by principle and total fees, before they approve sending money. Money can only be sent (pushed) not debited (pulled).

## Variations
Instead of listing every case, we list the equivalence classes for variations that can be done when sending money. These include positive cases, positive and negative boundary cases, and invalid cases. In all postive cases, the fulfillment should be recorded in the ledgers of both the payee and payer DFSPs and the central ledger.

Combinations of some equivalence classes should be tried. In general, negative cases, marked (x), are not combined with other variations unless mentioned and should have an error message.

### Destinations
- Same DFSP
- Another DFSP	
- (x) Invalid Destination customer

### Customers
Combine with destinations
- Same customer
- Different customer
- Same customer, different account but same DFSP

### Amount to send
Amounts don't need to be combined with other variations. 
- 1
- Some
- Exact account balance
- (x) More than account balance due to fees
- (x) More than account balance

### Limits
These limits should be true between DFSPs or in the same DFSP, so combine with destinations.

A cancelled or rejected transfer still counts against a customer limit. Refunds are separate transfers initiated by the DFSP that do not count against a customer limit.

- The maxmimum number of transfers in a day 
- (x) One more than the maximum number of transfers per day
- Send the maximum tansaction size
- (x) Send more than the maximum transaction size

Sending to yourself on the same DFSP probably shouldn't be counted toward the transfer limit, but that isn't implemented.

### States
A transfer can be one of several states. Some of the states have multiple ways they can occur. Each of these variations needs to be tested, but don't need to be combined with other variations.

- Unknown
    - Preparing and within timeout
	- After timeout, but not notified
- Cancelled (timeout)
	- Payer DFSP timeout
	- Center timeout during prepare
	- Payee DFSP timeout
	- Center timeout during fulfill. The center acknowledges the fulfill message, but sends a cancellation for the notification.
	- Final Payee timeout. In this state the transfer is already fulfilled. Even if the timeout occurs after reciept by the sender but before the sender ledger handles it, the sender DFSP should process the transfer.
- Rejected
	- Center rejects (ex: insufficient settlement funds)
	- Payee rejects
- Fulfilled

## Additional Send Money test for special conditions

### Thread contention/Sequence errors
- Remove a destination user after the quote but before the transfer is recieved be the destination DFSP.

### Time skew
- Verify time skew is not relevant by setting each service on different dates and sending money. This test may make the logs look odd. 

### Resilience
Despite these failures, no ledger should lose money and the transfer should eventually succeed when the failure is resolved.

- Message failures. Messages are dropped
    - Halt fulfillment notification messages for center
    - Halt fulfillment notification messages for payee DFSP
    - Halt fulfillment notification messages for payer DFSP. 
Transfer should go through due to retries when connection is re-established.

- Service failures. In each case the payment should complete afer the service is restarted. 
    - Take down payee DFSP after quote
    - Take down center DFSP before and after prepare
    - Take down payer ledger adapter after prepare
    - Take down client when a transfer is unknown (is retry initiated by the DFSP when the client is restarted?)

- Verify Idempotence - cause retries and verify only 1 transaction on ledgers for both DFSPs and the center.

### Settlement
To support deferred net settlement, the central ledger can easily list:

- Fulfilled transfers 
    - with fees broken out for separate accounts
- Balances by DFSP
- Cancelled and rejected transfers
- Unknown expired transfers


## Refunds
Refunds are not currently implemented.

In this system all transfers are final, so a a refund is a second transfer in the opposite direction for the original amount including both principle and fees. It contains data to link it to the original transfer it is negating.

DFSPs typically do not charge fees for the refund. 

Refunds may be charged a central fee if they charge on to every other transfer, which the DFSP can choose to pass on to the customer or not. 

The refund is marked as such so that the central ledger can report on it appropriately.
