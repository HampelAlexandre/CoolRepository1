**This is pre-release**

## Contents

- 1. [Creating a new project](#new-project)  
- 2. [Enabling Billing and appropriate APIs and services](#api-enabling)  
- 3. [Google Cloud SDK](#gcloud-sdk)
- 4. [Authentication and Credentials](#auth)
  

<a name="new-project">
### 1. Creating a new project
To get started with Google Cloud, first go to https://console.cloud.google.com/iam-admin/projects and create a new project.

[[/images/gcloud-create-project.png]]

<a name="api-enabling">
### 2. Enabling Billing and appropriate APIs and services
- Once you've created your project, you have to enable all the services your project requires.
- For instance, if you need a Google Compute instance (equivalent to an AWS EC2 instance), you'd want to make sure Google Compute Engine API is enabled. 
- To enable/disable APIs, and check your API usage, go to https://console.cloud.google.com/apis/dashboard

| Service/API needed    | Who needs it                    |
|-----------------------|---------------------------------|
| Google Compute Engine | Anything that needs an instance |
| Google Cloud Pub/Sub  | Scala Stream Collector          |

- Some services require Billing to be enabled. To enable and manage your billing accounts, go to https://console.cloud.google.com/billing. A pop-up will show, asking you to select the billing account with which to associate your project. If it doesn't, Billing was enabled by default when you created your project, probably because you have only one active billing account.

[[/images/gcloud-enable-billing.png]]


<a name="gcloud-sdk">
### 3. Google Cloud SDK

Google provides a second way for you to interact with its services: the Cloud SDK. It allows you to issue a large number of commands, to (for example): create Compute instances, publish and subscribe to Pub/Sub topics, create BigQuery tables, authenticate, among others.  

- Google Compute instances come with the Cloud SDK pre-installed. 
- If you intend to run some part of your project locally, you'll need to download and install the appropriate [Cloud SDK package](https://cloud.google.com/sdk/) for your platform.

<a name="auth">
### 4. Authentication and Credentials

- Go to [the credentials section of the API manager](https://console.cloud.google.com/apis/credentials). 
- Make sure your project is selected (in the dropdown menu to the left of the search bar). 
- Here you can manage the credentials for the different roles/accounts associated with your project. For instance, you wouldn't want to authenticate with your personal account on a Compute Instance to which multilple people have access. In that case, it is recommended to use a service account. 
- Compute Instances come with a default service account, but you can create more, with different privileges and different purposes.  
[[/images/gcloud-credentials.png]]
On this page, you can create credentials for your existing accounts. If you don't have a service account: 
- Click on Create Credentials > Service account key, then on the service account dropdown selector, pick "New service account". 
- Fill in the Service account name and ID, and pick its role. 
- Finally, click Create. This will download a JSON file with the credentials. This is the only occasion in which this file can be downloaded, so save it carefully. You'll need to place this file wherever you need this service account to be able to authenticate.  

[[/images/gcloud-service.account.png]]

You can also use the SDK to authenticate (if you want to authenticate with your personal account), doing:

    $ gcloud auth login

If you have multiple projects, this will default to the most recent one you worked on. If you need to change the current project, do:

    $ gcloud config set project YOUR_PROJECT_ID

Every gcloud command can be appended with '--help' for more info. For more detailed information on Service Accounts: https://developers.google.com/identity/protocols/OAuth2ServiceAccount
