![Physical Architecture](./L1P%20diagram.png)

| Instance Name | Size | Region | Elastic IP | Public DNS | Private DNS |	Private IP |
| ------------- | ---- | ------ | ---------- | ---------- | ----------- | ---------- |
| integrate-dfsp2 | m4.large | us-west-2b | 35.163.249.3 | ec2-35-163-249-3.us-west-2.compute.amazonaws.com | ip-172-31-22-112.us-west-2.compute.internal | 172.31.22.112 |
| integrate-dfsp1 | m4.large | us-west-2b | 35.163.231.111 | ec2-35-163-231-111.us-west-2.compute.amazonaws.com | ip-172-31-23-143.us-west-2.compute.internal | 172.31.23.143 |
| kafka, cassandra, 24g | m4.large | us-west-2c | 52.26.168.223 | ec2-52-26-168-223.us-west-2.compute.amazonaws.com | ip-172-31-4-114.us-west-2.compute.internal | 172.31.4.114 |
| central-directory-dev | m3.medium | us-west-2b | 52.42.75.16 | ec2-52-39-223-230.us-west-2.compute.amazonaws.com | ip-172-31-15-38.us-west-2.compute.internal | 172.31.15.38 |
| central-ledger-dev | m3.medium | us-west-2c | 52.39.223.230 | ec2-52-42-75-16.us-west-2.compute.amazonaws.com | ip-172-31-31-12.us-west-2.compute.internal | 172.31.31.12 |
| central-end-user-registry-dev | m3.medium | us-west-1a | 52.8.194.34 | ec2-52-8-194-34.us-west-1.compute.amazonaws.com | ip-172-31-14-82.us-west-1.compute.internal | 172.31.14.82 |
