# Rule service API

This service contains methods related to fees, limits and other rules 

1. **rule.condition.check** - check for limits/fraud and return applicable tier (local) fee
	* parameters
	    * tranferType - type of transfer (push, pending, bulk, etc.)
	    * destinationURL - recipient URL
	    * sourceURL - sender URL
	    * sourceAmount | 
	    * destinationAmount - the source or the destination amount of the transfer
	    * currency - the respective currency of the amount
  	* result
	    * destinationAmount | 
	    * sourceAmount - the destination or source amount, including fees/rates
	    * currency - the amount currency	 
	* errors
    	* rule.unknownDestination - receiver not found
    	* rule.unknownSource - sender not found
    	* rule.invalidAmount
    	* rule.invalidCurrency
    	* rule.fraudViolation   
    	* rule.limitViolation
1. **rule.push.execute** - instruct the rule service that a transfer will be executed
	* parameters
	    * tranferType - type of transfer (push, pending, bulk, etc.)
	    * destinationURL - recipient URL
	    * sourceURL - sender URL   
	    * sourceAmount - source amount
	    * destinationAmount - the destination amount of the transfer
	    * sourceCurrency - the source currency of the amount
	    * destinationCurrency - the destination currency of the amount
	    * transferId - a unique identifier of the transfer 
  	* result
	    * 
	* errors
    	* rule.unknownDestination - receiver not found
    	* rule.unknownSource - sender not found
    	* rule.invalidSourceAmount
    	* rule.invalidSourceCurrency
		* rule.invalidDestinationAmount
    	* rule.invalidDestinationCurrency
    	* rule.invalidTransferId
    	* rule.duplicatedTransferId
    	* rule.fraudViolation   
    	* rule.limitViolation


1. **rule.push.reverse** - instruct the rule service that a transfer with a token reference is roll backed
	* parameters
	    * TransferId - a unique identifier of the transfer
  	* result
	    * 
	* errors
    	* rule.invalidTransferId
    	* rule.alreadyReversed

1. **rule.voucher.check** - check voucher’s applicability
