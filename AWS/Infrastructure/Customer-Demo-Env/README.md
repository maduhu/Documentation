## Customer Demo Environment - AWS EC2 Instance Information
The Customer Demo environment is used for Client demos. It is more stable than the Test and QA environments and will be a few versions older. 

| Property | DFSP1 | DFSP2 |
| ----     | ----- | ----- |
| Instance Name | integrate-dfsp1 | integrate-dfsp2 |
| Instance ID |  |  |
| Public DNS |  |  |
| Public IP |  |  |
| Instance type | m4.large | m4.large |
| Private DNS |  |  |
| Private IPs |  |  |

### SSH

#### Mac  
* for dfsp1 - `ssh -i "interop-dev1.pem" ec2-user@ec2-35-163-231-111.us-west-2.compute.amazonaws.com`
* for dfsp2 - `ssh -i "interop-dev1.pem" ec2-user@ec2-35-163-249-3.us-west-2.compute.amazonaws.com`


#### Windows
 Use putty, and you will need to use the .ppk file linked above.  
 Under the `Connection/SSH/Auth` section you will need to select this .ppk file for `Private key file for authentication`


## API Info

### DFSP - Software Group

| Service | Port | Version | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------- | --------- |
| dfsp-ledger | 8014 | |  |   |
| dfsp-invoices | 8010 | |  |   |


### Mule Proxy  - Modusbox

| Service | Port | Version   | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------- | --------- |
| interop-directory-gateway | 8088 |  |  |  |
| interop-spsp-client-proxy | 8088 |  |  |  |
| interop-ledger-adapter | 8088 |  |  | |
| interop-spsp-backend | 8088 |  |  |  |

### ILP - Ripple

| Service | Port | Version | DFSP1 URL | DFSP2 URL |
| ------- | -----| --------- | --------- |
| ilp-spsp-client-rest | 3042 |  |         |           |
| ilp-spsp-server | 3043 |  |         |           |
| ilp-connector | 3044 | |  |  |

### IST - Dwolla

| Service | Port | Version | URL |
| ------- | -----| |-------| ----|
| central-ledger |  | | |
| central-directory |  | | |
| end-user-registry |  | | |


## Test Users
### DFSP1    
* bob    http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:3043/v1/receivers/88925537
* alice    http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:3043/v1/receivers/54200545
* merchant    http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:3043/v1/receivers/31909641
* dfsp1-testconnector    http://ec2-35-163-231-111.us-west-2.compute.amazonaws.com:3043/v1/receivers/91959846

### DFSP2    
* bob    http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:3043/v1/receivers/97181061
* alice    http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:3043/v1/receivers/52602716
* merchant    http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:3043/v1/receivers/63858707
* dfsp2-testconnector    http://ec2-35-163-249-3.us-west-2.compute.amazonaws.com:3043/v1/receivers/76838531

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
