### General

Log into our jfrog artifactory instance here:

`https://modusbox.jfrog.io/modusbox/webapp/#/login`

* `level1project - admin account capable of creating or deleting repositories`
* `level1project_circleci - read/write account for use by deployment scripts and circleci`

JFrog Artifactory supports many different repository types.  Each repository is capable of storing many artifacts.  It can also mirror other remote repositories.  Right now we are using the following repository types:
* maven
* npm
* docker

It also supports the concept of repository layouts.

Each repository should be capable of storing multiple artifacts and each of these should be versionable.  Given that it seems we would want a smaller number of repositories containing a class of artifact (like released code vs snapshot code) vs a repository per artifact.
