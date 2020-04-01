---
title: 'Zelfstudie: Contextuele gegevens met rollen - LUIS'
description: Zoek gerelateerde gegevens op basis van context. Een bestemmings- en doellocatie voor de fysieke verhuizing tussen twee gebouwen zijn bijvoorbeeld aan elkaar gerelateerd.
ms.topic: tutorial
ms.date: 03/30/2020
ms.openlocfilehash: fdb463896e531619ea7ebe7c384729763dc84138
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475820"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Zelfstudie: contextueel gerelateerde gegevens uit een utterance extraheren

In deze zelfstudie zoekt u gerelateerde gegevens op basis van context. Bijvoorbeeld herkomst- en bestemmingslocaties voor een overdracht van de ene stad naar de andere. Hiervoor zijn mogelijk beide gegevenselementen vereist en ze zijn aan elkaar gerelateerd.

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

1. Meld u aan bij de [ **LUIS preview** portal](https://preview.luis.ai).

1. Selecteer **+ Nieuwe app voor een gesprek,** voer de naam `HumanResources` in en houd de standaardcultuur, **Engels**. Laat de beschrijving en voorspellingsbron leeg. Selecteer **Done**.

## <a name="create-an-intent-to-move-employees-between-cities"></a>Een intentie maken om werknemers te verplaatsen tussen steden

Een intentie wordt gebruikt om uitingen van gebruikers te classificeren op basis van de intentie van de gebruiker, bepaald op basis van de tekst van de natuurlijke taal.

Om een utterance te classificeren, heeft de intentie voorbeelden nodig van gebruikersuitingen die met deze intentie moeten worden geclassificeerd.

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecteer **+ Maken**.

1. Voer in het pop-updialoogvenster `MoveEmployeeToCity` in en selecteer vervolgens **Done**.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van het pop-updialoogvenster voor het maken van een nieuwe intentie met](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Voeg verschillende voorbeelduitingen toe aan deze intentie die u verwacht dat een gebruiker vraagt.

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

De vooraf gebouwde entiteit, **geografieV2,** haalt locatiegegevens, inclusief plaatsnamen. Aangezien de uitingen twee plaatsnamen hebben, die betrekking hebben op elkaar in context, gebruiken rollen om die context te extraheren.

1. Selecteer **Entiteiten** in de navigatie aan de linkerkant.

1. Selecteer **+ Prebuilt entiteit** `geo` toevoegen en voer vervolgens de zoekbalk in om de vooraf gebouwde entiteiten te filteren.

    > [!div class="mx-imgBorder"]
    > ![Geografische V2-voorgebouwde entiteit toevoegen aan de app](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Schakel het selectievakje in en selecteer **Gereed**.

## <a name="add-roles-to-prebuilt-entity"></a>Rollen toevoegen aan vooraf gebouwde entiteit

1. Selecteer in de lijst **Entiteiten** de **regioV2** om de nieuwe entiteit te openen.
1. Als u een **+** rol wilt toevoegen, `Origin`selecteert `Destination`en voegt u de volgende twee rollen toe: , en .

    > [!div class="mx-imgBorder"]
    > ![Rollen toevoegen aan vooraf gebouwde entiteit](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

## <a name="label-entity-roles-in-example-utterances"></a>Entiteitsrollen labelen in voorbeelduitingen

1. Selecteer **Intents** in de navigatie aan de linkerkant en selecteer vervolgens de intentie **MoveEmployeeToCity.** Let op de plaatsnamen zijn gelabeld met de vooraf gebouwde entiteit **geografieV2**.
1. Selecteer in de contextwerkbalk het **palet Entiteit** met het _potloodpictogram_.

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

Als u de app wilt trainen, selecteert u **Trainen**. Training past de wijzigingen, zoals de nieuwe entiteiten en de gelabelde uitingen, toe op het actieve model.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>De app publiceren om deze te openen vanaf het HTTP-eindpunt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]


## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]


1. Ga naar het einde van de URL _YOUR_QUERY_HERE_ in `Please move Carl Chamerlin from Tampa to Portland`de adresbalk en vervang YOUR_QUERY_HERE door .

Deze utterance is niet hetzelfde als een van de gelabelde uitingen, `MoveEmployee` dus het is een goede test en moet de intentie retourneren met de entiteit die is geëxtraheerd.

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

    The correct intent is predicted and the entities array has both the origin and destination roles in the corresponding **entities** property.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

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
