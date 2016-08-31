# Including a new project in CircleCI #

## Create circle.yml file ##

CircleCI uses a file called “circle.yml” in every project in order to know how to work with it. Add a new file into the root folder of the project and name it "circle.yml", then copy the following content into it.

```yml
---
checkout:
  pre:
    - curl -H 'Authorization:token 2966049a0cf5e2723cd9728031bc8e10607e8b1a' -H 'Accept:application/vnd.github.v3.raw' -o /tmp/interop_maven_settings.xml -L https://raw.githubusercontent.com/LevelOneProject/automation/master/interop/interop_maven_settings.xml?token=AC_Z3FCGCSuxKBPvF5oDxwDqb71xSIC6ks5XzjH7wA%3D%3D

dependencies:
  override:
    - "mvn dependency:resolve -s /tmp/interop_maven_settings.xml"

test:
  override:
    - "mvn integration-test -s /tmp/interop_maven_settings.xml"
  post:
    - mkdir -p $CIRCLE_TEST_REPORTS/junit/
    - find . -type f -regex ".*/target/surefire-reports/.*xml" -exec cp {} $CIRCLE_TEST_REPORTS/junit/ \;
```


## What is this circle.yml file doing? ##


Before the code is pulled from github, a settings file for maven gets downloaded.
After that, the dependencies are retrieved using maven
Since CircleCI tries to autodetect the type of project and what to do with it, both the dependencies and test section is overrided, the only difference with the command that it would execute on its own is that we are specifying a maven settings file. 
After the tests run, a new folder is created in which the test results are copied, that is the way CIRCLECI knows how many tests were run and which were the results.



## Configure github and Jfrog account ##

### Configuring the project in CircleCI ###

Login into CircleCI and go to https://circleci.com/dashboard. There is a menu on your left, click on the third option ("Add Projects")

![AddProjects](AddProjects.png "AddProjects")

In the following screen you should choose the project you want to add from the list, clicking on the button on its right. In case there is no button on the project and you get a "Contact repo admin" message, you need to ask for administrative privileges in order to continue. 


### Setting the environment variables ###


The maven settings file includes credentials that needs to be set as environment variables. The following is a code snippet that shows how the settings file is configured:

```xml
<servers>
  <server>
     <id>git</id>  
     <username>${env.GIT_USERNAME}</username>  
     <password>${env.GIT_PASSWORD}</password>  
  </server> 
  <server>
    <username>${env.JFROG_USERNAME}</username>
    <password>${env.JFROG_PASSWORD}</password>
    <id>modusbox-release-local</id>
  </server>
  <server>
    <username>${env.JFROG_USERNAME}</username>
    <password>${env.JFROG_PASSWORD}</password>
    <id>modusbox-snapshot-local</id>
  </server>
</servers>
```


In order to add the variables go to the settings page of the project by clicking on the gear button next to the project title.

![ProjectSettings](ProjectSettings.png "Project Settings")

On the left side of the screen there is a submenu, click on the menu item called "Environment Variables" which is below the title "Build Settings". Once in there, you need to add the variables: JFROG_USERNAME and JFROG_PASSWORD. In case you dont know the right values please contact someone from modusbox team.

![EnvironmentVariables](EnvironmentVariables.png "Environment variables")

