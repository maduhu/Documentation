# Interledger Components and Flow
> This folder includes the documentation for the Interledger components

## Running the ILP/SPSP components

[Ansible](https://docs.ansible.com/ansible/playbooks.html) is used for deploying the [`ilp-connector`](https://github.com/interledgerjs/ilp-connector) and the [`ilp-service`](https://github.com/LevelOneProject/ilp-service). The [Ansible Playbook](./ansible/ansible.yml) can be run with the command:

```sh
ansible-playbook -v --extra-vars="docker_username=<FILL ME IN> docker_password=<FILL ME IN> docker_email=<FILL ME IN>" --inventory-file=hosts-test ansible.yml
```

This command should be run from the [ansible](./ansible) directory in this repository.

Ansible will use the SSH keys found in your normal SSH directory to log in to the servers.

The Docker credentials are those used for the private registry (modusbox-level1-docker.jfrog.io).

The Inventory File should either be the [hosts-test](./ansible/hosts-test) or [hosts-qa](./ansible/hosts-qa) depending on whether you want to deploy the components to the L1P Test or QA environment.

## ILP Ledger Adapter API

See [ILP Ledger Adapter API](./ledger-adapter.md)

## ILP Service API

See [ILP Service](http://github.com/LevelOneProject/ilp-service).

## ILP Component Architecture

**TODO:** Update block diagram with `ilp-service`

![ILP Component Block Diagram](./block-diagram.png)

| Component | Function | Interaction w/ Other Components | Owner | Language(s)
|---|---|---|---|---|
| ILP Service | Main entry point for sending interledger transfers | Includes the ILP Client | Ripple | JS |
| ILP Client | Get quotes, send interledger transfers, receive notifications of incoming transfers, fulfill transfer conditions | Included in SPSP Client and Server Get quotes from ILP Connector and authorizes transfers/holds on ILP Ledger Adapter and Central Clearing Ledger | Ripple | JS, later Java |
| ILP Connector | Route interledger payments, respond to quote requests, broadcast rates and routes to other connectors, listen for notifications of incoming transfers, authorize outgoing transfers | Responds to Client quote requests. Listens for notifications from ILP Ledger Adapter and Central Clearing Ledger | Ripple | JS |
| ILP Ledger Adapter | Implement functionality necessary to turn a DFSP's basic ledger into an ILP-compatible one: Crypto Condition validation, transfer holds, notifications | Called by ILP Client and Connectors | ModusBox (with Ripple guidance on API) | Java |
| Central Clearing Ledger | Hold/execute transfers, validate Crypto Conditions, send notifications | Called by ILP Connectors and sends notifications to Connectors | Dwolla | ? |
| DFSP Core System | Already existing accounting system. Implements basic ledger functionality (simple transfers without holds) | Called by ILP Ledger Adapter | Existing / Software Group | ? |

## ILP Flow

**TODO:** Update flow diagram with `ilp-service`

![ILP Flow Diagram](./flow-diagram.png)

