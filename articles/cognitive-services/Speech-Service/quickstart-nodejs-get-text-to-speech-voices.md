---
title: 'Quickstart: Tekst-naar-spraak-stemmen weergeven, Node.js - Speech Service'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u de volledige lijst met standaard- en neurale stemmen kunt verkrijgen voor een regio/eindpunt met behulp van Node.js. De lijst wordt geretourneerd als JSON en de beschikbaarheid van stemmen varieert per regio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: d7ec5b386a9e62606a8b46c4e66cea85f8098a83
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406823"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Quickstart: De lijst met tekst-naar-spraak-stemmen verkrijgen met behulp van Node.js

In deze quickstart leert u hoe u de volledige lijst met standaard- en neurale stemmen kunt verkrijgen voor een regio/eindpunt met behulp van Node.js. De lijst wordt geretourneerd als JSON en de beschikbaarheid van stemmen varieert per regio. Zie [Regio’s](regions.md) voor een lijst met ondersteunde regio’s.

Voor deze quickstart is een [Azure Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met een Spraakservice-resource vereist. Als u geen account hebt, kunt u de [gratis proefversie](get-started.md) gebruiken om een abonnementssleutel op te halen.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* <a href="https://nodejs.org/en/" target="_blank">Node 8.12.x of hoger<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio<span class="docon docon-navigate-external x-hidden-focus"></span></a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code<span class="docon docon-navigate-external x-hidden-focus"></span></a> of uw favoriete teksteditor
* Een Azure-abonnementssleutel voor de Spraakservice. [Gratis downloaden](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Een project maken en afhankelijkheden vereisen

Maak een nieuw Node.js-project met uw favoriete IDE of editor. Kopieer dit codefragment naar uw project in een bestand met de naam `get-voices.js`.

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

In de volgende sectie maken we de functie waarmee u de lijst met stemmen kunt ophalen en de JSON-uitvoer in het bestand kunt opslaan.

## <a name="make-a-request-and-save-the-response"></a>Een aanvraag maken en het antwoord opslaan

Hier gaat u de aanvraag maken en de lijst met geretourneerde stemmen opslaan. In dit voorbeeld wordt ervan uitgegaan dat u gebruikmaakt van het eindpunt US - west. Als uw resource is geregistreerd in een andere regio, moet u de `uri` bijwerken. Zie [Regio's voor Speech-service](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech) voor meer informatie.

Voeg vervolgens de vereiste headers voor de aanvraag toe. Ten slotte maakt u een aanvraag voor de service. Als de aanvraag is gelukt en er een 200-statuscode wordt geretourneerd, wordt het antwoord naar een bestand weggeschreven.

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

U bent bijna klaar. De laatste stap is het maken van een asynchrone functie. Met deze functie wordt uw abonnementssleutel uit een omgevingsvariabele gelezen, wordt een token opgehaald, wordt gewacht tot de aanvraag is voltooid en wordt vervolgens het JSON-antwoord naar een bestand weggeschreven.

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

* [Naslaginformatie voor de Text to Speech-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Aangepaste spraakstijlen maken](how-to-customize-voice-font.md)
* [Stemvoorbeelden vastleggen om een aangepaste stem te maken](record-custom-voice-samples.md)
