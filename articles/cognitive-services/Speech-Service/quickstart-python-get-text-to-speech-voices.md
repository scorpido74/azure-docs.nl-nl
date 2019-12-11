---
title: 'Snelstartgids: tekst-naar-spraak-stemmen weer geven, python-Speech-Service'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start leert u hoe u de volledige lijst met standaard-en Neural-stemmen kunt verkrijgen voor een regio/eind punt met behulp van python. De lijst wordt geretourneerd als JSON en de beschik baarheid van de spraak varieert per regio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: c645d01dd37fd370911019446c99c76c9da6221c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975972"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Snelstartgids: de lijst met tekst-naar-spraak-stemmen ophalen met behulp van python

In deze Quick Start leert u hoe u de volledige lijst met standaard-en Neural-stemmen kunt verkrijgen voor een regio/eind punt met behulp van python. De lijst wordt geretourneerd als JSON en de beschik baarheid van de spraak varieert per regio. Zie [regio's](regions.md)voor een lijst met ondersteunde regio's.

Deze Snelstartgids vereist een [Azure Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met een speech service-resource. Als u geen account hebt, kunt u de [gratis proefversie](get-started.md) gebruiken om een abonnementssleutel op te halen.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* Python 2.7.x of 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) of uw favoriete teksteditor
* Een sleutel van een Azure-abonnement voor de speech-service

## <a name="create-a-project-and-import-required-modules"></a>Een project maken en de vereiste modules importeren

Maak een nieuw Python-project met uw favoriete IDE of editor. Kopieer dit codefragment naar uw project in een bestand met de naam `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Als u deze modules nog niet hebt gebruikt, moet u ze installeren voordat u het programma uitvoert. Voer voor het installeren van deze pakketten voert u `pip install requests` uit.

Aanvragen worden gebruikt voor HTTP-aanvragen voor de tekst naar spraak-service.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Stel de abonnementssleutel en een prompt voor TTS maken

In de volgende gedeelten maakt u methoden voor verwerking van autorisatie, de Text to Speech-API aanroepen en valideren van het antwoord. Laten we beginnen met het maken van een klasse. Dit is waar gaan we onze methoden voor token exchange en het aanroepen van de Text to Speech-API.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

De `subscription_key` is de unieke sleutel vanuit de Azure-portal.

## <a name="get-an-access-token"></a>Een toegangstoken opvragen

Voor dit eind punt is een toegangs token voor verificatie vereist. Als u een toegangstoken, is een exchange vereist. In dit voor beeld wordt uw abonnements sleutel voor spraak Services voor een toegangs token uitgewisseld met behulp van het `issueToken`-eind punt.

In dit voor beeld wordt ervan uitgegaan dat uw speech service-abonnement zich in de regio vs-West bevindt. Als u een andere regio, werk de waarde voor `fetch_token_url`. Zie voor een volledige lijst [regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Kopieer deze code in de `GetVoices` klasse:

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
> Zie [verifiëren met een toegangs token](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)voor meer informatie over verificatie.

## <a name="make-a-request-and-save-the-response"></a>Een aanvraag indienen en het antwoord opslaan

Hier gaat u de aanvraag maken en de lijst met geretourneerde stemmen opslaan. Eerst moet u om in te stellen de `base_url` en `path`. In dit voorbeeld wordt ervan uitgegaan dat u het eindpunt van de VS-West. Als de bron naar een andere regio is geregistreerd, moet u bijwerken de `base_url`. Zie [Speech Service regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)(Engelstalig) voor meer informatie.

Voeg vervolgens de vereiste headers voor de aanvraag toe. Ten slotte maakt u een aanvraag naar de service. Als de aanvraag is gelukt en er een 200-status code wordt geretourneerd, wordt het antwoord naar het bestand geschreven.

Kopieer deze code in de `GetVoices` klasse:

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

U bent bijna klaar. De laatste stap is het instantiëren van de klasse en aanroepen van uw functies.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

Dat is alles. u bent klaar om het voor beeld uit te voeren. Ga vanaf de opdrachtregel (of terminalsessie) naar de projectmap en voer het volgende uit:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Resources opschonen

Denk eraan dat u eventuele vertrouwelijke informatie, zoals abonnementssleutels, verwijdert uit de broncode van de voorbeeld-app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Python-voorbeelden in GitHub bekijken](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Zie ook

* [Naslaginformatie voor de Text to Speech-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Het maken van aangepaste spraakstijlen](how-to-customize-voice-font.md)
* [Record stem voorbeelden voor het maken van een aangepaste spraak](record-custom-voice-samples.md)
