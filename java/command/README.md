# Running a slash command for Slack on Google Cloud Platform with Java

This tutorial demonstrates how to build and deploy a [slash command for
Slack](https://api.slack.com/slash-commands) on [Google Cloud Platform](https://cloud.google.com/).

Slash commands provide a way to call external web services from a [Slack](https://slack.com/)
conversation. For example, the [Giphy
app](https://get.slack.help/hc/en-us/articles/204714258-Add-Giphy-search-to-Slack) can be run by
**/giphy** in a conversation.

1\.  [Objectives](#objectives)  
2\.  [Costs](#costs)  
3\.  [Before you begin](#beforeyoubegin)  
4\.  [Getting the sample code](#gettingthesamplecode)  
5\.  [Create a Slack app](#createaslackapp)  
5.1\.  [Create a slash command](#createaslashcommand)  
5.2\.  [Install the command to your team](#installthecommandtoyourteam)  
6\.  [Deploying your app](#deployingyourapp)  
6.1\.  [Create a configuration](#createaconfiguration)  
6.2\.  [Copy the verification token](#copytheverificationtoken)  
6.3\.  [Add verification token to the configuration](#addverificationtokentotheconfiguration)  
6.4\.  [[Optional] Running locally](#[optional]runninglocally)  
6.5\.  [Deploying to App Engine](#deployingtoappengine)  
7\.  [Trying the slash command](#tryingtheslashcommand)  

<a name="objectives"></a>

## 1\. Objectives

- Deploy a Java application to the [App Engine flexible environment][flexible].
- Create a [slash command for Slack](https://api.slack.com/slash-commands).
- Load tokens from the [Google Cloud Runtime Config
  API](https://cloud.google.com/deployment-manager/runtime-configurator/) 

[flexible]: https://cloud.google.com/appengine/docs/flexible/java/

<a name="costs"></a>

## 2\. Costs

This tutorial uses billable components of Cloud Platform, including:

- Google App Engine Flexible Environment

Use the [Pricing Calculator][pricing] to generate a cost estimate based on your projected usage.

[pricing]: https://cloud.google.com/products/calculator

<a name="beforeyoubegin"></a>

## 3\. Before you begin

1.  Follow the [quickstart for Java in the App Engine flexible
    environment](https://cloud.google.com/appengine/docs/flexible/java/quickstart) to 
    set up your environment to deploy the sample applications App Engine.
    1.  Download and install the [Google Cloud SDK](https://cloud.google.com/sdk/docs/).
    1.  [Install and configure Apache Maven](http://maven.apache.org/index.html).
    1.  [Create a new Google Cloud Platform project, or use an existing
        one](https://console.cloud.google.com/project).
    1.  [Enable billing for your
        project](https://support.google.com/cloud/answer/6293499#enable-billing).
    1. Initialize the Cloud SDK.

           gcloud init

1.  Create a [new Slack team](https://slack.com/), or use an team where you have
    permissions to add custom integrations.

<a name="gettingthesamplecode"></a>

## 4\. Getting the sample code

Get the latest sample code from GitHub using Git or download the repository as a ZIP file.
([Download](https://github.com/GoogleCloudPlatform/slack-samples/archive/master.zip))

    git clone https://github.com/GoogleCloudPlatform/slack-samples.git


The `java/command/1-start` directory contains a simple Hello World application, which you will
modify to support Slack slash commands.

    cd java/command/1-start

<a name="createaslackapp"></a>

## 5\. Create a Slack app

Create a new Slack app by going to the [app management
page](https://api.slack.com/apps) and clicking the **Create new app** button.

1.  Give the app a name, such as "Hello World".
1.  Choose the Slack team for development and where you will eventually install it.

<a name="createaslashcommand"></a>

### 5.1\. Create a slash command

1.  Click the **Slash commands** feature in the navigation on the left-hand side.
1.  Click the **Create new command** button.
1.  Enter a command name, such as `/greet`.
1.  Enter `https://YOUR_PROJECT.appspot.com/hello` as your request URL, replacing `YOUR_PROJECT`
    with your Google Cloud project ID.
1.  Enter a short description, such as "Sends a greeting."

<a name="installthecommandtoyourteam"></a>

### 5.2\. Install the command to your team

1.  Go to the **Basic information** page using the left-hand navigation.
1.  Expand **Install your app to your team** and click the **Install app to team** button.

<a name="deployingyourapp"></a>

## 6\. Deploying your app

Your app requires some configuration. This sample uses the [RuntimeConfig
API](https://cloud.google.com/deployment-manager/runtime-configurator/) to store configuration
values, such as secret tokens.

<a name="createaconfiguration"></a>

### 6.1\. Create a configuration

Using the command-line [Google Cloud SDK](https://cloud.google.com/sdk/), create a new runtime
configuration.

    gcloud beta runtime-config configs create slack-samples-java

<a name="copytheverificationtoken"></a>

### 6.2\. Copy the verification token

To ensure that HTTP requests to your app originate from Slack, Slack provides a validation token.
You check that the token field of an incoming request matches the expected value.

1.  Select your app on the [app management page](https://api.slack.com/apps).
1.  Go to the **Basic information** page.
1.  Scroll to **App credentials** and copy the **Verification token** text.

<a name="addverificationtokentotheconfiguration"></a>

### 6.3\. Add verification token to the configuration

Create a variable called `slack-token` in the runtime configuration. Use the Cloud SDK from the
command-line to add the variable.

    gcloud beta runtime-config configs variables set \
        slack-token "YOUR-TOKEN-VALUE" \
        --is-text --config-name slack-samples-java

Replace `YOUR-TOKEN-VALUE` with the verification token value you copied from the Slack app
management page.

<a name="[optional]runninglocally"></a>

### 6.4\. [Optional] Running locally

To run the application locally, use the Maven Spring Boot plugin.

    mvn clean spring-boot:run

View the app at http://localhost:8080.

Since Slack requires a public URL to send webhooks, you may wish to [use a service like ngrok to
test your Slack application locally](https://api.slack.com/tutorials/tunneling-with-ngrok).

<a name="deployingtoappengine"></a>

### 6.5\. Deploying to App Engine

To deploy the app to App Engine, run

    mvn clean appengine:deploy

After the deploy finishes (can take up to 10 minutes), you can view your application at
https://YOUR_PROJECT.appspot.com, where YOUR_PROJECT is your Google Cloud project ID. You can see
the new version deployed on the App Engine section of the Google Cloud Console.

For a more detailed walkthrough, see the getting started guide for Java in the App Engine flexible
environment.

<a name="tryingtheslashcommand"></a>

## 7\. Trying the slash command

When you run the slash command, Slack will send a request to your app and show the result.

-   Type `/greet` in your Slack team.

You should see the text `Hello, world.` in response.
