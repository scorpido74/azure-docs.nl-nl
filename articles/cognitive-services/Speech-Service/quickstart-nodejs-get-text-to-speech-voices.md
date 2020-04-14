---
title: 'Snelstart: tekst-naar-spraakstemmen, Node.js - Spraakservice weergeven'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leer je hoe je de volledige lijst met standaard- en neurale stemmen voor een regio/eindpunt krijgen met Behulp van Node.js. De lijst wordt geretourneerd als JSON en de beschikbaarheid van spraak verschilt per regio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 9fe8bc06aafd17518d37c35034fac9b566e079ce
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261547"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Snelstart: de lijst met tekst-naar-spraakstemmen gebruiken Node.js

In deze quickstart leer je hoe je de volledige lijst met standaard- en neurale stemmen voor een regio/eindpunt krijgen met Behulp van Node.js. De lijst wordt geretourneerd als JSON en de beschikbaarheid van spraak verschilt per regio. Zie regio's voor een lijst met ondersteunde [regio's](regions.md).

Voor deze quickstart is een [Azure Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met een Spraakservicebron vereist. Als u geen account hebt, kunt u de [gratis proefversie](get-started.md) gebruiken om een abonnementssleutel op te halen.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* <a href="https://nodejs.org/en/" target="_blank">Knooppunt 8.12.x of hoger<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>of uw favoriete teksteditor
* Een Azure-abonnementssleutel voor de Spraakservice. [Ontvang er een gratis!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Een project maken en afhankelijkheden vereisen

Maak een nieuw Node.js-project met uw favoriete IDE- of editor. Kopieer dit codefragment naar uw project in een bestand met de naam `get-voices.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> Als u deze modules nog niet hebt gebruikt, moet u ze installeren voordat u het programma uitvoert. Voer voor het installeren van deze pakketten voert u `npm install request request-promise` uit.

## <a name="get-an-access-token"></a>Een toegangstoken opvragen

De rest-to-speech REST API vereist een toegangstoken voor verificatie. Om een toegangstoken te krijgen, is een uitwisseling vereist. Met deze functie wordt uw abonnementssleutel voor `issueToken` spraakservice uitgewisseld voor een toegangstoken met behulp van het eindpunt.

In dit voorbeeld wordt ervan uitgegaan dat uw spraakserviceabonnement zich in de regio West-VS bevindt. Als u een ander gebied gebruikt, `uri`werkt u de waarde bij voor . Zie [Regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)voor een volledige lijst .

Kopieer deze code naar uw project:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Zie [Verifiëren met een toegangstoken voor](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)meer informatie over verificatie.

In het volgende gedeelte maken we de functie om de lijst met stemmen op te halen en de JSON-uitvoer op te slaan in bestand.

## <a name="make-a-request-and-save-the-response"></a>Een verzoek indienen en het antwoord opslaan

Hier ga je het verzoek bouwen en de lijst met geretourneerde stemmen opslaan. In dit voorbeeld wordt ervan uitgegaan dat u het Eindpunt van west-VS gebruikt. Als uw resource is geregistreerd in een andere `uri`regio, moet u de . Zie [Spraakserviceregio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)voor meer informatie .

Voeg vervolgens vereiste kopteksten voor de aanvraag toe. Tot slot doet u een verzoek aan de service. Als de aanvraag is geslaagd en een statuscode van 200 wordt geretourneerd, wordt het antwoord naar bestand geschreven.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Alles samenvoegen

U bent bijna klaar. De laatste stap is het maken van een asynchrone functie. Deze functie leest uw abonnementssleutel uit een omgevingsvariabele, krijgt een token, wacht tot het verzoek is voltooid en schrijft vervolgens het JSON-antwoord op het bestand.

Als u niet bekend bent met omgevingsvariabelen of de voorkeur geeft aan `process.env.SPEECH_SERVICE_KEY` een proef met uw abonnementssleutel die als tekenreeks is gehard, vervangt u uw abonnementssleutel als tekenreeks.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

Nu kunt u de voorbeeld-app gaan uitvoeren. Ga vanaf de opdrachtregel (of terminalsessie) naar de projectmap en voer het volgende uit:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Resources opschonen

Denk eraan dat u eventuele vertrouwelijke informatie, zoals abonnementssleutels, verwijdert uit de broncode van de voorbeeld-app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Node.js-voorbeelden op GitHub verkennen](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Zie ook

* [API-verwijzing naar tekst naar spraak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Aangepaste spraaklettertypen maken](how-to-customize-voice-font.md)
* [Spraakvoorbeelden opnemen om een aangepaste stem te maken](record-custom-voice-samples.md)
