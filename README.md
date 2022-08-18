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

  - You can follow the steps provided in Microsoft Link.
  - Create a container filestoprocess

## 3: Create Custom Connector
  - Login to Power Apps.
  - Follow below 3 steps from screenshot to Import from Github.
  - Change the name of the connector as per your “XYZBankDataAPI”
  - Toggle the “Swagger Editor” button which will show the swagger code for the connector.We have to do InsertOne and FindOne operation as part of the use case and we would need the parameters to be set here in the swagger file for the app to pick them directly from the connector.
      - ### InsertOne:  
        - In the swagger file, go to /action/insertOne path.
        - Under Schema →properties→ document→ properties, replace the name and age properties with the below:

                 _id: {type: string, description: _id, title: PassportNumber}
                 firstname: {type: string, description: firstname, title: firstname}
                 lastname: {type: string, description: lastname, title: lastname}
                 DateOfBirth: {type: string, format: date, description: DateOfBirth,title: DateOfBirth}
                 passportNumber: {type: string, description: passportNumber, title:passportNumber}
                 emailId: {type: string, description: emailId, title: emailId}


     
          It should look like this after update:
         
          <img width="490" alt="InsertOne" src="https://user-images.githubusercontent.com/101181433/185377082-554db411-db96-4d8d-b45f-19e66c9742a0.png">


      - ### FindOne:
         - In the swagger file, go To /action/findOne path
         - Under Schema →properties→ filter→ properties, replace the name property with the below:
            
                  _id: {type: string, description: PassportNumber}	

           It should look like this after update:
           
           <img width="512" alt="FindOne" src="https://user-images.githubusercontent.com/101181433/185377096-6f5bf616-440c-4769-a33d-ee405eeaaa96.png">

   - Update the connector.
   - Go to Test Tab → New Connection. Enter the private API Key created in Generate an API key step.
   - This connection created will show up in the Dataverse→Connections in the Left Panel of Power Apps portal.
   
## 4: Build Power Apps Onboarding form
  - Go to Apps in the left panel or Power Apps Portal.
  - Create a new Canvas app. Give a name, choose Tablet Mode.
  - ## Add data source 
    - ### Blob Storage
      - Type Blob Storage → Add a connection. Enter the Azure storage account Name and Azure storage account Access Key(Get it from the Azure Portal where the storage container is created). Click Connect.
    - ### MongoDB DataAPI
      - Type MongoDB and you will see the connection you created with the Custom Connector.

  - ## Create the Form
    - We need 2 screens 
      1. To create a form which has all these below fields in the screenshot.
      2. To display a success message when the form is submitted successfully.
    - ### Screen1:
        - Heading and labels on the left. 
           - Click on Insert → Label and key in the name.
        - Input boxes on the right, 
           - Click on Insert → Text → Text Input.
           - Click on the first Text Input box, go to properties and add “Please enter firstName as mentioned in your passport” in the Hint text field. Similarly add Hint text to each of the Text Inputs.
            - Rename the Labels,TextInputs and Buttons as shown in the below screenshot.This will be easier to identify the fields for the actions/functions we write for Buttons.
           
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
        - Create a New Screen.
            - Add a Label and enter the text as: “Your details are successfully submitted".
            - Add the Check Icon and Cancel Icon from Icons dropdown as shown in the below screenshot.When the Cancel button is clicked, We need to reset the fields entered in the Screen1 and navigate back to the Screen1.

            - Click on the Cancel Icon created→ choose OnSelect Option → Add this function:

                      Reset(Fname);Reset(Lname);Reset(DOB);Reset(Passport);Reset(email);
                      Navigate(Screen1,ScreenTransition.Fade);


## Build Power Automate Flow:
  - Login to Power Automate.
  - Create a New Automated Cloud Flow. 
  - Give a name and choose flow’s trigger as “When a blob is added or modified”. (Hint : Just type blob in the search). Create the first 2 steps in the workflow as shown below.
    ### Note : Storage account name will be the name of the Storage Account created in the Azure Portal.
  - Create Step3 as below: Choose Dynamic Content → File Content
  - Step 4 and Step 5:

    - These steps are to split the file name that is uploaded in the Blob Container.
    - Step 4 takes Text: Display Name , Search Text :  ‘.’ 
    - Step 5 takes Text: Display Name,starting position: 0 and Length: Text Position(output of Step4)
    - Step 6:
      - Type MongoDB and then choose the DataAPI created and choose Find
      - Document as an Action. Enter all the details as below:
      ### Note : _id will be the output of the previous step(Substring)
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
     - Step 8:
        - Type Condition.Choose a Value -> Add each field of the Parse JSON output and compare with the AI Model output(Extract information from Identity Documents)
        - If Yes/No:
          - Add an Action → Type Gmail →Send email.
          - Add content as below:
        This is the final look of of Power Automate Workflow:
        
# Execution
  1. Click on the Application created in Power Apps.
  2. Enter the User Details in the form.
  3. Upload the Passport document.
  4. Click on Submit, wait for the Success Message.
  5. Go to the Atlas collection, there should be an entry made with the details entered in the form.
  6. Go to Azure Blob Storage, it must have the file that is uploaded through the Power Apps.
  7. Go to Power Automate, Wait for the trigger to get started.(usually takes 1-2 minutes to start the flow).
  8. Wait for the flow to complete. If the form details matches with the uploaded document.You should receive a mail saying Validation is Successful. Else you should receive a Validation failure email.















