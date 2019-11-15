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

The Logic Apps Designer has opened, and your first trigger has already been created to connect to Twitter. Click on the plus-sign to sign in with your Twitter account to give Logic Apps access to Twitter and be able to collect tweets. A pop-up will appear, guiding you through the sign in process. Authorize the app to access Twitter. After you have done this, the pop-up will disappear and you will get back to the Logic Apps Designer. 

![image](https://user-images.githubusercontent.com/48012580/68971997-10df4c80-07eb-11ea-90c6-3d5d761a4973.png)

Now that you have signed in and connected it to your Twitter account, click on Continue. 

![image](https://user-images.githubusercontent.com/48012580/68972033-25bbe000-07eb-11ea-9531-1e53844602f5.png)

Specify what kind of hashtags and words you want to monitor. If a tweet contains that text, it will trigger the Logic App. You can also choose how often you want to check for these items. Since we will use this for stream analytics, make the intervals short. You can leave it at default or shorten or lengthen it.

![image](https://user-images.githubusercontent.com/48012580/68972047-30767500-07eb-11ea-8b92-1fbac133c9c2.png)

Click on +New step below to add a new action. All options for a new action will appear. Search for sentiment and you see the Text Analytics options show up. Click on Detect Sentiment. 

![image](https://user-images.githubusercontent.com/48012580/68972071-3d936400-07eb-11ea-8198-767a8e61dac5.png)

First, you need to specify the API connection. Fill in a name to give the API connection a name. Then fill in the key 1 and endpoint URL collected at the end of the creation of the Text Analytics resource. Click on Create. 

![image](https://user-images.githubusercontent.com/48012580/68972095-484df900-07eb-11ea-8c6d-14439f2cbbb8.png)

Now you can specify what you want to get from the Text Analytics API. Click to add a new parameter, two options will show. Check the box for Text and click somewhere else. 

![image](https://user-images.githubusercontent.com/48012580/68972121-5a2f9c00-07eb-11ea-9714-af9519bbeb8c.png)

You can now add a parameter that will be considered the text in which the sentiment will be detected. Click on bar which says “The text to analyze” to see the Dynamic content pop up on the right. Search for text in this pop up and select the “Tweet text”. 

![image](https://user-images.githubusercontent.com/48012580/68972150-64519a80-07eb-11ea-8204-9e5b1f8f6f8f.png)

Tweet text is now added as the text to analyze for sentiment detection. When this Logic App would run, it will be triggered by a new tweet being posted with the search terms we have specified. The first action will be that the tweet text is taken to detect the sentiment. The output of this first action will be a sentiment score between 0 and 1 with 0 being negative and 1 indicating a positive sentiment. 
We want to do more than that. So let’s add another step by clicking on +New step. Search for key phrase to find a new action. Select Key Phrases.

![image](https://user-images.githubusercontent.com/48012580/68972167-70d5f300-07eb-11ea-810e-8c4bccc7858d.png)

Similarly, to the previous step, we want to add a new parameter and check the box for Text. Click somewhere else on the canvas.

![image](https://user-images.githubusercontent.com/48012580/68972184-79c6c480-07eb-11ea-9e86-f662eb271d60.png)

Click on the field to add a text to analyze. The dynamic content pop-up will appear. Search for text and select “Tweet text” again. 

![image](https://user-images.githubusercontent.com/48012580/68972200-877c4a00-07eb-11ea-9820-6e7baae8d25c.png)

Now, we have all the necessary information to send to Event Hubs. Add a new action by clicking on +New step. Then search for event hub and select Send event.

![image](https://user-images.githubusercontent.com/48012580/68972595-80097080-07ec-11ea-8277-c01e8e7ffad2.png)

We first need to create the API connection with Event Hubs. At this point you need to have created an Event Hubs Namespace with an access policy before being able to configure this action. Give your API connection a name and select the Event Hubs Namespace you want to connect to. 

![image](https://user-images.githubusercontent.com/48012580/68972625-8ef02300-07ec-11ea-9528-78b56163d7a1.png)

After selecting the Event Hubs Namespace, select the policy (use RootManageSharedAccessKey) and click on Create. 

![image](https://user-images.githubusercontent.com/48012580/68972667-a62f1080-07ec-11ea-8ea6-85ea29b11a0f.png)

Add a new parameter by click on the words, and check the box for Content. Click somewhere else again.

![image](https://user-images.githubusercontent.com/48012580/68972701-b34bff80-07ec-11ea-8e64-216191f41ace.png)

Click on Name of the Event hub to select which event hub you want to send your data to. 

![image](https://user-images.githubusercontent.com/48012580/68972722-bcd56780-07ec-11ea-9507-8cd145d87156.png)

Click on the field with “Content of the event” to add content. 

![image](https://user-images.githubusercontent.com/48012580/68972870-19d11d80-07ed-11ea-9440-a53d064a4c7c.png)

In that field, paste the following code (we will be replacing certain values):

`{
"Topic":"KeyPhrasesItem",
"SentimentScore":"Score",
"created_at":"CreatedAt",
"Author":"Name",
"text":"TweetText"
}`

For each field, we will replace the second value with dynamic content using the pop-up on the right. First, remove KeyPhrasesItem, make sure your cursor is in between the quotation marks where it used to say KeyPhrasesItem, search in the dynamic content pop-up for key phrases and select the Key Phrases Item. 

Since for every tweet, multiple key phrases are extracted, a “for each” loop will automatically be added when you select the Key Phrases Item. So your solution should look something like this now:

![image](https://user-images.githubusercontent.com/48012580/68972972-5bfa5f00-07ed-11ea-90c5-b5d00f2c13ca.png)

Click on Send event to go back to the content we were working on. A pop-up may appear telling you that it had to add a For each loop, just click on Got it and continue with the steps below. 

![image](https://user-images.githubusercontent.com/48012580/68972993-66b4f400-07ed-11ea-8ee8-3061f303ac67.png)

We will replace the second values with dynamic content for all fields. Let’s continue with the sentiment score. Remove Score, make sure your cursor is within quotation marks. In the dynamic content pop-up, search for score and select Score under the Detect Sentiment header.

![image](https://user-images.githubusercontent.com/48012580/68973017-76343d00-07ed-11ea-83f3-d38922965066.png)

Repeat these steps to find the dynamic content values to replace the second values in the content until you get to the following overview:

![image](https://user-images.githubusercontent.com/48012580/68973052-8f3cee00-07ed-11ea-8eb4-d80838758d94.png)

Save your Logic App on the left top of the Designer.

![image](https://user-images.githubusercontent.com/48012580/68973074-9a901980-07ed-11ea-9b60-0bd7411d01c9.png)

Once it’s saved, click on Run. 

![image](https://user-images.githubusercontent.com/48012580/68973085-a380eb00-07ed-11ea-8bdf-62330ac8dcba.png)

That’s it! Your Logic App will now run, every time a tweet with your specified search term is posted. If it’s successful, it should look like this:

![image](https://user-images.githubusercontent.com/48012580/68973105-ada2e980-07ed-11ea-965f-f9b211e9b449.png)

If you see an error anywhere, go back to the Logic App Designer to change your Logic App. 

If you continue with the DP-200 lab content (omit the Twitter Client part) and set up the Event Hub and Stream Analytics pipeline, the query in Stream Analytics remains the same and you will get a similar result. 
