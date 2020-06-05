---
title: Model met REST-aanroep in Java ophalen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/03/2020
ms.author: diberry
ms.openlocfilehash: f6fb3452f2c5540617a6d59f9c81421c7de2161f
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84416362"
---
[Referentie documentatie](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)  |  Voor [beeld](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/LUIS/java-model-with-rest/Model.java)

## <a name="prerequisites"></a>Vereisten

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio code](https://code.visualstudio.com/) of uw favoriete IDE

## <a name="example-utterances-json-file"></a>JSON-bestand met voorbeeldutterances

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Model via een programma wijzigen

1. Maak een nieuwe map voor uw Java-project, zoals `java-model-with-rest` .

1. Maak een submap `lib` met de naam en kopieer deze in de volgende Java-bibliotheken naar de `lib` submap:

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. Maak een nieuw bestand met de naam `Model.java`. Voeg de volgende code toe:

    [!code-java[Code snippet](~/cognitive-services-quickstart-code/java/LUIS/java-model-with-rest/Model.java)]

1. Vervang de waarden die beginnen met `YOUR-` met uw eigen waarden.

    |Informatie|Functie|
    |--|--|
    |`YOUR-APP-ID`| De ID van uw LUIS-app. |
    |`YOUR-AUTHORING-KEY`|De bewerkings sleutel voor uw 32-teken.|
    |`YOUR-AUTHORING-ENDPOINT`| Het eind punt van de ontwerp-URL. Bijvoorbeeld `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. U stelt de naam van de resource in wanneer u de resource hebt gemaakt.|

    Toegewezen sleutels en resources zijn zichtbaar in de LUIS-Portal in de sectie beheren op de pagina **Azure-resources** . De App-ID is beschikbaar in hetzelfde gedeelte beheren op de pagina **Toepassings instellingen** .

1. Voer bij een opdracht prompt in de map waarin u het bestand hebt gemaakt `Model.java` de volgende opdracht in om het Java-bestand te compileren:

    * Als u Windows gebruikt, gebruikt u deze opdracht:`javac -cp ";lib/*" Model.java`
    * Als u macOS of Linux gebruikt, gebruikt u deze opdracht:`javac -cp ":lib/*" Model.java`

1. Voer de Java-toepassing vanaf de opdracht regel uit door de volgende tekst in te voeren in de opdracht prompt:

    * Als u Windows gebruikt, gebruikt u deze opdracht:`java -cp ";lib/*" Model`
    * Als u macOS of Linux gebruikt, gebruikt u deze opdracht:`java -cp ":lib/*" Model`

1. Het ontwerp antwoord controleren:

    ```console
    [{"value":{"ExampleId":1137150691,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1137150692,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1137150693,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    {"statusId":9,"status":"Queued"}
    [{"modelId":"edb46abf-0000-41ab-beb2-a41a0fe1630f","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"a5030be2-616c-4648-bf2f-380fa9417d37","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"e4b6704b-1636-474c-9459-fe9ccbeba51c","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"031d3777-2a00-4a7a-9323-9a3280a30000","details":{"statusId":9,"status":"Queued","exampleCount":0}},{"modelId":"9250e7a1-06eb-4413-9432-ae132ed32583","details":{"statusId":3,"status":"InProgress","exampleCount":0,"progressSubstatus":"CollectingData"}}]
    ```

    Dit is de uitvoer die is opgemaakt voor de Lees baarheid:

    ```json
    [
      {
        "value": {
          "ExampleId": 1137150691,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150692,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150693,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    {
      "statusId": 9,
      "status": "Queued"
    }
    [
      {
        "modelId": "edb46abf-0000-41ab-beb2-a41a0fe1630f",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "a5030be2-616c-4648-bf2f-380fa9417d37",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "e4b6704b-1636-474c-9459-fe9ccbeba51c",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "031d3777-2a00-4a7a-9323-9a3280a30000",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "9250e7a1-06eb-4413-9432-ae132ed32583",
        "details": {
          "statusId": 3,
          "status": "InProgress",
          "exampleCount": 0,
          "progressSubstatus": "CollectingData"
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze Quick Start, verwijdert u de projectmap uit het bestands systeem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor een app](../luis-concept-best-practices.md)