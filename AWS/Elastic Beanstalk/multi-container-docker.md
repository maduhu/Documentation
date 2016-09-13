
Some thoughtful discussion about all the magic that needs to happen up to this point should go here ;)

# Configuration

Create a Dockerrun.aws.json file.  For a full list of possible options in this file see [Task Definition Parameters](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html)

Also note that any environment variables you may have set in your Dockerfile when you created your docker image will not be available when you run your image this way.  You will need to set those parameters in the environment section.  You can externalize these values and manage them in your elastic beanstalk instance.

Example configuration launching a single interop-dfsp-services docker instance stored in an AWS docker repository.
```json
{
  "AWSEBDockerrunVersion": 2,
  "volumes": [
    {
      "name": "mule-logs",
      "host": {
        "sourcePath": "/var/log/mule-dfsp-interop-services/logs"
      }
    }
  ],
  "containerDefinitions": [
    {
      "name": "mule-dfsp-interop-services",
      "image": "886403637725.dkr.ecr.us-west-2.amazonaws.com/interop",
      "environment": [
        {
          "name": "MULE_ENV",
          "value": "dev"
        },
        {
          "name": "MULE_HOME",
          "value": "/opt/mule"
        },
        {
          "name": "JMX_PORT",
          "value": "8999"
        },
        {
          "name": "MAX_JVM_MEMORY",
          "value": "1024"
        }
      ],
      "essential": true,
      "memory": 2048,
      "portMappings": [
        {
          "hostPort": 8081,
          "containerPort": 8081
        }
      ],
      "mountPoints": [
        {
          "sourceVolume": "mule-logs",
          "containerPath": "/opt/mule/logs"
        }
      ]
    }
  ]
}
```

