# Apigee Edge - External Message Logging with ELK stack

![Apigee Edge + ELK](docs/images/apigee-elk.png)
## About
This API proxy demonstrates message logging to ELK (Elastic, Logstash, Kibana) stack.

Those eager to try hands on -  Go to  [How to use It](#how-to-use-it)

Apigee supports message logging to external systems like Loggly etc; Though sending logs to Loggly is much-much simpler, it took me quite some time and efforts to send logs, specially JSON to ELK stack, the policy configuration of this apiproxy at Apigee Edge side is much easy, however parsing the logs in JSON format was the difficult task.  

The objective of creating this repository to help Apigee developers to QuickStart with logging with ELK stack.   

## Out of scope
 - Installation and configuration ELK stack, for demo purpose I followed this [link](https://logz.io/blog/elk-mac/) - installed on my local Mac.
 - If you installed and test ELK on your local, you will have to make Logstash IP publicly available so Apigee edge can connect and send the message; 
>Having said, you are free to setup and install ELK stack, use [`ngrok`](https://ngrok.com/) to publish locally installed Logstash to public IP/port. I used [`ngrok`](https://ngrok.com/) for that, which is really a great! You can install `ngrok` and tunnel your localhost to make it public accessible URL; I ran below command to do that, where `5044` is my local Logstash port.
 ```bash
 ./ngrok tcp 5044 --region eu
 ```
 >DO NOT FORGET to attach [Logstash configuration file](../master/logstash-sample.conf) to ensure correct parsing of the JSON coming from Apigee edge. 

## Prerequisite
- Apigee edge account
- Logstash IP and Port.
-- Make sure, there's connectivity between Apigee Edge and Logstash IP/Port. 
- Most important one, use Logstash configuration attached to this repo for parsing the log properly of you wish to have JSON parsed properly. 

## Recommended
- postman (to run postman collection)
- NodeJS (for newman and apigeetool)
- newman (node JS module to execute tests on local machine)
- apigeetool (deploying this proxy to Apigee edge)

# How to use it

## 1. Deploy API proxy to Apigee Edge
You can use either of the option mentioned below;
#### option 1: using `apigeetool`
You can deploy this API proxy by calling this command.

```bash
apigeetool deployproxy  -u {apigee_edge_account_email} -o {apigee_edge_org_name}  -e {environment_name} -n {proxy_name} -d . --verbose --debug
```
example; 
```bash
apigeetool deployproxy  -u kuldeep.bhati@devoteam.com -o abccorp-nonprod  -e test -n hello-world-elk-logging -d . --verbose --debug
```
For more information about `apigeetool` read here - https://www.npmjs.com/package/apigeetool

#### option 2: uploading a zip to apigee edge

1. Create a bundle zip file with below command;
```bash 
zip -r apiproxy.zip apiproxy
```

2. Upload it to apigee edge using proxy creation wizard.
Make sure it is deployed before testing.

## 2. Change IP/PORT of Logstash 
Once the API proxy is deployed, go to the message logging policy (setMessageLogging.xml) and add Logstash IP and Port as described in below code - https://gist.github.com/bhatikuldeep/d9fef45aefd9141dbfb3dba2bfc1c86d


## Unit Test (Optional)
Set the `proxy_endpoint` in tests/test.postman_environment.json file before executing the command below.

You can test this API proxy by calling this command.
```bash
newman run "tests/apiproxy.postman_collection.json" -e "tests/test.postman_environment.json"
```

## Issues, questions and feedback
https://github.com/bhatikuldeep/hello-world-elk-logging/issues

## Some Tips
- IMHO, attached message logging policy - [setMessageLogging.xml](../master/apiproxy/policies/setMessageLogging.xml) captures almost all necessary variables, please add/remove wherver necessary. Just make sure that JSON that you out is with whitespace removed to properly parse JSON at logstash side. I use http://jsonviewer.stack.hu/ to format JSON, validate JSON and remove white space. 


