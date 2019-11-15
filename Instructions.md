# TwitterLogicApp

## Overview
### Getting Twitter data into Event Hubs
In order to get data from Twitter into Event Hubs, we need to set up a Logic Apps that will be triggered by new tweets coming from Twitter, does Sentiment Analysis and Key Phrase Extraction (both part of the Text Analytics API), and send all this data to Event Hubs. Before setting up the Logic Apps, we therefore need the next things:
-	Cognitive Services resource (follow instructions below for this).
-	Event Hubs namespace resource (follow instructions of the [DP-200 lab](https://github.com/MicrosoftLearning/DP-200-Implementing-an-Azure-Data-Solution/blob/master/instructions/dp-200-06_instructions.md) for this).

> Make sure all resources exist in the same resource group to make it easier for Logic App to find the necessary resources. 

## Hands-on
### Sign in to the Azure portal
Sign in to the Azure portal with your Azure account credentials.

### Cognitive Services - Text Analytics
To be able to do Sentiment Analysis and Key Phrase Extraction, we need a Cognitive Services resource. Both of these features are part of the Text Analytics API. Go to the Azure portal and search for Text Analytics and click create, or use this [link](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics). 

**Property** | **Value**
---|---
Name | A descriptive name for your cognitive services resource. For example, TextAnalyticsResource.
Subscription | Select one of your available Azure subscriptions.
Location | The location of your cognitive service instance. Different locations may introduce latency, but have no impact on the runtime availability of your resource. Remember your Azure location, as you may need it when calling the Azure Cognitive Services.
Pricing tier | The cost of your Cognitive Services account depends on the options you choose and your usage. For more information, see the API pricing details. Choose the lowest possible for this 
Resource group | The Azure resource group that will contain your Cognitive Services resource. You can create a new group or add it to a pre-existing group. Use the same group for all resources related to this (i.e. same as Event Hubs Namespace and Logic Apps)

### Get the keys for your resource
1.	After your resource is successfully deployed, click on Go to resource under Next Steps.

![image](https://user-images.githubusercontent.com/48012580/68970604-f22b8680-07e7-11ea-8a9c-1a2d4400de9f.png)

2.	From the quickstart pane that opens, you can access your key and endpoint.

![image](https://user-images.githubusercontent.com/48012580/68970640-08d1dd80-07e8-11ea-910c-61262b1c7574.png)

## Logic App
### Create your logic app
From the Azure home page, in the search box, find and select Logic Apps.
![image](https://user-images.githubusercontent.com/48012580/68970716-37e84f00-07e8-11ea-95a7-2ab1708a43fe.png)
On the Logic Apps page, select Add.
![image](https://user-images.githubusercontent.com/48012580/68970741-46366b00-07e8-11ea-9990-dccf114750bf.png)
On the Logic App pane, provide details about your logic app as shown below. After you're done, select Create.
![image](https://user-images.githubusercontent.com/48012580/68970766-551d1d80-07e8-11ea-8662-291fc3df2de6.png)

**Property** | **Value** 
---|---
Name | Your logic app name
Subscription | Your Azure subscription name
Resource group | The name for the Azure resource group used to organize related resources. Put the Logic App in the same resource group as the Cognitive Service and Event Hubs Namespace. 
Location | The region where to store your logic app information. 
Log analytics | Keep the Off setting for diagnostic logging.

After Azure deploys your app, on the Azure toolbar, select Notifications > Go to resource for your deployed logic app.
![image](https://user-images.githubusercontent.com/48012580/68971561-0ec8be00-07ea-11ea-98f5-068fda5ce422.png)

Or, you can find and select your logic app by typing the name in the search box.
The Logic Apps Designer opens and shows a page with an introduction video and commonly used triggers. 

Next, add a trigger that fires when a new tweet is posted. Every logic app must start with a trigger, which fires when a specific event happens or when a specific condition is met. Each time the trigger fires, the Azure Logic Apps engine creates a logic app instance that starts and runs your workflow. 
![image](https://user-images.githubusercontent.com/48012580/68971586-2142f780-07ea-11ea-8ea6-c902d75f9b94.png)




