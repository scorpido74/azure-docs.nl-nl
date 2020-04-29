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
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 9fe8bc06aafd17518d37c35034fac9b566e079ce
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261547"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Snelstartgids: de lijst met tekst-naar-spraak-stemmen ophalen met behulp van node. js

In deze Quick Start leert u hoe u de volledige lijst met standaard-en Neural-stemmen kunt verkrijgen voor een regio/eind punt met behulp van node. js. De lijst wordt geretourneerd als JSON en de beschik baarheid van de spraak varieert per regio. Zie [regio's](regions.md)voor een lijst met ondersteunde regio's.

Deze Snelstartgids vereist een [Azure Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met een speech service-resource. Als u geen account hebt, kunt u de [gratis proefversie](get-started.md) gebruiken om een abonnementssleutel op te halen.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* <a href="https://nodejs.org/en/" target="_blank">Knoop punt 8.12. x of hoger<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>of uw favoriete tekst editor
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

In de volgende sectie maken we de functie om de lijst met stemmen op te halen en de JSON-uitvoer naar het bestand op te slaan.

## <a name="make-a-request-and-save-the-response"></a>Een aanvraag indienen en het antwoord opslaan

Hier gaat u de aanvraag maken en de lijst met geretourneerde stemmen opslaan. In dit voor beeld wordt ervan uitgegaan dat u gebruikmaakt van het eind punt vs West. Als uw resource is geregistreerd in een andere regio, moet u ervoor zorgen dat `uri`u de hebt bijgewerkt. Zie [Speech Service regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)(Engelstalig) voor meer informatie.

Voeg vervolgens de vereiste headers voor de aanvraag toe. Ten slotte maakt u een aanvraag voor de service. Als de aanvraag is gelukt en er een 200-status code wordt geretourneerd, wordt het antwoord naar het bestand geschreven.

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

* [Naslag informatie over de tekst-naar-spraak-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Aangepaste spraak lettertypen maken](how-to-customize-voice-font.md)
* [Spraak voorbeelden vastleggen om een aangepaste spraak te maken](record-custom-voice-samples.md)
