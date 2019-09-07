---
title: Uitingen importeren met behulp van node. js-LUIS
titleSuffix: Azure Cognitive Services
description: Meer informatie over het bouwen van een LUIS-app via een programma uit bestaande gegevens in CSV-indeling met behulp van het ontwerpen van LUIS-API.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 1bee26dc57fd844703e2c9c97b38b9a433227fbf
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387945"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Bouw een LUIS-app via een programma met behulp van Node.js

LUIS, biedt een programmeer-API die alles die werkt de [LUIS](luis-reference-regions.md) website heeft. Dit bespaart tijd wanneer u bestaande gegevens en het normaal zou zijn sneller te maken van een LUIS-app via een programma dan door de gegevens handmatig invoeren. 

## <a name="prerequisites"></a>Vereisten

* Meld u aan bij de [Luis](luis-reference-regions.md) -website en zoek uw [ontwerp sleutel](luis-concept-keys.md#authoring-key) in de account instellingen. U gebruikt deze sleutel voor het aanroepen van de API's voor ontwerpen.
* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
* Dit artikel begint met een CSV voor een hypothetische logboek bestanden van gebruikers aanvragen van een bedrijf. Download het [hier](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Installeer de meest recente Node.js met NPM. Dit ook downloaden via [hier](https://nodejs.org/en/download/).
* **(Aanbevolen)**  Visual Studio Code IntelliSense en foutopsporing kunt uitvoeren, downloadt u dit via [hier](https://code.visualstudio.com/) gratis.

Alle code in dit artikel is beschikbaar in de [github-opslag plaats Azure-samples language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv). 

## <a name="map-preexisting-data-to-intents-and-entities"></a>Bestaande gegevens worden toegewezen aan intenties en entiteiten
Zelfs als u een systeem dat niet is gemaakt met LUIS Denk eraan dat u hebt als het tekstuele gegevens bevat die is toegewezen aan gebruikers verschillende dingen wilt doen, is het mogelijk dat u kunnen komen met een toewijzing van de bestaande categorieën van de invoer van de gebruiker naar intents in LUIS. Als u belangrijke woorden of zinsdelen in wat de gebruikers gezegd identificeren kunt, kunnen deze woorden toewijzen aan entiteiten.

Open het [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) bestand. Het bevat een logboek van query's van gebruikers naar een hypothetische home automation-service, met inbegrip van hoe ze zijn gecategoriseerd, wat de gebruiker gezegd en sommige kolommen met nuttige informatie die buiten deze worden opgehaald. 

![CSV-bestand van de vooraf bestaande gegevens](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

U ziet dat de **RequestType** kolom intents, kan worden en de **aanvragen** kolom ziet u een voorbeeld-utterance. De andere velden kunnen entiteiten zijn als ze zich in de utterance voordoen. Omdat er intenties en entiteiten voorbeeld uitingen, hebt u de vereisten voor een eenvoudige, voorbeeld-app.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Stappen voor het genereren van een LUIS-app van niet-LUIS-gegevens
Een nieuwe LUIS-app genereren vanuit het CSV-bestand:

* De gegevens uit het CSV-bestand parseren:
    * Converteer naar een indeling die u kunt uploaden naar LUIS met behulp van de API voor ontwerpen. 
    * Verzamel uit de geparseerde gegevens informatie over intents en entiteiten. 
* Maak ontwerp-API-aanroepen naar:
    * Maak de app.
    * Voeg intents en entiteiten toe die zijn verzameld uit de geparseerde gegevens. 
    * Als u de LUIS-app hebt gemaakt, kunt u de voorbeeld-uitingen van de geparseerde gegevens toevoegen. 

U kunt deze programma stroom bekijken in het laatste deel van het `index.js` bestand. Kopiëren of [downloaden](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) deze code en sla het op in `index.js`.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Parseren van de CSV

De gegevens van de kolom die bestaan uit de uitingen in de CSV moeten worden geparseerd in een JSON-indeling die LUIS kan begrijpen. Deze JSON-indeling moet bevatten een `intentName` veld dat de intentie van de utterance identificeert. Het moet ook bevatten een `entityLabels` veld, die kan niet leeg zijn als er geen entiteiten in de utterance. 

Bijvoorbeeld, wordt de vermelding voor 'Inschakelen voor de verlichting' toegewezen aan deze JSON:

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

In dit voorbeeld wordt de `intentName` afkomstig is van de aanvraag van de gebruiker onder de **aanvraag** kolomkop in het CSV-bestand en de `entityName` afkomstig is van de andere kolommen met belangrijke informatie. Bijvoorbeeld, als er een vermelding voor **bewerking** of **apparaat**, en dat tekenreeks vindt ook plaats in de werkelijke aanvraag en deze kan worden aangemerkt als een entiteit. De volgende code toont dit proces te parseren. U kunt kopiëren of [downloaden](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) het en sla deze op `_parse.js`.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>De LUIS-app maken
Zodra de gegevens heeft zijn geparseerd in JSON, kunt u deze aan een LUIS-app toevoegen. De volgende code wordt de LUIS-app gemaakt. Kopiëren of [downloaden](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) , en op te slaan in `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Intents toevoegen
Nadat u een app hebt, moet u intents toe. De volgende code wordt de LUIS-app gemaakt. Kopiëren of [downloaden](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) , en op te slaan in `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Entiteiten toevoegen
De volgende code wordt de entiteiten toevoegt aan de LUIS-app. Kopiëren of [downloaden](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) , en op te slaan in `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Utterances toevoegen
Zodra de intenties en entiteiten zijn gedefinieerd in de LUIS-app, kunt u de uitingen toevoegen. De volgende code gebruikt de [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API, zodat u kunt maximaal 100 uitingen tegelijk toevoegen.  Kopiëren of [downloaden](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) , en op te slaan in `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>De code uitvoeren


### <a name="install-nodejs-dependencies"></a>Installeer Node.js-afhankelijkheden
De Node.js-afhankelijkheden installeren vanuit NPM in de terminal/opdrachtregel.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Configuratie-instellingen wijzigen
Om deze toepassing gebruikt, moet u de waarden in het bestand index.js wijzigen naar de eindpuntsleutel van uw eigen en geeft u de naam die u wilt dat de app hebben. U kunt ook de cultuur van de app instellen of wijzigen van het versienummer.

Open het bestand index.js en deze waarden aan de bovenkant van het bestand te wijzigen.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Het script uitvoeren
Voer het script uit vanaf de terminal/opdrachtregel met behulp van Node.js.

```console
> node index.js
```

of

```console
> npm start
```

### <a name="application-progress"></a>Voortgang van de toepassing
Terwijl de toepassing wordt uitgevoerd, ziet u de opdrachtregel wordt uitgevoerd. De uitvoer van de opdracht bevat de indeling van de antwoorden van LUIS.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>Open de LUIS-app
Zodra het script is voltooid, kunt u zich aanmelden bij [Luis](luis-reference-regions.md) en de Luis-app zien die u hebt gemaakt onder **mijn apps**. U zou het mogelijk om te zien van de uitingen die u hebt toegevoegd onder de **inschakelen**, **uitschakelen**, en **geen** intents.

![De bedoeling inschakelen](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Testen en trainen van uw app in de website van LUIS](luis-interactive-test.md)

## <a name="additional-resources"></a>Aanvullende resources

Deze voorbeeldtoepassing gebruikmaakt van de volgende LUIS APIs:
- [app maken](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [intents toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [entiteiten toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [utterances toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
