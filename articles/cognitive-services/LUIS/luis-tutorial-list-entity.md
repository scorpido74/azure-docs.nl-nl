---
title: Tekstovereenkomstentiteiten extactpen - LUIS
titleSuffix: Azure Cognitive Services
description: Meer informatie over het toevoegen van een lijstentiteit om LUIS-labelvariaties van een woord of woordgroep te helpen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: f3c99856eaffc454754618a1eac34630b985a77e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499477"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Een lijstentiteit gebruiken om de entiteitsdetectie te vergroten 
In dit artikel wordt het gebruik van een [lijstentiteit](luis-concept-entity-types.md) getoond om de entiteitsdetectie te vergroten. Lijstentiteiten hoeven niet te worden gelabeld omdat ze exact overeenkomen met de voorwaarden.  

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een lijstentiteit maken 
> * Genormaliseerde waarden en synoniemen toevoegen
> * Verbeterde entiteitsidentificatie valideren

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Laatste [Node.js](https://nodejs.org)
> * [Luis-app voor thuisautomatisering](luis-get-started-create-app.md). Als u de app Domotica niet hebt gemaakt, maakt u een nieuwe app en voegt u de vooraf gebouwde **domeinautomatisering**toe. Train en publiceer de app. 
> * [AuthoringKey,](luis-concept-keys.md#authoring-key) [EndpointKey](luis-concept-keys.md#endpoint-key) (als u vaak wordt opgevraagd), app-id, versie-id en [regio](luis-reference-regions.md) voor de LUIS-app.

> [!Tip]
> Wanneer je nog geen abonnement hebt kun je je gratis [registreren.](https://azure.microsoft.com/free/)

Alle code in dit artikel is beschikbaar op de [Azure-Samples GitHub-repository.](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity) 

## <a name="use-homeautomation-app"></a>HomeAutomation-app gebruiken
De HomeAutomation-app biedt u controle over apparaten zoals verlichting, entertainmentsystemen en omgevingscontroles zoals verwarming en koeling. Deze systemen hebben verschillende namen die fabrikant namen, bijnamen, afkortingen, en slang kan omvatten. 

Een systeem dat veel namen heeft in verschillende culturen en demografie is de thermostaat. Een thermostaat kan zowel koel- als verwarmingssystemen voor een huis of gebouw bedienen.

Idealiter moeten de volgende uitingen worden opgelost aan de prebuilt entiteit **HomeAutomation.Device:**

|#|Uiting|entiteit geïdentificeerd|Score|
|--|--|--|--|
|1|zet de wisselstroom in|HomeAutomation.Device - "ac"|0.8748562|
|2|zet het vuur hoger|HomeAutomation.Device - "warmte"|0.784990132|
|3|maak het kouder|||

De eerste twee uitingen worden toegewezen aan verschillende apparaten. De derde utterance, "make it colder", wordt niet toegewezen aan een apparaat, maar vraagt in plaats daarvan om een resultaat. LUIS weet niet dat de term "kouder" betekent dat de thermostaat het gevraagde apparaat is. In het ideale geval moet LUIS al deze uitingen naar hetzelfde apparaat oplossen. 

## <a name="use-a-list-entity"></a>Een lijstentiteit gebruiken
De homeautomation.device entiteit is zeer geschikt voor een klein aantal apparaten of met weinig variaties van de namen. Voor een kantoorgebouw of campus groeien de apparaatnamen verder dan het nut van de entiteit Thuisbezorgd.Device. 

Een **lijstentiteit** is een goede keuze voor dit scenario, omdat de set termen voor een apparaat in een gebouw of campus een bekende set is, zelfs als het een enorme set is. Door een lijstentiteit te gebruiken, kan LUIS elke mogelijke waarde in de set voor de thermostaat ontvangen en deze oplossen tot slechts het enkele apparaat "thermostaat". 

In dit artikel wordt een entiteitslijst met de thermostaat gemaakt. De alternatieve namen voor een thermostaat in dit artikel zijn: 

|alternatieve namen voor thermostaat|
|--|
| Ac |
| A/c|
| a-c|
|Verwarming|
|snel|
|Heter|
|Koude|
|Kouder|

Als LUIS vaak een nieuw alternatief moet bepalen, is een [woordenlijst](luis-concept-feature.md#how-to-use-phrase-lists) een beter antwoord.

## <a name="create-a-list-entity"></a>Een lijstentiteit maken
Maak een Node.js-bestand en kopieer de volgende code erin. Wijzig de waardevan de auteurSleutel, appId, versionId en regio.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Gebruik de volgende opdracht om de NPM-afhankelijkheden te installeren en de code uit te voeren om de lijstentiteit te maken:

```console
npm install && node add-entity-list.js
```

De uitvoer van de run is de id van de lijstentiteit:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Het model trainen
Train LUIS om de nieuwe lijst te laten beïnvloeden op de queryresultaten. Training is een tweedelige proces van training, dan controleren status als de opleiding wordt gedaan. Een app met veel modellen kan een paar momenten duren om te trainen. De volgende code traint de app en wacht vervolgens tot de training succesvol is. De code maakt gebruik van een wait-and-retry strategie om de 429 "Te veel aanvragen" fout te voorkomen. 

Maak een Node.js-bestand en kopieer de volgende code erin. Wijzig de waardevan de auteurSleutel, appId, versionId en regio.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Gebruik de volgende opdracht om de code uit te voeren om de app te trainen:

```console
node train.js
```

De output van de run is de status van elke iteratie van de training van de LUIS-modellen. Voor de volgende uitvoering was slechts één controle van de opleiding vereist:

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
Publiceer zodat de lijstentiteit beschikbaar is vanaf het eindpunt.

Maak een Node.js-bestand en kopieer de volgende code erin. Wijzig de waarden endpointKey, AppId en Regio. U uw authoringKey gebruiken als u niet van plan bent dit bestand buiten uw quotumlimiet te bellen.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Gebruik de volgende opdracht om de code uit te voeren om de app op te vragen:

```console
node publish.js
```

De volgende uitvoer bevat de url van het eindpunt voor eventuele query's. Echte JSON resultaten zou de echte appID. 

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

## <a name="query-the-app"></a>De app opvragen 
Vraag de app vanaf het eindpunt om te bewijzen dat de lijstentiteit LUIS helpt het apparaattype te bepalen.

Maak een Node.js-bestand en kopieer de volgende code erin. Wijzig de waarden endpointKey, AppId en Regio. U uw authoringKey gebruiken als u niet van plan bent dit bestand buiten uw quotumlimiet te bellen.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Gebruik de volgende opdracht om de code uit te voeren en de app op te vragen:

```console
node train.js
```

De uitvoer is de queryresultaten. Omdat de code de **verbose** naam/waardereeks aan de querytekenreeks heeft toegevoegd, bevat de uitvoer alle intenties en hun scores:

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

Het specifieke apparaat van **Thermostaat** wordt geïdentificeerd met een resultaatgerichte query van "zet de warmte hoger". Aangezien de oorspronkelijke entiteit Domotica.Device zich nog steeds in de app bevindt, u ook de resultaten ervan zien. 

Probeer de andere twee uitingen om te zien dat ze ook worden geretourneerd als een thermostaat. 

|#|Uiting|Entiteit|type|waarde|
|--|--|--|--|--|
|1|zet de wisselstroom in| Ac | ApparatenLijst | Thermostaat|
|2|zet het vuur hoger|Warmte| ApparatenLijst |Thermostaat|
|3|maak het kouder|Kouder|ApparatenLijst|Thermostaat|

## <a name="next-steps"></a>Volgende stappen

U een andere entiteit List maken om apparaatlocaties uit te breiden naar ruimten, vloeren of gebouwen. 
