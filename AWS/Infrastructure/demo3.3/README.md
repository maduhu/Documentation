## __DFSP1__

| Property | Description |
| ---- | ----------- |
| Instance Name | integrate-dfsp1 |
| Instance ID | i-0e209e2ea190edf8c |
| Public DNS | ec2-35-163-231-111.us-west-2.compute.amazonaws.com |
| Public IP | 35.163.231.111 |
| Instance type | m4.large |
| Private DNS | ip-172-31-23-143.us-west-2.compute.internal |
| Private IPs | 172.31.23.143 |

### Service Info

| Service | Port | Full Service URL |
| ------- | ---- | ----------- |
| dfsp1-interop-directory | 8088 |  [http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/directory/v1](http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/directory/v1/console/) |
| dfsp1-interop-spsp-client-proxy | 8088 |  [http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/console) |
| dfsp1-ilp-spsp-client-rest | 3042 | http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:3042 |
| dfsp1-ilp-spsp-server | 3043 | http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:3043 |
| dfsp1-interop-ledger-adapter | 8088 |  http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/ledger |
| dfsp1-ledger | 8014 | http://dfsp1:8014/ledger |
| dfsp1-ilp-connector | 3044 | http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:3044 |
| dfsp1-interop-spsp-backend | 8088 |  [http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1](http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1/console) |
|     |     |     |
| ist-ledger | 3075 | http://dfsp1:3075 |

### SSH

on a mac:  `ssh -i "interop-dev1.pem" ec2-user@ec2-35-163-231-111.us-west-2.compute.amazonaws.com`

on windows:  use putty, and you will need to use the .ppk file linked above.  under the `Connection/SSH/Auth` section you will need to select this .ppk file for `Private key file for authentication`

### Services

| Name | External Ports | Description |
| ---- | -------------- | ----------- |
| dfsp-interop-services | 8081 | mule services for sending dfsp (directory / spsp-client-proxy / ilp-ledger-adapter / spsp-backend ) |
| dfsp-mock-services | 9081 | temporary mule mocks to support integration |
| interop-demo-ledger | 3000 | five bells docker image admin / foo |

### QA

DFSP Directory Gateway

| path | Description |
| ---- | ----------- |
| /directory/v1 | directory gateway service root
| [console](http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/directory/v1/console/) | api demo console |


For adding user: [CentralDirectory Link] (https://github.com/LevelOneProject/Docs/blob/8a342835c15bb77d5e08af1937379788954be829/CentralDirectory/central_directory_endpoints.md)

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
| [console](http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1/console/) | api demo console |
| ... | see api documentation or console for additional functions |




## __DFSP2__

| Property | Description |
| ---- | ----------- |
| Instance Name | integrate-dfsp2 |
| Instance ID | i-08489438f19024a4a |
| Public DNS | ec2-35-163-249-3.us-west-2.compute.amazonaws.com |
| Public IP | 35.163.249.3 |
| Instance type | m4.large |
| Private DNS | ip-172-31-22-112.us-west-2.compute.internal |
| Private IPs | 172.31.22.112 |

### Service Info

| Service | Port | Full Service URL |
| ------- | ---- | ----------- |
| dfsp2-interop-directory | 8088 |  [http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8088/directory/v1](http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8088/directory/v1/console) |
| dfsp2-interop-spsp-client-proxy | 8088 |  [http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/console) |
| dfsp2-ilp-spsp-client-rest | 3042 | http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:3042 |
| dfsp2-ilp-spsp-server | 3043 | http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:3043 |
| dfsp2-interop-ledger-adapter | 8088 |  http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8088/ledger |
| dfsp2-ledger | 8014 | http://dfsp1:8014/ledger |
| dfsp2-ilp-connector | 3044 | http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:3044 |
| dfsp2-interop-spsp-backend | 8088 |  [http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1](http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1/console) |
|     |     |     |
| ist-ledger | 3075 | http://dfsp1:3075 |

### SSH

on a mac:  `ssh -i "interop-dev1.pem" ec2-user@ec2-35-163-249-3.us-west-2.compute.amazonaws.com`

on windows:  Same as above except for hostname

### Services

| Name | External Ports | Description |
| ---- | -------------- | ----------- |
| dfsp-interop-services | 8081 | mule services for sending dfsp (directory / spsp-client-proxy / ilp-ledger-adapter / spsp-backend ) |
| dfsp-mock-services | 9081 | temporary mule mocks to support integration |
| interop-demo-ledger | 3000 | five bells docker image admin / foo |

### QA

DFSP Directory Gateway

| path | Description |
| ---- | ----------- |
| /directory/v1 | directory gateway service root
| [console](http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8088/directory/v1/console/) | api demo console |


DFSP SPSP Client Proxy

| path | Description |
| ---- | ----------- |
| /spsp/client/v1 | spsp client proxy service root |
| [console](http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/console/) | api demo console |
| ... | see api documentation or console for additional functions |

ILP Ledger Adapter

This deployment of the ILP Ledger Adapter is connected to an instance of the five bells ledger running on interop-dev1.

| path | Description |
| ---- | ----------- |
| /ilp/ledger/v1 | spsp client proxy service root |
| [console](http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8088/ledger/console/) | api demo console |
| ... | see api documentation or console for additional functions |

SPSP Server Backend

| path | Description |
| ---- | ----------- |
| /spsp/backend/v1 | spsp server backend service root |
| [console](http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1/console/) | api demo console |
| ... | see api documentation or console for additional functions |
