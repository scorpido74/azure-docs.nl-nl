---
title: Download model met REST-oproep in Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 92552a9870f037555a6cde9daa67d3af112ccee7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368455"
---
## <a name="prerequisites"></a>Vereisten

* Azure Language Understanding - Authoring resource 32 tekensleutel en url voor het schrijven van eindpunten. Maken met de [Azure-portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) of [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importeer de [TravelAgent-app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) vanuit de GitHub-repository die gebruik maakt van de GitHub-opslagplaats voor cognitieve services.
* De LUIS toepassings-ID voor de geïmporteerde TravelAgent-app. De toepassings-id wordt weergegeven op het toepassingsdashboard.
* De versie-id in de toepassing die de utterances ontvangt. De standaard-id is '0.1'.
* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) of uw favoriete IDE

## <a name="example-utterances-json-file"></a>JSON-bestand met voorbeeldutterances

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Model programmatisch wijzigen

1. Maak een submap met de naam `lib` en kopie in de volgende java libs:

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

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

                // Add your endpoint, example is your-resource-name.api.cognitive.microsoft.com
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

1. Vervang de waarden `YOUR-` die beginnen met uw eigen waarden.

    |Informatie|Doel|
    |--|--|
    |`YOUR-KEY`|Uw 32 teken authoring toets.|
    |`YOUR-ENDPOINT`| Uw URL-eindpunt voor het ontwerpen. Bijvoorbeeld `replace-with-your-resource-name.api.cognitive.microsoft.com`. U stelt uw resourcenaam in toen u de resource maakte.|
    |`YOUR-APP-ID`| Uw LUIS-app-id. |

    Toegewezen sleutels en resources zijn zichtbaar in de LUIS-portal in de sectie Beheren op de pagina **Azure-bronnen.** De app-id is beschikbaar in dezelfde sectie Beheren, op de pagina **Toepassingsinstellingen.**

1. Voer de volgende opdracht in om het Java-bestand samen te stellen met een opdrachtprompt in dezelfde map als waar u het bestand hebt gemaakt:

    ```console
    javac -cp ":lib/*" Model.java
    ```

1. Voer de Java-toepassing uit vanaf de opdrachtregel door de volgende tekst in de opdrachtprompt in te voeren:

    ```console
    java -cp ":lib/*" Model
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze quickstart, verwijdert u het bestand uit het bestandssysteem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor een app](../luis-concept-best-practices.md)