### General

In order for your npm command line client to work with Artifactory you will firstly need to set the relevant authentication. For getting authentication details run the following command:

`curl -ulevel1project_circleci:AP83xNgxyEV2vih7R4g38FTeuX6 https://modusbox.jfrog.io/modusbox/api/npm/auth`

The response should be pasted in the ~/.npmrc (in Windows %USERPROFILE%/.npmrc) file. Here is an example of the content of the file:

```
_auth = bGV2ZWwxcHJvamVjdF9jaXJjbGVjaTpBUDgzeE5neHlFVjJ2aWg3UjRnMzhGVGV1WDY=
email = billh@crosslaketech.com
always-auth = true
```

Artifactory also support scoped packages. For getting authentication details run the following command:

```
curl -ulevel1project_circleci:AP83xNgxyEV2vih7R4g38FTeuX6 "https://modusbox.jfrog.io/modusbox/api/npm/level1-npm-release/auth/<SCOPE>"
```

The response should be pasted in the ~/.npmrc (in Windows %USERPROFILE%/.npmrc) file. Here is an example of the content of the file:

```
@<SCOPE>:registry=https://modusbox.jfrog.io/modusbox/api/npm/level1-npm-release/
/modusbox.jfrog.io/api/npm/level1-npm-release/:_password=QVA4M3hOZ3h5RVYydmloN1I0ZzM4RlRldVg2
/modusbox.jfrog.io/api/npm/level1-npm-release/:username=level1project_circleci
/modusbox.jfrog.io/api/npm/level1-npm-release/:email=billh@crosslaketech.com
/modusbox.jfrog.io/api/npm/level1-npm-release/:always-auth=true
```

Run the following command to replace the default npm registry with an Artifactory repository:

`npm config set registry https://modusbox.jfrog.io/modusbox/api/npm/level1-npm-release`

For scoped package run the following command:

`npm config set @<SCOPE>:registry https://modusbox.jfrog.io/modusbox/api/npm/level1-npm-release`
