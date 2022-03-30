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

