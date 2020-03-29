---
title: Uitingen importeren met Node.js - LUIS
titleSuffix: Azure Cognitive Services
description: Meer informatie over het programmatisch bouwen van een LUIS-app op basis van reeds bestaande gegevens in CSV-indeling met behulp van de LUIS Authoring API.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: ef5f6967b7ad9500672d00d93dd8acaca99e5948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499468"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Een LUIS-app programmatisch bouwen met Behulp van Node.js

LUIS biedt een programmatische API die alles doet wat de [LUIS-website](luis-reference-regions.md) doet. Dit kan tijd besparen wanneer u reeds bestaande gegevens hebt en het zou sneller zijn om een LUIS-app programmatisch te maken dan door met de hand informatie in te voeren. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Vereisten

* Meld u aan bij de [LUIS-website](luis-reference-regions.md) en vind uw [ontwerpsleutel](luis-concept-keys.md#authoring-key) in Accountinstellingen. U gebruikt deze sleutel om de auteurs-API's te bellen.
* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
* Dit artikel begint met een CSV voor de logbestanden van gebruikersverzoeken van een hypothetisch bedrijf. Download het [hier.](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv)
* Installeer de nieuwste Node.js met NPM. U kunt het [hier](https://nodejs.org/en/download/) downloaden.
* **[Aanbevolen]** Visual Studio Code voor IntelliSense en debugging, download het [vanaf hier](https://code.visualstudio.com/) gratis.

Alle code in dit artikel is beschikbaar op de [GitHub-repository voor taalgebruik van Azure-Samples.](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv) 

## <a name="map-preexisting-data-to-intents-and-entities"></a>Vooraf bestaande gegevens toewijzen aan intents en entiteiten
Zelfs als u een systeem hebt dat niet is gemaakt met LUIS in het achterhoofd, als het tekstuele gegevens bevat die worden toegewezen aan verschillende dingen die gebruikers willen doen, u mogelijk een toewijzing vinden van de bestaande categorieën gebruikersinvoer naar intents in LUIS. Als u belangrijke woorden of zinnen identificeren in wat de gebruikers hebben gezegd, kunnen deze woorden worden toegewezen aan entiteiten.

Open [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) het bestand. Het bevat een logboek van gebruikersquery's naar een hypothetische domoticaservice, inclusief hoe ze zijn gecategoriseerd, wat de gebruiker zei en sommige kolommen met nuttige informatie die eruit zijn getrokken. 

![CSV-bestand met reeds bestaande gegevens](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

U ziet dat de kolom **RequestType** intents kan zijn en in de kolom **Aanvraag** wordt een voorbeeldutterance weergegeven. De andere velden kunnen entiteiten zijn als ze in de utterance voorkomen. Omdat er intents, entiteiten en voorbeelduitingen zijn, hebt u de vereisten voor een eenvoudige voorbeeld-app.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Stappen om een LUIS-app te genereren op basis van niet-LUIS-gegevens
Ga als lid van het CSV-bestand naar een nieuwe LUIS-app:

* Ontleed de gegevens uit het CSV-bestand:
    * Converteren naar een indeling die u uploaden naar LUIS met behulp van de auteurs-API. 
    * Uit de ontleedgegevens verzamelt u informatie over intenties en entiteiten. 
* Maak het maken van API-aanroepen naar:
    * Maak de app.
    * Intents en entiteiten toevoegen die zijn verzameld uit de ontleedgegevens. 
    * Zodra u de LUIS-app hebt gemaakt, u de voorbeelduitingen van de ontleedde gegevens toevoegen. 

U deze programmastroom zien in `index.js` het laatste deel van het bestand. Kopieer of [download](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) deze code `index.js`en sla deze op in .

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Parse de CSV

De kolomvermeldingen die de uitingen in de CSV bevatten, moeten worden geparseerd in een JSON-indeling die LUIS kan begrijpen. Deze JSON-indeling `intentName` moet een veld bevatten dat de bedoeling van de utterance identificeert. Het moet ook `entityLabels` een veld bevatten, dat leeg kan zijn als er geen entiteiten in de utterance zijn. 

Bijvoorbeeld, de vermelding voor "Zet de lichten" kaarten aan deze JSON:

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

In dit voorbeeld `intentName` komt het afkomstig van de gebruikersaanvraag onder de `entityName` kolomkop **Aanvragen** in het CSV-bestand en het komt uit de andere kolommen met belangrijke informatie. Als er bijvoorbeeld een vermelding voor **Bewerking** of **apparaat**is en die tekenreeks ook in de werkelijke aanvraag optreedt, kan deze worden gelabeld als een entiteit. De volgende code toont dit ontweringproces aan. U kopiëren of [downloaden](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) `_parse.js`en opslaan in.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>De LUIS-app maken
Zodra de gegevens zijn ontleed in JSON, voeg deze toe aan een LUIS-app. Met de volgende code wordt de LUIS-app gemaakt. Kopieer of [download](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) het, `_create.js`en sla het op in .

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Intents toevoegen
Zodra u een app hebt, moet u deze indebedoelingen gebruiken. Met de volgende code wordt de LUIS-app gemaakt. Kopieer of [download](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) het, `_intents.js`en sla het op in .

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Entiteiten toevoegen
De volgende code voegt de entiteiten toe aan de LUIS-app. Kopieer of [download](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) het, `_entities.js`en sla het op in .

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Utterances toevoegen
Zodra de entiteiten en intenties zijn gedefinieerd in de LUIS-app, u de uitingen toevoegen. De volgende code maakt gebruik van de [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API, waarmee u maximaal 100 uitingen tegelijk toevoegen.  Kopieer of [download](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) het, `_upload.js`en sla het op in .

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>De code uitvoeren


### <a name="install-nodejs-dependencies"></a>Afhankelijkheden van Node.js installeren
Installeer de afhankelijkheid van Node.js van NPM in de terminal/opdrachtregel.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Configuratie-instellingen wijzigen
Als u deze toepassing wilt gebruiken, moet u de waarden in het bestand index.js wijzigen in uw eigen eindpuntsleutel en de naam opgeven die u wilt dat de app heeft. U ook de cultuur van de app instellen of het versienummer wijzigen.

Open het bestand index.js en wijzig deze waarden boven aan het bestand.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Het script uitvoeren
Voer het script uit vanaf een terminal/opdrachtregel met Node.js.

```console
> node index.js
```

of

```console
> npm start
```

### <a name="application-progress"></a>Voortgang van de toepassing
Terwijl de toepassing wordt uitgevoerd, wordt de opdrachtregel de voortgang weergegeven. De uitvoer van de opdrachtregel bevat de indeling van de antwoorden van LUIS.

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




## <a name="open-the-luis-app"></a>De LUIS-app openen
Zodra het script is voltooid, u zich aanmelden bij [LUIS](luis-reference-regions.md) en de LUIS-app bekijken die u hebt gemaakt onder **Mijn apps**. U moet de uitingen kunnen zien die u hebt toegevoegd onder de intenties **Turnon,** **TurnOff**en **Geen.**

![Intentie voor Turnon](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Test en train uw app op luis-website](luis-interactive-test.md)

## <a name="additional-resources"></a>Aanvullende bronnen

In deze voorbeeldtoepassing worden de volgende LUIS-API's gebruikt:
- [app maken](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [intents toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [entiteiten toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [uitingen toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
