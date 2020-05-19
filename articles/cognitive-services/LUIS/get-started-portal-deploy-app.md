---
title: 'Snelstartgids: een app implementeren met behulp van de LUIS-Portal'
description: In deze Quick start ziet u hoe u een app implementeert door een Voorspellings eindpunt resource te maken, de resource, training en publicatie van de app toe te wijzen.
ms.topic: quickstart
ms.date: 05/06/2020
ms.openlocfilehash: 9b22f1eda822b5354f0b434a04c2ea03e4c0ff2a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585059"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Snelstartgids: een app implementeren in de LUIS-Portal

Wanneer uw LUIS-app gereed is om utterance voor spellingen te retour neren naar een client toepassing (bijvoorbeeld een chat-bot), moet u de app implementeren in het Voorspellings eindpunt.

In deze Quick Start leert u hoe u een toepassing implementeert. U maakt een Voorspellings eindpunt resource, wijst de resource toe aan de app, Train de app en publiceert de app.

## <a name="prerequisites"></a>Vereisten

* Een [Azure-abonnement](https://azure.microsoft.com/free)ophalen.
* Voltooi de [Snelstartgids van de vorige Portal](get-started-portal-build-app.md) of [down load en importeer de app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).
* Als u apps hebt die de pre-date verificatie van Azure-bronnen hebben, kunt u deze [migreren naar een Azure-resource](luis-migration-authoring.md). Sommige Portal pagina's zien er anders uit wanneer e-mail verificatie actief is.

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Wijs de resource sleutel toe aan de LUIS-app in de LUIS-Portal

Telkens wanneer u een nieuwe ontwerp-of query Voorspellings bron voor LUIS maakt, moet u de resource toewijzen aan de LUIS-app. Nadat deze is toegewezen, hoeft u deze stap niet opnieuw uit te voeren tenzij u een nieuwe resource maakt. U kunt een nieuwe resource maken om de regio's van uw app uit te breiden of om een hoger aantal Voorspellings query's te ondersteunen.

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai) en kies de **myEnglishApp** -app in de lijst met apps.

1. Selecteer **beheren** in het menu in de rechter bovenhoek en selecteer vervolgens **Azure-resources**.

1. Als u de LUIS wilt toevoegen, selecteert u **Voorspellings bron toevoegen**.

    ![Als u de LUIS-Voorspellings resource wilt toevoegen, selecteert u Voorspellings resource toevoegen](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Selecteer uw Tenant, abonnement en resource naam. Selecteer **resource toewijzen**.

   ![Een resource toewijzen aan uw app](./media/get-started-portal-deploy-app/assign-resource.png)

1. Voer dezelfde stappen uit om de ontwerp sleutel toe te voegen aan uw app.

1. Zoek de nieuwe rij in de tabel voor de nieuwe Voorspellings resource en kopieer de URL van het eind punt. Het is op de juiste wijze ingeconstrueerd voor `HTTP GET` het maken van een aanvraag naar het Luis API-eind punt voor een voor spelling.

> [!TIP]
> Als u van plan bent om actief leren te gebruiken om uw LUIS-app te verbeteren, selecteert u **query parameters wijzigen** en selecteert u **logboeken opslaan**. Met deze actie wordt de voorbeeld-URL gewijzigd door de `log=true` query string-para meter toe te voegen. Kopieer en gebruik de gewijzigde voorbeeld query-URL bij het maken van voorspellings query's voor het runtime-eind punt.

## <a name="train-the-app"></a>De app trainen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-the-prediction-endpoint"></a>De app publiceren naar het Voorspellings eindpunt

[!INCLUDE [LUIS How to Train steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Voorspellings eindpunt aanvraag

In de portal `query=` wordt aan het einde van de URL waar de utterance van de gebruiker wordt toegevoegd aan de GET-aanvraag. Na de `query=` voert u dezelfde gebruikers utterance in die aan het einde van de vorige Snelstartgids zijn gebruikt:

```Is there a form named hrf-234098```

Zorg ervoor dat de query reeks de volgende paren bevat:

* `show-all-intents=true`
* `verbose=true`

In de browser wordt het antwoord weer gegeven:

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

Als u hetzelfde niveau van informatie in het test venster wilt zien, moet u de app publiceren. Nadat de app is gepubliceerd, selecteert u **vergelijken met gepubliceerd** in het test venster. Gebruik **JSON-weer gave weer geven** in het gepubliceerde test venster om dezelfde JSON te zien als de vorige stap. Op deze manier kunt u wijzigingen vergelijken met de huidige app waaraan u werkt met een app die naar het eind punt wordt gepubliceerd.

[![Huidige bewerking vergelijken versus gepubliceerde versie van de app](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze Snelstartgids, selecteert u **mijn apps** in het bovenste navigatie menu. Schakel het selectie vakje van de app in de lijst in en selecteer vervolgens **verwijderen** in de context werkbalk boven de lijst.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Algemene intenties en entiteiten identificeren](luis-tutorial-prebuilt-intents-entities.md)
