---
title: 'Snelstartgids: een app implementeren met behulp van de LUIS-Portal'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het implementeren van uw LUIS-app op het Voorspellings eindpunt nadat de app klaar is om utterance voor spellingen te retour neren naar een client toepassing, zoals een chat-bot. In deze Snelstartgids leert u hoe u een toepassing implementeert door een Voorspellings eindpunt resource te maken, de resource aan de app toe te wijzen, de app te trainen en de app te publiceren.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: ecae5c7db02436fe34fec19989f174504fd1e03a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488717"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Snelstartgids: een app implementeren in de LUIS-Portal

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]


Wanneer uw LUIS-app gereed is om utterance voor spellingen te retour neren naar een client toepassing (bijvoorbeeld een chat-bot), moet u de app implementeren in het Voorspellings eindpunt.

In deze Quick Start leert u hoe u een toepassing implementeert. U maakt een Voorspellings eindpunt resource, wijst de resource toe aan de app, Train de app en publiceert de app.

## <a name="prerequisites"></a>Vereisten

* Een [Azure-abonnement](https://azure.microsoft.com/free)ophalen.
* Voltooi de [Snelstartgids van de vorige Portal](get-started-portal-build-app.md) of [down load en importeer de app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>De eindpunt resource maken

U maakt de Voorspellings eindpunt resource in de Azure Portal. Deze bron mag alleen worden gebruikt voor eindpunt Voorspellings query's. Gebruik deze resource niet voor het ontwerpen van wijzigingen in de app.

1. Meld u aan bij de [Azure Portal](https://ms.portal.azure.com/).

1. Selecteer de groene **+** meldt u aan in het deel venster linksboven. Zoek naar `Cognitive Services` op Marketplace en selecteer deze.

1. Configureer het abonnement met de volgende instellingen:

   |Instelling|Waarde|Doel|
   |--|--|--|
   |Naam|`my-cognitive-service-resource`|De naam van de Azure-resource. U hebt deze naam nodig wanneer u de resource aan de app toewijst in de LUIS-Portal.|
   |Abonnement|Uw abonnement|Selecteer een van de abonnementen die zijn gekoppeld aan uw account.|
   |Locatie|**US - west**|De Azure-regio voor deze resource.|
   |Prijscategorie|**S0**|De standaard prijs categorie voor deze resource.|
   |Resourcegroep|`my-cognitive-service-resource-group`|Maak een nieuwe resource groep voor al uw cognitieve service bronnen. Wanneer u klaar bent met de resources, kunt u de resource groep verwijderen om uw abonnement op te schonen. |
   | | | |

   ![Keuze voor de Azure-API](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. Selecteer **maken** om de Azure-resource te maken.

   In de volgende sectie leert u hoe u deze nieuwe resource kunt verbinden met een LUIS-app in de LUIS-Portal.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Wijs de resource sleutel toe aan de LUIS-app in de LUIS-Portal

Telkens wanneer u een nieuwe resource voor LUIS maakt, moet u de resource toewijzen aan de LUIS-app. Nadat deze is toegewezen, hoeft u deze stap niet opnieuw uit te voeren tenzij u een nieuwe resource maakt. U kunt een nieuwe resource maken om de regio's van uw app uit te breiden of om een hoger aantal Voorspellings query's te ondersteunen.

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai) en kies de **myEnglishApp** -app in de lijst met apps.

1. Selecteer **beheren** in het menu in de rechter bovenhoek en selecteer vervolgens **Azure-resources**.

1. Als u de LUIS wilt toevoegen, selecteert u **Voorspellings bron toevoegen**.

    <!-- TBD: get screenshot-->

1. Selecteer uw Tenant, abonnement en resource naam. Selecteer **resource toewijzen**.

   ![Een resource toewijzen aan uw app](./media/get-started-portal-deploy-app/assign-resource.png)

1. De nieuwe rij in de tabel zoeken en de eind punt-URL kopiëren. Het is op de juiste wijze samengesteld om een `HTTP GET` aanvraag voor een voor spelling naar het LUIS-API-eind punt te maken.

## <a name="train-and-publish-the-app"></a>De app trainen en publiceren

Train de app wanneer u er klaar voor bent om deze te testen. Publiceer de app als u wilt dat de momenteel getrainde versie beschikbaar is voor client toepassingen van de eindpunt runtime voor het uitvoeren van query's.

1. Als de app niet is getraind, selecteert u **trainen** in het menu in de rechter bovenhoek.

1. Selecteer **publiceren** in het bovenste menu. Selecteer de productie sleuf en publiceer.

1. Wanneer de meldings balk wordt weer gegeven, is de publicatie voltooid.

1. Zoek op de pagina **Azure-resources** van de sectie beheren de lijst met toegewezen resources en de bijbehorende eind punt-url's.

1. Kopieer de voorbeeld query naar een browser venster en voeg uw gebruikers utterance toe als de para meter `query`.

## <a name="prediction-endpoint-request"></a>Voorspellings eindpunt aanvraag

De `query=` aan het einde van de URL is kort voor de **query** en is de plaats waar de utterance van de gebruiker wordt toegevoegd aan de GET-aanvraag. Nadat de `query=`, voert u dezelfde gebruikers utterance in die aan het einde van de vorige Snelstartgids worden gebruikt:

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

Als u hetzelfde niveau van informatie in het test venster wilt zien, moet u de app publiceren. Nadat de app is gepubliceerd, selecteert u **vergelijken met gepubliceerd** in het test venster. Gebruik **JSON-weer gave weer geven** in het gepubliceerde test venster om dezelfde JSON te zien als de vorige stap. Op deze manier kunt u de huidige app waarmee u werkt vergelijken met een app die naar het eind punt wordt gepubliceerd.

[de huidige bewerkings ![vergelijken en de gepubliceerde versie van de app](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze Snelstartgids, selecteert u **mijn apps** in het bovenste navigatie menu. Schakel het selectie vakje van de app in de lijst in en selecteer vervolgens **verwijderen** in de context werkbalk boven de lijst.

[app ![verwijderen uit de lijst met apps](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Algemene intenties en entiteiten identificeren](luis-tutorial-prebuilt-intents-entities.md)
