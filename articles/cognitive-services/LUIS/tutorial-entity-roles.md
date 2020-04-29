---
title: 'Zelf studie: contextuele gegevens met rollen-LUIS'
description: Zoek gerelateerde gegevens op basis van de context. Een bestemmings- en doellocatie voor de fysieke verhuizing tussen twee gebouwen zijn bijvoorbeeld aan elkaar gerelateerd.
ms.topic: tutorial
ms.date: 03/30/2020
ms.openlocfilehash: fdb463896e531619ea7ebe7c384729763dc84138
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80475820"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Zelf studie: contextuele gerelateerde gegevens uit een utterance ophalen

In deze zelfstudie zoekt u gerelateerde gegevens op basis van context. Bijvoorbeeld bron-en doel locaties voor een overdracht van de ene plaats naar een andere. Hiervoor zijn mogelijk beide gegevenselementen vereist en ze zijn aan elkaar gerelateerd.

Een rol kan worden gebruikt met een vooraf samengesteld of aangepast entiteits type en wordt gebruikt in beide voor beelden van uitingen en patronen.

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Nieuwe app maken
> * Intentie toevoegen
> * Informatie over de oorsprong en bestemming ophalen met behulp van rollen
> * Trainen
> * Publiceren
> * Intents en entiteits rollen ophalen uit het eind punt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Gerelateerde gegevens

Deze app bepaalt waar een werknemer moet worden verplaatst van de plaats van oorsprong naar de plaats van bestemming. Er wordt een GeographyV2-vooraf gedefinieerde entiteit gebruikt om de namen van steden te identificeren en het maakt gebruik van rollen om de locatie typen (oorsprong en bestemming) in de utterance te bepalen.

Er moet een rol worden gebruikt wanneer de entiteits gegevens die moeten worden geëxtraheerd:

* Is aan elkaar gerelateerd in de context van de utterance.
* Gebruikt een specifieke woorden keuze om elke rol aan te geven. Voorbeelden van deze woorden zijn: van/naar verlaten/gaan naar, weg van/naar.
* Beide rollen bevinden zich vaak in dezelfde utterance, waardoor LUIS kan leren van dit frequente contextuele gebruik.
* Moet als eenheid informatie worden gegroepeerd en verwerkt door de client-app.

## <a name="create-a-new-app"></a>Een nieuwe app maken

1. Meld u aan bij de [Luis **Preview** -Portal](https://preview.luis.ai).

1. Selecteer **+ nieuwe app voor gesprek**, voer de naam `HumanResources` in en behoud de standaard cultuur, **Engels**. Laat de Resource Description en voor spelling leeg. Selecteer **Done**.

## <a name="create-an-intent-to-move-employees-between-cities"></a>Een intentie maken om werknemers te verplaatsen tussen steden

Een intentie wordt gebruikt om gebruikers uitingen te classificeren op basis van de bedoeling van de gebruiker, bepaald uit de tekst in natuurlijke taal.

Voor het classificeren van een utterance heeft de bedoeling een voor beeld van een gebruikers uitingen nodig dat met deze intentie moet worden geclassificeerd.

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecteer **+ maken**.

1. Voer in het pop-updialoogvenster `MoveEmployeeToCity` in en selecteer vervolgens **Done**.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van het pop-updialoogvenster voor het maken van een nieuwe intentie met](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Voeg enkele voor beeld-uitingen toe aan deze intentie die een gebruiker verwacht te vragen.

    |Voorbeelden van utterances|
    |--|
    |John W. Smith verplaatsen van Seattle naar Orlando|
    |transfer Jill Jones from Seattle to Cairo|
    |Place John Jackson away from Tampa, coming to Atlanta |
    |Debra Doughtery verplaatsen naar Tulsa van Chicago|
    |mv Jill Jones leaving Cairo headed to Tampa|
    |Shift Alice Anderson to Oakland from Redmond|
    |Carl Chamerlin from San Francisco to Redmond|
    |Transfer Steve Standish from San Diego toward Bellevue |
    |lift Tanner Thompson from Kansas city and shift to Chicago|

    > [!div class="mx-imgBorder"]
    > ![Schermopname van LUIS met nieuwe uitingen in de intentie MoveEmployee](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>Vooraf samengestelde entiteit geographyV2 toevoegen

De vooraf samengestelde entiteit, **geographyV2**, extraheert locatie-informatie, inclusief plaatsnamen. Omdat de uitingen twee plaatsnamen heeft, met betrekking tot elkaar in de context, gebruikt u rollen om die context te extra heren.

1. Selecteer **entiteiten** in de navigatie aan de linkerkant.

1. Selecteer **+ vooraf gedefinieerde entiteit toevoegen**en voer `geo` vervolgens in de zoek balk in om de vooraf gemaakte entiteiten te filteren.

    > [!div class="mx-imgBorder"]
    > ![Een vooraf gemaakte geographyV2-entiteit toevoegen aan de app](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Schakel het selectie vakje in en selecteer **gereed**.

## <a name="add-roles-to-prebuilt-entity"></a>Rollen toevoegen aan vooraf samengestelde entiteit

1. Selecteer in de lijst **entiteiten** de **geographyV2** om de nieuwe entiteit te openen.
1. Als u een rol wilt toevoegen **+** , selecteert u de volgende twee rollen `Origin`en voegt `Destination`u deze toe:, en.

    > [!div class="mx-imgBorder"]
    > ![Rollen toevoegen aan vooraf samengestelde entiteit](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

## <a name="label-entity-roles-in-example-utterances"></a>Entiteits rollen in voorbeeld uitingen

1. Selecteer **intenties** in de navigatie aan de linkerkant en selecteer vervolgens de **MoveEmployeeToCity** intentie. U ziet dat de namen van steden worden aangeduid met de vooraf samengestelde entiteit **geographyV2**.
1. Selecteer in de context werkbalk het **entiteits palet** met het _Potlood pictogram_.

    > [!div class="mx-imgBorder"]
    > ![Het palet entiteit selecteren op de werk balk inhoud](media/tutorial-entity-roles/intent-detail-context-toolbar-select-entity-palette.png)

1. Selecteer de vooraf samengestelde entiteit **geographyV2**en selecteer vervolgens de **entiteits controle**.
1. Selecteer in de **entiteits controle**één rol, **doel**. Hiermee wijzigt u de muis aanwijzer. Gebruik de cursor om de tekst in alle uitingen te labelen die de doel locatie is.

    > [!div class="mx-imgBorder"]
    > ![Het palet rol selecteren in de entiteit](media/tutorial-entity-roles/entity-palette-select-entity-role.png)


1. Ga terug naar de **entiteits controle**, wijzig de rol in **Origin**. Gebruik de cursor om de tekst in alle uitingen te labelen die de oorspronkelijke locatie is.

## <a name="add-example-utterances-to-the-none-intent"></a>Voorbeelduitingen toevoegen aan de intentie None

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>De app trainen zodat de wijzigingen aan de intentie kunnen worden getest

Als u de app wilt trainen, selecteert u **trainen**. Training past de wijzigingen, zoals de nieuwe entiteiten en het gelabelde uitingen, toe op het actieve model.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>De app publiceren voor toegang tot het HTTP-eind punt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]


## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]


1. Ga naar het einde van de URL in de adres balk en vervang _YOUR_QUERY_HERE_ door `Please move Carl Chamerlin from Tampa to Portland`.

Deze utterance is niet hetzelfde als een van de gelabelde uitingen, dus het is een goede test en moet de `MoveEmployee` bedoeling retour neren met de entiteit geëxtraheerd.

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

* [Concepten van entiteiten](luis-concept-entity-types.md)
* [Concepten van rollen](luis-concept-roles.md)
* [Lijst met vooraf gemaakte entiteiten](luis-reference-prebuilt-entities.md)
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)
* [Rollen](luis-concept-roles.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een nieuwe intentie gemaakt en voorbeelden van utterances toegevoegd voor de contextueel geleerde gegevens van locaties van oorsprong en bestemming. Zodra de app is getraind en gepubliceerd, kan een clienttoepassing die informatie gebruiken om een verplaatsingsticket te maken met de relevante informatie.

> [!div class="nextstepaction"]
> [Informatie over het toevoegen van een samengestelde entiteit](luis-tutorial-composite-entity.md)
