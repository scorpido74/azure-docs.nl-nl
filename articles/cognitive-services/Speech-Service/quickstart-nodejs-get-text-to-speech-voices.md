---
title: 'Snelstartgids: tekst-naar-spraak stemmen, node. js-Speech-Service vermelden'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start leert u hoe u de volledige lijst met standaard-en Neural-stemmen kunt verkrijgen voor een regio/eind punt met behulp van node. js. De lijst wordt geretourneerd als JSON en de beschik baarheid van de spraak varieert per regio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bd063102a4c0cbdc4b62df027e643b117b412278
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812851"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Snelstartgids: de lijst met tekst-naar-spraak-stemmen ophalen met behulp van node. js

In deze Quick Start leert u hoe u de volledige lijst met standaard-en Neural-stemmen kunt verkrijgen voor een regio/eind punt met behulp van node. js. De lijst wordt geretourneerd als JSON en de beschik baarheid van de spraak varieert per regio. Zie [regio's](regions.md)voor een lijst met ondersteunde regio's.

Deze Snelstartgids vereist een [Azure Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met een speech service-resource. Als u geen account hebt, kunt u de [gratis proefversie](get-started.md) gebruiken om een abonnementssleutel op te halen.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* [Node 8.12.x of hoger](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) of uw favoriete teksteditor
* Een Azure-abonnementssleutel voor de Spraakservice. [Ontvang er gratis een](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Een project maken en afhankelijkheden vereisen

Maak een nieuw node. js-project met uw favoriete IDE of editor. Kopieer dit codefragment naar uw project in een bestand met de naam `get-voices.js`.

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

In de volgende sectie maken we de functie om de lijst met stemmen op te halen en de JSON-uitvoer naar het bestand op te slaan.

## <a name="make-a-request-and-save-the-response"></a>Een aanvraag indienen en het antwoord opslaan

Hier gaat u de aanvraag maken en de lijst met geretourneerde stemmen opslaan. In dit voorbeeld wordt ervan uitgegaan dat u het eindpunt van de VS-West. Als de bron naar een andere regio is geregistreerd, moet u bijwerken de `uri`. Zie [Speech Service regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)(Engelstalig) voor meer informatie.

Voeg vervolgens de vereiste headers voor de aanvraag toe. Ten slotte maakt u een aanvraag naar de service. Als de aanvraag is gelukt en er een 200-status code wordt geretourneerd, wordt het antwoord naar het bestand geschreven.

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

U bent bijna klaar. De laatste stap is het maken van een asynchrone functie. Met deze functie wordt uw abonnements sleutel van een omgevings variabele gelezen, wordt een token opgehaald, wordt gewacht tot de aanvraag is voltooid en wordt vervolgens het JSON-antwoord naar het bestand geschreven.

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
* [Het maken van aangepaste spraakstijlen](how-to-customize-voice-font.md)
* [Record stem voorbeelden voor het maken van een aangepaste spraak](record-custom-voice-samples.md)
