# DFSP documentation

## Microservices component diagram

DFSP functionality is split in the following services

- **dfsp-api** - contains business the logic and exposes it as API
- **[dfsp-directory](directory.md)** - methods related to lookup services, like finding URLs, obtaining lists of districts, towns, participants, etc.
- **[dfsp-identity](identity.md)** - methods for managing identity related data, like sessions, images, PINs, etc.
- **dfsp-ledger** - ledger service will keep account balances and transfers
- **dfsp-interledger** - service implementing the Interledger protocol
- **[dfsp-notification](notification.md)** - here we will put functionality like SMS, email and smart app notifications
- **[dfsp-rule](rule.md)** - here we will put fees, limits and other rules, like checking where a voucher can be used. Looks like a good place to also put the AML functionality, but this is hard to determine at the moment.
- **[dfsp-subscription](subscription.md)** - methods related to managing the data associated to a subscription, but not related to accounts.
- **[dfsp-transfer](transfer.md)** - here we will put all methods that relate to movement of money between accounts
- **[dfsp-account](account.md)** - this will be for methods that affect ledger accounts, like creating new ones or relations between account and other data like NFC, biometric, float, phone, signatories, etc.

## Default ports

Each service has some default ports in the development environment. Below you can find these defaults for each project.

| project                                                                       | debug console    |  httpserver port
| ---------------                                                               | ------------     | ---------------
| [dfsp-api](https://github.com/LevelOneProject/dfsp-api)                       | 30010            | 8010
| [dfsp-directory](https://github.com/LevelOneProject/dfsp-directory)           | 30011            | 8011
| [dfsp-identity](https://github.com/LevelOneProject/dfsp-identity)             | 30012            | 8012
| [dfsp-interledger](https://github.com/LevelOneProject/dfsp-interledger)       | 30013            | 8013
| [dfsp-ledger](https://github.com/LevelOneProject/dfsp-ledger)                 | 30014            | 8014
| [dfsp-notification](https://github.com/LevelOneProject/dfsp-notification)     | 30015            | 8015
| [dfsp-rule](https://github.com/LevelOneProject/dfsp-rule)                     | 30016            | 8016
| [dfsp-subscription](https://github.com/LevelOneProject/dfsp-subscription)     | 30017            | 8017
| [dfsp-transfer](https://github.com/LevelOneProject/dfsp-transfer)             | 30018            | 8018
| [dfsp-ussd](https://github.com/LevelOneProject/dfsp-ussd)                     | 30019            | 8019

## Development environment setup

Look in [Development environment setup](development.md)

## Component diagram

![microservices component diagram](./microServices.png)

## Push transfer sequence diagram

![Push transfer sequence diagram](./transfer.push.create.png)

## Bulk transfer sequence diagram

![Bulk transfer sequence diagram](./transfer.bulk.create.png)
