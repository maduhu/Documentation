## Test Environment - AWS EC2 Instance Information
The test environment is used for development and new or possibly breaking changes.

| Property | DFSP1 | DFSP2 |
| ----     | ----- | ----- |
| Instance Name | integrate-test-dfsp1 | integrate-test-dfsp2 |
| Instance ID | i-03e931e01532a6287 | i-0193fad0b1cd06e7b |
| Public DNS | ec2-52-32-130-4.us-west-2.compute.amazonaws.com | ec2-35-166-236-69.us-west-2.compute.amazonaws.com |
| Public IP | 52.32.130.4 | 35.166.236.69 |
| Instance type | m4.large | m4.large |
| Private DNS | ip-172-31-35-189.us-west-2.compute.internal | ip-172-31-45-167.us-west-2.compute.internal |
| Private IPs | 172.31.35.189 | 172.31.45.167 |

## SSH

### Mac
* for dfsp1 - `ssh -i "interop-dev1.pem" ec2-user@ec2-52-32-130-4.us-west-2.compute.amazonaws.com`
* for dfsp2 - `ssh -i "interop-dev1.pem" ec2-user@ec2-35-166-236-69.us-west-2.compute.amazonaws.com`

### Windows
  Use putty, and you will need to use the .ppk file linked above.  Under the `Connection/SSH/Auth` section you will need to select this .ppk file for `Private key file for authentication`.


## API Information

### DFSP - Software Group

| Service | Port | Version | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------| --------- | --------- |
| dfsp-ledger | 8014 | | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8014/ledger](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8014/documentation) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8014/ledger](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8014/documentation)  |
| dfsp-invoices | 8010 | | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8010](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8010/documentation) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8010](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8010/documentation)  |


### Mule Proxy  - Modusbox

| Service | Port | Version | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------| --------- | --------- |
| interop-directory-gateway | 8088 | 0.4.23 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/directory/v1](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/directory/v1/open-api/) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/directory/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/directory/v1/open-api/) |
| interop-spsp-client-proxy | 8088 | 0.4.14 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/open-api) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/open-api) |
| interop-ledger-adapter | 8088 | 0.1.4 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/ledger](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/open-api) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ledger](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/open-api) |
| interop-spsp-backend | 8088 | 0.1.14 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1/open-api) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1/open-api) |

### ILP - Ripple

| Service | Port | Version | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------| --------- | --------- |
| ilp-spsp-client-rest | 3042 |           |           ||
| ilp-spsp-server | 3043 |           |           ||
| ilp-connector | 3044 | | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:3044/v1](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:3044/v1) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3044/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3044/v1) |

### IST - Dwolla

| Service | Port | Version | URL |
| ------- | -----| --------| ----|
| central-ledger |  | | [http://central-ledger-1139971789.us-west-2.elb.amazonaws.com/](http://central-ledger-1139971789.us-west-2.elb.amazonaws.com/documentation)|
| central-directory |  | | [ http://central-directory-214462011.us-west-2.elb.amazonaws.com/]( http://central-directory-214462011.us-west-2.elb.amazonaws.com/documentation) |
| end-user-registry |  |  | [http://central-end-user-registry-1833170602.us-west-2.elb.amazonaws.com/](http://central-end-user-registry-1833170602.us-west-2.elb.amazonaws.com/documentation)|


## Test Users
### DFSP1    
* bob    http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:3043/v1/receivers/17500419
* alice    http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:3043/v1/receivers/48607986
* merchant    http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:3043/v1/receivers/11597861
* dfsp1-testconnector    http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:3043/v1/receivers/29290645

### DFSP2    
* bob    http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3043/v1/receivers/33859321
* alice    http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3043/v1/receivers/66422611
* merchant    http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3043/v1/receivers/99826154
* dfsp2-testconnector    http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3043/v1/receivers/81455568

## Start/Stop scripts

### Mule
* /home/ec2-user/scripts/modusbox/stop_mule.sh
* /home/ec2-user/scripts/modusbox/start_mule.sh

### DFSP
* See [Ansible docs for DFSP](https://github.com/LevelOneProject/Docs/tree/master/DFSP/dfspDeploymentProcess)

### ILP/SPSP
* See [Ansible docs for ILP](https://github.com/LevelOneProject/Docs/blob/master/ILP/README.md)

### IST
* <TO BE FILLED>
