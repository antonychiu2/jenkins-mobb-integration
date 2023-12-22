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

## Mobb API Key

After logging into the Mobb portal, click on the "settings" icon on the bottom left, then select "Access tokens". From here, you can generate an API key by selecting the "Add API Key" button.

To integrate with Snyk, you  will also need to generate a Snyk API Key. This can be achieved by following this guide in Snyk documentation. 

