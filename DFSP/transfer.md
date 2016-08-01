# Transfer service API

1. **transfer.push.quote** - fetch fee/rate, name and other data required before approving push transfer
  * parameters
    * sourceAccountNumber - sender account
    * sourceAmount - amount to be sent
    * destinationUserNumber / destinationAccountNumber - recipient
    * destinationAmount - amount to be received
    * currency - currency of the amount
  * result
    * destinationAmount - amount with fee/rate applied
    * sourceAmount - amount with fee/rate applied
    * currency - currency of the amount
    * name - recipient name
    * token - token, controlling timeouts, routing, etc.
  * errors
    * transfer.unknownDestination - receiver not found
    * transfer.invalidAmount
    * transfer.invalidCurrency
    * transfer.fraudViolation
1. **transfer.push.add** - prepare and execute a push transfer
  * parameters
    * sourceAccountNumber - sender account
    * sourceAmount - amount to be sent
    * destinationUserNumber / destinationAccountNumber - recipient
    * destinationAmount - amount to be received
    * message - a message associated with the transaction
    * currency - currency of the amount
    * token - the token received during quoting
  * result
    * receipt - transaction approval identifier, unique to sender's DFSP
  * error - all errors from quote +
    * transfer.quoteTimeout - in case fee/rate is not applicable anymore
1. **transfer.pending.~** - methods for pending transfers
1. **transfer.bulk.~** - methods for bulk transfers
1. **transfer.voucher.~** - methods for vouchers
1. **transfer.float.~** - methods for float transfers
