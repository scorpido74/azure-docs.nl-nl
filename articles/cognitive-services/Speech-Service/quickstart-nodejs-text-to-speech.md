---
title: 'Quickstart: Tekst naar spraak converteren, Node.js - Speech-service'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u tekst naar spraak kunt converteren met behulp van Node.js en de REST API voor tekst naar spraak. De voorbeeldtekst in deze handleiding is gestructureerd als SSML (Speech Synthesis Markup Language). Hiermee kunt u de stem en de taal van het spraakantwoord kiezen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: 38f8df1e721d2e29d4871a05263d9f4559aaedc4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406806"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Quickstart: Tekst naar spraak converteren met Node.js

In deze quickstart leert u hoe u tekst naar spraak kunt converteren met behulp van Node.js en de REST API voor tekst naar spraak. De aanvraagbody in deze handleiding is gestructureerd als [SSML (Speech Syntesis Markup Language)](speech-synthesis-markup.md) zodat u de stem en de taal van het antwoord kunt kiezen.

Voor deze quickstart is een [Azure Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met een Spraakservice-resource vereist. Als u geen account hebt, kunt u de [gratis proefversie](get-started.md) gebruiken om een abonnementssleutel op te halen.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* <a href="https://nodejs.org/en/" target="_blank">Node 8.12.x of hoger <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"></span></a>, <a href="https://code.visualstudio.com/download" target="_blank"> Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"></span></a> of uw favoriete teksteditor
* Een Azure-abonnementssleutel voor de Spraakservice. [Gratis downloaden](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Een project maken en afhankelijkheden vereisen

Maak een nieuw Node.js-project met uw favoriete IDE of editor. Kopieer dit codefragment naar uw project in een bestand met de naam `tts.js`.

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

Voor de REST API voor tekst naar spraak is een toegangstoken voor verificatie vereist. Voor het verkrijgen van een toegangstoken is een uitwisseling vereist. Met deze functie wordt de abonnementssleutel van uw Speech-service uitgewisseld met een toegangstoken voor het `issueToken`-eindpunt.

In dit voorbeeld wordt ervan uitgegaan dat uw spraakserviceabonnement zich bevindt in de regio US - west. Als u een andere regio gebruikt, werkt u de waarde voor `uri` bij. Zie [Regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis) voor een volledige lijst.

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
> Zie [Verifiëren met een toegangstoken](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token) voor meer informatie over verificatie.

In de volgende sectie maken we de functie voor het aanroepen van de tekst-naar-spraak-API en het opslaan van het gesyntheseerde spraakantwoord.

## <a name="make-a-request-and-save-the-response"></a>Een aanvraag maken en het antwoord opslaan

Hier gaat u de aanvraag voor de tekst-naar-spraak-API maken en het spraakantwoord opslaan. In dit voorbeeld wordt ervan uitgegaan dat u gebruikmaakt van het eindpunt US - west. Als uw resource is geregistreerd in een andere regio, moet u de `uri` bijwerken. Zie [Regio's voor Speech-service](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech) voor meer informatie.

Vervolgens moet u de vereiste headers voor de aanvraag toevoegen. Zorg ervoor dat u `User-Agent` bijwerkt met de naam van uw resource (in de Azure-portal) en stel `X-Microsoft-OutputFormat` in op de audio-uitvoer van uw voorkeur. Zie [Audio-uitvoer](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) voor een volledige lijst met uitvoerindelingen.

Maak vervolgens de aanvraagbody met behulp van SSML (Speech synthese Markup Language). In dit voorbeeld wordt de structuur gedefinieerd en wordt de `text`-invoer gebruikt die u eerder hebt gemaakt.

>[!NOTE]
> In dit voorbeeld wordt de spraakstijl `JessaRUS` gebruikt. Zie [Taalondersteuning](language-support.md) voor een volledige lijst van door Microsoft geleverde stemmen/talen.
> Als u geïnteresseerd bent in het maken van een unieke, herkenbare stem voor uw merk, raadpleegt u [Aangepaste spraakstijlen maken](how-to-customize-voice-font.md).

Ten slotte maakt u een aanvraag voor de service. Als de aanvraag is gelukt en er een 200-statuscode wordt geretourneerd, wordt het spraakantwoord weggeschreven als `TTSOutput.wav`.

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

U bent bijna klaar. De laatste stap is het maken van een asynchrone functie. Met deze functie wordt uw abonnementssleutel uit een omgevingsvariabele gelezen, een prompt voor tekst weergegeven, een token opgehaald, gewacht tot de aanvraag is voltooid en vervolgens de tekst naar spraak geconverteerd en de audio opgeslagen als een WAV-bestand.

Als u niet bekend bent met omgevingsvariabelen of als u liever wilt testen met een abonnementssleutel die is vastgelegd als tekenreeks, vervangt u `process.env.SPEECH_SERVICE_KEY` door uw abonnementssleutel als tekenreeks.

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

Nu kunt u de voorbeeld-tekst-naar-spraak-app gaan uitvoeren. Ga vanaf de opdrachtregel (of terminalsessie) naar de projectmap en voer het volgende uit:

```console
node tts.js
```

Wanneer u hierom wordt gevraagd, typt u de tekst in die u wilt converteren naar spraak. Als dit lukt, bevindt het spraakbestand zich in de projectmap. Speel het af met uw favoriete mediaspeler.

## <a name="clean-up-resources"></a>Resources opschonen

Denk eraan dat u eventuele vertrouwelijke informatie, zoals abonnementssleutels, verwijdert uit de broncode van de voorbeeld-app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Node.js-voorbeelden op GitHub verkennen](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Zie ook

* [Naslaginformatie voor de Text to Speech-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Aangepaste spraakstijlen maken](how-to-customize-voice-font.md)
* [Stemvoorbeelden vastleggen om een aangepaste stem te maken](record-custom-voice-samples.md)
