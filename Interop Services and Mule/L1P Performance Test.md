## Performance Tests ## 

### Introduction

This document provides the details about the performance test tools and steps required to setup an environment to execute the performance test and report the Metrics.

#### Related GitHub Issues

* https://github.com/LevelOneProject/Interop-Issues/issues/186
* https://github.com/LevelOneProject/Interop-Issues/issues/187

#### Applications for performance test

A New AWS EC2 Instances will be created for the performance tests. As an Initial step, identify all the required application with its corresponding baseline versions for the performance tests and deploy on the new AWS EC2 environment.

#### Applications

* DFSP - Version ?
* Interop - Version ?
* ILP - Version ?
* IST - Version ?

The List of applications/Services required for performing the performance tests can be referenced under the following link

QA - https://github.com/LevelOneProject/Docs/tree/master/AWS/Infrastructure/PI4-QA-Env
TEST - https://github.com/LevelOneProject/Docs/tree/master/AWS/Infrastructure/PI4-Test-Env

#### Support Applications

ELK - For Logs and Tracing the above applications
Metrics Dashboard 

Configure the new perf test Environment to pull the logs for both ELK and for the metrics dashboards

## Tools for performance testing

#### Test Tools

JMeter will be used to execute the End to End performance tests for all the L1P Applications/Services

More Details about configuring JMeter, Functional and performance test cases can be accessed on the below link

https://github.com/LevelOneProject/Docs/tree/master/JMeter

## Test Scenarios

  ### Scenario 1 - Create Users
    * Create an User in DFSP1
    * Create an User in DFSP2

  ### Scenario 2 - Send Money
    * Check the balance for DFSP1 user and DFSP2 User before transfer
    * Send Money from DFSP1 to DFSP 2
    * Check the balance for DFSP1 user and DFSP2 User after transfer

  ### Scenario 3 - Send Invoice
    * Check the balance for DFSP1 user and DFSP2 User before transfer
    * Send Invoice from DFSP1 to DFSP 2
    * DFSP 2 User approve the Invoice
    * Check the balance for DFSP1 user and DFSP2 User before transfer

  ### Scenario 4 - Bulk Payment?

  ### Scenario 5 - Interop - ILP Ledger
    * Create Accounts
    * Get Accounts
    * Post Transfers
    * Get transfers

## Execution Plan:

  ### Iteration 1:
         No of Threads :
         Interval :

 ### Iteration 2:
        No of Threads :
         Interval :

## Application Logs

All logs related to Level One Project applications can be accessed in Kibana

URL : http://35.167.132.14/ (kibanaadmin/l1p)

## Metrics Report

Metrics reports for the executed performance tests can be accessed thru the below link

http://ec2-35-166-189-14.us-west-2.compute.amazonaws.com:9000/

More Details on how the metrics dashboards are created can be accessed from the below link

https://github.com/LevelOneProject/interop-metrics-ui/blob/master/available-metrics.md
