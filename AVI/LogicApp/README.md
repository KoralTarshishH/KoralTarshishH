
//  Logic App step by step guide  //

# Pre Requisites:
1. Azure Subscription
2. Azure Resource Group
3. Azure Blob storage account and container - for files we want to upload and index to The Azure Video Indexer account
4. Logic App Workflow: Microsoft.Logic/workflows - Enable System assign managed identity and grant it permissions :
    * Blob data contibutor RBAC role on the storage account scope
    * Owner RBAC role on the Azure Video Indexer account scope
5. Azure Video Indexer account - paid account (to be able to use the redact faces API we need to fill the form: https://customervoice.microsoft.com/Pages/ResponsePage.aspx?                id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUQjA5SkYzNDM4TkcwQzNEOE1NVEdKUUlRRCQlQCN0PWcu )

# Requested parameters:
1. AVIAccountID - The ID of the Azure Video Indexer account
2. AVIAccountName - Azure Video Indexer account name
3. SubscriptionID - The Azure Video Indexer account subscription ID
4. RGName - The Azure Video Indexer account Resource group name
5. LocationAVI - The Azure Video Indexer account region
6. StorageAccountName - Source storage account name that hostes the files we want to upload and index to The Azure Video Indexer account
7. ContainerName - The Blob container name

# API connections:
1. connection for Azure Blob Storage - for the trigger action (Authentication type: Logic Apps Managed Identity)
2. connection for Azure Blob Storage - for the Create SAS URI by path (V2) action (Authentication type: Access key -because this action not supported the Logic Apps Managed Identity Authentication type)
3. connection for AVI account - for Upload video and index action (the API Key is the developer key from AVI developer portal)


# Trigger:
1. The trigget is set to When_a_blob_is_added_or_modified and connected to Azure Storage Account VIA a blob connection
 
# Actions 
1. HTTP action- (Action name: AVI get access token) generating an access token for the Video Indexer API (Generates Contributor permissions for account scope)
2. Initialize variable - (Action name: Initialize variable AVI Access Token) Initialize variable for Azure Video Indexer account access token
3. Set variable -(Action name: Set variable AVI Access Token)
4. Initialize variable - (Action name: Initialize variable video ID) Initialize variable for the Video ID
5. Initialize variable - (Action name: initialized var redacted file name) Initialize variable for the redacted file name
6. Create SAS URI by path (V2) - for the file we want to upload + index to AVI 
7. Upload video and index - (Built-in action) Upload + Index the file to AVI account
8. Set variable -(Action name: Set variable video ID) for the video that uploaded and indexed at the last action
9. Set variable -(Action name: Set variable redacted file name) new name for the redacted file
10. HTTP action- (Action name: Redact Faces API) starts redaction job

# Important Notes:
1. for the trigger action (hen_a_blob_is_added_or_modified) Choose how often do you want to check for items and don't forjet to change the Time Zone and Start Time. (This action should use the connection that has been mention at 1st API connctions section)
2. for the first HTTP Action: AVI get access token - set the Adanced parameters (Authentication parameter) to Authentication type: Managed identity, Managed Identity: System-assigned managed identity
3. for the Create SAS URI by path (V2) action -  This action should use the connection that have been mention at 2nd API connctions section
4. for the Upload video and index action - before you set at the location field the LocationAVI parameter check if your region exists in the combo-box - if so you can set the LocationAVI parameter, anf if not you'll need to use the HTTP action to use the Uplopad and Index API for AVI instade of the built-in action. 
in case you don't use the built-in Upload video and index action, you'll need to change every & character with %26 in the request URI as mention here: https://docs.microfocus.com/OMi/10.62/Content/OMi/ExtGuide/ExtApps/URL_encoding.htm 
(this action should use the connection that has been mention at 3rd API connctions section )


# AVI Important documentations: 

https://github.com/MicrosoftDocs/azure-video-indexer/blob/main/azure-video-indexer/face-redaction-with-api.md --->  Face redaction API
https://learn.microsoft.com/en-us/azure/azure-video-indexer/avi-support-matrix --->  AVI service limitations
https://api-portal.videoindexer.ai/  --->  AVI developer portal to get the 



