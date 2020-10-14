---
title: 'Quickstart: een LUIS-app maken'
description: Deze quickstart laat zien hoe u een LUIS-app maakt die gebruikmaakt van het vooraf gemaakte domein `HomeAutomation` voor het in- en uitschakelen van lichten en apparaten. Dit vooraf gemaakte domein biedt u intenties, entiteiten voorbeelden van utterances. Wanneer u klaar bent, hebt u een LUIS-eindpunt in de cloud.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/05/2020
ms.openlocfilehash: 28bf79b61c0278a3f45820a23cd2c69f0b609700
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91316479"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Snelstart: Een vooraf gemaakte app voor huisautomatisering gebruiken

In deze snelstart maakt u een LUIS-app die gebruikmaakt van het vooraf gemaakte domein `HomeAutomation` voor het in- en uitschakelen van lichten en apparaten. Dit vooraf gemaakte domein biedt u intenties, entiteiten voorbeelden van utterances. Wanneer u klaar bent, hebt u een LUIS-eindpunt in de cloud.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Een nieuwe app maken
U kunt uw toepassingen maken en beheren in **Mijn apps**.

1. Selecteer **+ Nieuwe app voor gesprek** in de lijst Mijn apps en selecteer vervolgens nogmaals **+ Nieuwe app voor gesprek** in de lijst met opties.

1. Geef in het dialoogvenster uw toepassing de naam `Home Automation`.
1. Selecteer **Engels** als de cultuur.
1. Voer eventueel een beschrijving in.
1. Selecteer geen voorspellingsresource als u de resource nog niet hebt gemaakt. Als u het voorspellingseindpunt van uw app (fasering of productie) wilt gebruiken, moet u een voorspellingsbron toewijzen.
1. Selecteer **Gereed**.

    De app wordt in LUIS gemaakt.

    ![Geef in het dialoogvenster uw toepassing de naam `Home Automation`](./media/create-new-app-details.png)

    >[!NOTE]
    >De cultuur kan niet worden gewijzigd nadat de toepassing is gemaakt.

## <a name="add-prebuilt-domain"></a>Vooraf gemaakt domein toevoegen

1. Selecteer **Vooraf gemaakte domeinen** in het navigatiedeelvenster links.
1. Zoek naar **HomeAutomation**.
1. Selecteer **Domein toevoegen** op de kaart HomeAutomation.

    > [!div class="mx-imgBorder"]
    > ![Selecteer 'Vooraf gemaakte domeinen' en zoek vervolgens naar 'HomeAutomation'. Selecteer 'Domein toevoegen' op de kaart HomeAutomation.](media/luis-quickstart-new-app/home-automation.png)

    Wanneer het vooraf gemaakte domein is toegevoegd, wordt in het vak met vooraf gemaakte domeinen de knop **Remove domain** weergegeven.

## <a name="intents-and-entities"></a>Intenties en entiteiten

1. Selecteer **Intenties** om de intenties van het HomeAutomation-domein te controleren. De intenties van de vooraf gemaakte domeinen bevatten voorbeelduitingen.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van de lijst met intenties van HomeAutomation](media/luis-quickstart-new-app/home-automation-intents.png "Schermopname van de lijst met intenties van HomeAutomation")

    > [!NOTE]
    > **None** is een intentie die door alle LUIS-apps wordt geboden. U kunt deze gebruiken voor het afhandelen van utterances die niet overeenkomen met de functionaliteit die uw app biedt.

1. Selecteer de intentie **HomeAutomation.TurnOff**. De intentie bevat een lijst met voorbeelduitingen die zijn gelabeld met entiteiten.

    > [!div class="mx-imgBorder"]
    > [![Schermopname van de intentie HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Schermopname van de intentie HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>LUIS-app trainen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Uw app testen
Nadat u uw app hebt getraind, kunt u deze testen.

1. Selecteer **Testen** in de navigatiebalk rechtsboven.

1. Typ een test-uiting, zoals `Turn off the lights` in het interactieve testdeelvenster en druk op Enter.

    ```
    Turn off the lights
    ```

    In dit voorbeeld wordt `Turn off the lights` correct geïdentificeerd als de best scorende intentie van **HomeAutomation.TurnOff**.

    ![Schermafbeelding van deelvenster Test met utterance gemarkeerd](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Selecteer **Inspecteren** om meer informatie over de voorspelling weer te geven.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van het testvenster met inspectie-informatie](media/luis-quickstart-new-app/test.png)

1. Sluit het testvenster.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>App publiceren om eindpunt-URL op te vragen

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Een query uitvoeren op het V3 API-voorspellingseindpunt

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. Zorg ervoor dat in de adresbalk van de browser voor de queryreeks de volgende naam- en waardebalken in de URL staan. Als ze niet voorkomen in de queryreeks, voegt u deze toe:

    |Naam/waarde-paar|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. Ga in de adresbalk van de browser naar het einde van de URL en voer `turn off the living room light` in als waarde voor de _query_ en druk vervolgens op Enter.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
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
                            "score": 0.902181149,
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

    Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

U kunt het eindpunt aanroepen vanuit code:

> [!div class="nextstepaction"]
> [Een LUIS-eindpunt aanroepen met behulp van code](luis-get-started-cs-get-intent.md)
