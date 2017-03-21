## Links to working implementations
This document links to the working API specifications in swagger or RAML, since machine names may change, use the current [machine reference](./AWS/Infrastructure/machines.md) to get the DNS name or IP address of the appropriate portal server. For example, integrate-dfsp1 was ec2-35-163-231-111.us-west-2.compute.amazonaws.com when this was written.

For the DFSP API links, use the [DFSP Port Guide](./DFSP#default-ports) to get the ports and link. Example: for the DFSP directory API, use the DFSP machine which might be ec2-35-163-231-111.us-west-2.compute.amazonaws.com, combine with the port, 8011, and the swagger link, /documentation, to get http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8011/documentation.

For the Portal links use the ports below with the same DFSP machine. Example: for the DFSP machine, use port 8088, and /directory/v1/console to get http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/directory/v1/console/.

### Service Info

| Service | Port | Full Service URL |
| ------- | ---- | ----------- |
| dfsp1-interop-directory | 8088 |  [http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/directory/v1](http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/directory/v1/console) |
| dfsp1-interop-spsp-client-proxy | 8088 |  [http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/console) |
| dfsp1-ilp-spsp-client-rest | 3042 | http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:3042 |
| dfsp1-ilp-spsp-server | 3043 | http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:3043 |
| dfsp1-interop-ledger-adapter | 8088 |  http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/ledger |
| dfsp1-ledger | 8014 | http://http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8014/ledger |
| dfsp1-ilp-connector | 3044 | http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:3044 |

| ist-ledger | 3075 | http://:3075 |

### SSH

on a mac:  `ssh -i "interop-dev1.pem" ec2-user@ec2-35-163-231-111.us-west-2.compute.amazonaws.com`

on windows:  use putty, and you will need to use the .ppk file.  under the `Connection/SSH/Auth` section you will need to select this .ppk file for `Private key file for authentication`

### Services

| Name | External Ports | Description |
| ---- | -------------- | ----------- |
| dfsp-interop-services | 8081 | mule services for sending dfsp (directory / spsp-client-proxy / ilp-ledger-adapter / spsp-backend ) |
| dfsp-mock-services | 9081 | temporary mule mocks to support integration |
| interop-demo-ledger | 3000 | five bells docker image admin / foo |

### Dev

DFSP Directory Gateway

| path | Description |
| ---- | ----------- |
| /directory/v1 | directory gateway service root
| [console](http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/directory/v1/console/) | api demo console |
| [user/get](http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/directory/v1/user/get) | /directory/user/get |
| [user/add](http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/directory/v1/user/add) | for testing only - add accounts to directory |

This proxy API is based on, and should match, the [Central Directory API](./CentralDirectory/central_directory_endpoints.md)

DFSP SPSP Client Proxy

| path | Description |
| ---- | ----------- |
| /spsp/client/v1 | spsp client proxy service root |
| [console](http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/console/) | api demo console |
| ... | see api documentation or console for additional functions |

ILP Ledger Adapter

This deployment of the ILP Ledger Adapter is connected to an instance of the five bells ledger running on interop-dev1.

| path | Description |
| ---- | ----------- |
| /ilp/ledger/v1 | spsp client proxy service root |
| [console](http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/ledger/console/) | api demo console |
| ... | see api documentation or console for additional functions |

SPSP Server Backend

| path | Description |
| ---- | ----------- |
| /spsp/backend/v1 | spsp server backend service root |
| [console](http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8090/spsp/backend/v1/console/) | api demo console |
| ... | see api documentation or console for additional functions |
