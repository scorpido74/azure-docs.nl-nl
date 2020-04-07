---
title: 'Snelstart: een app implementeren met de LUIS-portal'
description: Met deze snelle start ziet u hoe u een app implementeert door een voorspellingseindpuntbron te maken, de bron, de training en het publiceren van de app toe te wijzen.
ms.topic: quickstart
ms.date: 04/06/2020
ms.openlocfilehash: aaf86766c2357c5382b78cd4a35fd4b159e5c0f3
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756302"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Snelstart: een app implementeren in de LUIS-portal

Wanneer uw LUIS-app klaar is om utterancevoorspellingen terug te sturen naar een clienttoepassing (bijvoorbeeld een chatbot), moet u de app implementeren op het voorspellingseindpunt.

In deze quickstart leert u een toepassing te implementeren. U maakt een eindpuntbron voor voorspellingen, wijst de bron toe aan de app, traint de app en publiceert de app.

## <a name="prerequisites"></a>Vereisten

* Ontvang een [Azure-abonnement.](https://azure.microsoft.com/free)
* Voltooi de [vorige portal snelstart](get-started-portal-build-app.md) of [download en importeer de app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).
* Als u apps hebt die vooraf zijn datum voor Azure-bronverificatie, [migreert u naar een Azure-bron.](luis-migration-authoring.md) Sommige portalpagina's zien er anders uit wanneer e-mailverificatie van kracht is.

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>De resourcesleutel toewijzen aan de LUIS-app in de LUIS-portal

Elke keer dat u een nieuwe bron voor het ontwerpen of voorspellen van query's voor LUIS maakt, moet u de bron toewijzen aan de LUIS-app. Nadat deze is toegewezen, hoeft u deze stap niet opnieuw te doen, tenzij u een nieuwe bron maakt. U een nieuwe bron maken om de regio's van uw app uit te breiden of om een hoger aantal voorspellingsquery's te ondersteunen.

1. Meld u aan bij de [preview LUIS-portal](https://preview.luis.ai) en kies de **myEnglishApp-app** in de lijst met apps.

1. Selecteer **Beheren** in het menu rechtsboven en selecteer **Vervolgens Azure Resources**.

1. Als u de LUIS wilt toevoegen, selecteert u **Voorspellingsbron toevoegen**.

    ![Als u de LUIS-voorspellingsbron wilt toevoegen, selecteert u Voorspellingsbron toevoegen](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Selecteer uw tenant, abonnement en resourcenaam. Selecteer **Resource toewijzen**.

   ![Een resource toewijzen aan uw app](./media/get-started-portal-deploy-app/assign-resource.png)

1. Voer dezelfde stappen uit om de ontwerpsleutel aan uw app toe te voegen.

1. Zoek de nieuwe rij in de tabel voor de nieuwe voorspellingsbron en kopieer de URL van het eindpunt. Het is correct geconstrueerd om `HTTP GET` een aanvraag te doen voor het LUIS API-eindpunt voor een voorspelling.

> [!TIP]
> Als u Actief leren wilt gebruiken om uw LUIS-app te verbeteren, selecteert u **Queryparameters wijzigen** en selecteert **u Logboeken opslaan.** Met deze actie wordt de `log=true` voorbeeld-URL gewijzigd door de parameter querystring toe te voegen. Kopieer en gebruik de gewijzigde voorbeeldquery-URL bij het maken van voorspellingsquery's naar het eindpunt runtime.

## <a name="train-the-app"></a>De app trainen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>De app publiceren naar het voorspellingseindpunt

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Eindpuntaanvraag voor voorspelling

In de previewportal wordt `query=` aan het einde van de URL de utterance van de gebruiker toegevoegd aan het GET-verzoek. Voer `query=`na de : De volgende snelstart dezelfde gebruikersutterance in die aan het einde van de vorige quickstart wordt gebruikt:

```Is there a form named hrf-234098```

Controleer of de querytekenreeks de volgende paren bevat:

* `show-all-intents=true`
* `verbose=true`

De browser toont het antwoord:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
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

Als u ditzelfde niveau van informatie in het testvenster wilt zien, moet u de app publiceren. Nadat de app is gepubliceerd, selecteert u **Vergelijken met gepubliceerd** in het testvenster. Gebruik **Json-weergave weergeven** in het gepubliceerde testvenster om dezelfde JSON te zien als de vorige stap. Op deze manier u wijzigingen vergelijken met de huidige app waaraan u werkt met een app die wordt gepubliceerd naar het eindpunt.

[![Momenteel bewerken vergelijken met gepubliceerde versie van de app](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze quickstart, selecteert u **Mijn apps** in het bovenste navigatiemenu. Schakel het selectievakje van de app in de lijst in en selecteer **Verwijderen op** de contextwerkbalk boven de lijst.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Algemene intenties en entiteiten identificeren](luis-tutorial-prebuilt-intents-entities.md)
