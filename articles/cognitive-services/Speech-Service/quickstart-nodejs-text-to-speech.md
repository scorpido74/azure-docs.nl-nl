---
title: 'Snelstartgids: tekst naar spraak, node. js-Speech Service converteren'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start leert u hoe u tekst naar spraak kunt converteren met behulp van node. js en de tekst-naar-spraak-REST API. De voorbeeld tekst in deze hand leiding is gestructureerd als SSML (Speech synthese Markup Language). Hiermee kunt u de stem en de taal van het antwoord op de spraak kiezen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 8cf9641a1b7a5d1aada13522d612458d5032f883
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81258684"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>Snelstartgids: tekst-naar-spraak converteren met behulp van node. js

In deze Quick Start leert u hoe u tekst naar spraak kunt converteren met behulp van node. js en de tekst-naar-spraak-REST API. De aanvraag tekst in deze hand leiding is gestructureerd als [SSML (Speech synthese Markup Language)](speech-synthesis-markup.md), waarmee u de stem en de taal van het antwoord kunt kiezen.

Deze Snelstartgids vereist een [Azure Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met een speech service-resource. Als u geen account hebt, kunt u de [gratis proefversie](get-started.md) gebruiken om een abonnementssleutel op te halen.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* <a href="https://nodejs.org/en/" target="_blank">Knoop punt 8.12. x of hoger<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>of uw favoriete tekst editor
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

Voor de REST API tekst naar spraak is een toegangs token voor verificatie vereist. Voor het verkrijgen van een toegangs token is een uitwisseling vereist. Met deze functie wordt de abonnements sleutel van uw spraak service voor een toegangs `issueToken` token met behulp van het eind punt uitgewisseld.

In dit voor beeld wordt ervan uitgegaan dat uw speech service-abonnement zich in de regio vs-West bevindt. Als u een andere regio gebruikt, werkt u de waarde voor `uri`bij. Zie [regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)voor een volledige lijst.

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

Hier gaat u de aanvraag voor de tekst-naar-spraak-API maken en het antwoord op de spraak actie Opslaan. In dit voor beeld wordt ervan uitgegaan dat u gebruikmaakt van het eind punt vs West. Als uw resource is geregistreerd in een andere regio, moet u ervoor zorgen dat `uri`u de hebt bijgewerkt. Zie [Speech Service regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)(Engelstalig) voor meer informatie.

Vervolgens moet u de vereiste headers voor de aanvraag toevoegen. Zorg ervoor dat u bijwerkt `User-Agent` met de naam van uw resource (in de Azure Portal) en stel `X-Microsoft-OutputFormat` deze in op de audio-uitvoer van uw voor keur. Zie [audio-uitvoer](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)voor een volledige lijst met uitvoer indelingen.

Maak vervolgens de aanvraag tekst met behulp van SSML (Speech synthese Markup Language). In dit voor beeld wordt de structuur gedefinieerd en `text` wordt de invoer gebruikt die u eerder hebt gemaakt.

>[!NOTE]
> In dit voor beeld `JessaRUS` wordt het letter type Voice gebruikt. Zie [taal ondersteuning](language-support.md)voor een volledige lijst van door micro soft geboden stemmen/talen.
> Zie [aangepaste spraak lettertypen maken](how-to-customize-voice-font.md)als u geïnteresseerd bent in het maken van een unieke, herken bare spraak voor uw merk.

Ten slotte maakt u een aanvraag voor de service. Als de aanvraag is gelukt en er een 200-status code wordt geretourneerd, wordt het spraak antwoord `TTSOutput.wav`als volgt geschreven.

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

Als u niet bekend bent met omgevings variabelen of als u wilt testen met uw abonnements sleutel als een teken reeks `process.env.SPEECH_SERVICE_KEY` , vervangt u door uw abonnements sleutel als teken reeks.

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

Dat is alles. u bent klaar om uw tekst-naar-spraak-voorbeeld-app uit te voeren. Ga vanaf de opdrachtregel (of terminalsessie) naar de projectmap en voer het volgende uit:

```console
node tts.js
```

Wanneer u hierom wordt gevraagd, typt u in datgene wat u wilt converteren van tekst naar spraak. Als dit lukt, bevindt het spraak bestand zich in de projectmap. Speel het af met uw favoriete media speler.

## <a name="clean-up-resources"></a>Resources opschonen

Denk eraan dat u eventuele vertrouwelijke informatie, zoals abonnementssleutels, verwijdert uit de broncode van de voorbeeld-app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Node.js-voorbeelden op GitHub verkennen](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Zie ook

* [Naslag informatie over de tekst-naar-spraak-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Aangepaste spraak lettertypen maken](how-to-customize-voice-font.md)
* [Spraak voorbeelden vastleggen om een aangepaste spraak te maken](record-custom-voice-samples.md)
