# MongoDB Atlas + Microsoft PowerApps/PowerAutomateUseCase

## Description

   #### This use-case shows how easily a front end for the Customer Onboarding can be developed using Power Apps and the back end process of validating the uploaded proofs can be automated and triggered in real-time using Power Automate and this entire application can seamlessly integrate with MongoDB Atlas which stores the details entered by user on the front-end and also stores the results of the validation.
    
## Prerequisites

  #### 1: Spin up a MongoDB Atlas cluster [here](https://account.mongodb.com/account/login)
  #### 2: Get Azure subscription to store the files uploaded in Blob storage[here](https://azure.microsoft.com/en-in/free/)
  #### 3: Get your Power Apps/Power Automate License [here](https://powerapps.microsoft.com/en-us/pricing/)

## Below are the High level steps followed for the setup:

#### 1: [MongoDB Cluster Setup](#mongodb-cluster-setup)
#### 2: [Create a Blob Storage](#create-a-blob-storage)
#### 3: [Connect to MongoDB Standard connector](#connect-to-mongodb-standard-connector)
#### 4: [Build Power Apps Onboarding form](#build-power-apps-onboarding-form)
#### 5: [Build Power Automate Flow](#build-power-automate-flow)

# Setup
## MongoDB cluster Setup

### A: Configure Atlas Environment

  - Log-on to Atlas account.
  
  - In the project's Security tab, choose to add a new user called main_user, for this user select Add Default Privileges and in the Default Privileges section add the roles readWriteAnyDatabase.
  - Create a Sandbox Cluster in a cloud provider region of your choice with default settings.
  - In the Security tab, add a new IP Whitelist for your laptop's current IP address
### B: Enable the Data API in Atlas
  - In the left-hand Deployment menu, click to navigate to Data API.

  - Slide the Data API Enabled toggle to ON for the cluster you're using for this PoV. Select Enable when prompted and wait for the deployment to complete:

   <img width="600" alt="DataAPI" src="https://user-images.githubusercontent.com/101181433/185349961-ac6fb7eb-c78e-4bda-b6ba-3d10adfb6ac6.png">

  - Copy your Data API App ID from the URL Endpoint as you will need it for Postman. In the above screenshot, data-qdcie is my App ID.
### C: Generate an API key
    
   - Switch to the API Keys tab and click Generate API Key.
   - Give your API key an appropriate name then click the Generate API Key button.
   - Copy the private API Key for use and safekeeping that is displayed once generated. 
   ### Note: This is the only time you can retrieve the full private key.

## Create a Blob Storage

  - You can follow the steps provided in [Microsoft Link](https://azure.microsoft.com/en-in/free/).
  - Create a container filestoprocess

## Connect to MongoDB Custom connector
  - Login to Power Apps.
  - Follow below 3 steps from screenshot to use the MongoDB Custom connector.
  
<img width="150" alt="Step1" src="https://user-images.githubusercontent.com/101181433/196114084-020ec9e7-183b-4a03-9900-a0278e1b1a9a.png">
<img width="200" alt="Step2" src="https://user-images.githubusercontent.com/101181433/196098527-3a8cec52-7767-4ff8-8f46-8f42c2b28ba5.png">
<img width="300" alt="Step3" src="https://user-images.githubusercontent.com/101181433/196098530-1cfcdba9-4493-48a6-8e74-05cfe8323194.png">


  - Change the name of the connector as per your choice
  - Toggle the “Swagger Editor” button which will show the swagger code for the connector. Replace the swagger file content with the MongoDBDataAPI.swagger.
      
   - Update the connector.
   - Go to Test Tab → New Connection. Enter the private API Key created in Generate an API key step.
   
 <img width="300" alt="testTab" src="https://user-images.githubusercontent.com/101181433/185431420-4ee47fbf-193d-40dd-8401-cfbe8e9ef61e.png">

   - This connection created will show up in the Dataverse→Connections in the Left Panel of Power Apps portal.
   
## Build Power Apps Onboarding form
  - Go to Apps in the left panel or Power Apps Portal.
  - Create a new Canvas app. Give a name, choose Tablet Mode.
   <img width="300" alt="TabletMode" src="https://user-images.githubusercontent.com/101181433/185431906-9bca0b70-4e25-4bd5-8d62-89ea4e426b6a.png">

  - ## Add data source 
    - ### Blob Storage
      - Type Blob Storage → Add a connection. Enter the Azure storage account Name and Azure storage account Access Key(Get it from the Azure Portal where the storage container is created). Click Connect.
       <img width="209" alt="DataSource" src="https://user-images.githubusercontent.com/101181433/185431920-9a508684-c919-42d9-8de6-dcd83dcd2b65.png">

    - ### MongoDB DataAPI
      - Type MongoDB and you will see the connection you created with the Custom Connector.

  - ## Create the Form
    - We need 7 screens 
     1. User can open a New Application or choose to open an existing application saved earlier by providing the application Number.
      
         <img width="300" alt="Welcome" src="https://user-images.githubusercontent.com/101181433/196109017-9c07440e-f021-40c4-bf55-871fb37e2064.png">
         
     2. Clicking on New Application takes us to the below form to fill all the details.
         
         <img width="350" alt="New Form" src="https://user-images.githubusercontent.com/101181433/196110393-8adc8484-fda6-48e6-8107-619bd9987569.png">
         
     3. Saving the form should display us saved successfully message on screen.
         
         <img width="350" alt="Save Success" src="https://user-images.githubusercontent.com/101181433/196110685-ff4aae02-5102-4047-8b7f-d8da189559af.png">
         
     4. Submitting the form should display us Submitted successfully message on screen.
         
         <img width="350" alt="Submit success" src="https://user-images.githubusercontent.com/101181433/196110682-81401e95-84c5-4939-aa76-f53354fee932.png">
         
     5. If user chooses to open the existing form, it should ask the user to enter the Application Number.
      
         <img width="300" alt="Enter Application Number" src="https://user-images.githubusercontent.com/101181433/196111704-e2e9e42b-b9cf-43fe-a362-d0fb1a1e3d76.png">
         
     6. If the application was saved ealier, then it should take the user to the Edit form page where the entries can be edited.
      
         <img width="300" alt="Edit Form" src="https://user-images.githubusercontent.com/101181433/196112230-ab25fa11-467e-4fba-8fb7-f5874a26219d.png">
         
     7. If the application was already submitten, then it should be non editable. 
      
         <img width="300" alt="Already Submiited Form" src="https://user-images.githubusercontent.com/101181433/196111714-33a96843-d23b-408a-b56c-1526733e9f31.png">
         
    - ### Screen1:
        
        <img width="429" alt="Screen1" src="https://user-images.githubusercontent.com/101181433/185433251-bd49989c-20ba-4f82-bc33-f14897047102.png">
     
        - Heading and labels on the left. 
           - Click on Insert → Label and key in the name.
        - Input boxes on the right, 
           - Click on Insert → Text → Text Input.
           - Click on the first Text Input box, go to properties and add “Please enter firstName as mentioned in your passport” in the Hint text field. Similarly add Hint text to each of the Text Inputs.
            - Rename the Labels,TextInputs and Buttons as shown in the below screenshot.This will be easier to identify the fields for the actions/functions we write for Buttons.
            
            <img width="173" alt="Labels" src="https://user-images.githubusercontent.com/101181433/185433291-6fb7f31a-1da5-4925-8e2d-f600ef3bdcd7.png">

           
         - ### Upload Button:
            - Click on Insert → Media → Add Picture. Edit the text to “Upload”.
            - Click on the Media Button created→ choose OnSelect Option → Add this function: 
            
                   AzureBlobStorage.CreateFile("filestoprocess",Concatenate(Passport.Text,".pdf"),Upload.Media); 
                    
                 - Filestoprocess →  Folder name in Blob Storage
                 - Concatenate(Passport.Text,".pdf") →  File name( I chose it to be the Passport number entered by the user in the TextInput we created earlier)
                 - Upload.Media → For Uploading the file.
          - ### Submit Button:
            - Click on Insert → Button. Edit the text to “Submit”.
            - Click on the Button created→ choose OnSelect Option → Add this function: 

                    MongoDB.InsertDocument("Content-Type","Access-Control-Request-headers","api-key",{dataSource:"Sandbox",database:"XYZBank",collection:"onboarding",document:{firstname:Upper(Fname.Text),lastname:Upper(Lname.Text),DateOfBirth:DOB.Text,passportNumber:Passport.Text,_id:Passport.Text,emailId:email.Text}});
                    Navigate(Screen2,ScreenTransition.Fade);

         - ### Reset Button:
           - Click on Insert → Button. Edit the text to “Submit”.
            - Click on the Button created→ choose OnSelect Option → Add this function:

                  Reset(Fname);Reset(Lname);Reset(DOB);Reset(Passport);Reset(email);
                  
     - ### Screen2:
     
        <img width="340" alt="Screen2" src="https://user-images.githubusercontent.com/101181433/185433956-145fb2e3-8535-4300-b47a-7522c815f4ba.png">
        
        - Create a New Screen.
            - Add a Label and enter the text as: “Your details are successfully submitted".
            - Add the Check Icon and Cancel Icon from Icons dropdown as shown in the below screenshot.When the Cancel button is clicked, We need to reset the fields entered in the Screen1 and navigate back to the Screen1.
            
            
            <img width="532" alt="CancelButton" src="https://user-images.githubusercontent.com/101181433/185433946-2794ae2e-ad47-4388-a41e-fc75e27668be.png">


            - Click on the Cancel Icon created→ choose OnSelect Option → Add this function:

                      Reset(Fname);Reset(Lname);Reset(DOB);Reset(Passport);Reset(email);
                      Navigate(Screen1,ScreenTransition.Fade);


## Build Power Automate Flow:
  - Login to Power Automate.
  - Create a New Automated Cloud Flow.
  
    <img width="300" alt="AutomatedCloudFlow" src="https://user-images.githubusercontent.com/101181433/185534239-7d7d8740-e2e9-4a43-a47a-2c74210e9f60.png">
    
  - Give a name and choose flow’s trigger as “When a blob is added or modified”. (Hint : Just type blob in the search). Create the first 2 steps in the workflow as shown below.
    #### Note : Storage account name will be the name of the Storage Account created in the Azure Portal.
    
    <img width="300" alt="Step2" src="https://user-images.githubusercontent.com/101181433/185534234-3f90391a-1f9b-4aae-9625-abc7d98539b5.png">
    
  - Create Step3 as below: Choose Dynamic Content → File Content
     
     <img width="300" alt="Step3" src="https://user-images.githubusercontent.com/101181433/185534228-75e045a8-37ca-4401-b5ce-b2dde785d207.png">
     
  - Step 4 and Step 5:

    - These steps are to split the file name that is uploaded in the Blob Container.
    - Step 4 takes Text: Display Name , Search Text :  ‘.’ 
    - Step 5 takes Text: Display Name,starting position: 0 and Length: Text Position(output of Step4)
    
      <img width="300" alt="Step4,5" src="https://user-images.githubusercontent.com/101181433/185534573-e0899d7e-8e51-4d7c-beab-c6d7dfc50493.png">
      
    - Step 6:
      - Type MongoDB and then choose the DataAPI created and choose Find
      - Document as an Action. Enter all the details as below:
      #### Note : _id will be the output of the previous step(Substring)
        
        <img width="300" alt="PAutomate_findDocument" src="https://user-images.githubusercontent.com/101181433/196105643-9f205ae2-fa76-4611-9de6-41364fb70490.png">
        
    - Step 7:
         - Type Parse JSON.
         - Content : output of the Find Operation.
         - Schema→ Generate from sample
         - Paste the JSON output of the previous step and click Done.For Example:
         
                                          {
                                           "document": {
                                             "_id": "N1242853",
                                             "firstname": "KOPAL",
                                             "lastname": "GUPTA",
                                             "DateOfBirth": "1986-06-10",
                                             "passportNumber": "N1242853",
                                             "emailId": "xxxxx@xxx.com"
                                                       } 
                                           }
                                           
             <img width="300" alt="Step7" src="https://user-images.githubusercontent.com/101181433/185534557-09ff018b-d8e2-4661-9a11-fb743d2df84b.png">
                                        
     - Step 8:
        - Type Condition.Choose a Value -> Add each field of the Parse JSON output and compare with the AI Model output(Extract information from Identity Documents)
        
        <img width="300" alt="Step8" src="https://user-images.githubusercontent.com/101181433/185534838-c1b96dbc-81b7-4eda-bf77-749d8b92bbad.png">
        
        - If Yes/No:
          - Add an Action → Type Gmail →Send email.
          - Add mail content as per your choice:
          
          <img width="450" alt="Condition" src="https://user-images.githubusercontent.com/101181433/185534832-46ce904e-10e1-4925-aaab-0ab96a0ac951.png">
          
          - The status of the application has to stored back in MongoDB. Under each Action (Yes/No) add a "update document" after the "Send email" Action.For YES status should be "Valid" and for NO status should be "Invalid".
          
         <img width="300" alt="PAutomate_UpdateStatus" src="https://user-images.githubusercontent.com/101181433/196105022-a73cee64-f9cd-46df-b127-30d5a5d74c54.png">
         
        This is the final look of of Power Automate Workflow:
        
        <img width="300" alt="Final" src="https://user-images.githubusercontent.com/101181433/185534822-c974d9bc-e8e5-453c-a343-a6c5f0721f44.png">
        
# Execution
  1. Click on the Application created in Power Apps.
  2. Enter the User Details in the form.
  3. Upload the Passport document.
  4. Click on Submit, wait for the Success Message.
  5. Go to the Atlas collection, there should be an entry made with the details entered in the form.
  6. Go to Azure Blob Storage, it must have the file that is uploaded through the Power Apps.
  7. Go to Power Automate, Wait for the trigger to get started.(usually takes 1-2 minutes to start the flow).
  8. Wait for the flow to complete. If the form details matches with the uploaded document.You should receive a mail saying Validation is Successful. Else you should receive a Validation failure email.















