---
title: 'Quick Start: app maken-LUIS'
description: In deze Quick start ziet u hoe u een LUIS-app maakt die gebruikmaakt van het vooraf ontwikkelde domein `HomeAutomation` om verlichting en apparaten in en uit te scha kelen. Dit vooraf gemaakte domein biedt u intenties, entiteiten voorbeelden van utterances. Wanneer u klaar bent, hebt u een LUIS-eindpunt in de cloud.
ms.topic: quickstart
ms.date: 05/05/2020
ms.openlocfilehash: 186a60a7fd8315d68718ceedd3b5cadb4d3645e8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589125"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Snelstart: Een vooraf gemaakte app voor huisautomatisering gebruiken

In deze snelstart maakt u een LUIS-app die gebruikmaakt van het vooraf gemaakte domein `HomeAutomation` voor het in- en uitschakelen van lichten en apparaten. Dit vooraf gemaakte domein biedt u intenties, entiteiten voorbeelden van utterances. Wanneer u klaar bent, hebt u een LUIS-eindpunt in de cloud.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Een nieuwe app maken
U kunt uw toepassingen maken en beheren in **Mijn apps**.

1. Selecteer in de lijst mijn apps **+ nieuwe app voor conversatie**en selecteer vervolgens in de lijst met opties **+ nieuwe app voor gesprek** opnieuw.

1. Geef uw toepassing een naam in het dialoog venster `Home Automation` .
1. Selecteer **Engels** als cultuur.
1. Voer een optionele beschrijving in.
1. Selecteer geen Voorspellings resource als u de resource nog niet hebt gemaakt. Als u het Voorspellings eindpunt van uw app (staging of productie) wilt gebruiken, moet u een Voorspellings bron toewijzen.
1. Selecteer **Done**.

    LUIS maakt de app.

    ![Geef in het dialoog venster de naam Start Automation van uw toepassing op](./media/create-new-app-details.png)

    >[!NOTE]
    >De cultuur kan niet worden gewijzigd nadat de toepassing is gemaakt.

## <a name="add-prebuilt-domain"></a>Vooraf gemaakt domein toevoegen

1. Selecteer in de linkernavigatiebalk vooraf **gebouwde domeinen**.
1. Zoek naar **HomeAutomation**.
1. Selecteer **domein toevoegen** op de HomeAutomation-kaart.

    ![Selecteer vooraf gemaakte domeinen en zoek vervolgens naar ' HomeAutomation '. Selecteer domein toevoegen op de HomeAutomation-kaart.](media/luis-quickstart-new-app/home-automation.png)

    Wanneer het vooraf gemaakte domein is toegevoegd, wordt in het vak met vooraf gemaakte domeinen de knop **Remove domain** weergegeven.

## <a name="intents-and-entities"></a>Intenties en entiteiten

1. Selecteer **intenties** om de HomeAutomation-domein intentie te controleren. De vooraf ontwikkelde domein intentie hebben voor beeld-uitingen.

    ![Scherm opname van de lijst met HomeAutomation intentie](media/luis-quickstart-new-app/home-automation-intents.png "Scherm opname van de lijst met HomeAutomation intentie")

    > [!NOTE]
    > **None** is een intentie die door alle LUIS-apps wordt geboden. U kunt deze gebruiken voor het afhandelen van utterances die niet overeenkomen met de functionaliteit die uw app biedt.

1. Selecteer de intentie **HomeAutomation.TurnOff**. De intentie bevat een lijst met voor beelden van uitingen die zijn gelabeld met entiteiten.

    > [!div class="mx-imgBorder"]
    > [![Schermopname van de intentie HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Schermopname van de intentie HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>LUIS-app trainen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Uw app testen
Nadat u uw app hebt getraind, kunt u deze testen.

1. Selecteer **test** in de rechter navigatie.

1. Typ een test utterance `Turn off the lights` in het deel venster interactieve test en druk op ENTER.

    ```
    Turn off the lights
    ```

    In dit voor beeld `Turn off the lights` is het goed geïdentificeerd als de bovenste Score intentie van **HomeAutomation. TurnOff**.

    ![Schermafbeelding van deelvenster Test met utterance gemarkeerd](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Selecteer **controleren** om meer informatie over de voor spelling te bekijken.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding van het test paneel met inspectie-informatie](media/luis-quickstart-new-app/test.png)

1. Sluit het test venster.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>App publiceren om eindpunt-URL op te vragen

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Een query uitvoeren op het prediction-eind punt van de V3 API

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. Zorg ervoor dat in de adres balk van de browser voor de query reeks de volgende naam-en waarde balken in de URL staan. Als ze niet voor komen in de query reeks, voegt u deze toe:

    |Naam/waarde-paar|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. Ga in de adres balk van de browser naar het einde van de URL en voer `turn off the living room light` de waarde voor de _query_ in en druk op ENTER.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.984315455
                },
                "HomeAutomation.QueryState": {
                    "score": 0.009912962
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.00626645749
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.00572059769
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.00379381469
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00366983772
                },
                "None": {
                    "score": 0.000623856
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.907323956,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

    Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

U kunt het eindpunt aanroepen vanuit code:

> [!div class="nextstepaction"]
> [Een LUIS-eindpunt aanroepen met behulp van code](luis-get-started-cs-get-intent.md)
