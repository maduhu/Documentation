| Property | Description |
| ---- | ----------- |
| Instance ID | i-0826afc008e9c45a8 |
| Public DNS | ec2-52-37-54-209.us-west-2.compute.amazonaws.com |
| Public IP | 52.37.54.209 |
| Instance type | m4.large |
| Private DNS | ip-172-31-24-6.us-west-2.compute.internal |
| Private IPs | 172.31.24.6 |

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
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8081/directory/v1/console/) | api demo console |
| [user/get](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8081/directory/v1/user/get) | /directory/user/get |
| [user/add](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8081/directory/v1/user/add) | for testing only - add accounts to directory |

DFSP SPSP Client Proxy
| path | Description |
| ---- | ----------- |
| /spsp/client/v1 | spsp client proxy service root |
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8081/spsp/client/v1/console/) | api demo console |
| ... | see api documentation or console for additional functions |

SPSP Server Backend
| path | Description |
| ---- | ----------- |
| /spsp/backend/v1 | spsp server backend service root |
| [console](http://ec2-52-37-54-209.us-west-2.compute.amazonaws.com:8081/spsp/backend/v1/console/) | api demo console |
| ... | see api documentation or console for additional functions |
