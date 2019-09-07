---
title: 'Zelfstudie: Zelf studie samengestelde entiteit-LUIS'
titleSuffix: Azure Cognitive Services
description: Voeg een samengestelde entiteit die u wilt de opgehaalde gegevens van verschillende typen te bundelen in een enkele containerentiteit. De clienttoepassing kan door de gegevens bundeling, gerelateerde gegevens in verschillende gegevenstypen eenvoudig extraheren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: f20661a74397487e141e69681f207418db8ac386
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70388031"
---
# <a name="tutorial-group-and-extract-related-data"></a>Zelfstudie: Gerelateerde gegevens groeperen en herkennen
In deze zelfstudie voegt u een samengestelde entiteit die u wilt de opgehaalde gegevens van verschillende typen in een enkele containerentiteit bundelen. De clienttoepassing kan door de gegevens bundeling, gerelateerde gegevens in verschillende gegevenstypen eenvoudig extraheren.

Het doel van de samengestelde entiteit is het groeperen van gerelateerde entiteiten in een entiteit van bovenliggende categorie. De informatie bestaat als afzonderlijke entiteiten voordat een samengestelde wordt gemaakt. 

De samengestelde entiteit is geschikt voor dit type gegevens omdat de gegevens:

* Aan elkaar zijn gerelateerd. 
* Verschillende Entiteitstypen gebruiken.
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

1. Selecteer in de `place John Jackson in engineering`utterance de `John Jackson`entiteit persoonnaam en selecteer vervolgens **omloop in samengestelde entiteit** in de pop-upmenu lijst voor de volgende utterance. 

    ![Scherm opname van het selecteren van verlopende tekst in de vervolg keuzelijst](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Selecteer vervolgens de laatste entiteit onmiddellijk `engineering` in de utterance. Een groene menubalk wordt onder de geselecteerde woorden die wijzen op een samengestelde entiteit getekend. Voer in het pop-upmenu, de naam van de samengestelde `TransferEmployeeInfo` en selecteer enter. 

    ![Scherm opname van het invoeren van een samengestelde naam in het dialoog venster vervolg keuzelijst](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. In **welk type entiteit wilt u maken?** alle vereiste velden bevinden zich in de lijst: `personName` en. `Department` Selecteer **Done**. U ziet dat de vooraf samengestelde entiteit, persoonnaam, is toegevoegd aan de samengestelde entiteit. Als u een vooraf gedefinieerde entiteit weergegeven tussen het begin en einde van de tokens van een samengestelde entiteit hebt kan, moet de samengestelde entiteit die vooraf gemaakte entiteiten bevatten. Als de vooraf gemaakte entiteiten niet opgenomen zijn, de samengestelde entiteit niet correct wordt voorspeld, maar elk afzonderlijk element is.

    ![Scherm opname van het invoeren van een samengestelde naam in het dialoog venster vervolg keuzelijst](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Label voorbeeld uitingen met samengestelde entiteit

1. Selecteer de meest linkse-entiteit die in de samengestelde worden moet in elke utterance voorbeeld. Selecteer vervolgens **verpakken in samengestelde entiteit**.

1. Selecteer het laatste woord in de samengestelde entiteit en selecteer vervolgens **TransferEmployeeInfo** in het pop-upmenu. 

1. Controleer of dat alle uitingen in het doel zijn gelabeld met de samengestelde entiteit. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>De app trainen zodat de wijzigingen aan de intentie kunnen worden getest 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>De app publiceren zodat op het getrainde model query's kunnen worden uitgevoerd vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van eindpunt 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Ga naar het einde van de URL in het adres en voer `Move Jill Jones to DevOps` in. De laatste querystring-parameter is `q`, de query utterance. 

    Omdat deze test om te controleren of dat de samengestelde correct wordt opgehaald, kan een test ofwel een bestaande voorbeeld-utterance of een nieuwe utterance bevatten. Er is een goede test om op te nemen van de onderliggende entiteiten in de samengestelde entiteit.

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

   Deze utterance retourneert een matrix samengestelde entiteiten. Elke entiteit is een type en de waarde opgegeven. Ga voor meer precisie voor elke onderliggende entiteit, gebruiken de combinatie van het type en de waarde van het matrixitem samengestelde aan het bijbehorende item niet vinden in de matrix entiteiten.  

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* [Zelf studie voor lijst entiteit](luis-quickstart-intents-only.md)
* Conceptuele informatie van [samengestelde entiteit](luis-concept-entity-types.md)
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt gemaakt van een samengestelde entiteit om in te kapselen bestaande entiteiten. Hiermee wordt de clienttoepassing een reeks gerelateerde gegevens zoeken in verschillende gegevenstypen om door te gaan van de conversatie. Een clienttoepassing voor deze app Human Resources vragen welke dag en tijd het verplaatsen moet beginnen en eindigen. Het kan ook vragen over andere logistiek van de verplaatsing, zoals een fysiek telefoon nummer. 

> [!div class="nextstepaction"] 
> [Informatie over het toevoegen van een enkele entiteit met een woordgroepenlijst met](luis-quickstart-primary-and-secondary-data.md)  
