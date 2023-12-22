# Sample Integration of Mobb with Snyk SAST scan in Jenkins 

# Usage

## Register

To perform this integration, you will need the following:

* Sign up for a free account at https://mobb.ai
* Sign up for a free Snyk Account https://snyk.io 
* Access to a Jenkins instance
* Access to a GitHub repository where the source code resides

## GitHub Personal Access Token (PAT)

The first step is to generate a GitHub Personal Access Token (PAT). You can generate one by clicking on your profile -> Settings -> Developer Settings -> Personal Access Tokens -> Fine-grained tokens. 

For this integration, we need to provide the following permissions: 

* Commit Statuses - Read and Write
* Contents - Read and Write
* Pull Requests - Read and Write

Note down the token that is generated and store it in a safe place. 

## Generate Mobb API Key and Snyk API Key

After logging into the Mobb portal, click on the "settings" icon on the bottom left, then select "Access tokens". From here, you can generate an API key by selecting the "Add API Key" button.

To integrate with Snyk, you  will also need to generate a Snyk API Key. This can be achieved by following this [guide](https://docs.snyk.io/snyk-api/authentication-for-api) from Snyk documentation. 

## Jenkins - Credentials

At this point, we should have 3 API keys ready: 
* GitHub Personal Access Token (PAT)
* Mobb API Key
* Snyk API Key

The next step is to load them into Jenkins Credentials. To do so, go to "Dashboard" -> "Manage Jenkins" -> "Credentials"

|Credential Entry  |Credential Kind |ID |
| ----------------- | --------------- | -- |
| GitHub Personal Access Token (PAT)  | Secret text | `your github username` |
| Mobb API Key  | Secret text | `MOBB_API_KEY` |
| Snyk API Key  | Secret text | `SNYK_API_KEY` |

By the time you have loaded the 3 entries, you should have something similar to this:
![image](https://github.com/antonychiu2/jenkins-mobb-integration/assets/5158535/801d1290-9429-4423-861f-c101a1bc5b70)


## Jenkins - Plugins

This integration makes use of the following Jenkins plugins, please ensure you have these installed in your Jenkins environment:
* [GitHub Plugin](https://plugins.jenkins.io/github)
* [GitHub Pull Request Builder](https://plugins.jenkins.io/ghprb)

## Jenkins - Configure Plugins Credentials
The next step is to configure the plugins to ensure they are using the crednentials we've provided. Go to "Dashboard" -> "Manage Jenkins" -> "System". 

First, locate the section called **GitHub**. Provide a Name to the connection. Under the "Credentials" section, click on the drop-down and select your GitHub Credential. 

Make sure to click on the "Test Connection" to ensure Jenkins is able to access your GitHub account using the provided credentials. 
![image](https://github.com/antonychiu2/jenkins-mobb-integration/assets/5158535/0b6a871d-df33-4c54-8597-57c86cbbebdd)

Second, locate the section called **GitHub Pull Request Builder**. Similar to the previous step, select your GitHub credential. Under the "Shared Secret" section, enter your GitHub Personal Access Token. Make sure to test the connection again to verify that Jenkins is able to connect to your GitHub account using the supplied credentials. 

![image](https://github.com/antonychiu2/jenkins-mobb-integration/assets/5158535/027d7efb-24cf-4a15-9ec8-04efafe0cf27)

## GitHub - Create Webhook

The next step is to configure Webhook for GitHub to publish events to Jenkins. Specifically, we want to trigger the Jenkins Job when there is a pull request to initiate the SAST Scan and trigger Mobb analyze to parse the result of the SAST scan. 

> [!NOTE]
> GitHub must have network connectivity to your Jenkins instance in order to perform this step. Speak to your network administrator on setting up this connection. This particular sample integration was built by exposing Jenkins to the web via a secure tunnel using [Cloudflare Zerotrust Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/). 
 

To setup the Webhook, first go to your GitHub repository. Select "Settings" -> "Webhooks". 

For the Payload URL, you want to specify your Jenkins URL in the format:
`https:\\<JENKINS_DOMAIN>:<JENKINS_PORT>/ghprbhook/`

For Content Type, select `application/json`
![image](https://github.com/antonychiu2/jenkins-mobb-integration/assets/5158535/c11e183a-d84e-4586-aab2-050d36afce9b)

For events to trigger the webhook, select "Let me select individual events". Under the event list, select "Pull requests". 
![image](https://github.com/antonychiu2/jenkins-mobb-integration/assets/5158535/5597944b-a119-4d6b-8cd5-73debfa7af1b)

To verify that the Webhook is able to connect to Jenkins, go to "Recent Deliveries" tab. Verify that there is a green checkmark next to your most recent request. 
![image](https://github.com/antonychiu2/jenkins-mobb-integration/assets/5158535/62496a31-5a96-4ce8-becc-5ef0efa05d6b)

We are finally done with the infrastructure-related configurations! Next, we will move onto configuring the actual pipeline. 
