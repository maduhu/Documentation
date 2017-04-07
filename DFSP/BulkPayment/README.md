
# Bulk Payments

-----

## 1. Bulk payment initiation

In the DFSP there should be a web interface available where every user (with certain user rights) of the DFSP can login with user number and PIN and initiate a bulk payment.

When initiating a bulk payment the user should be able to select an account from which the bulk payments will be send.

L1P shall support upload of the bulk file with payments from a web interface. The file format should be .csv. The fields in the file will be:

- Sequence Number (row)
- User Number or Phone
- Client Name
- Date of birth
- National id
- Amount

## 2. Maker/Checker

There should be a maker/checker concept implemented for bulk payments. Maker and Checker will be roles that can be assigned to different users. Maker role is going to upload the file with the bulk payments and checker role is going to initiate the bulk payments.

## 3. Bulk file verification

All the fields in the bulk file shall be mandatory.

In case the user number or phone number are not valid the funds will not be disbursed to this user.

## 4. Handling fees

The discriminatory fees should be recorded in the same way as it is done for a regular transaction - different row in the ledger for each payment. There is possibility to configure different fees per different transaction type, so there could be special set of fee configured for the bulk payments.
The rational behind having the fees recorded as a separate line for each transaction is:

- Calculation and record of the discriminatory fees are done within the DFSP and we do not foresee any performance issues to come from that approach.

- The fees must be recorded as a separate row in the ledger

## 5. Re-sending of funds in case a temporary error is detected

Upon sending bulk payments the system should detect temporary errors and retry sending of funds again to those user.

Temporary errors could be technical such as missing connectivity or some service is down or non technical such as not enough funds in the account, tier limit is hit, etc.

The system should detect a special case - a user has already a user number but for a service different from mWallet/bank account. In this case the system should notify the user to open a mWallet/bank account.

When initiating a bulk payment the system shall allow entering of the end date/time after which the bulk report will be finalized and no more retries to recover from temporary errors will be done.

The L1P should have some logic for retrying bulk payments which had failed due to temporary errors.


## 6. Handling bulk payments transactions

The diagram below illustrates both cases - whether the user has a mwallet account or not.
* In case the user has an existing mwallet account the money will be attempted to be transferred to it
* In the other case - the user's default DFSP will indicate that there's no mwallet account associated with the given user and therefore a temporary error will be recorded in sending DFSP's database and the user will be notified.

![](./src/bulk_payment_single_record_processing.png)
