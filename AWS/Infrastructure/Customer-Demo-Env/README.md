## Customer Demo Environment - AWS EC2 Instance Information
The Customer Demo environment is used for Client demos. It is more stable than the Test and QA environments and will be a few versions older. 

| Property | DFSP1 | DFSP2 |
| ----     | ----- | ----- |
| Instance Name | dfsp1-demo | dfsp2-demo |
| Instance ID | i-0b29f70d8aa42ded5 | i-0695f7869efb2d1b1 |
| Public DNS | ec2-52-8-184-95.us-west-1.compute.amazonaws.com | ec2-52-8-68-18.us-west-1.compute.amazonaws.com |
| Public IP | 52.8.184.95 | 52.8.68.18 |
| Instance type | m4.xlarge | m4.xlarge |
| Private DNS | ip-172-31-13-86.us-west-1.compute.internal | ip-172-31-3-14.us-west-1.compute.internal |
| Private IPs | 172.31.13.86 | 172.31.3.14 |

### SSH

#### Mac  
* for dfsp1 - `ssh -i "interop-dev1.pem" ubuntu@ec2-52-8-184-95.us-west-1.compute.amazonaws.com`
* for dfsp2 - `ssh -i "interop-dev1.pem" ubuntu@ec2-52-8-68-18.us-west-1.compute.amazonaws.com`


#### Windows
 Use putty, and you will need to use the .ppk file linked above.  
 Under the `Connection/SSH/Auth` section you will need to select this .ppk file for `Private key file for authentication`


## API Info

### DFSP - Software Group

| Service | Port | Version | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------| --------- | --------- |
| dfsp-account | 8009 | 0.9.26-master | [http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8009](http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8009/documentation) | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8009](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8009/documentation)  |
| dfsp-api | 8010 | 1.0.0-quotes.81-major_quotes | [http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8010](http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8010/documentation) | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8010](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8010/documentation)  |
| dfsp-directory | 8011 | 0.7.26-master | [http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8011](http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8011/documentation) | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8011](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8011/documentation)  |
| dfsp-identity | 8012 | 0.10.4-master | [http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8012](http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8012/documentation) | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8012](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8012/documentation)  |
| dfsp-ledger | 8014 | 2.0.0-quotes.29-major_quotes | [http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8014](http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8014/documentation) | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8014](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8014/documentation)  |
| dfsp-rule | 8016 | 0.5.24-master | [http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8016](http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8016/documentation) | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8016](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8016/documentation)  |
| dfsp-subscription | 8017 | 0.5.8-master | [http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8017](http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8017/documentation) | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8017](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8017/documentation)  |
| dfsp-transfer | 8018 | 0.20.23-master | [http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8018](http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8018/documentation) | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8018](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8018/documentation)  |
| dfsp-ussd | 8019 | 1.0.0-quotes.42-major_quotes | [http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8019](http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8019/documentation) | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8019](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8019/documentation)  |
| dfsp-admin | 8020 | 0.17.42-master | [http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8020](http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8020/documentation) | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8020](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8020/documentation)  |

### Mule Proxy  - Modusbox

| Service | Port | Version | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------| --------- | --------- |
| interop-dfsp-directory | 8088 | 0.4.23 | [http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8088/directory/v1](http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8088/directory/v1/open-api/) | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/directory/v1](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/directory/v1/open-api/) |
| interop-ilp-ledger | 8088 | 0.1.5 | [http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8088/spsp/client/v1/open-api) | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/spsp/client/v1/open-api) |
| interop-scheme-adapter | 8088 | 1.0.12 | [http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8088/ledger](http://ec2-52-8-184-95.us-west-1.compute.amazonaws.com:8088/ilp/ledger/v1/open-api) | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/ledger](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/ilp/ledger/v1/open-api) |

### ILP - Ripple

| Service | Port | Version | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------| --------- | --------- |
| ilp-service| 3043 | v2.1.4 |         |           |
| ilp-connector | 3000 | v21.1.3 |  |  |

### IST - Dwolla

| Service | Port | Version | URL |
| ------- | -----| --------| ----|
| central-ledger | 3002 | v1.85.3 | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:3002](ec2-54-153-95-230.us-west-1.compute.amazonaws.com:3002/documentation)|
| central-ledger-admin | 3004 | v1.85.3 | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:3002](ec2-54-153-95-230.us-west-1.compute.amazonaws.com:3002/documentation)|
| central-directory | 3000 | v0.21.3 | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:3000](ec2-54-153-95-230.us-west-1.compute.amazonaws.com:3000/documentation)|
| central-end-user-registry | 3001 | v0.9.0 | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:3001](ec2-54-153-95-230.us-west-1.compute.amazonaws.com:3001/documentation)|
| central-fraud-sharing | 3003 | v1.5.0 | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:3001](ec2-54-153-95-230.us-west-1.compute.amazonaws.com:3001/documentation)|
| central-hub | 4001 | v0.5.0 | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:3002](ec2-54-153-95-230.us-west-1.compute.amazonaws.com:3002/documentation)|


## Test Users
### DFSP1    
* bob    http://ec2-35-163-231-111.us-west-1.compute.amazonaws.com:3043/v1/receivers/88925537
* alice    http://ec2-35-163-231-111.us-west-1.compute.amazonaws.com:3043/v1/receivers/54200545
* merchant    http://ec2-35-163-231-111.us-west-1.compute.amazonaws.com:3043/v1/receivers/31909641
* dfsp1-testconnector    http://ec2-35-163-231-111.us-west-1.compute.amazonaws.com:3043/v1/receivers/91959846

### DFSP2    
* bob    http://ec2-35-163-249-3.us-west-1.compute.amazonaws.com:3043/v1/receivers/97181061
* alice    http://ec2-35-163-249-3.us-west-1.compute.amazonaws.com:3043/v1/receivers/52602716
* merchant    http://ec2-35-163-249-3.us-west-1.compute.amazonaws.com:3043/v1/receivers/63858707
* dfsp2-testconnector    http://ec2-35-163-249-3.us-west-1.compute.amazonaws.com:3043/v1/receivers/76838531

## Start/Stop scripts

#### Mule
* /home/ec2-user/scripts/modusbox/stop_mule.sh
* /home/ec2-user/scripts/modusbox/start_mule.sh

#### DFSP
* See [DFSP deployment process](https://github.com/LevelOneProject/Docs/tree/master/DFSP/dfspDeploymentProcess)

#### ILP/SPSP
* See [Ansible docs for ILP](https://github.com/LevelOneProject/Docs/blob/master/ILP/README.md)

#### IST
* <TO BE FILLED>
