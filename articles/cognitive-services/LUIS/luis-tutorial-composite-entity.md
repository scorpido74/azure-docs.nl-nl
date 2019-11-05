---
title: 'Zelf studie: zelf studie samengestelde entiteit-LUIS'
titleSuffix: Azure Cognitive Services
description: Voeg een samengestelde entiteit toe om geëxtraheerde gegevens van verschillende typen te bundelen in één entiteit die een container bevat. Door de gegevens te bundelen, kan de client toepassing eenvoudig gerelateerde gegevens in verschillende gegevens typen ophalen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: adb8941fd60a955a44a04717958c5203b721639a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498983"
---
# <a name="tutorial-group-and-extract-related-data"></a>Zelf studie: gerelateerde gegevens groeperen en extra heren
In deze zelf studie voegt u een samengestelde entiteit toe om geëxtraheerde gegevens van verschillende typen te bundelen in één container met een entiteit. Door de gegevens te bundelen, kan de client toepassing eenvoudig gerelateerde gegevens in verschillende gegevens typen ophalen.

Het doel van de samengestelde entiteit is het groeperen van gerelateerde entiteiten in een bovenliggende categorie-entiteit. De informatie bestaat als afzonderlijke entiteiten voordat een samengestelde samen stelling wordt gemaakt. 

De samengestelde entiteit is geschikt voor dit type gegevens omdat de gegevens:

* Zijn aan elkaar gerelateerd. 
* Verschillende typen entiteit gebruiken.
* Moet als eenheid informatie worden gegroepeerd en verwerkt door de client-app.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

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

1.  Down load en sla het [JSON-bestand](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json) van de app op in de zelf studie voor de lijst-entiteit.

2. Importeer de JSON in een nieuwe app.

3. Ga naar het gedeelte **Beheren**, open het tabblad **Versies**, kloon de versie en noem deze `composite`. Klonen is een uitstekende manier om te experimenteren met verschillende functies van LUIS zonder dat de oorspronkelijke versie wordt gewijzigd. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

## <a name="composite-entity"></a>Samengestelde entiteit

In deze app wordt de afdelings naam gedefinieerd in de **afdeling** lijst entiteit en bevat synoniemen. 

De **TransferEmployeeToDepartment** -intentie heeft voor beeld uitingen om aan te vragen dat een werk nemer wordt verplaatst naar een nieuwe afdeling. 

Voor beelden van uitingen voor deze intentie zijn:

|Voorbeelden van utterances|
|--|
|move John W. Smith to the accounting department|
|transfer Jill Jones from to R&D|
 
De verplaatsings aanvraag moet de afdelings naam en de naam van de werk nemer bevatten. 

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Voeg de vooraf samengestelde entiteit van de Persoonnaam toe om te helpen bij het ophalen van common data type

LUIS biedt verschillende vooraf gemaakte entiteiten voor het ophalen van algemene gegevens. 

1. Selecteer **samen stellen** in de bovenste navigatie en selecteer vervolgens **entiteiten** in het navigatie menu links.

1. Selecteer de knop **Manage prebuilt entity**.

1. Selecteer **[persoons](luis-reference-prebuilt-person.md)** namen in de lijst met vooraf gemaakte entiteiten en selecteer vervolgens **gereed**.

    ![Schermopname van het selecteren van een nummer in het dialoogvenster met de vooraf gedefinieerde entiteiten](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Deze entiteit helpt u naam herkenning toe te voegen aan uw client toepassing.

## <a name="create-composite-entity-from-example-utterances"></a>Samengestelde entiteit maken van voor beeld uitingen

1. Selecteer **Intents** in de linkernavigatie.

1. Selecteer **TransferEmployeeToDepartment** in de lijst intenties.

1. Selecteer in het `place John Jackson in engineering`utterance de entiteit persoons waarde `John Jackson`en selecteer vervolgens **omloop in samengestelde entiteit** in de pop-upmenu lijst voor de volgende utterance. 

    ![Scherm opname van het selecteren van verlopende tekst in de vervolg keuzelijst](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Selecteer vervolgens onmiddellijk de laatste entiteit `engineering` in de utterance. Er wordt een groene balk getekend onder de geselecteerde woorden die een samengestelde entiteit aangeven. Voer in het pop-upmenu de samengestelde naam `TransferEmployeeInfo` Selecteer vervolgens ENTER. 

    ![Scherm opname van het invoeren van een samengestelde naam in het dialoog venster vervolg keuzelijst](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. In **welk type entiteit wilt u maken?** alle vereiste velden bevinden zich in de lijst: `personName` en `Department`. Selecteer **Done**. U ziet dat de vooraf samengestelde entiteit, persoonnaam, is toegevoegd aan de samengestelde entiteit. Als u een vooraf samengestelde entiteit zou kunnen weer geven tussen de begin-en eind tokens van een samengestelde entiteit, moet de samengestelde entiteit die vooraf gemaakte entiteiten bevatten. Als de vooraf gemaakte entiteiten niet zijn opgenomen, wordt de samengestelde entiteit niet correct voor speld, maar elk afzonderlijk element is.

    ![Scherm opname van het invoeren van een samengestelde naam in het dialoog venster vervolg keuzelijst](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Voor beeld van een label uitingen met samengestelde entiteit

1. Selecteer in elk voor beeld utterance de meest linkse entiteit die moet worden samengesteld. Selecteer vervolgens **omloop in samengestelde entiteit**.

1. Selecteer het laatste woord in de samengestelde entiteit en selecteer vervolgens **TransferEmployeeInfo** in het pop-upmenu. 

1. Controleer of alle uitingen in de intentie zijn gelabeld met de samengestelde entiteit. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>De app trainen zodat de wijzigingen aan de intentie kunnen worden getest 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>De app publiceren zodat op het getrainde model query's kunnen worden uitgevoerd vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van eindpunt 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Ga naar het einde van de URL in het adres en voer `Move Jill Jones to DevOps` in. De laatste query string-para meter is `q`, de utterance. 

    Omdat deze test is om te controleren of de samen stelling correct is geëxtraheerd, kan een test een bestaande voorbeeld utterance of een nieuwe utterance toevoegen. Een goede test is het toevoegen van alle onderliggende entiteiten in de samengestelde entiteit.

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

   Deze utterance retourneert een samengestelde entiteiten matrix. Elke entiteit krijgt een type en een waarde. Als u meer precisie voor elke onderliggende entiteit wilt vinden, gebruikt u de combi natie van type en waarde uit het samengestelde-matrix item om het overeenkomstige item in de entiteiten matrix te vinden.  

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* [Zelf studie voor lijst entiteit](luis-quickstart-intents-only.md)
* Conceptuele informatie van [samengestelde entiteit](luis-concept-entity-types.md)
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)


## <a name="next-steps"></a>Volgende stappen

In deze zelf studie is een samengestelde entiteit gemaakt om bestaande entiteiten te integreren. Hierdoor kan de client toepassing een groep gerelateerde gegevens in verschillende gegevens typen vinden om de conversatie voort te zetten. Een client toepassing voor deze human resources-app kan vragen welke dag en tijd de verplaatsing moet beginnen en eindigen. Het kan ook vragen over andere logistiek van de verplaatsing, zoals een fysiek telefoon nummer. 

> [!div class="nextstepaction"] 
> [Meer informatie over het toevoegen van een eenvoudige entiteit met een woordgroepen lijst](luis-quickstart-primary-and-secondary-data.md)  
