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
| dfsp-account | 8009 | 0.9.14 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8009](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8009/documentation) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8009](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8009/documentation)  |
| dfsp-api | 8010 | 0.28.26 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8010](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8010/documentation) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8010](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8010/documentation)  |
| dfsp-directory | 8011 | 0.7.14 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8011](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8011/documentation) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8011](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8011/documentation)  |
| dfsp-identity | 8012 | 0.9.13 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8012](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8012/documentation) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8012](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8012/documentation)  |
| dfsp-ledger | 8014 | 1.1.18 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8014](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8014/documentation) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8014](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8014/documentation)  |
| dfsp-rule | 8016 | 0.5.11 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8016](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8016/documentation) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8016](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8016/documentation)  |
| dfsp-subscription | 8017 | 0.3.16 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8017](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8017/documentation) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8017](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8017/documentation)  |
| dfsp-transfer | 8018 | 0.20.13 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8018](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8018/documentation) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8018](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8018/documentation)  |
| dfsp-ussd | 8019 | 0.23.21 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8019](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8019/documentation) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8019](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8019/documentation)  |
| dfsp-admin | 8020 | 0.15.8 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8020](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8020/documentation) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8020](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8020/documentation)  |


### Mule Proxy  - Modusbox

| Service | Port | Version | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------| --------- | --------- |
| interop-directory-gateway | 8088 | 0.4.23 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/directory/v1](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/directory/v1/open-api/) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/directory/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/directory/v1/open-api/) |
| interop-spsp-client-proxy | 8088 | 0.4.14 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/open-api) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/open-api) |
| interop-ledger-adapter | 8088 | 0.1.4 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/ledger](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/open-api) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ledger](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/open-api) |
| interop-spsp-backend | 8088 | 0.1.14 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1/open-api) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1/open-api) |
| interop-scheme-adapter | 8088 | 0.1.1 | [http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/scheme/adapter/v1](http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:8088/scheme/adapter/v1/open-api) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/scheme/adapter/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/scheme/adapter/v1/open-api) |

### ILP - Ripple

| Service | Port | Version | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------| --------- | --------- |
| ilp-connector | 3044 | 17.0.1 | N/A | N/A |
| ilp-service | 3045 | 0.3.1 | [ec2-52-32-130-4.us-west-2.compute.amazonaws.com:3045] | [ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3045] |

### Central Services - Dwolla

| Service | Port | Version | URL |
| ------- | -----| --------| ----|
| central-ledger | 80 | 1.81.0 | [http://central-ledger-test-1778278640.us-west-2.elb.amazonaws.com](http://central-ledger-test-1778278640.us-west-2.elb.amazonaws.com)|
| central-directory | 80 | 0.16.0 | [ http://central-directory-test-2067903239.us-west-2.elb.amazonaws.com](http://central-directory-test-2067903239.us-west-2.elb.amazonaws.com) |
| central-fraud-sharing | 80 | 1.4.0 | [http://central-fraud-sharing-test-1335812161.us-west-2.elb.amazonaws.com](http://central-fraud-sharing-test-1335812161.us-west-2.elb.amazonaws.com) |
| end-user-registry | 80 | 0.9.0 | [http://central-end-user-registry-test-1765383584.us-west-2.elb.amazonaws.com](http://central-end-user-registry-test-1765383584.us-west-2.elb.amazonaws.com) |


## Test Users
### DFSP1
* bob    http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:3043/v1/receivers/43112673
* alice    http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:3043/v1/receivers/15125122
* merchant    http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:3043/v1/receivers/51965171
* dfsp1-testconnector    http://ec2-52-32-130-4.us-west-2.compute.amazonaws.com:3043/v1/receivers/70377406

### DFSP2
* bob    http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3043/v1/receivers/52652310
* alice    http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3043/v1/receivers/16433370
* merchant    http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3043/v1/receivers/7642271
* dfsp2-testconnector    http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3043/v1/receivers/17923071

## Start/Stop and deployment scripts

### Mule
* /home/ec2-user/scripts/modusbox/stop_mule.sh
* /home/ec2-user/scripts/modusbox/start_mule.sh

### DFSP
* See [Ansible docs for DFSP](https://github.com/paymoja/Docs/tree/master/DFSP/dfspDeploymentProcess)

### ILP/SPSP
* See [Ansible docs for ILP](https://github.com/paymoja/Docs/blob/master/ILP/README.md)

### Central Services
Uses Node instead of Ansible for deployment scripts that so that these can be called by CircleCI. The code for these in each repo under a deploy directory. 
Example: [Central director](https://github.com/paymoja/central-directory/blob/master/deploy/node/index.js)
