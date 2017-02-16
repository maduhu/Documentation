## AWS EC2 Instance Info

| Property | DFSP1 | DFSP2 |
| ----     | ----- | ----- |
| Instance Name | integrate-test-dfsp1 | integrate-test-dfsp2 |
| Instance ID | i-03e931e01532a6287 | i-0193fad0b1cd06e7b |
| Public DNS | ec2-35-166-189-14.us-west-2.compute.amazonaws.com | ec2-35-166-236-69.us-west-2.compute.amazonaws.com |
| Public IP | 35.166.189.14 | 35.166.236.69 |
| Instance type | m4.large | m4.large |
| Private DNS | ip-172-31-35-189.us-west-2.compute.internal | ip-172-31-45-167.us-west-2.compute.internal |
| Private IPs | 172.31.35.189 | 172.31.45.167 |

#### SSH

on a mac:  
* for dfsp1 - `ssh -i "interop-dev1.pem" ec2-user@ec2-35-166-189-14.us-west-2.compute.amazonaws.com`
* for dfsp2 - `ssh -i "interop-dev1.pem" ec2-user@ec2-35-166-236-69.us-west-2.compute.amazonaws.com`


on windows:  use putty, and you will need to use the .ppk file linked above.  under the `Connection/SSH/Auth` section you will need to select this .ppk file for `Private key file for authentication`


## API Info

#### DFSP - Software Group

| Service | Port | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------- | --------- |
| dfsp-ledger | 8014 | [http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8014/ledger](http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8014/documentation) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8014/ledger](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8014/documentation)  |
| dfsp-invoices | 8010 | [http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8010](http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8010/documentation) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8010](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8010/documentation)  |


#### Mule Proxy  - Modusbox

| Service | Port | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------- | --------- |
| interop-directory-gateway | 8088 |  [http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/directory/v1](http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/directory/v1/console/) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/directory/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/directory/v1/console/) |
| interop-spsp-client-proxy | 8088 |  [http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/console) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/console) |
| interop-ledger-adapter | 8088 |  [http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/ledger](http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/console) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ledger](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ilp/ledger/v1/console) |
| interop-spsp-backend | 8088 |  [http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1](http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1/console) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1/console) |

#### ILP - Ripple

| Service | Port | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------- | --------- |
| ilp-spsp-client-rest | 3042 |           |           |
| ilp-spsp-server | 3043 |           |           |
| ilp-connector | 3044 | [http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:3044/v1](http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:3044/v1) | [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3044/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3044/v1) |

#### IST - Dwolla

| Service | Port | URL |
| ------- | -----| ----| 
| central-ledger |  | [http://central-ledger-dev.us-west-2.elasticbeanstalk.com](http://central-ledger-dev.us-west-2.elasticbeanstalk.com/documentation)|
| central-directory |  |  |
| end-user-registry |  |  |


## Test Users
#### DFSP1    
* bob    http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:3043/v1/receivers/94844611
* alice    http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:3043/v1/receivers/25777533
* merchant    http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:3043/v1/receivers/40298708
* dfsp1-testconnector    http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:3043/v1/receivers/19541399
    
#### DFSP2    
* bob    http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3043/v1/receivers/76596265
* alice    http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3043/v1/receivers/29101346
* merchant    http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3043/v1/receivers/26072010
* dfsp2-testconnector    http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3043/v1/receivers/48489818

