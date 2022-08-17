# MongoDB-PowerAppsPowerAutomateUseCase

Setup
1: MongoDB cluster Setup

1. Configure Atlas Environment

Log-on to Atlas account.
In the project's Security tab, choose to add a new user called main_user, for this user select Add Default Privileges and in the Default Privileges section add the roles readWriteAnyDatabase.
Create a Sandbox Cluster in a cloud provider region of your choice with default settings.
In the Security tab, add a new IP Whitelist for your laptop's current IP address
2: Enable the Data API in Atlas
In the left-hand Deployment menu, click to navigate to Data API.
Slide the Data API Enabled toggle to ON for the cluster you're using for this PoV. Select Enable when prompted and wait for the deployment to complete:
