---
title: 'Zelfstudie: Contextuele gegevens met rollen - LUIS'
titleSuffix: Azure Cognitive Services
description: Zoek gerelateerde gegevens op basis van context. Een bestemmings- en doellocatie voor de fysieke verhuizing tussen twee gebouwen zijn bijvoorbeeld aan elkaar gerelateerd.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: cd646ef061a0be06a9b1a56b72a4f35d9796aa63
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75447913"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Zelfstudie: contextueel gerelateerde gegevens uit een utterance extraheren

In deze zelfstudie zoekt u gerelateerde gegevens op basis van context. Denk bijvoorbeeld aan de locaties van oorsprong en bestemming voor een overplaatsing tussen vestigingen. Hiervoor zijn mogelijk beide gegevenselementen vereist en ze zijn aan elkaar gerelateerd.

Een rol kan worden gebruikt met elk vooraf gebouwd of aangepast entiteitstype en wordt gebruikt in zowel voorbeelduitingen als patronen.

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Nieuwe app maken
> * Intentie toevoegen
> * Oorsprongs- en bestemmingsgegevens achterhalen met rollen
> * Trainen
> * Publiceren
> * Intents en entiteitsrollen ophalen vanaf endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Gerelateerde gegevens

Deze app bepaalt waar een werknemer moet worden verplaatst van de plaats van oorsprong naar de plaats van bestemming. Het maakt gebruik van een GeograafV2 vooraf gebouwde entiteit om de plaatsnamen te identificeren en het gebruikt rollen om de locatietypen (oorsprong en bestemming) binnen de utterance te bepalen.

Een rol moet worden gebruikt wanneer de entiteitsgegevens moeten worden geëxtraheerd:

* Is gerelateerd aan elkaar in de context van de utterance.
* Gebruikt specifieke woordkeuze om elke rol aan te geven. Voorbeelden van deze woorden zijn: van/naar verlaten/gaan naar, weg van/naar.
* Beide rollen zijn vaak in dezelfde uiting, waardoor LUIS om te leren van deze frequente contextuele gebruik.
* Moet als eenheid informatie worden gegroepeerd en verwerkt door de client-app.

## <a name="create-a-new-app"></a>Een nieuwe app maken

1. Meld u aan bij de preview [https://preview.luis.ai](https://preview.luis.ai)LUIS-portal met de URL van .

1. Selecteer **Nieuwe app maken,** voer de naam `HumanResources` in en houd de standaardcultuur, **Engels**. Laat de beschrijving leeg.

1. Selecteer **Done**.

## <a name="create-an-intent-to-move-employees-between-cities"></a>Een intentie maken om werknemers te verplaatsen tussen steden

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecteer **Create new intent**.

1. Voer in het pop-updialoogvenster `MoveEmployeeToCity` in en selecteer vervolgens **Done**.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van het pop-updialoogvenster voor het maken van een nieuwe intentie met](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Voeg voorbeelden van utterances toe aan de intentie.

    |Voorbeelden van utterances|
    |--|
    |bewegen John W. Smith verlaten Seattle op weg naar Orlando|
    |transfer Jill Jones from Seattle to Cairo|
    |Place John Jackson away from Tampa, coming to Atlanta |
    |verplaats Debra Doughtery naar Tulsa uit Chicago|
    |mv Jill Jones leaving Cairo headed to Tampa|
    |Shift Alice Anderson to Oakland from Redmond|
    |Carl Chamerlin from San Francisco to Redmond|
    |Transfer Steve Standish from San Diego toward Bellevue |
    |lift Tanner Thompson from Kansas city and shift to Chicago|

    > [!div class="mx-imgBorder"]
    > ![Schermopname van LUIS met nieuwe uitingen in de intentie MoveEmployee](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>Vooraf gebouwde entiteitgeografie V2 toevoegen

De vooraf gebouwde entiteit, geografieV2, haalt locatiegegevens uit, inclusief plaatsnamen. Aangezien de uitingen twee plaatsnamen hebben, die betrekking hebben op elkaar in context, gebruiken rollen om die context te extraheren.

1. Selecteer **Entiteiten** in de navigatie aan de linkerkant.

1. Selecteer **Vooraf gebouwde entiteit** `geo` toevoegen en selecteer vervolgens in de zoekbalk om de vooraf gebouwde entiteiten te filteren.

    > [!div class="mx-imgBorder"]
    > ![Geografische V2-voorgebouwde entiteit toevoegen aan de app](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Schakel het selectievakje in en selecteer **Gereed**.
1. Selecteer in de lijst **Entiteiten** de **regioV2** om de nieuwe entiteit te openen.
1. Voeg twee `Origin`rollen `Destination`toe en .

    > [!div class="mx-imgBorder"]
    > ![Rollen toevoegen aan vooraf gebouwde entiteit](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

1. Selecteer **Intents** in de navigatie aan de linkerkant en selecteer vervolgens de intentie **MoveEmployeeToCity.** Let op de plaatsnamen zijn gelabeld met de vooraf gebouwde entiteit **geografieV2**.
1. Selecteer op de contextwerkbalk het **palet Entiteit**.

    > [!div class="mx-imgBorder"]
    > ![Entiteitspalet selecteren op de werkbalk inhoud](media/tutorial-entity-roles/intent-detail-context-toolbar-select-entity-palette.png)

1. Selecteer de voorgebouwde **entiteit, geografieV2,** en selecteer vervolgens de **entiteitscontrole**.
1. Selecteer in de **entiteitscontrole**één rol, **Bestemming**. Hiermee wordt de muiscursor gewijzigd. Gebruik de cursor om de tekst te labelen in alle uitingen die de doellocatie zijn.

    > [!div class="mx-imgBorder"]
    > ![Rol selecteren in entiteitspalet](media/tutorial-entity-roles/entity-palette-select-entity-role.png)


1. Terug naar de **entiteitsinspecteur**, wijzig de rol naar **Origin**. Gebruik de cursor om de tekst te labelen in alle uitingen die de oorsprongslocatie zijn.

## <a name="add-example-utterances-to-the-none-intent"></a>Voorbeelduitingen toevoegen aan de intentie None

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>De app trainen zodat de wijzigingen aan de intentie kunnen worden getest

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>De app publiceren zodat op het getrainde model query's kunnen worden uitgevoerd vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Ga naar het einde van de URL in de adresbalk en voer `Please move Carl Chamerlin from Tampa to Portland` in. De laatste querytekenreeksparameter is `q`de **utterancequery**. Deze utterance is niet hetzelfde als een van de gelabelde uitingen, `MoveEmployee` dus het is een goede test en moet de intentie retourneren met de entiteit die is geëxtraheerd.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.9706451
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.9706451
        },
        {
          "intent": "None",
          "score": 0.0307451729
        }
      ],
      "entities": [
        {
          "entity": "tampa",
          "type": "builtin.geographyV2.city",
          "startIndex": 32,
          "endIndex": 36,
          "role": "Origin"
        },
        {
          "entity": "portland",
          "type": "builtin.geographyV2.city",
          "startIndex": 41,
          "endIndex": 48,
          "role": "Destination"
        }
      ]
    }
    ```

    De juiste intentie wordt voorspeld en de array entiteiten heeft zowel de oorsprong s- als bestemmingsrollen in de overeenkomstige **eigenschap entiteiten.**

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* [Concepten entiteiten](luis-concept-entity-types.md)
* [Rollenconcepten](luis-concept-roles.md)
* [Lijst met vooraf gebouwde entiteiten](luis-reference-prebuilt-entities.md)
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)
* [Rollen](luis-concept-roles.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een nieuwe intentie gemaakt en voorbeelden van utterances toegevoegd voor de contextueel geleerde gegevens van locaties van oorsprong en bestemming. Zodra de app is getraind en gepubliceerd, kan een clienttoepassing die informatie gebruiken om een verplaatsingsticket te maken met de relevante informatie.

> [!div class="nextstepaction"]
> [Informatie over het toevoegen van een samengestelde entiteit](luis-tutorial-composite-entity.md)
