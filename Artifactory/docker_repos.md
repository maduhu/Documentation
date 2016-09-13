
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

