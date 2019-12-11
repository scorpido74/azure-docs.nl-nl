---
title: 'Snelstartgids: tekst naar spraak, node. js-Speech Service converteren'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start leert u hoe u tekst naar spraak kunt converteren met behulp van node. js en de tekst-naar-spraak-REST API. De voorbeeldtekst opgenomen in deze handleiding is opgebouwd als spraak synthese Markup Language (SSML). Hiermee kunt u de spraak en taal van het antwoord spraak te kiezen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: b120acd25874585a744fb774aafe15d32d7baf08
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976499"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Snelstartgids: tekst-naar-spraak converteren met behulp van node. js

In deze Quick Start leert u hoe u tekst naar spraak kunt converteren met behulp van node. js en de tekst-naar-spraak-REST API. De hoofdtekst van de aanvraag in deze handleiding is opgebouwd als [spraak synthese Markup Language (SSML)](speech-synthesis-markup.md), waarmee u de spraak en taal van het antwoord te kiezen.

Deze Snelstartgids vereist een [Azure Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met een speech service-resource. Als u geen account hebt, kunt u de [gratis proefversie](get-started.md) gebruiken om een abonnementssleutel op te halen.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* [Node 8.12.x of hoger](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) of uw favoriete teksteditor
* Een Azure-abonnementssleutel voor de Spraakservice. [Ontvang er gratis een](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Een project maken en afhankelijkheden vereisen

Maak een nieuw node. js-project met uw favoriete IDE of editor. Kopieer dit codefragment naar uw project in een bestand met de naam `tts.js`.

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

De Text to Speech REST-API is een toegangstoken voor verificatie vereist. Als u een toegangstoken, is een exchange vereist. Met deze functie wordt de abonnements sleutel van uw spraak service voor een toegangs token voor het `issueToken`-eind punt uitgewisseld.

In dit voor beeld wordt ervan uitgegaan dat uw speech service-abonnement zich in de regio vs-West bevindt. Als u een andere regio, werk de waarde voor `uri`. Zie voor een volledige lijst [regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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
> Zie [verifiëren met een toegangs token](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)voor meer informatie over verificatie.

In de volgende sectie maken we de functie voor het aanroepen van de tekst-naar-spraak-API en het opslaan van het gesynthesizerde spraak antwoord.

## <a name="make-a-request-and-save-the-response"></a>Een aanvraag indienen en het antwoord opslaan

Hier gaat u de aanvraag voor de tekst-naar-spraak-API maken en het antwoord op de spraak actie Opslaan. In dit voorbeeld wordt ervan uitgegaan dat u het eindpunt van de VS-West. Als de bron naar een andere regio is geregistreerd, moet u bijwerken de `uri`. Zie [Speech Service regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)(Engelstalig) voor meer informatie.

Vervolgens moet u vereiste headers voor de aanvraag toevoegen. Zorg ervoor dat bij te werken `User-Agent` met de naam van uw resource (te vinden in Azure portal) en stel `X-Microsoft-OutputFormat` aan uw gewenste audio-uitvoer. Zie voor een volledige lijst van de uitvoerindeling op te geven, [Audio-uitvoer](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Vervolgens maken de hoofdtekst van de aanvraag met behulp van spraak synthese Markup Language (SSML). In dit voorbeeld definieert u de structuur en maakt gebruik van de `text` invoer u eerder hebt gemaakt.

>[!NOTE]
> In dit voorbeeld wordt de `JessaRUS` spraakstijl. Voor een volledige lijst van door Microsoft geleverd stemmen/talen, Zie [taalondersteuning](language-support.md).
> Als u geïnteresseerd bent in het maken van een unieke, herkenbare stem voor uw merk, Zie [het maken van aangepaste spraakstijlen](how-to-customize-voice-font.md).

Ten slotte maakt u een aanvraag naar de service. Als de aanvraag is gelukt en er een 200-status code wordt geretourneerd, wordt het spraak antwoord als `TTSOutput.wav`geschreven.

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

U bent bijna klaar. De laatste stap is het maken van een asynchrone functie. Met deze functie wordt uw abonnements sleutel van een omgevings variabele gelezen, wordt u gevraagd om tekst, een token op te halen, te wachten tot de aanvraag is voltooid, de tekst naar spraak te converteren en de audio op te slaan als een. wav-bestand.

Als u niet bekend bent met omgevings variabelen of als u wilt testen met uw abonnements sleutel als een teken reeks, vervangt u `process.env.SPEECH_SERVICE_KEY` door uw abonnements sleutel als teken reeks.

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

Dat is alles, u kunt nu uw Text to Speech voorbeeldapp uit te voeren. Ga vanaf de opdrachtregel (of terminalsessie) naar de projectmap en voer het volgende uit:

```console
node tts.js
```

Wanneer u hierom wordt gevraagd, typt u in alles wat u wilt converteren van tekst naar spraak. Als dit lukt, wordt de spraak-bestand bevindt zich in de projectmap. Spelen met behulp van uw favoriete MediaPlayer.

## <a name="clean-up-resources"></a>Resources opschonen

Denk eraan dat u eventuele vertrouwelijke informatie, zoals abonnementssleutels, verwijdert uit de broncode van de voorbeeld-app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Node.js-voorbeelden op GitHub verkennen](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Zie ook

* [Naslaginformatie voor de Text to Speech-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Het maken van aangepaste spraakstijlen](how-to-customize-voice-font.md)
* [Record stem voorbeelden voor het maken van een aangepaste spraak](record-custom-voice-samples.md)
