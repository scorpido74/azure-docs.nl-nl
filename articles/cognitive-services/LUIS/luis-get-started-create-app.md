---
title: 'Quick Start: app maken-LUIS'
titleSuffix: Azure Cognitive Services
description: Maak een LUIS-app die gebruikmaakt van het vooraf gemaakte domein `HomeAutomation` voor het in- en uitschakelen van lichten en apparaten. Dit vooraf gemaakte domein biedt u intenties, entiteiten voorbeelden van utterances. Wanneer u klaar bent, hebt u een LUIS-eindpunt in de cloud.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 748c51e74db20ac101dc2dff0d924567acded114
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703240"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Snelstartgids: Een vooraf gemaakte app voor huisautomatisering gebruiken

In deze snelstart maakt u een LUIS-app die gebruikmaakt van het vooraf gemaakte domein `HomeAutomation` voor het in- en uitschakelen van lichten en apparaten. Dit vooraf gemaakte domein biedt u intenties, entiteiten voorbeelden van utterances. Wanneer u klaar bent, hebt u een LUIS-eindpunt in de cloud.

## <a name="prerequisites"></a>Vereisten

Voor dit artikel hebt u een gratis LUIS-account nodig, dat u kunt maken in de LUIS-portal op [https://www.luis.ai](https://www.luis.ai). 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>Een nieuwe app maken
U kunt uw toepassingen maken en beheren in **Mijn apps**. 

1. Selecteer **Create new app**.

    [![Schermafbeelding van de lijst met apps](media/luis-quickstart-new-app/app-list.png "Schermafbeelding van de lijst met apps")](media/luis-quickstart-new-app/app-list.png)

1. Geef in het dialoogvenster uw toepassing de naam 'Home Automation'.

    [![Schermafbeelding van het dialoogvenster Nieuwe app maken](media/luis-quickstart-new-app/create-new-app-dialog.png "Schermafbeelding van het dialoogvenster Nieuwe app maken")](media/luis-quickstart-new-app/create-new-app-dialog.png)

1. Kies de cultuur voor uw toepassing. Kies voor deze Home Automation-app de optie Engels. Selecteer vervolgens **Done**. De Home Automation-app wordt door LUIS gemaakt. 

    >[!NOTE]
    >De cultuur kan niet worden gewijzigd nadat de toepassing is gemaakt. 

## <a name="add-prebuilt-domain"></a>Vooraf gemaakt domein toevoegen

Selecteer **Prebuilt domains** in het navigatiedeelvenster aan de linkerkant. Zoek vervolgens naar 'Home'. Selecteer **Add domain**.

[![Schermafbeelding van het Home Automation-domein dat is te zien in het menu met vooraf gemaakte domeinen](media/luis-quickstart-new-app/home-automation.png "Schermafbeelding van het Home Automation-domein dat is te zien in het menu met vooraf gemaakte domeinen")](media/luis-quickstart-new-app/home-automation.png)

Wanneer het vooraf gemaakte domein is toegevoegd, wordt in het vak met vooraf gemaakte domeinen de knop **Remove domain** weergegeven.

[![Schermafbeelding van het Home Automation-domein met een verwijderknop](media/luis-quickstart-new-app/remove-domain.png "Schermafbeelding van het Home Automation-domein met een verwijderknop")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Intenties en entiteiten

Selecteer **Intents** in het navigatiedeelvenster aan de linkerkant om de intenties van het domein 'Home Automation' te controleren. Elke intentie bevat voorbeelden van utterances.

Scherm afbeelding van de lijst scherm afbeelding van ![HomeAutomation intenties](media/luis-quickstart-new-app/home-automation-intents.png "van de lijst met HomeAutomation intentiesen")]

> [!NOTE]
> **None** is een intentie die door alle LUIS-apps wordt geboden. U kunt deze gebruiken voor het afhandelen van utterances die niet overeenkomen met de functionaliteit die uw app biedt. 

Selecteer de intentie **HomeAutomation.TurnOff**. U kunt zien dat de intentie een lijst met utterances bevat die zijn gelabeld met entiteiten.

[![Schermafbeelding van de intentie HomeAutomation.TurnOff bedoeling](media/luis-quickstart-new-app/home-automation-turnoff.png "Schermafbeelding van de intentie HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>LUIS-app trainen

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Uw app testen
Nadat u uw app hebt getraind, kunt u deze testen. Selecteer **Test** in de bovenste navigatiebalk. Typ een test-utterance, zoals 'Turn off the lights' in het interactieve testdeelvenster en druk op Enter. 

```
Turn off the lights
```

Controleer of de best scorende intentie overeenkomt met de intentie die u verwachtte voor elke test-utterance.

In dit voor beeld wordt `Turn off the lights` correct geïdentificeerd als de bovenste Score intentie van **HomeAutomation. TurnOff**.

[![Schermafbeelding van het testdeelvenster met een utterance gemarkeerd](media/luis-quickstart-new-app/test.png "Schermafbeelding van het testdeelvenster met een utterance gemarkeerd")](media/luis-quickstart-new-app/test.png)


Selecteer **controleren** om meer informatie over de voor spelling te bekijken.

![Scherm opname van het test paneel met utterance gemarkeerd](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

Selecteer **Test** nogmaals om het deelvenster samen te vouwen. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>App publiceren om eindpunt-URL op te vragen

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-v2-api-prediction-endpoint"></a>Een query uitvoeren op het v2 API-Voorspellings eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Ga naar het einde van de URL in het adres, voer `turn off the living room light` in en druk op Enter. 

    #### <a name="v2-prediction-endpointtabv2"></a>[V2-Voorspellings eindpunt](#tab/V2)

    `https://<region>.api.cognitive.microsoft.com/luis/**v2.0**/apps/<appID>?subscription-key=<YOUR_KEY>&**q=<user-utterance-text>**`

    In de browser wordt de **v2 API** -versie van het JSON-antwoord van uw http-eind punt weer gegeven.

    ```json
    {
      "query": "turn off the lights",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.995867
      },
      "entities": [
        {
          "entity": "lights",
          "type": "HomeAutomation.DeviceType",
          "startIndex": 13,
          "endIndex": 18,
          "resolution": {
            "values": [
              "light"
            ]
          }
        }
      ]
    }
    ```
    
    #### <a name="v3-prediction-endpointtabv3"></a>[V3-Voorspellings eindpunt](#tab/V3)

    Voor een [v3 API-query](luis-migration-api-v3.md)wijzigt u in de browser de HTTPS-aanvraag van de methode Get, waarbij u de waarden van punt haken wijzigt voor uw eigen waarden.     

    `https://<region>.api.cognitive.microsoft.com/luis/**v3.0-preview**/apps/<appID>/**slots**/**production**/**predict**?subscription-key=<YOUR_KEY>&**query=<user-utterance-text>**`

    ```json
    {
        "query": "turn off the lights",
        "prediction": {
            "normalizedQuery": "turn off the lights",
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.99649024
                }
            },
            "entities": {
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ]
            }
        }
    }
    ```


    Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).
    
    * * * 

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

U kunt het eindpunt aanroepen vanuit code:

> [!div class="nextstepaction"]
> [Een LUIS-eindpunt aanroepen met behulp van code](luis-get-started-cs-get-intent.md)
