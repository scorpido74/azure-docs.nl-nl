---
title: Intentie met REST-aanroep in Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 4e2fb81b19694136896b1dee07c3bd74c63fc01b
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414468"
---
## <a name="prerequisites"></a>Vereisten

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio code](https://code.visualstudio.com/) of uw favoriete IDE
* ID van open bare app: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="get-luis-key"></a>LUIS-sleutel ophalen

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent-programmatically"></a>De intentie programmatisch ophalen

Gebruik Java om het [Voorspellings eindpunt](https://aka.ms/luis-apim-v3-prediction) te doorzoeken en een Voorspellings resultaat te krijgen.

1. Maak een submap met de naam `lib` en kopieer deze in de volgende Java-bibliotheken:

    * [Commons-logging-1.2. jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3. jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6. jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. Kopieer de volgende code om een klasse te maken in een bestand met de naam `Predict.java`:

    ```java
    import java.io.*;
    import java.net.URI;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    
    public class Predict {
    
        public static void main(String[] args) 
        {
            HttpClient httpclient = HttpClients.createDefault();
    
            try
            {
    
                // The ID of a public sample LUIS app that recognizes intents for turning on and off lights
                String AppId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";
                
                // Add your endpoint key 
                String Key = "YOUR-KEY";
    
                // Add your endpoint, example is westus.api.cognitive.microsoft.com
                String Endpoint = "YOUR-ENDPOINT";
    
                String Utterance = "turn on all lights";
    
                // Begin endpoint URL string building
                URIBuilder endpointURLbuilder = new URIBuilder("https://" + Endpoint + "/luis/prediction/v3.0/apps/" + AppId + "/slots/production/predict?");
    
                // query string params
                endpointURLbuilder.setParameter("query", Utterance);
                endpointURLbuilder.setParameter("subscription-key", Key);
                endpointURLbuilder.setParameter("show-all-intents", "true");
                endpointURLbuilder.setParameter("verbose", "true");
    
                // create URL from string
                URI endpointURL = endpointURLbuilder.build();
    
                // create HTTP object from URL
                HttpGet request = new HttpGet(endpointURL);
    
                // access LUIS endpoint - analyze text
                HttpResponse response = httpclient.execute(request);
    
                // get response
                HttpEntity entity = response.getEntity();
    
    
                if (entity != null) 
                {
                    System.out.println(EntityUtils.toString(entity));
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
    * `YOUR-ENDPOINT` met uw eind punt. Bijvoorbeeld `westus2.api.cognitive.microsoft.com`.


1. Compileer het Java-programma vanaf de opdracht regel: 

    ```console
    javac -cp ":lib/*" Predict.java
    ```

1. Voer het Java-programma uit vanaf de opdracht regel:

    ```console
    java -cp ":lib/*" Predict
    ```

1. Bekijk het Voorspellings antwoord dat wordt geretourneerd als JSON:

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Het JSON-antwoord dat is opgemaakt voor de Lees baarheid: 

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="luis-keys"></a>LUIS-sleutels

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze Quick Start, verwijdert u het bestand uit het bestands systeem. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uitingen en Train toevoegen met Java](../get-started-get-model-rest-apis.md)