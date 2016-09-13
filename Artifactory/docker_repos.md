
### Overview

The following instructions use the docker command line client with the `level1project_circleci` account against the `level1-docker-ilp` docker repository.  If you are executing these commands from a new AWS instance you will first need to install docker.  Otherwise skip to the General section below.

### Install Docker on AWS Instance

This only needs to be done once when a new instance is created.

Update the installed packages and package cache on your instance.

`[ec2-user ~]$ sudo yum update -y`

Install Docker.

`[ec2-user ~]$ sudo yum install -y docker`

Start the Docker service.

```
[ec2-user ~]$ sudo service docker start
Starting cgconfig service:                                 [  OK  ]
Starting docker:	                                   [  OK  ]
```

Add the ec2-user to the docker group so you can execute Docker commands without using sudo.

`[ec2-user ~]$ sudo usermod -a -G docker ec2-user`

Log out and log back in again to pick up the new docker group permissions.

Verify that the ec2-user can run Docker commands without sudo.

```
[ec2-user ~]$ docker info
Containers: 2
Images: 24
Storage Driver: devicemapper
 Pool Name: docker-202:1-263460-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 702.3 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 1.864 MB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.89-RHEL6 (2014-09-01)
Execution Driver: native-0.2
Kernel Version: 3.14.27-25.47.amzn1.x86_64
Operating System: Amazon Linux AMI 2014.09
```

### General

To login use the docker login command.

`docker login modusbox-level1-docker-ilp.jfrog.io`

And provide your Artifactory username and password or API key.
If anonymous access is enabled you do not need to login.
To manually set your credentials, or if you are using Docker v1, copy the following snippet to your ~/.docker/config.json file.
```json
{
	"auths": {
		"https://modusbox-level1-docker-ilp.jfrog.io" : {
			"auth": "<USERNAME>:<PASSWORD> (converted to base 64)",
			"email": "youremail@email.com"
		}
	}
}
```

To enter multiple registries see the [following example](https://www.jfrog.com/confluence/display/RTF/Using+Docker+V1#UsingDockerV1-3.SettingUpAuthentication).

### Deploy

To push an image tag an image using the docker tag and then docker push command.

```docker tag ubuntu modusbox-level1-docker-ilp.jfrog.io/<DOCKER_REPOSITORY>:<DOCKER_TAG>```

```docker push modusbox-level1-docker-ilp.jfrog.io/<DOCKER_REPOSITORY>:<DOCKER_TAG>```

### Resolve

To pull an image use the docker pull command specifying the docker image and tag.

```docker pull modusbox-level1-docker-ilp.jfrog.io/<DOCKER_REPOSITORY>:<DOCKER_TAG>```

