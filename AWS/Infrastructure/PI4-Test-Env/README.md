## __DFSP1__

| Property | Description |
| ---- | ----------- |
| Instance Name | integrate-test-dfsp1 |
| Instance ID | i-03e931e01532a6287 |
| Public DNS | ec2-35-166-189-14.us-west-2.compute.amazonaws.com |
| Public IP | 35.166.189.14 |
| Instance type | m4.large |
| Private DNS | ip-172-31-35-189.us-west-2.compute.internal |
| Private IPs | 172.31.35.189 |

### SSH

on a mac:  `ssh -i "interop-dev1.pem" ec2-user@ec2-35-166-189-14.us-west-2.compute.amazonaws.com`

on windows:  use putty, and you will need to use the .ppk file linked above.  under the `Connection/SSH/Auth` section you will need to select this .ppk file for `Private key file for authentication`

## __DFSP2__

| Property | Description |
| ---- | ----------- |
| Instance Name | integrate-test-dfsp2 |
| Instance ID | i-0193fad0b1cd06e7b |
| Public DNS | ec2-35-166-236-69.us-west-2.compute.amazonaws.com |
| Public IP | 35.166.236.69 |
| Instance type | m4.large |
| Private DNS | ip-172-31-45-167.us-west-2.compute.internal |
| Private IPs | 172.31.45.167 |

### SSH

on a mac:  `ssh -i "interop-dev1.pem" ec2-user@ec2-35-166-236-69.us-west-2.compute.amazonaws.com`

on windows:  Same as above except for hostname

### Service Info DFSP1

| Service | Port | Full Service URL |
| ------- | ---- | ----------- |
| dfsp1-interop-directory | 8088 |  [http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/directory/v1](http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/directory/v1/console/) |
| dfsp1-interop-spsp-client-proxy | 8088 |  [http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/console) |
| dfsp1-ilp-spsp-client-rest | 3042 | http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:3042 |
| dfsp1-ilp-spsp-server | 3043 | http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:3043 |
| dfsp1-interop-ledger-adapter | 8088 |  http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/ledger |
| dfsp1-ledger | 8014 | http://dfsp1:8014/ledger |
| dfsp1-ilp-connector | 3044 | http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:3044 |
| dfsp1-interop-spsp-backend | 8088 |  [http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1](http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1/console) |
|     |     |     |
| ist-ledger | 3075 | http://dfsp1:3075 |


### Service Info DFSP2

| Service | Port | Full Service URL |
| ------- | ---- | ----------- |
| dfsp2-interop-directory | 8088 |  [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/directory/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/directory/v1/console) |
| dfsp2-interop-spsp-client-proxy | 8088 |  [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/client/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/client/v1/console) |
| dfsp2-ilp-spsp-client-rest | 3042 | http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3042 |
| dfsp2-ilp-spsp-server | 3043 | http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3043 |
| dfsp2-interop-ledger-adapter | 8088 |  http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/ledger |
| dfsp2-ledger | 8014 | http://dfsp1:8014/ledger |
| dfsp2-ilp-connector | 3044 | http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:3044 |
| dfsp2-interop-spsp-backend | 8088 |  [http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1](http://ec2-35-166-236-69.us-west-2.compute.amazonaws.com:8088/spsp/backend/v1/console) |
|     |     |     |
| ist-ledger | 3075 | http://dfsp1:3075 |
