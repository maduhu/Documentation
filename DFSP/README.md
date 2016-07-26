# Microservices component diagram

DFSP functionality is split in the following services

- **dfsp** - contains business the logic and exposes it as API
- **transfer** - here we will put all methods that relate to movement of money between accounts
- **rule** - here we will put fees, limits and other rules, like checking where a voucher can be used. Looks like a good place to also put the AML functionality, but this is hard to determine at the moment.
- **notification** - here we will put functionality like sms, email and smart app notifications
- **account** - this will be for methods that affect ledger accounts, like creating new ones or relations between account and other data like nfc, biometric, float, phone, signatories, etc.
- **subscription** - methods related to managing the data associated to a subscription, but not related to accounts.
- **directory** - methods related to lookup services, like finding URLs, obtaining lists of districts, towns, participants, etc.
- **identity** - methods for managing identity related data, like sessions, images, PINs, etc.

The diagram below shows how services interact:
![microservices component diagram](./microServices.png)



# Push transfer sequence diagram

![Push transfer sequence diagram](./transfer.push.create.png)