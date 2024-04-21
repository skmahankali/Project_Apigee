# Please go through this before you start looking into the project

In this guide, you'll swiftly set up and run a sample Apigee 127 API project, which serves as a proxy for the Twitter search API.

- [Overview](#overview)
- [Installation Guide](#installation)
- [Project Initialization](#project-setup)
- [App Configuration and Execution](#app-configuration)
- [Troubleshooting Tips](#troubleshooting)

## <a name="overview"></a>Overview

This application demonstrates an API proxy for the Twitter search API. It involves a Node.js server on your local machine communicating with Apigee Edge via the Volos.js module's management API. Apigee Edge manages OAuth requests in this scenario.

Here's the process flow:

![Flow Diagram](https://raw.githubusercontent.com/apigee-127/a127-documentation/master/a127/images/with-edge.png)

1. Volos.js contacts Apigee Edge for OAuth validation, where it can also handle quota, caching, and analytics functionalities. Only metadata is sent to Edge, not the API payload.
2. Edge responds, indicating the validity of the key.
3. If valid, the API call is proxied to the local Node.js API implementation.
4. Finally, the API response is returned from the API implementation.

**About Volos.js:** [Volos.js](https://github.com/apigee-127/a127-documentation/wiki/Understanding-Volos.js) is a Node.js solution for developing and deploying APIs at a production level. It facilitates common features like OAuth 2.0, Caching, and Quota Management into your APIs. Volos.js can either support these features through Apigee Edge or independently of Edge.

## <a name="installation"></a>Installation Guide

1. Clone the example project from GitHub:

    ```
    $ git clone https://github.com/apigee-127/example-project.git
    ```

2. In the `example-project` directory, run: 

    ```
    $ npm install
    ```

3. Install Apigee 127:

    ```
    $ npm install -g apigee-127
    ```

   The `-g` option automatically adds the `a127` executable commands to your PATH. If you skip `-g`, manually add the `apigee-127/bin` directory to your PATH. You might need to use `sudo` for this command.

## <a name="project-setup"></a>Project Initialization

Before running the example project, set up the following:

1. Create a [Twitter app](https://dev.twitter.com/apps). Any app will suffice, even a simple or dummy one. Later, you'll need to obtain the Access Token and Access Token Secret keys from the app. Ensure that if you're using keys from an existing app, they are not expired.

2. Execute the following command and follow the prompts to configure your Apigee account information:

    ```
    $ a127 account create <anAccountName>
    ```

    > If you don't have an account on Apigee.com, you'll be prompted to create one.

3. Create an Apigee Remote Service named "RemoteProxy":

    ```
    $ a127 service create RemoteProxy
    ```

   Note: The service name is crucial as it's pre-configured for you in `config/default.yaml`.

4. Bind the "RemoteProxy" service to your application:

    ```
    $ a127 project bind RemoteProxy
    ```

## <a name="app-configuration"></a>App Configuration and Execution

1. In the `example-project` directory, duplicate `config/secrets.sample.js` and rename it to `config/secrets.js`.
   
2. Edit `config/secrets.js` with your Twitter API keys and Access Tokens. These keys can be found in the management console for your Twitter app, under the API Keys tab.

    ```javascript
    exports.twitter = {
      consumer_key:        '123xxxxxxxxxxxxxxxx',
      consumer_secret:     '456yyyyyyyyyyyyyyyy',
      access_token:        '789aaaaaaaaaaaaaaaa',
      access_token_secret: '123bbbbbbbbbbbbbbbb'
    };
    ```

3. In the same file, modify the Apigee Edge object with your Edge account information.

    ```javascript
    exports.apigee = {
      organization: 'jdoe',
      user: 'jdoe@apigee.com',
      password: 'mypassword'
    };
    ```

4. Execute:

    ```
    $ node bin/create-app.js
    ```

   > This step provisions a developer app on Apigee Edge.

5. Execute:

    ```
    $ a127 project start
    ```

6. Use the provided curl commands (from another console window) to test the application:

    **Twitter Search:** This command invokes the Twitter search API through the Apigee 127 proxy, locally on your machine. The OAuth Bearer token is generated through Volos and added to this curl command by the app.

        ```
        $ curl -H "Authorization: Bearer 123ababababababa" "http://localhost:10010/twitter?search=apigee"
        ```

    **Get a Password Token:** This command returns a new access token on behalf of the user. You can try substituting this access token in the Twitter search API call.

        ```
        $ curl -X POST "http://localhost:10010/accesstoken" -d "grant_type=password&client_id=xxxxxxxxxxxxx&client_secret=yyyyyyyyyy&username=jdoe&password=password"
        ```

## <a name="troubleshooting"></a>Troubleshooting Tips

* Incorrect or expired app keys might cause errors. Ensure your Twitter credentials are up to date.