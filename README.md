# Configuring Azure Function Host Locally

Based on the documentation here: [Contributor Onboarding · Azure/azure-functions-host Wiki](https://github.com/Azure/azure-functions-host/wiki/Contributor-Onboarding)

## Requirements

Visual Studio 2022 [Download](https://visualstudio.microsoft.com/downloads/)

## Getting the Functions Host Running Locally

Download a copy of the Dev branch or whichever branch you want to test locally and extract the contents to a safe location.
[Azure/azure-functions-host: The host/runtime that powers Azure Functions (github.com)](https://github.com/Azure/azure-functions-host)

Open up the WebJob.Script.sln with Visual Studios 2022

Set the WebJobs.Script.WebHost to the startup project
1. Right click the solution in the solution explorer
2. Click on Properties
3. Select "Single startup project"
4. select WebJobs.Script.WebHost 

![image](https://user-images.githubusercontent.com/92878154/151272413-bf79f37b-09d0-4efb-a374-de1595d59c95.png)

With the Properties opened up…
1. Select Configuration under the Configuration Properties
2. Set every project EXCEPT the WebJobs.Script.WebHost Configuration to "WebJobs.Script.WebHost.Core"

![image](https://user-images.githubusercontent.com/92878154/151272443-e7626a79-94c2-4de7-8c2f-b26994f99e54.png)

Now we need to add some Environment Variables.
1. Click Debug from the top menu and select WebJobs.Script.WebHost Debug Properties

![image](https://user-images.githubusercontent.com/92878154/151272524-60f71233-5833-46f9-87a9-8cd1812b3f07.png)

Now Select WebJosbs.Script.WebHost as the launching program
1. Add in 2 variables, Note that they may already be there. 

```
ASPNETCORE_ENVIRONMENT=Development
AZURE_FUNCTIONS_ENVIRONMENT=Development
```
![image](https://user-images.githubusercontent.com/92878154/151272843-02bb5483-5060-4577-8aab-3ab3368af5c7.png)

At this point, we want to verify that host will come up locally for us.

Ensure that WebJobs.Script.WebHost shows up in the top menu like below, and click on the debug button.
![image](https://user-images.githubusercontent.com/92878154/151273069-a3c08727-85eb-4fe7-894c-a1ff2a756db5.png)

It may take a minute to come up.
You can view the Console window that opens up, which will contain some logging info during the startup.

After it is running it should automatically open a browser window which will show that your functions x.0 app is up and running.

![image](https://user-images.githubusercontent.com/92878154/151273117-75c28b07-ced9-4f49-825d-e8c7671facb5.png)


## Setting Up DotNet Functions

To get the Locally running function host to identify and trigger a locally stored and built function is pretty straight-forward.

First we just open Visual Studios 2022 and create a simple HttpTrigger (I will mention other trigger types later).

*Note: That when creating an HttpTrigger to select the Authorization Level as Anonymous. This is because when running the function host locally, it does not create the function keys used to trigger the function.*

Once you are ready to publish the function, right click on the function in the solution explorer and click publish.

![image](https://user-images.githubusercontent.com/92878154/151273252-f97a2a5e-f74d-4337-a332-e15b8103eb71.png)

Rather then selecting the usual Azure, we will select Folder and click next.

![image](https://user-images.githubusercontent.com/92878154/151273275-126fea57-2b09-4580-a20e-5bd95097a9fe.png)

On the Publish page, select the folder path of the published content. Remember that it is relative to the project path.

*Note: You will need the full path for the final section.*

![image](https://user-images.githubusercontent.com/92878154/151273310-15e87abb-e23b-4d0d-90df-10a1782c242d.png)

Once you are on this scree, you can click Publish.

![image](https://user-images.githubusercontent.com/92878154/151273383-1ae94c66-831a-48b5-8e4c-1b5e3ca19b4b.png)

After it has successfully published you can click on the Target location link  to open the location in a file explorer window.

Select the Url bar and highlight the full path and copy it to the clipboard

![image](https://user-images.githubusercontent.com/92878154/151273401-664b9540-8c3d-4e19-ae2a-e5b3d4eb2d70.png)


## Configuring and trigger the local function with the locally running functions host

We now need to add the full path to the locally published function to the functions host.

Open back up the Function Host sln in Visual Studios 2022, if it isn't already still open.

Once it is open, select Debug from the top menu bar and select WebJobs.Script.WebHost Debug Properties

![image](https://user-images.githubusercontent.com/92878154/151273450-ac3e7f17-ea5e-4930-a472-0a595d30a08e.png)

Now select the WebJobs.Script.WebHost and we are going to add a new Environment variable to the list.

Add the AzureWebJobsScriptRoot which will have the value of the full path to the published folder from earlier like below.

*Note: Remember to separate the variables with a comma (,)*

![image](https://user-images.githubusercontent.com/92878154/151273471-c250afe0-112a-45a9-a7f5-2f65acce5cc4.png)


## Using another Function trigger type and adding dependencies

In order to use another Function Trigger type, we need to add in the AzureWebJobsStorage appSettings and any other needed appSetting.

Once you created and published to folder a function of another trigger type, such as a QueueTrigger, we need to add the needed dependency / settings and connection string.

In the solution Explorer expand the WebJobs.Script.WebHost

![image](https://user-images.githubusercontent.com/92878154/151273552-d5ccc117-b415-42de-a0dd-e5122b6b00f4.png)

Scroll down and you will see appsettings.json, double click to open it.

![image](https://user-images.githubusercontent.com/92878154/151273582-9c7e1063-e67f-4942-82ff-ff0766a8f1e1.png)

Now we will add in the AzureWebJobsStorage and QueueConnectionString to the appsettings.json

*Note: Your appsettings.json may look a little different then mine.*

The AzureWebJobsStorage is the connection string to a Azure blob storage account.

The QueueConnectionString is the connection string used by my Queue Trigger function to connect to the storage queue.

*Note: Your appsetting name may be different then mine, depending on what you named it in your function*

![image](https://user-images.githubusercontent.com/92878154/151273614-3792b99f-68c6-4600-a9a5-515b7798ac9b.png)


## Adding in Additional Logging

To add in additional logging output, we can override the Functions host logging levels.

*Note: the below settings are recommended for Debug environment only and should not be used in a fully deployed function or functions host.*

```
 "AzureFunctionsJobHost__logging__fileLoggingMode": "always",
  "AzureFunctionsJobHost__logging__console__isEnabled": "true",
  "AzureFunctionsJobHost__logging__logLevel__default": "Trace",
  "AzureFunctionsJobHost__logging__logLevel__Host.Results": "Trace",
  "AzureFunctionsJobHost__logging__logLevel__Function": "Trace",
  "AzureFunctionsJobHost__logging__logLevel__Host.Aggregator": "Trace",
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  }
```

![image](https://user-images.githubusercontent.com/92878154/151273679-2f02aa49-0502-4660-bca4-f894a2c7a692.png)











