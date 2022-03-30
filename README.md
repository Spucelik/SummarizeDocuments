# Summarize text from large documents
Ever have a large document that you don’t have time to read and want a summary so you can determine if it’s worth investing the time?  The Summarize Text API in Cognitive Services was designed to extract key sentences in a document as they relate to the topic so you can quickly understand what the document is about.
## Overview
![overview](/images/1-Overview.png)
* The document is first uploaded to a SharePoint library
* Power Automate will OCR and extract the text of the document
* The text is then sent to the summarization API via an Azure Function.
* The resulting summary text is then updated in a multi-line text field in the SharePoint document library where it is indexed and searchable.

## Setup
In your Azure subscription you’ll need to configure the Cognitive Services Text Analytics feature.  This will setup all the necessary components you need, specifically the Endpoint to invoke the service and key to authenticate properly.  I created a new resource group on Azure to contain all the components we’ll need.

![Azure Setup](/images/2-Setup.png)

_Endpoint API needed to call the service_

![overview](/images/3-Setup.png)

_Key needed when calling the service_

Once you have the feature setup, you can deploy the [Azure Function](https://github.com/Spucelik/SummarizeDocumentAzureFunction).

## Power Automate
The core of solution resides in the [Power Automate solution](https://github.com/Spucelik/SummarizeDocuments/blob/df6ad85d32552144744e8637be192539c9761a5d/SummarizeDocumentsPowerAutomateSolution.zip).  Activities can be configured to process the document and OCR and extract the text needed to summarize the document.

![Power Automate](/images/4-PowerAutomate.png)

_PowerAutomate core activities_

To accomplish there are 2 main steps that need to be configured in Power Automate:
1.	Recognize Text In Image – use AI Builder in Power Automate to train a model that will “Extract all the text in photos and PDF Documents (OCR).  This will extract text in PDF (Image and text) documents which is what we see the most. 

    ![Power Automate](/images/5-PowerAutomate.png)

2.	HTTP call to an Azure function – The summarization API does not have a user friendly interface so I created an Azure function that will conduct all the processing necessary and returns the summary of the document.

## Putting it all together
Now that we have the results from AI Builder, we can send this to the Azure Function for processing.

### Send the contents of the file to AI Builder

![Power Automate](/images/6-PowerAutomate.png)

### Edit the OCRed text

The result of the AI Builder activity is a JSON string that needs to be parsed.  Ultimately what were looking for is the “text” attribute in the JSON file.  This will get appended to our variable as we loop through the entire JSON output.

![Power Automate](/images/7-JSON.png)

![Power Automate](/images/8-PowerAutomate.png)

_Loop through the JSON file extracting the “text” attribute_

### Deploy the Azure function
As part of this solution, I have created an Azure function that will need to be deployed to the resource group created earlier.  Download the Visual Studio solution from [GitHub](https://github.com/Spucelik/SummarizeDocumentAzureFunction) and update the AzureKeyCredential with a valid Key from the [Cognitive Services Text Analytics](https://portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) feature you created earlier.

![Text Analytics](/images/9-TextAnalytics.png)

Build and deploy the Azure function to the resource group created above.  This will provide you with the endpoint URL need to make the HTTP call in Power Automate.

![Power Automate](/images/10-HTTP.png)

### Call the Azure Function
Now that you have the Azure function deployed, we have everything configured to send the extracted text to the summarization API for processing.

![Power Automate](/images/11-HTTP.png)

Once you have the results back, update the library.

![Power Automate](/images/12-PowerAutomate.png)

The resulting text is then displayed in the library for users to view and search.

![Power Automate](/images/13-Library.png)