---
title: Extact tekst overeenkomst entiteiten-LUIS
titleSuffix: Azure Cognitive Services
description: Meer informatie over het toevoegen van een lijst entiteit om LUIS label variaties van een woord of woord groep te helpen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499477"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Een lijst entiteit gebruiken om de entiteits detectie te verg Roten 
In dit artikel wordt het gebruik van een [lijst entiteit](luis-concept-entity-types.md) gedemonstreerd om de entiteits detectie te verg Roten. De lijst entiteiten hoeven niet te worden gelabeld omdat ze exact overeenkomen met de termen.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een lijst entiteit maken 
> * Genormaliseerde waarden en synoniemen toevoegen
> * Verbeterde Entiteits-ID valideren

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Laatste [node. js](https://nodejs.org)
> * [HOMEAUTOMATION Luis-app](luis-get-started-create-app.md). Als u de Home Automation-app niet hebt gemaakt, maakt u een nieuwe app en voegt u het vooraf gedefinieerde domein **HomeAutomation**toe. Train en publiceer de app. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (als query's meermaals worden doorzocht), App-ID, versie-id en [regio](luis-reference-regions.md) voor de Luis-app.

> [!Tip]
> Als u nog geen abonnement hebt, kunt u zich registreren voor een [gratis account](https://azure.microsoft.com/free/).

Alle code in dit artikel is beschikbaar in de [github-opslag plaats Azure-samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>HomeAutomation-app gebruiken
De HomeAutomation-app biedt u de controle over apparaten zoals lampen, entertainment systemen en omgevings controles zoals verwarming en koeling. Deze systemen hebben verschillende namen die namen van fabrikanten, bijnamen, acroniemen en slang kunnen bevatten. 

Een systeem met veel namen in verschillende cult uren en demografische gegevens is de Thermo staat. Een thermo staat kan zowel koel-als verwarmings systemen voor een huis of gebouw best uren.

In het ideale geval worden de volgende uitingen omgezet in de vooraf samengestelde entiteit **HomeAutomation. apparaat**:

|#|utterance|entiteit geïdentificeerd|Score|
|--|--|--|--|
|1|AC inschakelen|HomeAutomation. apparaat-"AC"|0,8748562|
|2|de hitte in te scha kelen|HomeAutomation. apparaat-"hitte"|0,784990132|
|3|IT-koeler maken|||

De eerste twee uitingen worden toegewezen aan verschillende apparaten. De derde utterance, ' Maak IT kouder ', is niet toegewezen aan een apparaat maar vraagt een resultaat op. LUIS weet niet dat de term ' kouder ', de Thermo staat het aangevraagde apparaat is. In het ideale geval moet LUIS al deze uitingen op hetzelfde apparaat omzetten. 

## <a name="use-a-list-entity"></a>Een lijst entiteit gebruiken
De entiteit HomeAutomation. apparaat is geweldig voor een klein aantal apparaten of met enkele variaties van de namen. Voor een kantoor gebouw of campus groeien de apparaatnamen zich buiten het nut van de HomeAutomation. device-entiteit. 

Een **lijst entiteit** is een goede keuze voor dit scenario, omdat de set voor waarden voor een apparaat in een gebouw of campus een bekende set is, zelfs als het een enorme set is. Met behulp van een lijst entiteit kan LUIS elke mogelijke waarde in de set voor de Thermo staat ontvangen en deze omzetten in alleen het apparaat "Thermo staat". 

Dit artikel gaat over het maken van een entiteits lijst met de Thermo staat. De alternatieve namen voor een thermo staat in dit artikel zijn: 

|alternatieve namen voor Thermo staat|
|--|
| auto |
| a/c|
| a-c|
|hitte|
|direct|
|hotter|
|Huizen|
|kouder|

Als LUIS een nieuw alternatief vaak moet bepalen, is een [woordgroepen lijst](luis-concept-feature.md#how-to-use-phrase-lists) een beter antwoord.

## <a name="create-a-list-entity"></a>Een lijst entiteit maken
Maak een node. js-bestand en kopieer de volgende code hierin. Wijzig de waarden voor authoringKey, appId, versionId en Region.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Gebruik de volgende opdracht om de NPM-afhankelijkheden te installeren en de code uit te voeren om de lijst entiteit te maken:

```console
npm install && node add-entity-list.js
```

De uitvoer van de uitvoering is de ID van de lijst entiteit:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Het model trainen
Train LUIS zodat de nieuwe lijst de query resultaten kan beïnvloeden. Training is een tweedelig proces van training en vervolgens de status controleren als de training is voltooid. Het kan even duren voordat een app met veel modellen traint. De volgende code traint de app vervolgens totdat de training is geslaagd. De code maakt gebruik van een wacht-en-nieuwe strategie om te voor komen dat de 429 "te veel aanvragen"-fout. 

Maak een node. js-bestand en kopieer de volgende code hierin. Wijzig de waarden voor authoringKey, appId, versionId en Region.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Gebruik de volgende opdracht om de code uit te voeren om de app te trainen:

```console
node train.js
```

De uitvoer van de uitvoering is de status van elke herhaling van de training van de LUIS-modellen. Voor de volgende uitvoering is slechts één controle op de training vereist:

```console
1 trained = true
[ { modelId: '2c549f95-867a-4189-9c35-44b95c78b70f',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '5530e900-571d-40ec-9c78-63e66b50c7d4',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '519faa39-ae1a-4d98-965c-abff6f743fe6',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9671a485-36a9-46d5-aacd-b16d05115415',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '9ef7d891-54ab-48bf-8112-c34dcd75d5e2',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } },
  { modelId: '8e16a660-8781-4abf-bf3d-f296ebe1bf2d',
    details: { statusId: 2, status: 'UpToDate', exampleCount: 45 } } ]

```
## <a name="publish-the-model"></a>Het model publiceren
Publiceren, zodat de lijst entiteit beschikbaar is vanuit het eind punt.

Maak een node. js-bestand en kopieer de volgende code hierin. Wijzig de waarden voor endpointKey, appId en Region. U kunt uw authoringKey gebruiken als u niet van plan bent dit bestand aan te roepen buiten uw quotum limiet.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Gebruik de volgende opdracht om de code uit te voeren voor het uitvoeren van een query op de app:

```console
node publish.js
```

De volgende uitvoer bevat de eind punt-URL voor alle query's. Echte JSON-resultaten zouden de echte appID bevatten. 

```json
{ 
  versionId: null,
  isStaging: false,
  endpointUrl: 'https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>',
  region: null,
  assignedEndpointKey: null,
  endpointRegion: 'westus',
  publishedDateTime: '2018-01-29T22:17:38Z' }
}
```

## <a name="query-the-app"></a>Query's uitvoeren op de app 
Voer een query uit op de app vanuit het eind punt om te bewijzen dat de lijst entiteit helpt het apparaattype te LUIS te bepalen.

Maak een node. js-bestand en kopieer de volgende code hierin. Wijzig de waarden voor endpointKey, appId en Region. U kunt uw authoringKey gebruiken als u niet van plan bent dit bestand aan te roepen buiten uw quotum limiet.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Gebruik de volgende opdracht om de code uit te voeren en de app te doorzoeken:

```console
node train.js
```

De uitvoer is de query resultaten. Omdat de code de **uitgebreide** naam/waarde-paar aan de query reeks heeft toegevoegd, bevat de uitvoer alle intenten en hun scores:

```json
{
  "query": "turn up the heat",
  "topScoringIntent": {
    "intent": "HomeAutomation.TurnOn",
    "score": 0.139018849
  },
  "intents": [
    {
      "intent": "HomeAutomation.TurnOn",
      "score": 0.139018849
    },
    {
      "intent": "None",
      "score": 0.120624863
    },
    {
      "intent": "HomeAutomation.TurnOff",
      "score": 0.06746891
    }
  ],
  "entities": [
    {
      "entity": "heat",
      "type": "HomeAutomation.Device",
      "startIndex": 12,
      "endIndex": 15,
      "score": 0.784990132
    },
    {
      "entity": "heat",
      "type": "DevicesList",
      "startIndex": 12,
      "endIndex": 15,
      "resolution": {
        "values": [
          "Thermostat"
        ]
      }
    }
  ]
}
```

Het specifieke apparaat van de **Thermo** staat wordt aangeduid met een resultaat gerichte query van ' de hitte in te scha kelen '. Omdat de oorspronkelijke HomeAutomation. apparaat-entiteit zich nog steeds in de app bevindt, ziet u ook de resultaten. 

Probeer de andere twee uitingen om te zien dat ze ook als Thermo staat worden geretourneerd. 

|#|utterance|Vennootschap|type|waarde|
|--|--|--|--|--|
|1|AC inschakelen| auto | DevicesList | Thermo staat|
|2|de hitte in te scha kelen|temperatuur| DevicesList |Thermo staat|
|3|IT-koeler maken|kouder|DevicesList|Thermo staat|

## <a name="next-steps"></a>Volgende stappen

U kunt een andere lijst entiteit maken om de locaties van apparaten uit te breiden naar kamers, vloeren of gebouwen. 
