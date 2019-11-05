---
title: Vooraf gebouwde modellen voor Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS bevat een reeks vooraf gemaakte modellen voor het snel toevoegen van veelvoorkomende, gespreks gebruikers scenario's.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 2e90a5f3f7cd2cfde2a2ead26674d2348a24ae6f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507779"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Vooraf samengestelde modellen voor veelvoorkomende gebruiks scenario's toevoegen 

LUIS bevat een reeks vooraf ontwikkelde intenties van de vooraf ontwikkelde domeinen voor het snel toevoegen van algemene intents en uitingen. Dit is een snelle en eenvoudige manier om mogelijkheden toe te voegen aan uw gesprek-client-app zonder dat u de modellen hoeft te ontwerpen voor deze mogelijkheden. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-a-prebuilt-domain"></a>Een vooraf gedefinieerd domein toevoegen

1. Selecteer uw app op de pagina **mijn apps** . Hiermee opent u uw app in de sectie **Build** van de app. 

1. Selecteer op de pagina **intenties** vooraf **gemaakte domeinen toevoegen** op de werk balk aan de linkerkant. 

1. Selecteer de **kalender** intentie en selecteer vervolgens de knop **domein toevoegen** .

    ![Kalender vooraf gebouwd domein toevoegen](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Selecteer in het linkernavigatievenster de optie **intenties** om de agenda-intenties weer te geven. Elke intentie van dit domein wordt voorafgegaan door `Calendar.`. Samen met uitingen worden twee entiteiten voor dit domein toegevoegd aan de app: `Calendar.Location` en `Calendar.Subject`. 

### <a name="train-and-publish"></a>Trainen en publiceren

1. Nadat het domein is toegevoegd, traint u de app door **Train** te selecteren in de bovenste werk balk aan de rechter kant. 

1. Selecteer in de bovenste werk balk de optie **publiceren**. Publiceren naar **productie**. 

1. Wanneer de melding over de groene geslaagde poging wordt weer gegeven, selecteert u de koppeling naar **de lijst met eind punten** om de eind punten weer te geven.

1. Selecteer een eind punt. Er wordt een nieuw browser tabblad geopend in dat eind punt. Blijf het browser tabblad geopend en ga verder naar de sectie **testen** .

### <a name="test"></a>Testen

Test de nieuwe intentie op het eind punt door een waarde toe te voegen voor de **q** -para meter: `Schedule a meeting with John Smith in Seattle next week`.

LUIS retourneert de juiste intentie en het onderwerp van de vergadering:

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="add-a-prebuilt-intent"></a>Een vooraf ontwikkelde intentie toevoegen

1. Selecteer uw app op de pagina **mijn apps** . Hiermee opent u uw app in de sectie **Build** van de app. 

1. Selecteer op de pagina **intenties** de optie **vooraf gemaakte intentie toevoegen** op de werk balk boven de lijst met doel stellingen. 

1. Selecteer de optie **Utilities. annuleren** in het pop-updialoogvenster. 

    ![Vooraf gebouwde intentie toevoegen](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Selecteer de knop **gereed** .

### <a name="train-and-test"></a>Trainen en testen

1. Nadat het doel is toegevoegd, traint u de app door **Train** te selecteren in de bovenste werk balk met de rechter muisknop. 

1. Test de nieuwe intentie door **test** te selecteren in de rechter werkbalk. 

1. Voer uitingen in het tekstvak in om het volgende te annuleren:

    |Utterance testen|Voorspellingsscore|
    |--|:--|
    |Ik wil mijn vlucht annuleren.|0,67|
    |De aankoop annuleren.|0,52|
    |De vergadering annuleren.|0,56|

    ![Preconstrueerde opzet testen](./media/luis-prebuilt-intents/test.png)

## <a name="add-a-prebuilt-entity"></a>Een vooraf samengestelde entiteit toevoegen

1. Open uw app door te klikken op de naam op de pagina **mijn apps** en klik vervolgens op **entiteiten** aan de linkerkant. 

1. Klik op de pagina **entiteiten** op **vooraf samengestelde entiteit toevoegen**.

1. Selecteer in het dialoog venster **vooraf gemaakte entiteiten toevoegen** de datetimeV2-vooraf gedefinieerde entiteit. 

    ![Het dialoog venster vooraf samengestelde entiteit toevoegen](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Selecteer **Done**. Nadat de entiteit is toegevoegd, hoeft u de app niet te trainen. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publiceren om het vooraf ontwikkelde model te bekijken vanuit het Voorspellings eindpunt

De eenvoudigste manier om de waarde van een vooraf gebouwd model weer te geven, is door een query uit te zoeken vanuit het gepubliceerde eind punt. 

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Entiteiten markeren die een vooraf gebouwd entity token bevatten
 Als u tekst hebt, zoals `HH-1234`, die u wilt markeren als een aangepaste entiteit _en_ u een [vooraf gebouwd nummer](luis-reference-prebuilt-number.md) hebt toegevoegd aan het model, kunt u de aangepaste entiteit niet markeren in de Luis-Portal. U kunt deze markeren met de API. 

 Als u dit type token wilt markeren, waarbij een deel ervan al is gemarkeerd met een vooraf samengestelde entiteit, verwijdert u de vooraf opgebouwde entiteit uit de LUIS-app. U hoeft de app niet te trainen. Markeer vervolgens het token met uw eigen aangepaste entiteit. Voeg vervolgens de vooraf opgebouwde entiteit weer toe aan de LUIS-app.

 Voor een ander voor beeld kunt u het utterance als een lijst met voor keuren voor klasse beschouwen: `I want first year spanish, second year calculus, and fourth year english lit.` als de LUIS-app het Volg nummer van de prebuild heeft toegevoegd, `first`, `second`en `fourth` al zijn gemarkeerd met een rang telwoord. Als u het rang telwoord en de klasse wilt vastleggen, kunt u een samengestelde entiteit maken en deze omlopen rond het vooraf gedefinieerde rang telwoord en de aangepaste entiteit voor klassenaam.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Model bouwen vanuit. CSV met REST Api's](./luis-tutorial-node-import-utterances-csv.md)
