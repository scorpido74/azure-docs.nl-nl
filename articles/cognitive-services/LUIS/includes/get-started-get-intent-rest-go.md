---
title: Alles voor deel nemen met de REST-oproep in go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: b36e967e960ec6a2b39409ea7be5f3f98f69b9c8
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838494"
---
## <a name="prerequisites"></a>Vereisten

* [Go](https://golang.org/)-programmeertaal  
* [Visual Studio Code](https://code.visualstudio.com/)
* Id van openbare app: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS-sleutel ophalen

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>De intentie programmatisch ophalen

U kunt Go gebruiken voor toegang tot de dezelfde resultaten die u in het browservenster in de vorige stap hebt gezien. 

1. Maak een nieuw bestand met de naam `endpoint.go`. Voeg de volgende code toe:
    
   [!code-go[Go code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/go/endpoint.go?range=36-98)]

2. Voer, in dezelfde map waarin u het bestand hebt gemaakt, met een opdrachtprompt `go build endpoint.go` in om het Go-bestand te compileren. De opdrachtprompt retourneert geen informatie voor een geslaagde build.

3. Voer de Go-toepassing uit vanaf de opdrachtregel door de volgende tekst in te voeren in de opdrachtprompt: 

    ```CMD
    go run endpoint.go -appID df67dcdb-c37d-46af-88e1-8b97951ca1c2 -endpointKey <add-your-key> -region westus
    ```
    
    Vervang `<add-your-key>` door de waarde van de sleutel.  
    
    Het antwoord van de opdrachtregel is: 
    
    ```CMD
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    region has value westus
    utterance has value turn on the bedroom light
    response
    {
        "query": "turn on the bedroom light",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.809439957
        },
        "entities": [
            {
            "entity": "bedroom",
            "type": "HomeAutomation.Room",
            "startIndex": 12,
            "endIndex": 18,
            "score": 0.8065475
            }
        ]
    }
    ```


## <a name="luis-keys"></a>LUIS-sleutels

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze snelstart, sluit u het Visual Studio-project en verwijdert u de projectmap uit het bestandssysteem. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uitingen toevoegen en trainen met Go](../luis-get-started-go-add-utterance.md)