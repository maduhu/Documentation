## Customer Demo Environment - AWS EC2 Instance Information
The Customer Demo environment is used for Client demos. It is more stable than the Test and QA environments and will be a few versions older. 

| Property | DFSP1 | DFSP2 |
| ----     | ----- | ----- |
| Instance Name | demo-dfsp1 | demo-dfsp2 |
| Instance ID | i-0be4f45016bc2288d | i-0be4f45016bc2288d |
| Public DNS | ec2-52-8-68-18.us-west-1.compute.amazonaws.com | ec2-54-153-95-230.us-west-1.compute.amazonaws.com |
| Public IP | 52.8.68.18 | 54.153.95.230 |
| Instance type | m4.large | m4.large |
| Private DNS | ip-172-31-10-105.us-west-1.compute.internal | ip-172-31-2-238.us-west-1.compute.internal |
| Private IPs | 172.31.10.105 | 172.31.2.238 |

### SSH

#### Mac  
* for dfsp1 - `ssh -i "interop-dev2.pem" ec2-user@ec2-52-8-68-18.us-west-1.compute.amazonaws.com`
* for dfsp2 - `ssh -i "interop-dev2.pem" ec2-user@ec2-54-153-95-230.us-west-1.compute.amazonaws.com`


#### Windows
 Use putty, and you will need to use the .ppk file linked above.  
 Under the `Connection/SSH/Auth` section you will need to select this .ppk file for `Private key file for authentication`


## API Info

### DFSP - Software Group

| Service | Port | Version | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------| --------- | --------- |
| dfsp-account | 8009 | 0.9.14 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8009](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8009/documentation) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8009](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8009/documentation)  |
| dfsp-api | 8010 | 0.28.26 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8010](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8010/documentation) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8010](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8010/documentation)  |
| dfsp-directory | 8011 | 0.7.14 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8011](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8011/documentation) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8011](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8011/documentation)  |
| dfsp-identity | 8012 | 0.9.13 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8012](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8012/documentation) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8012](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8012/documentation)  |
| dfsp-ledger | 8014 | 1.1.18 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8014](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8014/documentation) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8014](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8014/documentation)  |
| dfsp-rule | 8016 | 0.5.11 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8016](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8016/documentation) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8016](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8016/documentation)  |
| dfsp-subscription | 8017 | 0.3.16 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8017](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8017/documentation) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8017](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8017/documentation)  |
| dfsp-transfer | 8018 | 0.20.13 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8018](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8018/documentation) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8018](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8018/documentation)  |
| dfsp-ussd | 8019 | 0.23.21 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8019](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8019/documentation) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8019](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8019/documentation)  |
| dfsp-admin | 8020 | 0.15.8 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8020](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8020/documentation) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8020](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8020/documentation)  |

### Mule Proxy  - Modusbox

| Service | Port | Version | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------| --------- | --------- |
| interop-directory-gateway | 8088 | 0.4.23 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/directory/v1](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/directory/v1/open-api/) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8088/directory/v1](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8088/directory/v1/open-api/) |
| interop-spsp-client-proxy | 8088 | 0.4.14 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/spsp/client/v1/open-api) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8088/spsp/client/v1/open-api) |
| interop-ledger-adapter | 8088 | 0.1.4 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/ledger](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/ilp/ledger/v1/open-api) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8088/ledger](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8088/ilp/ledger/v1/open-api) |
| interop-spsp-backend | 8088 | 0.1.14 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/spsp/backend/v1](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/spsp/backend/v1/open-api) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8088/spsp/backend/v1](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8088/spsp/backend/v1/open-api) |
| interop-scheme-adapter | 8088 | 0.1.1 | [http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/scheme/adapter/v1](http://ec2-52-8-68-18.us-west-1.compute.amazonaws.com:8088/scheme/adapter/v1/open-api) | [http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8088/scheme/adapter/v1](http://ec2-54-153-95-230.us-west-1.compute.amazonaws.com:8088/scheme/adapter/v1/open-api) |

### ILP - Ripple

| Service | Port | Version | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------| --------- | --------- |
| ilp-spsp-client-rest | 3042 | v5.0.4-5-g99e3654 |         |           |
| ilp-spsp-server | 3043 | v5.0.3-2-g3e2614d |         |           |
| ilp-connector | 3044 | v19.0.0-3-gec73934 |  |  |

### IST - Dwolla

| Service | Port | Version | URL |
| ------- | -----| --------| ----|
| central-ledger |  | v1.81.0 | *UPDATE* [http://central-ledger-1139971789.us-west-1.elb.amazonaws.com/](http://central-ledger-1139971789.us-west-1.elb.amazonaws.com/documentation)|
| central-directory |  | v0.14.0 | *UPDATE* [ http://central-directory-214462011.us-west-1.elb.amazonaws.com/]( http://central-directory-214462011.us-west-1.elb.amazonaws.com/documentation) |
| end-user-registry |  | v0.9.0 | *UPDATE* [http://central-end-user-registry-1833170602.us-west-1.elb.amazonaws.com/](http://central-end-user-registry-1833170602.us-west-1.elb.amazonaws.com/documentation)|


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
