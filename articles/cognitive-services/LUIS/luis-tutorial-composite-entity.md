---
title: 'Zelfstudie: Zelfstudie van samengestelde entiteit - LUIS'
titleSuffix: Azure Cognitive Services
description: Voeg in deze zelfstudie een samengestelde entiteit toe om geëxtraheerde gegevens van verschillende typen te bundelen in één entiteit die bevat. Door de gegevens te bundelen, kan de clientapplicatie eenvoudig gerelateerde gegevens in verschillende gegevenstypen extraheren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: f2b2c3f52610cd9fae0845b15aebf032a088000b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75447953"
---
# <a name="tutorial-group-and-extract-related-data"></a>Zelfstudie: Gerelateerde gegevens groeperen en extraheren
Voeg in deze zelfstudie een samengestelde entiteit toe om geëxtraheerde gegevens van verschillende typen te bundelen in één entiteit die bevat. Door de gegevens te bundelen, kan de clientapplicatie eenvoudig gerelateerde gegevens in verschillende gegevenstypen extraheren.

Het doel van de samengestelde entiteit is om verwante entiteiten te groeperen in een bovenliggende categorieentiteit. De informatie bestaat als afzonderlijke entiteiten voordat een composiet wordt gemaakt.

De samengestelde entiteit past goed bij dit type gegevens, omdat de gegevens:

* Zijn met elkaar verbonden.
* Gebruik verschillende entiteitstypen.
* Moet als eenheid informatie worden gegroepeerd en verwerkt door de client-app.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Intent maken
> * Een samengestelde entiteit toevoegen
> * Trainen
> * Publiceren
> * Intenties en entiteiten ophalen van eindpunt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Voorbeeld-app importeren

1.  Download en sla het [JSON-bestand](
https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json?raw=true) van de app op uit de zelfstudie van de entiteit List.

2. Importeer de JSON in een nieuwe app via de [LUIS-portal.](https://www.luis.ai)

3. Ga naar het gedeelte **Beheren**, open het tabblad **Versies**, kloon de versie en noem deze `composite`. Klonen is een uitstekende manier om te experimenteren met verschillende functies van LUIS zonder dat de oorspronkelijke versie wordt gewijzigd. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

## <a name="composite-entity"></a>Samengestelde entiteit

In deze app wordt de afdelingsnaam gedefinieerd in de entiteit van de **afdelingslijst** en bevat het synoniemen.

De intentie **TransferEmployeeToDepartment** heeft voorbeelduitingen om een werknemer te vragen naar een nieuwe afdeling te worden verplaatst.

Voorbeelduitingen voor deze intentie zijn:

|Voorbeelden van utterances|
|--|
|move John W. Smith to the accounting department|
|transfer Jill Jones from to R&D|

De verhuisaanvraag moet de naam van de afdeling en de naam van de werknemer bevatten.

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>De voorgebouwde entiteit PersonName toevoegen om te helpen bij algemene gegevenstypeextractie

LUIS biedt verschillende vooraf gemaakte entiteiten voor het ophalen van algemene gegevens.

1. Selecteer **Bouwen** in de bovenste navigatie en selecteer **Entiteiten** in het linkernavigatiemenu.

1. Selecteer de knop **Manage prebuilt entity**.

1. Selecteer **[PersonName](luis-reference-prebuilt-person.md)** in de lijst met vooraf gebouwde entiteiten en selecteer **Gereed**.

    ![Schermopname van het selecteren van een nummer in het dialoogvenster met de vooraf gedefinieerde entiteiten](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Met deze entiteit u naamsbekendheid toevoegen aan uw clienttoepassing.

## <a name="create-composite-entity-from-example-utterances"></a>Samengestelde entiteit maken op uit voorbeelduitingen

1. Selecteer **Intents** in de linkernavigatie.

1. Selecteer **TransferEmployeeToDepartment** in de lijst met intenties.

1. Selecteer in `place John Jackson in engineering`de utterance de `John Jackson`entiteit personName en selecteer **Vervolgens Wrap in samengestelde entiteit** in de lijst met pop-upmenu's voor de volgende utterance.

    ![Schermafbeelding van het selecteren van wrap composite in vervolgkeuzedialoogvenster](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Selecteer vervolgens onmiddellijk de `engineering` laatste entiteit in de utterance. Er wordt een groene balk getekend onder de geselecteerde woorden die een samengestelde entiteit aangeven. Voer in het pop-upmenu `TransferEmployeeInfo` de samengestelde naam in en selecteer enter.

    ![Schermafbeelding van het invoeren van samengestelde naam in vervolgkeuzedialoogvenster](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. In **Welk type entiteit wilt u maken?** `personName` `Department` Selecteer **Done**. Merk op dat de vooraf gebouwde entiteit, personName, is toegevoegd aan de samengestelde entiteit. Als u een vooraf gebouwde entiteit laten verschijnen tussen de begin- en eindtokens van een samengestelde entiteit, moet de samengestelde entiteit deze vooraf gebouwde entiteiten bevatten. Als de vooraf gebouwde entiteiten niet zijn opgenomen, wordt de samengestelde entiteit niet correct voorspeld, maar elk afzonderlijk element wel.

    ![Schermafbeelding van het invoeren van samengestelde naam in vervolgkeuzedialoogvenster](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Voorbeelduitingen labelen met samengestelde entiteit

1. Selecteer in elk voorbeeldutterance de meest linkse entiteit die zich in de compositie moet bevindt. Selecteer vervolgens **Wrap in samengestelde entiteit**.

1. Selecteer het laatste woord in de samengestelde entiteit en selecteer **TransferEmployeeInfo** in het pop-upmenu.

1. Controleer of alle uitingen in de intentie zijn gelabeld met de samengestelde entiteit.

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>De app trainen zodat de wijzigingen aan de intentie kunnen worden getest

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>De app publiceren zodat op het getrainde model query's kunnen worden uitgevoerd vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Ga naar het einde van de URL in het adres en voer `Move Jill Jones to DevOps` in. De laatste parameter van de queryreeks is `q`, de utterance query.

    Aangezien deze test is om te controleren of de composiet correct is geëxtraheerd, kan een test een bestaande voorbeeldutterance of een nieuwe utterance bevatten. Een goede test is om alle onderliggende entiteiten op te nemen in de samengestelde entiteit.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   Met deze utterance wordt een samengestelde array met entiteiten geretourneerd. Elke entiteit krijgt een type en waarde. Als u meer precisie voor elke onderliggende entiteit wilt vinden, gebruikt u de combinatie van type en waarde uit het samengestelde arrayitem om het overeenkomstige item in de array entiteiten te vinden.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* [Zelfstudie van de entiteit van de lijst](luis-quickstart-intents-only.md)
* [Conceptuele informatie van samengestelde entiteit](luis-concept-entity-types.md)
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)


## <a name="next-steps"></a>Volgende stappen

Met deze zelfstudie is een samengestelde entiteit gemaakt om bestaande entiteiten in te kapselen. Hierdoor kan de clienttoepassing een groep gerelateerde gegevens in verschillende gegevenstypen vinden om het gesprek voort te zetten. Een clienttoepassing voor deze Human Resources-app kan vragen welke dag en tijd de verhuizing moet beginnen en beëindigen. Het kan ook vragen over andere logistiek van de verhuizing, zoals een fysieke telefoon.

> [!div class="nextstepaction"]
> [Onzekere voorspellingen herstellen door eindpuntuitingen te controleren](luis-tutorial-review-endpoint-utterances.md)
