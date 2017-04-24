# Content

- [System Requirements](#system-requirements)
- [Installation and Configuration](#installation-and-configuration)
- [Usage](#usage)
- [Additional Info](#additional-info)

---


## System Requirements
1. Ansible host - UN*X operating system
2. AWS credentials
3. *sudo* or *su* access 
 
>**Note:** SG Ansible host machine is Ubuntu 16.04 LTS

## Installation and Configuration

1. Install Ansible.

   * With package manager  
   
    ```bash  
    apt-get install ansible 
    ```
 
   * With python <span style="color:blue">*pip*</span> installer  
    ```bash
    pip install ansible
    
    ```
2. Install and configure  AWS Command Line Interface.

    2.1.  Installation
         
    * With OS package manager (using Ubuntu):
    ```bash
    sudo apt-get install awscli
    ```
    * With python <span style="color:blue">*pip*</span> installer:
    ```bash
    pip install awscli
    ```
    2.2. Configuration
    * Using <span style="color:blue">*aws*</span> cli command:
    ```bash
    georgi:~ $ aws configure
    AWS Access Key ID [None]: AWS_ID
    AWS Secret Access Key [None]: AWS_KEY
    Default region name [None]: us-west-2
    Default output format [None]: json
    georgi:~ $
    ```
    
    * Manually:
    ```bash
    mkdir ~/.aws
    echo "[default]\naws_access_key_id = <AWS_KEY_ID>\naws_secret_access_key = <AWS_SECRET_KEY>" >~/.aws/credentials
    echo "[default]\noutput = json\nregion = us-west-2">~/.aws/config
    ```
3. Prepare SG Deployment Tool.

    3.1. Download and unpack the dfspDeploymentProcess archive.
    
    3.2. Download and copy *interop-dev1.pem* to ~/.ssh.
    ```bash
    cp ~/path_to_interop-dev1.pem ~/.ssh/
    chmod 600 ~/.ssh/interop-dev1.pem
    ```
    3.3. Modify the *ansible.cfg* file:
    ```bash
    ....
    private_key_file = /home/YOUR_USER/.ssh/interop-dev1.pem
    ...
    ```
## Usage
* Install/Upgrade SG services on the *dfsp1-test* cluster:

    ```bash
    ansible-playbook -i amazon.yml dev.yml --limit=dfsp1-test
     ```
* Install/Upgrade SG services on *dfspX-test* clusters:

    ```bash
    ansible-playbook -i amazon.yml dev.yml --limit=dfsp-test
     ```
* Install/Upgrade SG services on *dfsp1* clusters:

    ```bash
    ansible-playbook -i amazon.yml dev.yml --limit=dfsp1
     ```
* Install/Upgrade all dfsp clusters:

    ```bash
    ansible-playbook -i amazon.yml dev.yml
     ```
* Install/Upgrade a specific version of an SG service on dfsp1-test cluster:

    ```bash
    ansible-playbook -i amazon.yml dev.yml --limit=dfsp1-test  -t dfsp-admin -e "admin_version=0.10.7-master"
     ```
* Install/Upgrade a specific SG service with a specific version, recreate Postgres and restart all services:

    ```bash
    ansible-playbook -i amazon.yml dev.yml --limit=dfsp1-test  -t "postgres,dfsp-admin" -e "admin_version=0.10.7-master"
     ```
* Restart SG services on *dfsp1-test* cluster:

    ```bash
    ansible-playbook -i amazon.yml dev.yml -t restart --limit=dfsp1-test
     ```
* Recreate Postgres and restart all SG services on *dfsp1-test* cluster:

    ```bash
    ansible-playbook -i amazon.yml dev.yml -t postgres --limit=dfsp1-test
     ```
       

## Addition Info
1. Current workflow:
* Get AWS password from ansible host using *aws ecr get-login* command.
* Regenerate *.docker/config.json* on the target machine via ansible module: docker_login.
* Create SG network and install *docker-py* dependency.
* Stop the specific service.
* Upgrade docker images.
* Recreate the docker container.
* Start the docker container.
* Validate the docker service url.
2. Show all available tags.
```bash
georgi:dfspDeploymentProcess [master]$ ansible-playbook -i amazon.yml dev.yml --list-tags 

playbook: dev.yml

  play #1 (dfsp-all): Get AWS PASS from Ansible host and prepare target machines	TAGS: [always]
      TASK TAGS: [always]

  play #2 (dfsp-all): Stop SG Services	TAGS: []
      TASK TAGS: [dfsp-account, dfsp-admin, dfsp-api, dfsp-directory, dfsp-identity, dfsp-ledger, dfsp-rule, dfsp-subscription, dfsp-transfer, dfsp-ussd, postgres, restart]

  play #3 (dfsp-all): Deploy SG Services	TAGS: []
      TASK TAGS: [dfsp-account, dfsp-admin, dfsp-api, dfsp-directory, dfsp-identity, dfsp-ledger, dfsp-rule, dfsp-subscription, dfsp-transfer, dfsp-ussd, postgres]

  play #4 (dfsp-all): Start SG Services	TAGS: []
      TASK TAGS: [dfsp-account, dfsp-admin, dfsp-api, dfsp-directory, dfsp-identity, dfsp-ledger, dfsp-rule, dfsp-subscription, dfsp-transfer, dfsp-ussd, postgres, restart]
georgi:dfspDeploymentProcess [master]$ 
```
3. Show all available hosts.
```bash
georgi:dfspDeploymentProcess [master]$ ansible-playbook -i amazon.yml dev.yml --list-hosts

playbook: dev.yml

  play #1 (dfsp-all): Get AWS PASS from Ansible host and prepare target machines	TAGS: [always]
    pattern: [u'dfsp-all']
    hosts (4):
      ec2-35-166-236-69.us-west-2.compute.amazonaws.com
      ec2-35-163-231-111.us-west-2.compute.amazonaws.com
      ec2-35-163-249-3.us-west-2.compute.amazonaws.com
      ec2-52-32-130-4.us-west-2.compute.amazonaws.com

  play #2 (dfsp-all): Stop SG Services	TAGS: []
    pattern: [u'dfsp-all']
    hosts (4):
      ec2-35-166-236-69.us-west-2.compute.amazonaws.com
      ec2-35-163-231-111.us-west-2.compute.amazonaws.com
      ec2-35-163-249-3.us-west-2.compute.amazonaws.com
      ec2-52-32-130-4.us-west-2.compute.amazonaws.com

  play #3 (dfsp-all): Deploy SG Services	TAGS: []
    pattern: [u'dfsp-all']
    hosts (4):
      ec2-35-166-236-69.us-west-2.compute.amazonaws.com
      ec2-35-163-231-111.us-west-2.compute.amazonaws.com
      ec2-35-163-249-3.us-west-2.compute.amazonaws.com
      ec2-52-32-130-4.us-west-2.compute.amazonaws.com

  play #4 (dfsp-all): Start SG Services	TAGS: []
    pattern: [u'dfsp-all']
    hosts (4):
      ec2-35-166-236-69.us-west-2.compute.amazonaws.com
      ec2-35-163-231-111.us-west-2.compute.amazonaws.com
      ec2-35-163-249-3.us-west-2.compute.amazonaws.com
      ec2-52-32-130-4.us-west-2.compute.amazonaws.com
georgi:dfspDeploymentProcess [master]$ 
```
4. Links:
* [Official Ansible Site](https://www.ansible.com/)
* [Ansible Docker Modules](https://docs.ansible.com/ansible/list_of_cloud_modules.html#docker)
* [Ansible Filters](http://docs.ansible.com/ansible/playbooks_filters.html)
