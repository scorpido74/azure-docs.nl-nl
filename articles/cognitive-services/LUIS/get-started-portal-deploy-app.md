---
title: 'Quickstart: Een app implementeren met behulp van de LUIS-Portal'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het implementeren van uw LUIS-app op het Voorspellings eindpunt nadat de app klaar is om utterance voor spellingen te retour neren naar een client toepassing, zoals een chat-bot. In deze Snelstartgids leert u hoe u een toepassing implementeert door een Voorspellings eindpunt resource te maken, de resource aan de app toe te wijzen, de app te trainen en de app te publiceren.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f640921e6f48559db3f1414551d6ed974df15e4f
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703224"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Quickstart: Een app implementeren in de LUIS-Portal

Wanneer uw LUIS-app gereed is om utterance voor spellingen te retour neren naar een client toepassing (bijvoorbeeld een chat-bot), moet u de app implementeren in het Voorspellings eindpunt.

In deze Quick Start leert u hoe u een toepassing implementeert. U maakt een Voorspellings eindpunt resource, wijst de resource toe aan de app, Train de app en publiceert de app.

## <a name="prerequisites"></a>Vereisten

* Een [Azure-abonnement](https://azure.microsoft.com/free)ophalen.
* Voltooi de [Snelstartgids van de vorige Portal](get-started-portal-build-app.md) of [down load en importeer de app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>De eindpunt resource maken

U maakt de Voorspellings eindpunt resource in de Azure Portal. Deze bron mag alleen worden gebruikt voor eindpunt Voorspellings query's. Gebruik deze resource niet voor het ontwerpen van wijzigingen in de app.

1. Meld u aan bij [Azure Portal](https://ms.portal.azure.com/).

1. Selecteer het groene **+-** teken in het deel venster linksboven. Zoek op de Marketplace naar `Cognitive Services` en selecteer deze.

1. Configureer het abonnement met de volgende instellingen:

   |Instelling|Value|Doel|
   |--|--|--|
   |Name|`my-cognitive-service-resource`|De naam van de Azure-resource. U hebt deze naam nodig wanneer u de resource aan de app toewijst in de LUIS-Portal.|
   |Subscription|Uw abonnement|Selecteer een van de abonnementen die zijn gekoppeld aan uw account.|
   |Location|**US - west**|De Azure-regio voor deze resource.|
   |Prijscategorie|**S0**|De standaard prijs categorie voor deze resource.|
   |Resource group|`my-cognitive-service-resource-group`|Maak een nieuwe resource groep voor al uw cognitieve service bronnen. Wanneer u klaar bent met de resources, kunt u de resource groep verwijderen om uw abonnement op te schonen. |
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

1. De nieuwe rij in de tabel zoeken en kopieer de eindpunt-URL. Het is op de juiste wijze samengesteld om een `HTTP GET`-aanvraag voor een voor spelling te maken naar het LUIS-API-eind punt.

## <a name="train-and-publish-the-app"></a>De app trainen en publiceren

Train de app wanneer u er klaar voor bent om deze te testen. Publiceer de app als u wilt dat de momenteel getrainde versie beschikbaar is voor client toepassingen van de eindpunt runtime voor het uitvoeren van query's.

1. Als de app niet is getraind, selecteert u **trainen** in het menu in de rechter bovenhoek.

1. Selecteer **publiceren** in het bovenste menu. Accepteer de standaard instellingen voor de omgeving en selecteer **publiceren**.

1. Wanneer de meldings balk groen wordt weer gegeven boven in het browser venster, selecteert **u verwijzen naar de lijst met eind punten**.

   ![De meldings balk van de app is gepubliceerd in de browser](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Zoek op de pagina **sleutels en eindpunt instellingen** de lijst met toegewezen resources en de bijbehorende eind punt-url's aan de onderkant.

1. Selecteer de eind punt-URL die is gekoppeld aan de nieuwe resource naam. Met deze actie wordt een webbrowser geopend met een correct geconstrueerde URL om een `GET`-aanvraag te doen voor de Voorspellings eindpunt runtime.

## <a name="prediction-endpoint-request"></a>Voorspellings eindpunt aanvraag

<!-- V3FIX -->

De `q=` aan het einde van de URL is kort voor de **query** en is de plaats waar de utterance van de gebruiker wordt toegevoegd aan de GET-aanvraag. Na de `q=` voert u dezelfde gebruikers utterance in die aan het einde van de vorige Snelstartgids worden gebruikt:

```Is there a form named hrf-234098```

In de browser wordt het antwoord weer gegeven. Dit is dezelfde JSON als uw client toepassing ontvangt:

```JSON
{
"query": "Is there a form named hrf-234098",
"topScoringIntent": {
    "intent": "FindForm",
    "score": 0.9768753
},
"intents": [
    {
    "intent": "FindForm",
    "score": 0.9768753
    },
    {
    "intent": "None",
    "score": 0.0216071066
    }
],
"entities": [
    {
    "entity": "hrf-234098",
    "type": "Human Resources Form Number",
    "startIndex": 22,
    "endIndex": 31
    }
    ]
}
```

Dit antwoord geeft u meer informatie dan het standaard test venster in de vorige zelf studie. Als u hetzelfde niveau van informatie in het test venster wilt zien, moet u de app publiceren. Nadat de app is gepubliceerd, selecteert u **vergelijken met gepubliceerd** in het test venster. Gebruik **JSON-weer gave weer geven** in het gepubliceerde test venster om dezelfde JSON te zien als de vorige stap. Op deze manier kunt u de huidige app waarmee u werkt vergelijken met een app die naar het eind punt wordt gepubliceerd.

[![Compare die momenteel worden bewerkt versus gepubliceerde versie van de app](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)




## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze Snelstartgids, selecteert u **mijn apps** in het bovenste navigatie menu. Schakel het selectie vakje van de app in de lijst in en selecteer vervolgens **verwijderen** in de context werkbalk boven de lijst.

[![Delete-app uit mijn lijst met apps](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Algemene intenties en entiteiten identificeren](luis-tutorial-prebuilt-intents-entities.md)
