---
title: 'Zelf studie: eind punt uitingen-LUIS controleren'
titleSuffix: Azure Cognitive Services
description: Verbeter de voorspellingen van de app door de uitingen die worden ontvangen via het LUIS-HTTP-eindpunt (en waar LUIS niet zeker over is) te controleren of corrigeren. Bij sommige uitingen moet mogelijk de intentie worden gecontroleerd en bij andere de entiteit.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 04f30818e3c871d74d94bfd92bd3f73e4e6637a0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499416"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Zelf studie: herstel onzekere voor spellingen door eind punt uitingen te controleren
In deze zelfstudie leert u de voorspellingen van de app te verbeteren door de uitingen die worden ontvangen via het LUIS-HTTP-eindpunt (en waar LUIS niet zeker over is) te controleren of corrigeren. Bij sommige uitingen moet mogelijk de intentie worden gecontroleerd en bij andere de entiteit. Controleer de eindpuntuitingen regelmatig als onderdeel van uw geplande LUIS-onderhoud. 

Dit beoordelingsproces is een andere manier waarop LUIS meer over uw app-domein kan leren. De uitingen die in de beoordelingslijst worden weergegeven, zijn door LUIS geselecteerd. Deze lijst:

* is specifiek voor deze app;
* is bedoeld om de nauwkeurigheid van de app te verbeteren; 
* dient op periodieke basis te worden gecontroleerd. 

Door de eindpuntuitingen te boordelen, kunt u de voorspelde intenties controleren of corrigeren. U kunt ook aangepaste entiteiten die niet of onjuist zijn voorspeld van een label voorzien. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Eindpuntuitingen controleren
> * Frasenlijst bijwerken
> * App trainen
> * App publiceren
> * Eindpunt van app opvragen om JSON-antwoord van LUIS te zien

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Voorbeeld-app importeren

Ga door met de in de laatste zelfstudie gemaakt app, **Human Resources**. 

Voer de volgende stappen uit:

1.  Download het [JSON-bestand van de app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json) en sla het op.

1. Importeer de JSON in een nieuwe app.

1. Ga naar het gedeelte **Beheren**, open het tabblad **Versies**, kloon de versie en noem deze `review`. Klonen is een uitstekende manier om te experimenteren met verschillende functies van LUIS zonder dat de oorspronkelijke versie wordt gewijzigd. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

1. Train en publiceer de nieuwe app.

1. Gebruik het eindpunt om de volgende uitingen toe te voegen. U doet dit ofwel met een [script](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) of vanuit het eindpunt in een browser. U dient deze uitingen toe te voegen:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

    Als u alle versies van de app hebt, omdat u de hele reeks zelfstudies hebt gevolgd, zal het u wellicht verrassen dat de lijst **Eindpuntuitingen beoordelen** niet per versie verschilt. Er is één groep uitingen om te beoordelen, ongeacht welke versie u actief bewerkt of welke versie van de app wordt gepubliceerd op het eindpunt. 

## <a name="review-endpoint-utterances"></a>Eindpuntuitingen controleren

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecteer in de linkernavigatiebalk **Eindpuntuitingen beoordelen**. De lijst is gefilterd voor de intentie **ApplyForJob**. 

    [![Schermafbeelding van de knop Eindpuntuitingen beoordelen in de navigatiebalk aan de linkerkant](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png#lightbox)

1. Schakelen tussen de **Weergave Entiteiten** om de entiteiten met labels weer te geven. 
    
    [![Schermafbeelding van het paneel Eindpuntuitingen beoordelen met de wisselknop Entiteiten weergeven gemarkeerd](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png#lightbox)


    Deze utterance, `I'm looking for a job with Natural Language Processing`, bevindt zich niet in de juiste intentie. 

    De reden waarom de utterance een misvoor speld is, is dat de **ApplyForJob** -intentie 21 uitingen heeft vergeleken met de 7 uitingen in **GetJobInformation**. De intentie met meer uitingen heeft een hogere voor spelling. Het is belang rijk dat de hoeveelheid en de kwaliteit van de uitingen in de intenties evenwichtig is.

1.  Als u deze utterance wilt uitlijnen, selecteert u de juiste intentie en markeert u de taak entiteit hierin. Voeg het gewijzigde utterance toe aan de app door het selectie vakje groen in te scha kelen. 

    |Utterance|Correcte intentie|Ontbrekende entiteiten|
    |:--|:--|:--|
    |`I'm looking for a job with Natural Language Processing`|GetJobInfo|Taak - 'Natuurlijke taalverwerking'|

    Als u `natural language processing` van een instantie van een woordgroep wilt wijzigen in een taak entiteit, selecteert u de woord groep en selecteert u vervolgens **taak** in de lijst. Als u slechts een deel van de tekst van de woordgroep voor een andere entiteit wilt selecteren, moet u de woordgroep verwijderen als een entiteit, een label met een andere entiteit en vervolgens de entiteit met de superwoord groep opnieuw Toep assen op de app. 

    Als u de utterance toevoegt, wordt de utterance van het **eind punt van de beoordeling uitingen** naar de **GetJobInformation** -intentie verplaatst. De eindpuntuiting is nu een voorbeelduiting voor deze intentie. 

    Naast het correct uitlijnen van deze utterance, moeten er meer uitingen worden toegevoegd aan de **GetJobInformation** intentie. Deze stappen kunt u bij wijze van oefening zelf uitvoeren. Alle intenties, met uitzondering van de intentie **None**, zouden ongeveer hetzelfde aantal voorbeelduitingen moeten bevatten. De intentie **None** zou 10% van het totale aantal uitingen in de app moeten bevatten. 

    Beoordeel de resterende uitingen in deze intentie, waarbij u de uitingen van een label kunt voorzien en de **Uitgelijnde intentie** kunt corrigeren als deze onjuist zijn.

    De uitingen-lijst van het **controle-eind punt** mag niet langer deze uitingen hebben. Als meer uitingen worden weergegeven, dient u de lijst af te blijven gaan, de intenties waar nodig te corrigeren en eventuele ontbrekende entiteiten van een label te voorzien, tot de lijst leeg is. 

    Selecteer de volgende intentie in de filterlijst en blijf vervolgens uitingen corrigeren en entiteiten van labels voorzien. Vergeet niet voor iedere intentie als laatste stap ofwel **Toevoegen aan uitgelijnde intentie** te selecteren op de rij van die uiting of het selectievakje te selecteren bij die intentie en boven de tabel op **Selectie toevoegen** te klikken.

    Ga door totdat alle intenties en entiteiten in de filterlijst over een lege lijst beschikken. Dit is een hele kleine app. Het beoordelingsproces duurt slechts een paar minuten. 

## <a name="update-phrase-list"></a>Frasenlijst bijwerken
Houd de woordgroepenlijst actueel door alle nieuwe gedetecteerde taaknamen toe te voegen. 

1. Selecteer **Woordgroepenlijsten** in de linkernavigatiebalk.

2. Selecteer de woordgroepenlijst **Taken**.

3. Voeg `Natural Language Processing` toe als een waarde en selecteer vervolgens **Opslaan**. 

## <a name="train"></a>Trainen

LUIS is niet op de hoogte van eventuele wijzigingen totdat deze wordt getraind. 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publiceren

Als u deze app hebt geïmporteerd, dient u **Sentimentanalyse** te selecteren.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Intenties en entiteiten ophalen van eindpunt

Probeer een uiting die lijkt op de gecorrigeerde uiting. 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Ga naar het einde van de URL in het adres en voer `Are there any natural language processing jobs in my department right now?` in. De laatste parameter van de queryreeks is `q`, de utterance **query**. 

   ```json
   {
    "query": "are there any natural language processing jobs in my department right now?",
    "topScoringIntent": {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    "intents": [
      {
        "intent": "GetJobInformation",
        "score": 0.9247605
      },
      {
        "intent": "ApplyForJob",
        "score": 0.129989788
      },
      {
        "intent": "FindForm",
        "score": 0.006438211
      },
      {
        "intent": "EmployeeFeedback",
        "score": 0.00408575451
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00194211153
      },
      {
        "intent": "None",
        "score": 0.00166400627
      },
      {
        "intent": "Utilities.Help",
        "score": 0.00118593348
      },
      {
        "intent": "MoveEmployee",
        "score": 0.0007885918
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.0006373631
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0005980781
      },
      {
        "intent": "Utilities.Confirm",
        "score": 3.719905E-05
      }
    ],
    "entities": [
      {
        "entity": "right now",
        "type": "builtin.datetimeV2.datetime",
        "startIndex": 64,
        "endIndex": 72,
        "resolution": {
          "values": [
            {
              "timex": "PRESENT_REF",
              "type": "datetime",
              "value": "2018-07-05 15:23:18"
            }
          ]
        }
      },
      {
        "entity": "natural language processing",
        "type": "Job",
        "startIndex": 14,
        "endIndex": 40,
        "score": 0.9869922
      },
      {
        "entity": "natural language processing jobs",
        "type": "builtin.keyPhrase",
        "startIndex": 14,
        "endIndex": 45
      },
      {
        "entity": "department",
        "type": "builtin.keyPhrase",
        "startIndex": 53,
        "endIndex": 62
      }
    ],
    "sentimentAnalysis": {
      "label": "positive",
      "score": 0.8251864
    }
   }
   }
   ```

   De juiste intentie is voorspeld met een hoge score en de entiteit **Job** is gedetecteerd als `natural language processing`. 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Kan de beoordeling worden vervangen door meer uitingen toe te voegen? 
U vraagt zich misschien af waarom we niet meer voorbeelduitingen toevoegen. Wat is het doel van de beoordeling van eindpuntuitingen? In een echte LUIS-app zijn de eindpuntuitingen van gebruikers met een woordkeuze en indeling die u nog niet hebt gebruikt. Als u de dezelfde woordkeuze en rangschikking had gebruikt, zou de oorspronkelijke voorspelling een hoger percentage hebben gehad. 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Waarom staat de bovenste intentie in de lijst met uitingen? 
Sommige eindpuntuitingen hebben een hoge voorspellingsscore in de beoordelingslijst. U dient deze uitingen alsnog te beoordelen en verifiëren. Ze zijn opgenomen in de lijst omdat de beoordeling van de tweede intentie een score had die te dicht lag bij de score van de hoogst scorende intentie. Er moet ongeveer 15% verschil zijn tussen de bovenste twee intenties.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u uitingen bekeken die bij het eindpunt zijn ingediend en waar LUIS niet zeker van was. Zodra deze uitingen zijn gecontroleerd en als voorbeelduitingen naar de juiste intenties zijn verplaatst, kan LUIS de voorspellingsnauwkeurigheid vergroten.

> [!div class="nextstepaction"]
> [Meer informatie over het gebruik van patronen](luis-tutorial-pattern.md)
