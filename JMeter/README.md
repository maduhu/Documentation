Contains an initial attempt at providing performance and functional tests for OSS assets.

### Setup

You will need [Apache JMeter 3.X](http://jmeter.apache.org/download_jmeter.cgi) to load these files.

- Download the appropriate archive for your operating system and expand it.
- Download [jmeter-plugin-manager](https://jmeter-plugins.org/get/) and place it in the `lib/ext` folder under the path you extracted jmeter
- Navigate to the `/bin` folder under the path you extracted jmeter
- Start JMeter by executing `jmeter.bat` or `jmeter.sh` depending on your os
- Navigate to Options Menu -> Plugin Manager -> Available Plugins (middle tab)
- Select `JMeter Plugins JSON` and then click `Apply Changes and Restart JMeter`

### Test Configuration

The test is driven by several components
- environment configuration
- test case data
- desired throughput
- number of concurrent clients

Environment Configuration:

User variables required to configure the test are located in the `User Paramerters` element as shown below

![User Parameters](https://github.com/LevelOneProject/Docs/blob/master/JMeter/media/user_params.jpg "User Params")

In this section you should configure the following attributes:
- ledgerHost - the host name the test should connect ro
- ledgerPort - the port
- expiresAt - what data for expresAt should be included.  Some date in the future.
- ledgerUrl - by default its being built by host and port but you can change it here.  these goes inside some of the request messages
- authorization - the value to be included in the Authorization header element.  This value is correct for admin / foo.  This is a temporary hack.

 
