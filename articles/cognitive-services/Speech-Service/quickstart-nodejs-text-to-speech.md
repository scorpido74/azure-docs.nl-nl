---
title: 'Snelstart: tekst-naar-spraak converteren, Node.js - Spraakservice'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u tekst-naar-spraak converteert met Node.js en de API Voor tekst-naar-spraakrest. De voorbeeldtekst in deze handleiding is gestructureerd als Spraaksyntheseopmaaktaal (SSML). Hiermee u de stem en taal van de spraakrespons kiezen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: b120acd25874585a744fb774aafe15d32d7baf08
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74976499"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Snelstart: tekst-naar-spraak converteren met Node.js

In deze quickstart leert u hoe u tekst-naar-spraak converteert met Node.js en de REST-API voor tekst naar spraak. De aanvraaginstantie in deze handleiding is gestructureerd als [Speech Synthesis Markup Language (SSML),](speech-synthesis-markup.md)waarmee u de stem en taal van het antwoord kiezen.

Voor deze quickstart is een [Azure Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met een Spraakservicebron vereist. Als u geen account hebt, kunt u de [gratis proefversie](get-started.md) gebruiken om een abonnementssleutel op te halen.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* [Node 8.12.x of hoger](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) of uw favoriete teksteditor
* Een Azure-abonnementssleutel voor de Spraakservice. [Ontvang er een gratis!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Een project maken en afhankelijkheden vereisen

Maak een nieuw Node.js-project met uw favoriete IDE- of editor. Kopieer dit codefragment naar uw project in een bestand met de naam `tts.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> Als u deze modules nog niet hebt gebruikt, moet u ze installeren voordat u het programma uitvoert. Voer voor het installeren van deze pakketten voert u `npm install request request-promise xmlbuilder readline-sync` uit.

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

In de volgende sectie maken we de functie om de text-to-speech-API aan te roepen en de gesynthetiseerde spraakrespons op te slaan.

## <a name="make-a-request-and-save-the-response"></a>Een verzoek indienen en het antwoord opslaan

Hier ga je het verzoek bouwen om de text-to-speech API en sla de spraakrespons op. In dit voorbeeld wordt ervan uitgegaan dat u het Eindpunt van west-VS gebruikt. Als uw resource is geregistreerd in een andere `uri`regio, moet u de . Zie [Spraakserviceregio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)voor meer informatie .

Vervolgens moet u vereiste kopteksten voor de aanvraag toevoegen. Zorg ervoor dat `User-Agent` u de naam van uw bron bijwerkt `X-Microsoft-OutputFormat` (in de Azure-portal) en ingesteld op de audio-uitvoer van uw voorkeur. Zie [Audio-uitvoer](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)voor een volledige lijst met uitvoerindelingen .

Bouw vervolgens de aanvraaginstantie met behulp van Spraaksynthese-opmaaktaal (SSML). In dit voorbeeld wordt de `text` structuur gedefinieerd en wordt de invoer gebruikt die u eerder hebt gemaakt.

>[!NOTE]
> In dit `JessaRUS` voorbeeld wordt het spraaklettertype gebruikt. Zie [Taalondersteuning](language-support.md)voor een volledige lijst met door Microsoft geleverde stemmen/talen.
> Zie [Aangepaste spraaklettertypen maken](how-to-customize-voice-font.md)als u geïnteresseerd bent in het maken van een unieke, herkenbare stem voor uw merk.

Tot slot doet u een verzoek aan de service. Als de aanvraag is geslaagd en een statuscode van 200 `TTSOutput.wav`wordt geretourneerd, wordt het spraakantwoord geschreven als .

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Alles samenvoegen

U bent bijna klaar. De laatste stap is het maken van een asynchrone functie. Deze functie leest uw abonnementssleutel uit een omgevingsvariabele, vraagt om tekst, krijgt een token, wacht tot het verzoek is voltooid, converteert de tekst naar spraak en slaat de audio op als een .wav.

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
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

Dat is het, je bent klaar om je text-to-speech sample app uit te voeren. Ga vanaf de opdrachtregel (of terminalsessie) naar de projectmap en voer het volgende uit:

```console
node tts.js
```

Wanneer u daarom wordt gevraagd, typt u alles in wat u wilt converteren van tekst naar toespraak. Als dit is gelukt, bevindt het spraakbestand zich in uw projectmap. Speel het met je favoriete mediaspeler.

## <a name="clean-up-resources"></a>Resources opschonen

Denk eraan dat u eventuele vertrouwelijke informatie, zoals abonnementssleutels, verwijdert uit de broncode van de voorbeeld-app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Node.js-voorbeelden op GitHub verkennen](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Zie ook

* [API-verwijzing naar tekst naar spraak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Aangepaste spraaklettertypen maken](how-to-customize-voice-font.md)
* [Spraakvoorbeelden opnemen om een aangepaste stem te maken](record-custom-voice-samples.md)
