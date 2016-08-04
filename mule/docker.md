# Mule's docker image
The docker image is based on Java's official docker image. 

We decided to incorporate the application into the image, that is why we need to change the dockerfile in order to do a COPY command to copy the zip file into mule's applications folder: "/opt/mule/apps"

Mule folders for domains, confs and logs are volumes so those can be mapped to host directories. 
### Building it

#### Parametes
There are two different parameters to build it, one is the version and the other is the port that exposes. In case a parameter is missing  then the defaults are 3.8.0 for version and 8081 for ports.
The versions that are supported are: 3.8.0, 3.7.0, 3.6.1, 3.6.0 and 3.5.0

#### Command

In order to build it the following command is used replacing the <<>> with the actual values:

```docker build -t <<userName>>/<<imageName>>:<<tagname>> -f <<dockerFileName>> <<dockerBuildPath>>```

username is optional and is needed in case the image wants to be pushed.
If dockerfile is named "Dockerfile" then there is no need to specify the -f <<dockerFileName>> argument.
Mule's application zip should be saved into dockerBuildPath .

#### Final output
After you run this command you end up having a mule image that is ready to be instantiated into a container.

### Running a container from the image you created

In order to run a container you should use the following command:

```docker run -d -p 8081:8081 --name <<containerName>>```

If you want to get into the container you can use the following command 

```docker exec -ti <<containerName>> bash```
