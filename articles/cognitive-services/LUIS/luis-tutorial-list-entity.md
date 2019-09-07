---
title: Extact tekst overeenkomst entiteiten-LUIS
titleSuffix: Azure Cognitive Services
description: Informatie over het toevoegen van een entiteit lijst om te helpen LUIS label variaties van een woord of woordgroep.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: a722ce39a679fa13e1fe849c46b44f786ea5ee42
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390277"
---
# <a name="use-a-list-entity-to-increase-entity-detection"></a>Een lijst met entiteit gebruiken voor het verhogen van detectie van entiteit 
In dit artikel wordt het gebruik van een [lijst entiteit](luis-concept-entity-types.md) gedemonstreerd om de entiteits detectie te verg Roten. Lijst met entiteiten hoeft te worden gelabeld als ze exact overeenkomen met de voorwaarden zijn.  

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een lijst met entiteit maken 
> * Genormaliseerde waarden en synoniemen toevoegen
> * Verbeterde entiteits-id valideren

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Meest recente [Node.js](https://nodejs.org)
> * [LUIS-app HomeAutomation](luis-get-started-create-app.md). Als u niet de start Automation-app hebt gemaakt hebt, een nieuwe app maken en toevoegen van het vooraf gedefinieerde domein **HomeAutomation**. Train en publiceer de app. 
> * [AuthoringKey](luis-concept-keys.md#authoring-key), [EndpointKey](luis-concept-keys.md#endpoint-key) (als het uitvoeren van query's is vaak), app-ID, versie-ID en [regio](luis-reference-regions.md) voor de LUIS-app.

> [!Tip]
> Als u nog geen een abonnement, kunt u zich registreren voor een [gratis account](https://azure.microsoft.com/free/).

Alle code in dit artikel is beschikbaar in de [github-opslag plaats Azure-samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-list-entity). 

## <a name="use-homeautomation-app"></a>HomeAutomation app gebruiken
De app HomeAutomation geeft u controle over apparaten zoals lichten, entertainmentsystemen en omgeving van besturingselementen, zoals verwarming en koeling. Deze systemen zijn diverse verschillende namen die de fabrikant van namen, bijnamen acroniemen en slang kunnen opnemen. 

Een systeem met veel namen in verschillende culturen en demografische gegevens is de thermostaat. Een thermostaat kunt beheren, zowel koeling en verwarming van een huis of gebouw.

In het ideale geval de volgende uitingen moeten worden omgezet naar de vooraf gedefinieerde entiteit **HomeAutomation.Device**:

|#|Utterance|entiteit geïdentificeerd|score|
|--|--|--|--|
|1|de ac inschakelen|HomeAutomation.Device - "ac"|0.8748562|
|2|inschakelen van de heatmap|HomeAutomation.Device - "voor"|0.784990132|
|3|Controleer kouder|||

De eerste twee uitingen worden toegewezen aan verschillende apparaten. De derde utterance 'Maak deze kouder', niet is toegewezen aan een apparaat, maar in plaats daarvan een resultaat-aanvragen. LUIS bekend niet is dat de term 'kouder', betekent dat de thermostaat is het gewenste apparaat. LUIS moet worden al deze uitingen in het ideale geval omgezet naar hetzelfde apparaat. 

## <a name="use-a-list-entity"></a>Een lijst met entiteit gebruiken
De entiteit HomeAutomation.Device is erg handig voor een klein aantal apparaten of met enkele variaties van de namen. Voor een kantoorgebouw of campus groter de apparaatnamen wordt dan het nut van de entiteit HomeAutomation.Device. 

Een **entiteit lijst** is een goede keuze voor dit scenario omdat de set voorwaarden voor een apparaat in een gebouw of campus een bekende set is, zelfs als het een grote set. Met behulp van een entiteit lijst, LUIS ontvangen van een mogelijke waarde in de verzameling van de thermostaat, en naar alleen de één apparaat "thermostaat" oplossen. 

Dit artikel gaat over het maken van een entiteits lijst met de Thermo staat. De alternatieve namen voor een thermo staat in dit artikel zijn: 

|alternatieve namen voor thermostaat|
|--|
| netstroom |
| wisselstroomadapter|
| een c|
|brandstof|
|hot|
|hotter|
|koude|
|kouder|

Als LUIS nodig heeft om een nieuw alternatief vaak te bepalen en vervolgens een [woordgroepenlijst](luis-concept-feature.md#how-to-use-phrase-lists) is een beter antwoord.

## <a name="create-a-list-entity"></a>Een lijst met entiteit maken
Maak een Node.js-bestand en kopieer de volgende code naar het. Wijzig de waarden authoringKey, appId, versionId en regio.

   [!code-javascript[Create DevicesList List Entity](~/samples-luis/documentation-samples/tutorial-list-entity/add-entity-list.js "Create DevicesList List Entity")]

Gebruik de volgende opdracht om te installeren de NPM-afhankelijkheden en voert u de code voor het maken van de entiteit lijst:

```console
npm install && node add-entity-list.js
```

De uitvoer van de uitvoering is de ID van de entiteit van de lijst:

```console
026e92b3-4834-484f-8608-6114a83b03a6
```

## <a name="train-the-model"></a>Het model trainen
LUIS trainen in volgorde van de nieuwe lijst invloed heeft op de queryresultaten. Training is een proces tweedelige van training, klikt u vervolgens de status controleren als de training wordt gedaan. Een app met meerdere modellen duurt enkele minuten om te trainen. De volgende code traint van de app en wacht totdat de training geslaagd is. De code maakt gebruik van een strategie voor wait-en-opnieuw proberen om te voorkomen dat de 429 ' te veel aanvragen ' fout. 

Maak een Node.js-bestand en kopieer de volgende code naar het. Wijzig de waarden authoringKey, appId, versionId en regio.

   [!code-javascript[Train LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/train.js "Train LUIS")]

Gebruik de volgende opdracht om uit te voeren van de code voor het trainen van de app:

```console
node train.js
```

De uitvoer van de uitvoering is de status van elke herhaling van de training van de LUIS-modellen. De volgende uitvoering vereist slechts één selectievakje training:

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
## <a name="publish-the-model"></a>Publiceer het model
Publiceren, zodat de entiteit lijst beschikbaar via het eindpunt is.

Maak een Node.js-bestand en kopieer de volgende code naar het. Wijzig de waarden endpointKey, toepassings-id en de regio. U kunt uw authoringKey gebruiken als u niet van plan bent om aan te roepen van dit bestand buiten de limiet voor uw.

   [!code-javascript[Publish LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/publish.js "Publish LUIS")]

Gebruik de volgende opdracht in de code om op te vragen van de app uit te voeren:

```console
node publish.js
```

De volgende uitvoer bevat de eindpunt-url voor alle query's. Echte JSON-resultaten, zou de echte appID bevatten. 

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

## <a name="query-the-app"></a>Query uitvoeren op de app. 
Query uitvoeren op de app van het eindpunt om te bewijzen dat de entiteit lijst bijdraagt aan een LUIS bepalen van het apparaattype.

Maak een Node.js-bestand en kopieer de volgende code naar het. Wijzig de waarden endpointKey, toepassings-id en de regio. U kunt uw authoringKey gebruiken als u niet van plan bent om aan te roepen van dit bestand buiten de limiet voor uw.

   [!code-javascript[Query LUIS](~/samples-luis/documentation-samples/tutorial-list-entity/query.js "Query LUIS")]

Gebruik de volgende opdracht naar de code worden uitgevoerd en de app op te vragen:

```console
node train.js
```

De uitvoer is de queryresultaten. Omdat de code toegevoegd de **uitgebreide** naam/waarde-paar aan de querytekenreeks, de uitvoer bevat alle intents en hun scores:

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

Het apparaat van **thermostaat** wordt aangeduid met een resultaat-georiënteerde query van 'inschakelen van de heatmap'. Omdat de oorspronkelijke HomeAutomation.Device entiteit nog steeds in de app is, kunt u ook de resultaten bekijken. 

Probeer de andere twee uitingen om te zien dat ze ook worden geretourneerd als een thermostaat. 

|#|Utterance|entiteit|type|waarde|
|--|--|--|--|--|
|1|de ac inschakelen| netstroom | DevicesList | Thermostaat|
|2|inschakelen van de heatmap|heatmap| DevicesList |Thermostaat|
|3|Controleer kouder|kouder|DevicesList|Thermostaat|

## <a name="next-steps"></a>Volgende stappen

U kunt een andere lijst entiteit om uit te breiden apparaat locaties te ruimten, verdiepingen, gebouwen maken. 
