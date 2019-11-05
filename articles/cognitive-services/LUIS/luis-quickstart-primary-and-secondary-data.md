---
title: 'Zelf studie: eenvoudige entiteit, woordgroepen lijst-LUIS'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie haalt u via machine learning verkregen gegevens over een functienaam op uit een utterance met behulp van de entiteit Simple. Als u de nauwkeurigheid van het extraheren wilt vergroten, voegt u een woordenlijst toe met termen die specifiek gekoppeld zijn aan de Simple-entiteit.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: a917176cb06e833745996326520341c1f819c5bf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465415"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>Zelf studie: namen extra heren met een eenvoudige entiteit en een woordgroepen lijst

In deze zelfstudie haalt u via machine learning verkregen gegevens over een functienaam op uit een utterance met behulp van de entiteit **Simple**. Als u de nauwkeurigheid van het extraheren wilt vergroten, voegt u een woordenlijst toe met termen die specifiek gekoppeld zijn aan de Simple-entiteit.

De Simple-entiteit detecteert een concept met enkelvoudige gegevens dat is opgenomen in woorden of woordgroepen.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Entiteit Simple toevoegen 
> * Woordenlijst toevoegen om signalering van woorden te verbeteren
> * Trainen 
> * Publiceren 
> * Intenties en entiteiten ophalen van eindpunt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]


## <a name="simple-entity"></a>Eenvoudige entiteit

Tijdens deze zelfstudie wordt een nieuwe Simple-entiteit toegevoegd waarmee de functienaam kan worden geëxtraheerd. Het doel van de entiteit Simple in deze LUIS-app is om LUIS te leren wat een functienaam is en waar dit kan worden gevonden in een utterance. Het deel van de utterance dat de functienaam vormt, kan per utterance verschillen afhankelijk van woordkeuze en lengte van de utterance. LUIS heeft voorbeelden nodig van functienamen uit alle intents waarin gebruik wordt gemaakt van functienamen.  

Een Simple-entiteit is een goede keuze voor dit type gegevens wanneer:

* Gegevens één concept vormen.
* Gegevens niet goed zijn opgemaakt als een reguliere expressie.
* Gegevens niet van algemene aard zijn zoals een vooraf gedefinieerde entiteit van een telefoonnummer of gegevens.
* Gegevens niet precies overeenkomen met een lijst van bekende woorden, zoals een lijstentiteit.
* Gegevens bevatten geen andere gegevens items, zoals een samengestelde entiteit of contextuele rollen.

Laten we de volgende utterances uit een chatbot als voorbeeld nemen:

|Utterance|Extraheerbare functienaam|
|:--|:--|
|Ik wil solliciteren op de nieuwe functie als boekhouder.|boekhouder|
|Verzend mijn CV voor de functie van technicus.|technicus|
|Sollicitatieformulier invullen voor vacature 123456|123456|

Het is moeilijk om te bepalen wat de functienaam is omdat dit een zelfstandig naamwoord, werkwoord of een woordgroep van meerdere woorden kan zijn. Bijvoorbeeld:

|Taken|
|--|
|technicus|
|software-engineer|
|senior software-engineer|
|manager technisch team |
|luchtverkeersleider|
|automonteur|
|ambulancebestuurder|
|operateur|
|onderhoudsmonteur|
|monteur voor draaiende machineonderdelen|

In deze LUIS-app komen functienamen in verschillende intenties voor. Door deze woorden in alle utterances binnen intents van een label te voorzien, leert LUIS meer over wat elke functienaam is en waar deze kan worden gevonden in utterances.

Zodra de entiteiten in de voorbeeldutterances zijn gemarkeerd, is het belangrijk dat u een woordgroepenlijst toevoegt om het signaal van de Simple-entiteit te verbeteren. Een woordgroepenlijst wordt **niet** gebruikt als een exacte overeenkomst en hoeft niet elke mogelijke waarde te zijn die u verwacht. 

## <a name="import-example-app"></a>Voorbeeld-app importeren

1.  Download het [app-JSON-bestand](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json) uit de zelfstudie Intenties en sla het op.

2. Importeer de JSON in een nieuwe app.

3. Ga naar het gedeelte **Beheren**, open het tabblad **Versies**, kloon de versie en noem deze `simple`. Klonen is een uitstekende manier om te experimenteren met verschillende functies van LUIS zonder dat de oorspronkelijke versie wordt gewijzigd. Omdat de versienaam wordt gebruikt als onderdeel van de URL-route, kan de naam geen tekens bevatten die niet zijn toegestaan in een URL.

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>Entiteiten markeren in de voorbeelduitingen van een intentie

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecteer op de pagina **Intent** de intentie **ApplyForJob**. 

1. In de utterance `I want to apply for the new accounting job` selecteert u `accounting`, voert u `Job` in het bovenste veld van het snelmenu in en selecteert u **Create new entity** in het snelmenu. 

    [![Scherm opname van LUIS met ' ApplyForJob ' voor het maken van entiteits stappen gemarkeerd](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Scherm opname van LUIS met ' ApplyForJob ' voor het maken van entiteits stappen gemarkeerd")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. Controleer de naam en het type van de entiteit in het pop-upvenster en selecteer **Done**.

    ![Een modaal pop-updialoogvenster voor een entiteit Simple creëren met de naam van de functie en het type Simple](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. Markeer in de resterende uitingen de taakgerelateerde woorden met de entiteit **Taak** door het woord of zinsdeel te selecteren en vervolgens in het contextmenu **Taak** te selecteren. 

    [![Scherm opname van LUIS labeling taak entiteit gemarkeerd](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Scherm opname van LUIS labeling taak entiteit gemarkeerd")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>Meer voorbeelduitingen toevoegen en entiteit markeren

Eenvoudige entiteiten hebben veel voorbeelden nodig om voorspellingen van hoge betrouwbaarheid te bieden. 
 
1. Voeg meer utterances toe en markeer de woorden en woordgroepen als entiteit **Job**. 

    |Utterance|De entiteit Job|
    |:--|:--|
    |Ik solliciteer voor de functie van programmamanager op de afdeling Onderzoek en Ontwikkeling|Programmamanager|
    |Hier is mijn sollicitatiebrief naar de functie van kok.|kok|
    |Mijn cv voor de functie van kampleider heb ik bijgesloten.|kampleider|
    |Dit is mijn cv voor de functie van administratief medewerker.|administratief medewerker|
    |Ik wil graag solliciteren op de managementfunctie op de verkoopafdeling.|management, verkoopafdeling|
    |Dit is mijn cv voor de nieuwe functie als boekhouder.|boekhouder|
    |Mijn sollicitatiebrief voor de functie van barmedewerker is bijgesloten.|barmedewerker|
    |Ik wil met deze brief solliciteren naar de functie van dakdekker en glaszetter.|dakdekker, glaszetter|
    |Mijn cv voor de functie van buschauffeur vindt u hier.|buschauffeur|
    |Ik ben een gediplomeerd verpleegkundige. Hier is mijn cv.|gediplomeerd verpleegkundige|
    |Ik wil u hierbij mijn sollicatieformulieren aanbieden voor de positie als docent die ik in de krant heb gelezen.|docent|
    |Dit is mijn cv voor de functie van vakkenvuller op de afdeling Fruit en groenten.|vakkenvuller|
    |Solliciteren als tegelzetter.|tegelzetter|
    |Bijgevoegd cv voor landschapsarchitect.|landschapsarchitect|
    |Mijn cv voor de functie als biologiedocent is bijgesloten.|biologiedocent|
    |Ik wil graag solliciteren naar een functie binnen het vakgebied fotografie.|fotografie|

## <a name="mark-job-entity-in-other-intents"></a>Taakentiteit in andere intenties markeren

1. Selecteer **Intents** in het linkermenu.

1. Selecteer **GetJobInformation** in de lijst met intents. 

1. De taken in de voorbeelduitingen labelen

    Als er meer voorbeeld uitingen in één intentie is dan een andere intentie, heeft die intentie een hogere kans om het hoogste voor speld doel te zijn. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>De app trainen zodat de wijzigingen aan de intentie kunnen worden getest 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>De app publiceren zodat op het getrainde model query's kunnen worden uitgevoerd vanaf het eindpunt

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van eindpunt 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Ga naar het einde van de URL in het adres en voer `Here is my c.v. for the engineering job` in. De laatste parameter van de queryreeks is `q`, de utterance **query**. Deze utterance is niet hetzelfde als een van de gelabelde utterances en dit is dus een goede test die de utterances van `ApplyForJob` als resultaat moet geven.

    ```json
    {
      "query": "Here is my c.v. for the engineering job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.98052007
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.98052007
        },
        {
          "intent": "GetJobInformation",
          "score": 0.03424581
        },
        {
          "intent": "None",
          "score": 0.0015820954
        }
      ],
      "entities": [
        {
          "entity": "engineering",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 34,
          "score": 0.668959737
        }
      ]
    }
    ```
    
    LUIS heeft de juiste intent, **ApplyForJob**, gevonden en de juiste entiteit, **Job**, uitgepakt met een waarde van `engineering`.


## <a name="names-are-tricky"></a>Namen zijn verraderlijk
De LUIS-app heeft weliswaar de juiste intentie gevonden met een hoge betrouwbaarheidsfactor en heeft de functienaam geëxtraheerd, maar namen zijn verraderlijk. Probeer het met utterance `This is the lead welder paperwork`.  

In de volgende JSON antwoordt LUIS met de juiste intentie, `ApplyForJob`, maar is de functienaam `lead welder` niet geëxtraheerd. 

```json
{
  "query": "This is the lead welder paperwork",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.860295951
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.860295951
    },
    {
      "intent": "GetJobInformation",
      "score": 0.07265678
    },
    {
      "intent": "None",
      "score": 0.00482481951
    }
  ],
  "entities": []
}
```

Omdat alles een naam kan zijn, is LUIS beter in staat om entiteiten te herkennen als er een woordgroepenlijst bestaat die het signaleringsvermogen versterkt.

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>Als u de signalering van de taakgerelateerde woorden wilt verbeteren, voegt u een woordenlijst met taakgerelateerde woorden toe

Open [jobs-phrase-list.csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) in de GitHub-opslagplaats met Azure-voorbeelden. De lijst bevat meer dan 1000 woorden en woordgroepen. Zoek in de lijst naar woorden met betrekking tot functies die relevant voor uw zijn. Als de woorden of woordgroepen waar u naar op zoek bent niet in de lijst voorkomen, kunt u deze zelf toevoegen.

1. In de sectie **Build** van de LUIS-app, selecteert u **Phrase lists** onder het menu **Improve app performance**.

1. Selecteer **Create new phrase list**. 

1. Noem de nieuwe woordgroepenlijst `JobNames` en kopieer de lijst van jobs-phrase-list.csv naar het tekstvak voor het invoeren van **waarden**.

    [![Scherm opname van dialoog venster nieuwe woordgroepen lijst maken pop-up](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Scherm opname van dialoog venster nieuwe woordgroepen lijst maken pop-up")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Als u wilt dat meer woorden worden toegevoegd aan de woordgroepenlijst, selecteert u **Recommand**, bekijkt u vervolgens de nieuwe **Gerelateerde waarden** en voegt de relevante toe. 

    Zorg ervoor dat **Deze waarden zijn verwisselbaar** aangevinkt is, omdat deze waarden allemaal als synoniemen voor taken moeten worden behandeld. Meer informatie over uitwisselbare en niet-uitwisselbare [Woordenlijstconcepten](luis-concept-feature.md#how-to-use-phrase-lists).

1. Selecteer **gereed** om de lijst met zinsdelen te activeren.

    [![Scherm afbeelding van het dialoog venster nieuwe woordgroepen lijst maken pop-up met woorden in het vak waarden van woordgroepen lijst](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Scherm afbeelding van het dialoog venster nieuwe woordgroepen lijst maken pop-up met woorden in het vak waarden van woordgroepen lijst")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. U moet de app opnieuw trainen en publiceren om de woordgroepenlijst te kunnen gebruiken.

1. Een eindpunt opnieuw opvragen met de dezelfde utterance: `This is the lead welder paperwork.`

    Het JSON-antwoord bevat de geëxtraheerde entiteit:

    ```json
      {
      "query": "This is the lead welder paperwork.",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.983076453
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.983076453
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0120766377
        },
        {
          "intent": "None",
          "score": 0.00248388131
        }
      ],
      "entities": [
        {
          "entity": "lead welder",
          "type": "Job",
          "startIndex": 12,
          "endIndex": 22,
          "score": 0.8373154
        }
      ]
    }
    ```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* [Zelfstudie: intenties zonder entiteiten](luis-quickstart-intents-only.md)
* [Entiteit Simple](luis-concept-entity-types.md) - conceptuele informatie
* [Woordenlijst](luis-concept-feature.md) - conceptuele informatie
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie gebruikt de app Human Resources een via machine learning verkregen Simple-entiteit om functienamen te vinden in utterances. Omdat functienamen tal van verschillende beschrijvingen kunnen hebben, had de app een woordgroepenlijst nodig om de woorden voor functienamen sneller te kunnen genereren. 

> [!div class="nextstepaction"]
> [Een vooraf gemaakte entiteit KeyPhrase toevoegen](luis-quickstart-intent-and-key-phrase.md)
