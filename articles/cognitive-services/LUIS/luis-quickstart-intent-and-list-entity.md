---
title: 'Zelfstudie: Exacte tekst overeenkomst-LUIS'
titleSuffix: Azure Cognitive Services
description: Leer hoe u gegevens ophaalt die overeenkomen met een vooraf gedefinieerde lijst met items. Elk item in de lijst kan synoniemen hebben die ook exact overeenkomen
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 25882f2aaa5d32dfb594d5f8fdb9cdc46f2ad958
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307616"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>Zelfstudie: Gegevens met exacte tekstovereenkomst ophalen uit een utterance

In deze zelfstudie leert u hoe u entiteitsgegevens ophaalt die overeenkomen met een vooraf gedefinieerde lijst met items. 

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * App maken
> * Intentie toevoegen
> * Een entiteit List toevoegen 
> * Trainen 
> * Publiceren
> * Intenties en entiteiten ophalen van eindpunt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Wat is een lijstentiteit?

Een lijstentiteit is een exacte tekstovereenkomst met de woorden in de utterance. 

Elk item in de lijst kan een lijst met synoniemen bevatten. In het geval van de Human Resources-app kan een afdeling worden geïdentificeerd aan de hand van verschillende belangrijke gegevenselementen, zoals een officiële naam, veelgebruikte acroniemen en factureringscodes. 

De Human Resources-app moet bepalen naar welke afdeling een werknemer wordt overgeplaatst. 

Een entiteit List is een goede keuze voor dit type gegevens wanneer:

* De gegevenswaarden deel uitmaken van een bekende set.
* De set maximale [begrenzingen](luis-boundaries.md) van LUIS voor dit entiteitstype niet overschrijdt.
* De tekst in de utterance is een exact overeenkomst met een synoniem of de canonieke naam. LUIS gebruikt de lijst alleen voor exact tekstovereenkomsten. Stammen, meervoudsvormen en andere varianten kunnen niet worden verwerkt met alleen een lijstentiteit. Hiervoor kunt u overwegen een [patroon](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) met de optionele tekstsyntaxis te gebruiken. 

## <a name="create-a-new-app"></a>Een nieuwe app maken

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>Een intentie maken om werknemers over te plaatsen naar een andere afdeling

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selecteer **Create new intent**. 

3. Voer in het pop-updialoogvenster `TransferEmployeeToDepartment` in en selecteer vervolgens **Done**. 

    ![Schermopname van het pop-updialoogvenster voor het maken van een nieuwe intentie met](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Voeg voorbeelden van utterances toe aan de intentie.

    |Voorbeelden van utterances|
    |--|
    |Smith naar de boekhoudafdeling|
    |Jill Jones overdragen van naar R&D|
    |Afdeling 1234 heeft een nieuw lid genaamd Bill Bradstreet|
    |Plaats John Jackson in Engineering |
    |verplaats Debra Doughtery naar Inside Sales|
    |mv Jill Jones aan IT|
    |Verplaats Alice Anderson naar DevOps|
    |Carl Chamerlin voor Finance|
    |Steve Standish tot 1234|
    |Tanner Thompson naar 3456|

    [![Schermafbeelding van intentie met voorbeelden van utterances](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "Schermafbeelding van intentie met voorbeelden van utterances")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>Lijstentiteit Department

Er zijn nu enkele voorbeelden van utterances toegevoegd voor de intentie **TransferEmployeeToDepartment** en dus is het nu zaak om ervoor te zorgen dat LUIS begrijpt wat een afdeling is. 

De primaire, _canonieke_, naam voor elk item is de afdelingsnaam. Voorbeelden van de synoniemen voor elke canonieke naam: 

|Canonieke naam|Synoniemen|
|--|--|
|Boekhouding|acct<br>accting<br>3456|
|Development Operations|DevOps<br>4949|
|Engineering|eng<br>enging<br>4567|
|Finance|fin<br>2020|
|Information Technology|it<br>2323|
|Inside Sales|isale<br>insale<br>1414|
|Research and Development|R&D<br>1234|

1. Selecteer **Entities** in het linkerpaneel.

1. Selecteer **Create new intent**.

1. Voer in het pop-updialoogvenster `Department` in als naam voor de entiteit en **List** als het entiteitstype. Selecteer **Done**.  

    [![Schermopname van een pop-upvenster voor het maken van een nieuwe entiteit](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "Schermopname van een pop-upvenster voor het maken van een nieuwe entiteit")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. Voer op de pagina van de entiteit Department `Accounting` in als de nieuwe waarde.

1. Als synoniemen voegt u de synoniemen uit de vorige tabel toe.

1. Ga door met het toevoegen van de canonieke namen en de bijbehorende synoniemen. 

## <a name="add-example-utterances-to-the-none-intent"></a>Voorbeelduitingen toevoegen aan de intentie None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>De app trainen zodat de wijzigingen aan de intentie kunnen worden getest 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>De app publiceren zodat op het getrainde model query's kunnen worden uitgevoerd vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Ga naar het einde van de URL in het adres en voer `shift Joe Smith to IT` in. De laatste parameter van de queryreeks is `q`, de utterance **query**. Deze utterance is niet hetzelfde als een van de gelabelde utterances en dit is dus een goede test die de intentie `TransferEmployeeToDepartment` met `Department` geëxtraheerd als resultaat moet geven.

   ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
   ```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* [Entiteit List](luis-concept-entity-types.md#list-entity) - conceptuele informatie
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een nieuwe intentie gemaakt, voorbeelden van utterances toegevoegd en vervolgens een List-entiteit gemaakt om exacte tekstovereenkomsten te extraheren uit utterances. Na het trainen en het publiceren van de app is met een query naar het eindpunt de intentie achterhaald. Vervolgens zijn de geëxtraheerde gegevens geretourneerd.

Ga door met deze app en [voeg een samengestelde entiteit toe](luis-tutorial-composite-entity.md).

> [!div class="nextstepaction"]
> [Een vooraf samengestelde entiteit met een rol aan de app toevoegen](tutorial-entity-roles.md)

