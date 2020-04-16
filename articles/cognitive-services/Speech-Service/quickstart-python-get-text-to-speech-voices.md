---
title: Tekst-naar-spraakstemmen, Python - Spraakservice weergeven
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u de volledige lijst met standaard- en neurale stemmen voor een regio/eindpunt krijgen met Python. De lijst wordt geretourneerd als JSON en de beschikbaarheid van spraak verschilt per regio.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: b388c8d8b61e2fc638ae2bce5bc6d9eeb25ee0d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401019"
---
# <a name="get-the-list-of-text-to-speech-voices-using-python"></a>Download de lijst met tekst-naar-spraakstemmen met Python

In dit artikel leert u hoe u de volledige lijst met standaard- en neurale stemmen voor een regio/eindpunt krijgen met Python. De lijst wordt geretourneerd als JSON en de beschikbaarheid van spraak verschilt per regio. Zie regio's voor een lijst met ondersteunde [regio's](regions.md).

Voor dit artikel is een [Azure Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vereist met een Spraakservicebron. Als u geen account hebt, kunt u de [gratis proefversie](get-started.md) gebruiken om een abonnementssleutel op te halen.

## <a name="prerequisites"></a>Vereisten

* Python 2.7.x of 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>of uw favoriete teksteditor
* Een Azure-abonnementssleutel voor de spraakservice

## <a name="create-a-project-and-import-required-modules"></a>Een project maken en de vereiste modules importeren

Maak een nieuw Python-project met uw favoriete IDE of editor. Kopieer dit codefragment naar uw project in een bestand met de naam `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Als u deze modules nog niet hebt gebruikt, moet u ze installeren voordat u het programma uitvoert. Voer voor het installeren van deze pakketten voert u `pip install requests` uit.

Aanvragen worden gebruikt voor HTTP-aanvragen voor de tekst-naar-spraakservice.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>De abonnementssleutel instellen en een prompt maken voor TTS

In de volgende secties maakt u methoden voor het afhandelen van autorisatie, het aanroepen van de API voor tekst naar spraak en het valideren van het antwoord. Laten we beginnen met het maken van een klasse. Dit is waar we onze methoden voor token-uitwisseling en het aanroepen van de text-to-speech-API plaatsen.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

Het `subscription_key` is uw unieke sleutel van de Azure-portal.

## <a name="get-an-access-token"></a>Een toegangstoken opvragen

Dit eindpunt vereist een toegangstoken voor verificatie. Om een toegangstoken te krijgen, is een uitwisseling vereist. In dit voorbeeld wordt uw abonnementssleutel voor `issueToken` spraakservice uitgewisseld voor een toegangstoken met behulp van het eindpunt.

In dit voorbeeld wordt ervan uitgegaan dat uw spraakserviceabonnement zich in de regio West-VS bevindt. Als u een ander gebied gebruikt, `fetch_token_url`werkt u de waarde bij voor . Zie [Regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)voor een volledige lijst .

Kopieer deze code `GetVoices` naar de klasse:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Zie [Verifiëren met een toegangstoken voor](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)meer informatie over verificatie.

## <a name="make-a-request-and-save-the-response"></a>Een verzoek indienen en het antwoord opslaan

Hier ga je het verzoek bouwen en de lijst met geretourneerde stemmen opslaan. Eerst moet je de `base_url` `path`en. In dit voorbeeld wordt ervan uitgegaan dat u het Eindpunt van west-VS gebruikt. Als uw resource is geregistreerd in een andere `base_url`regio, moet u de . Zie [Spraakserviceregio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)voor meer informatie .

Voeg vervolgens vereiste kopteksten voor de aanvraag toe. Tot slot doet u een verzoek aan de service. Als de aanvraag is geslaagd en een statuscode van 200 wordt geretourneerd, wordt het antwoord naar bestand geschreven.

Kopieer deze code `GetVoices` naar de klasse:

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Alles samenvoegen

U bent bijna klaar. De laatste stap is om uw klas te instantiëren en uw functies te bellen.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

Dat is het, je bent klaar om het monster uit te voeren. Ga vanaf de opdrachtregel (of terminalsessie) naar de projectmap en voer het volgende uit:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Resources opschonen

Denk eraan dat u eventuele vertrouwelijke informatie, zoals abonnementssleutels, verwijdert uit de broncode van de voorbeeld-app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Python-voorbeelden in GitHub bekijken](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Zie ook

* [API-verwijzing naar tekst naar spraak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Aangepaste spraaklettertypen maken](how-to-customize-voice-font.md)
* [Spraakvoorbeelden opnemen om een aangepaste stem te maken](record-custom-voice-samples.md)
