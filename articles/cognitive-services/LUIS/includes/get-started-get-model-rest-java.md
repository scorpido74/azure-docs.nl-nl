---
title: Model met REST-aanroep in Java ophalen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: a8129bf057b0e80a5f656f5ab3d578156b18a23c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503778"
---
## <a name="prerequisites"></a>Vereisten

* Start sleutel.
* Importeer de [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) -app uit de cognitieve-Services-Language-Standing github-opslag plaats.
* De LUIS-toepassings-ID voor de geïmporteerde TravelAgent-app. De toepassings-id wordt weergegeven op het toepassingsdashboard.
* De versie-ID in de toepassing die de uitingen ontvangt. De standaard-id is '0.1'.
* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio code](https://code.visualstudio.com/) of uw favoriete IDE

## <a name="example-utterances-json-file"></a>JSON-bestand met voorbeeldutterances

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="get-luis-key"></a>LUIS-sleutel ophalen

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>Model via een programma wijzigen

Gebruik Go om een door de machine geleerde entiteits- [API](https://aka.ms/luis-apim-v3-authoring) aan de toepassing toe te voegen. 

1. Maak een submap met de naam `lib` en kopieer deze in de volgende Java-bibliotheken:

    * [Commons-logging-1.2. jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3. jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6. jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. Maak een nieuw bestand met de naam `Model.java`. Voeg de volgende code toe:


    ```java
    import java.io.*;
    import java.net.URI;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    
    //javac -cp ":lib/*" Model.java
    //java -cp ":lib/*" Model
    
    public class Model {
    
        public static void main(String[] args) 
        {
            try
            {
    
                // The ID of a public sample LUIS app that recognizes intents for turning on and off lights
                String AppId = "YOUR-APP-ID";
                
                // Add your endpoint key 
                String Key = "YOUR-KEY";
    
                // Add your endpoint, example is westus.api.cognitive.microsoft.com
                String Endpoint = "YOUR-ENDPOINT";
    
                String Utterance = "[{'text': 'go to Seattle today','intentName': 'BookFlight','entityLabels': [{'entityName': 'Location::LocationTo',"
                    + "'startCharIndex': 6,'endCharIndex': 12}]},{'text': 'a barking dog is annoying','intentName': 'None','entityLabels': []}]";
    
                String Version = "1.0";
    
                // Begin endpoint URL string building
                URIBuilder addUtteranceURL = new URIBuilder("https://" + Endpoint + "/luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/examples");
                URIBuilder trainURL = new URIBuilder("https://" + Endpoint + "/luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/train");
    
                // create URL from string
                URI addUtterancesURI = addUtteranceURL.build();
                URI trainURI = trainURL.build();
    
                // add utterances POST
                HttpClient addUtterancesClient = HttpClients.createDefault();
                HttpPost addutterancesRequest = new HttpPost(addUtterancesURI);
                addutterancesRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                addutterancesRequest.setHeader("Content-type","application/json");
                HttpResponse addutterancesResponse = addUtterancesClient.execute(addutterancesRequest);
                HttpEntity addutterancesEntity = addutterancesResponse.getEntity();
                if (addutterancesEntity != null) 
                {
                    System.out.println(EntityUtils.toString(addutterancesEntity));
                }
    
                // train POST
                HttpClient trainClient = HttpClients.createDefault();
                HttpPost trainRequest = new HttpPost(trainURI);
                trainRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                trainRequest.setHeader("Content-type","application/json");
                HttpResponse trainResponse = trainClient.execute(trainRequest);
                HttpEntity trainEntity = trainResponse.getEntity();
                if (trainEntity != null) 
                {
                    System.out.println(EntityUtils.toString(trainEntity));
                }
    
                // training status GET
                HttpClient trainStatusClient = HttpClients.createDefault();
                HttpGet trainStatusRequest = new HttpGet(trainURI);
                trainStatusRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                trainStatusRequest.setHeader("Content-type","application/json");
                HttpResponse trainStatusResponse = trainStatusClient.execute(trainStatusRequest);
                HttpEntity trainStatusEntity = trainStatusResponse.getEntity();
                if (trainStatusEntity != null) 
                {
                    System.out.println(EntityUtils.toString(trainStatusEntity));
                }            
            }
    
            catch (Exception e)
            {
                System.out.println(e.getMessage());
            }
        }   
    }
    ```
1. Vervang de volgende waarden:

    * `YOUR-KEY` met uw start sleutel
    * `YOUR-ENDPOINT` met uw eind punt, bijvoorbeeld `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` met de ID van uw app

1. Voer bij een opdracht prompt in de map waarin u het bestand hebt gemaakt de volgende opdracht in om het Java-bestand te compileren:

    ```console
    javac -cp ":lib/*" Model.java
    ```  

1. Voer de Java-toepassing vanaf de opdracht regel uit door de volgende tekst in te voeren in de opdracht prompt: 

    ```console
    java -cp ":lib/*" Model
    ```

## <a name="luis-keys"></a>LUIS-sleutels

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze Quick Start, verwijdert u het bestand uit het bestands systeem. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor een app](../luis-concept-best-practices.md)