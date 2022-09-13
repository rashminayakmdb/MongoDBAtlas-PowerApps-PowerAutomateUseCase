# MongoDB+PowerApps/PowerAutomateUseCase

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
   ### Note: This is the only time you can retrieve the full private key.

## 2: Create a Blob Storage:

  - You can follow the steps provided in Microsoft Link.
  - Create a container filestoprocess

## 3: Connect to MongoDB Standard connector
  - Login to Power Apps.
  - Follow below 3 steps from screenshot to use the MongoDB connector.
  
  <img width="198" alt="Screenshot 2022-09-13 at 11 20 37 AM" src="https://user-images.githubusercontent.com/101181433/189820464-764763cd-416e-4b22-aaa9-4b57bf26ecdb.png">

  <img width="523" alt="Screenshot 2022-09-13 at 11 21 56 AM" src="https://user-images.githubusercontent.com/101181433/189820367-163a162b-74b7-4ebe-a139-4aadcba8af8c.png">

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
           
         - <img width="512" alt="FindOne" src="https://user-images.githubusercontent.com/101181433/185377096-6f5bf616-440c-4769-a33d-ee405eeaaa96.png">

   - Update the connector.
   - Go to Test Tab → New Connection. Enter the private API Key created in Generate an API key step.
   
   - <img width="1098" alt="testTab" src="https://user-images.githubusercontent.com/101181433/185431420-4ee47fbf-193d-40dd-8401-cfbe8e9ef61e.png">

   - This connection created will show up in the Dataverse→Connections in the Left Panel of Power Apps portal.
   
## 4: Build Power Apps Onboarding form
  - Go to Apps in the left panel or Power Apps Portal.
  - Create a new Canvas app. Give a name, choose Tablet Mode.
   <img width="519" alt="TabletMode" src="https://user-images.githubusercontent.com/101181433/185431906-9bca0b70-4e25-4bd5-8d62-89ea4e426b6a.png">

  - ## Add data source 
    - ### Blob Storage
      - Type Blob Storage → Add a connection. Enter the Azure storage account Name and Azure storage account Access Key(Get it from the Azure Portal where the storage container is created). Click Connect.
       <img width="209" alt="DataSource" src="https://user-images.githubusercontent.com/101181433/185431920-9a508684-c919-42d9-8de6-dcd83dcd2b65.png">

    - ### MongoDB DataAPI
      - Type MongoDB and you will see the connection you created with the Custom Connector.

  - ## Create the Form
    - We need 2 screens 
      1. To create a form which has all these below fields in the screenshot.
      2. To display a success message when the form is submitted successfully.
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
  
    <img width="306" alt="AutomatedCloudFlow" src="https://user-images.githubusercontent.com/101181433/185534239-7d7d8740-e2e9-4a43-a47a-2c74210e9f60.png">
    
  - Give a name and choose flow’s trigger as “When a blob is added or modified”. (Hint : Just type blob in the search). Create the first 2 steps in the workflow as shown below.
    #### Note : Storage account name will be the name of the Storage Account created in the Azure Portal.
    
    <img width="418" alt="Step2" src="https://user-images.githubusercontent.com/101181433/185534234-3f90391a-1f9b-4aae-9625-abc7d98539b5.png">
    
  - Create Step3 as below: Choose Dynamic Content → File Content
     
     <img width="372" alt="Step3" src="https://user-images.githubusercontent.com/101181433/185534228-75e045a8-37ca-4401-b5ce-b2dde785d207.png">
     
  - Step 4 and Step 5:

    - These steps are to split the file name that is uploaded in the Blob Container.
    - Step 4 takes Text: Display Name , Search Text :  ‘.’ 
    - Step 5 takes Text: Display Name,starting position: 0 and Length: Text Position(output of Step4)
    
      <img width="372" alt="Step4,5" src="https://user-images.githubusercontent.com/101181433/185534573-e0899d7e-8e51-4d7c-beab-c6d7dfc50493.png">
      
    - Step 6:
      - Type MongoDB and then choose the DataAPI created and choose Find
      - Document as an Action. Enter all the details as below:
      #### Note : _id will be the output of the previous step(Substring)
        
        <img width="373" alt="Step6" src="https://user-images.githubusercontent.com/101181433/185534568-c53390d9-f353-411a-8694-9fdd74fd128c.png">
        
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
                                           
             <img width="363" alt="Step7" src="https://user-images.githubusercontent.com/101181433/185534557-09ff018b-d8e2-4661-9a11-fb743d2df84b.png">
                                        
     - Step 8:
        - Type Condition.Choose a Value -> Add each field of the Parse JSON output and compare with the AI Model output(Extract information from Identity Documents)
        
        <img width="369" alt="Step8" src="https://user-images.githubusercontent.com/101181433/185534838-c1b96dbc-81b7-4eda-bf77-749d8b92bbad.png">
        
        - If Yes/No:
          - Add an Action → Type Gmail →Send email.
          - Add content as below:
          
          <img width="585" alt="Condition" src="https://user-images.githubusercontent.com/101181433/185534832-46ce904e-10e1-4925-aaab-0ab96a0ac951.png">
          
        This is the final look of of Power Automate Workflow:
        
        <img width="346" alt="Final" src="https://user-images.githubusercontent.com/101181433/185534822-c974d9bc-e8e5-453c-a343-a6c5f0721f44.png">
        
# Execution
  1. Click on the Application created in Power Apps.
  2. Enter the User Details in the form.
  3. Upload the Passport document.
  4. Click on Submit, wait for the Success Message.
  5. Go to the Atlas collection, there should be an entry made with the details entered in the form.
  6. Go to Azure Blob Storage, it must have the file that is uploaded through the Power Apps.
  7. Go to Power Automate, Wait for the trigger to get started.(usually takes 1-2 minutes to start the flow).
  8. Wait for the flow to complete. If the form details matches with the uploaded document.You should receive a mail saying Validation is Successful. Else you should receive a Validation failure email.















