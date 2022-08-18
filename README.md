# MongoDB-PowerAppsPowerAutomateUseCase

# Setup
## 1: MongoDB cluster Setup

### A: Configure Atlas Environment

  - Log-on to Atlas account.
  
  - In the project's Security tab, choose to add a new user called main_user, for this user select Add Default Privileges and in the Default Privileges section add the roles readWriteAnyDatabase.
  - Create a Sandbox Cluster in a cloud provider region of your choice with default settings.
  - In the Security tab, add a new IP Whitelist for your laptop's current IP address
### B: Enable the Data API in Atlas
  - In the left-hand Deployment menu, click to navigate to Data API.

  - Slide the Data API Enabled toggle to ON for the cluster you're using for this PoV. Select Enable when prompted and wait for the deployment to complete:

   <img width="1287" alt="DataAPI" src="https://user-images.githubusercontent.com/101181433/185349961-ac6fb7eb-c78e-4bda-b6ba-3d10adfb6ac6.png">

  - Copy your Data API App ID from the URL Endpoint as you will need it for Postman. In the above screenshot, data-qdcie is my App ID.
### C: Generate an API key
    
   - Switch to the API Keys tab and click Generate API Key.
   - Give your API key an appropriate name then click the Generate API Key button.
   - Copy the private API Key for use and safekeeping that is displayed once generated. 
   ### NOTE: This is the only time you can retrieve the full private key.

## 2: Create a Blob Storage:
You can follow the steps provided in Microsoft Link.
Create a container filestoprocess
