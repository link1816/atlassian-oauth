# JIRA Developer Documentation : JIRA REST API Example - OAuth authentication
This page shows you how to allow REST clients to authenticate themselves using OAuth. 
This is one of three methods that you can use for authentication against the JIRA REST API; 
the other two being basic authentication and cookie-based authentication (see related information).

# Overview
The instructions below describe how to use a Java client to provide OAuth authentication when making requests to JIRA's REST endpoints. It assumes you are familiar with the OAuth terminology (e.g. Consumer, Service Provider, request token, access token, etc.). For more information about OAuth refer to the OAuth specification.

***Looking for a Provider in a Language other than Java?***

Atlassian provides samples of OAuth providers in a number of other languages. Visit the sample repo on Bitbucket to download and work with these samples.

## Step 1: Configuring JIRA
The first step is to register a new consumer in JIRA. This is done through the Application Links administration screens in JIRA.
Create a new Application Link. When creating the Application Link use a placeholder URL or the correct URL to your client,
if your client can be reached via HTTP and choose the Generic Application type. After this Application Link has been created,
edit the configuration and go to the incoming authentication configuration screen and select OAuth. 
Enter in this the public key and the consumer key which your client will use when making requests to JIRA. 
After you have entered all the information click OK and ensure OAuth authentication is enabled.

## Step 2: Configuring the client
Your client will require the following information to be able to make authentication requests to JIRA.

application link settings
- request token url:JIRA_BASE_URL + "/plugins/servlet/oauth/request-token"
- authorization url : JIRA_BASE_URL + "/plugins/servlet/oauth/authorize""
- access token url : JIRA_BASE_URL + "/plugins/servlet/oauth/access-token"
- oauth signing type: RSA-SHA1
- consumer key: as configured in Step 1

## Example Java OAuth client
This example java code demonstrates how to write a client to make requests to JIRA's rest endpoints using OAuth authentication. To be able to use OAuth authentication the client application has to do the "OAuth dance" with JIRA. This dance consists of three parts.

+ Obtain a request token
+ Ask the user to authorize this request token
+ Swap the request token for an access token
After the client application has a valid access token, this can be used to make authenticated requests to JIRA.

## Before you begin
You'll need to configure JIRA and download the example client first. This example client uses the consumer key "hardcoded-consumer" and the public key is:

```
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAxZDzGUGk6rElyPm0iOua0lWg84nOlhQN1gmTFTIu5WFyQFHZF6OA4HX7xATttQZ6N21yKMakuNdRvEudyN/coUqe89r3Ae+rkEIn4tCxGpJWX205xVF3Cgsn8ICj6dLUFQPiWXouoZ7HG0sPKhCLXXOvUXmekivtyx4bxVFD9Zy4SQ7IHTx0V0pZYGc6r1gF0LqRmGVQDaQSbivigH4mlVwoAO9Tfccf+V00hYuSvntU+B1ZygMw2rAFLezJmnftTxPuehqWu9xS5NVsPsWgBL7LOi3oY8lhzOYjbMKDWM6zUtpOmWJA52cVJW6zwxCxE28/592IARxlJcq14tjwYwIDAQAB
```
You have to create an Application Link as described in Step 1 above and use this consumer key and the public key and leave the callback URL field empty.



Java Archive rest-oauth-client-1.0-sources.jar

Sep 27, 2011 by Felix Schmitz [Atlassian]

Labels

No labels
   + Edit Labels
   + Preview $itemLabel $itemLabel

Java Archive rest-oauth-client-1.0.one-jar.jar

Sep 27, 2011 by Felix Schmitz [Atlassian]

Labels

No labels
Edit Labels
Preview $itemLabel $itemLabel

Drag and drop to upload or browse for files 

Upload file

File description

Download All

The rest-oauth-client-1.0.one-jar.jar contains the sample client and the rest-oauth-client-1.0-sources.jar contains the source code.

If you are using JIRA 5.2 or earlier:** **The sample client uses HTTP POST to communicate with JIRA.  Support for OAuth parameters in the body of an HTTP POST was added in JIRA 6.0.  In order to run this sample client against a version of JIRA earlier than 6.0, the sample client (specifically com.atlassian.oauth.client.example.AtlassianOAuthClient) will need to be changed to use HTTP GET when communicating with JIRA.

### 1. Obtain a request token from JIRA
Execute this command:

```bash
java -jar rest-oauth-client-1.0.one-jar.jar requestToken JIRA_BASE_URL
```
Replace JIRA_BASE_URL with the URL to your JIRA instance. After executing this command you should see a response like


Token is W9QuE8hba6laXm2RcPGANVusKHnXUJcx
Token secret is rx4T2R3ax7an3H0vJLq9XB9DOP3aiNMl
Retrieved request token. go to http://localhost:8090/jira/plugins/servlet/oauth/authorize?oauth_token=W9QuE8hba6laXm2RcPGANVusKHnXUJcx
### 2. Authorize this token
Go to the URL in system out and login into JIRA and approve the access. Afterwards JIRA will say that you have successfully authorised the access. It mentions a verification code which we need for the next step.

### 3. Swap the request token with an access token
Execute the following command

```bash
java -jar rest-oauth-client-1.0.one-jar.jar accessToken JIRA_BASE_URL REQUEST_TOKEN TOKEN_SECRET VERIFIER
```
Replace JIRA_BASE_URL, REQUEST_TOKEN, TOKEN_SECRET and VERIFIER with the correct values.

In the response you should see


Access token is : QddAGsDSS0FkXCb1zRCCzzeShZRnUXYH
This access token will allow you to make authenticated requests to JIRA.

### 4. Make an authentication request to a rest-end point
To make an authenticated request to a rest resource in JIRA execute this command:

```bash
java -jar rest-oauth-client-1.0.one-jar.jar request ACCESS_TOKEN JIRA_REST_URL
```
Replace ACCESS_TOKEN, JIRA_REST_URL and ISSUE_KEY with the correct values. JIRA_REST_URL, e.g. http://localhost:8090/jira/rest/api/2/issue/HSP-1 This will return the issue JSON object for the issue with the key "HSP-1"

You should see a response like:

```json
{
"expand": "html,names,schema",
"id": "10000",
"self": "http://localhost:8090/jira/rest/api/2/issue/HSP-1",
"key": "HSP-1",
"fields": {
"summary": "Bug due two weeks ago",
"issuetype": {
"self": "http://localhost:8090/jira/rest/api/2/issuetype/1",
"id": "1",
"description": "A problem which impairs or prevents the functions of the product.",
"iconUrl": "http://localhost:8090/jira/images/icons/bug.gif",
"name": "Bug",
"subtask": false
},
"status": {
"self": "http://localhost:8090/jira/rest/api/2/status/5",
"iconUrl": "http://localhost:8090/jira/images/icons/status_resolved.gif",
"name": "Resolved",
"id": "5"
},
"labels": [(0)],
"votes": {
"self": "http://localhost:8090/jira/rest/api/2/issue/HSP-1/votes",
"votes": 0,
"hasVoted": false
},
"assignee": {
"self": "http://localhost:8090/jira/rest/api/2/user?username=admin",
"name": "admin",
"emailAddress": "admin@example.com",
"avatarUrls": {
"16x16": "http://localhost:8090/jira/secure/useravatar?size=small&avatarId=10062",
"48x48": "http://localhost:8090/jira/secure/useravatar?avatarId=10062"
},
"displayName": "Administrator",
"active": true
},
"resolution": {
"self": "http://localhost:8090/jira/rest/api/2/resolution/1",
"id": "1",
"description": "A fix for this issue is checked into the tree and tested.",
"name": "Fixed"
},
"fixVersions": [(0)],
"security": null,
"resolutiondate": "2011-09-26T15:44:39.220+1000",
"sub-tasks": [(0)],
"reporter": {
"self": "http://localhost:8090/jira/rest/api/2/user?username=admin",
"name": "admin",
"emailAddress": "admin@example.com",
"avatarUrls": {
"16x16": "http://localhost:8090/jira/secure/useravatar?size=small&avatarId=10062",
"48x48": "http://localhost:8090/jira/secure/useravatar?avatarId=10062"
},
"displayName": "Administrator",
"active": true
},
"project": {
"self": "http://localhost:8090/jira/rest/api/2/project/HSP",
"id": "10000",
"key": "HSP",
"name": "homosapien",
"roles": {},
"avatarUrls": {
"16x16": "http://localhost:8090/jira/secure/projectavatar?size=small&pid=10000&avatarId=10011",
"48x48": "http://localhost:8090/jira/secure/projectavatar?pid=10000&avatarId=10011"
}
},
"versions": [(0)],
"environment": null,
"created": "2011-09-26T15:44:23.888+1000",
"updated": "2011-09-26T15:44:39.295+1000",
"priority": {
"self": "http://localhost:8090/jira/rest/api/2/priority/5",
"iconUrl": "http://localhost:8090/jira/images/icons/priority_trivial.gif",
"name": "Trivial",
"id": "5"
},
"description": null,
"duedate": "2011-09-25",
"components": [(1)
{
"self": "http://localhost:8090/jira/rest/api/2/component/10000",
"id": "10000",
"name": "New Component 1"
}
],
"comment": {
"startAt": 0,
"maxResults": 0,
"total": 0,
"comments": [(0)]
},
"watches": {
"self": "http://localhost:8090/jira/rest/api/2/issue/HSP-1/watchers",
"watchCount": 0,
"isWatching": false
}
},
"transitions": "http://localhost:8090/jira/rest/api/2/issue/HSP-1/transitions",
"editmeta": "TODO",
"changelog": "TODO"
}
```

## Advanced topics
Using helper libraries
If you want to use OAuth to make requests to JIRA, the best way to do this is to find a helper library which takes care of signing the requests and reading the tokens from the response. The example below is using the net.oauth library.

```XML
<dependencies>
        <dependency>
            <groupId>net.oauth.core</groupId>
            <artifactId>oauth</artifactId>
            <version>20090617</version>
        </dependency>
        <dependency>
            <groupId>net.oauth.core</groupId>
            <artifactId>oauth-httpclient4</artifactId>
            <version>20090617</version>
        </dependency>
</dependencies>
```
CAPTCHA
CAPTCHA is 'triggered' after several consecutive failed log in attempts, after which the user is required to interpret a distorted picture of a word and type that word into a text field with each subsequent log in attempt. If CAPTCHA has been triggered, you cannot use JIRA's REST API to authenticate with the JIRA site.

You can check this in the error response from JIRA -- If there is an X-Seraph-LoginReason header with a a value of AUTHENTICATION_DENIEDorAUTHENTICATED_FAILED, this means the application rejected the login without even checking the password. This is the most common indication that JIRA's CAPTCHA feature has been triggered.
