# Transfer service API

## transfer.push.quote
Fetch fee/rate, name and other data required before approving push transfer
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

## transfer.push.add
Prepare and execute a push transfer
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
    
## transfer.pending.~
Methods for pending transfers
## transfer.bulk.~
Methods for bulk transfers
## transfer.voucher.~
Methods for vouchers
## Transfer.float.~
Methods for float transfers
