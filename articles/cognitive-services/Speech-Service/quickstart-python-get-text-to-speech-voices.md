---
title: Lijst met tekst-naar-spraak-stemmen, python-Speech-Service
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u de volledige lijst met standaard-en Neural-stemmen kunt ophalen voor een regio/eind punt met behulp van python. De lijst wordt geretourneerd als JSON en de beschik baarheid van de spraak varieert per regio.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.custom: tracking-python
ms.openlocfilehash: ec1b03bf0b3cf95f65013bddbc54e15ab985198e
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84607994"
---
# <a name="get-the-list-of-text-to-speech-voices-using-python"></a>De lijst met tekst-naar-spraak-stemmen ophalen met behulp van python

In dit artikel leert u hoe u de volledige lijst met standaard-en Neural-stemmen kunt ophalen voor een regio/eind punt met behulp van python. De lijst wordt geretourneerd als JSON en de beschik baarheid van de spraak varieert per regio. Zie [regio's](regions.md)voor een lijst met ondersteunde regio's.

Voor dit artikel is een [Azure Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vereist met een speech service-resource. Als u geen account hebt, kunt u de [gratis proefversie](get-started.md) gebruiken om een abonnementssleutel op te halen.

## <a name="prerequisites"></a>Vereisten

* Python 2.7.x of 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"></span> </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio code <span class="docon docon-navigate-external x-hidden-focus"></span> </a>of uw favoriete tekst editor
* Een sleutel van een Azure-abonnement voor de speech-service

## <a name="create-a-project-and-import-required-modules"></a>Een project maken en de vereiste modules importeren

Maak een nieuw Python-project met uw favoriete IDE of editor. Kopieer dit codefragment naar uw project in een bestand met de naam `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Als u deze modules nog niet hebt gebruikt, moet u ze installeren voordat u het programma uitvoert. Voer voor het installeren van deze pakketten voert u `pip install requests` uit.

Aanvragen worden gebruikt voor HTTP-aanvragen voor de tekst naar spraak-service.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>De abonnements sleutel instellen en een prompt voor TTS maken

In de volgende secties maakt u methoden voor het afhandelen van autorisatie, het aanroepen van de tekst-naar-spraak-API en het valideren van het antwoord. Laten we beginnen met het maken van een klasse. Hier worden onze methoden voor het uitwisselen van tokens en het aanroepen van de tekst-naar-spraak-API.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

De `subscription_key` is uw unieke sleutel van de Azure Portal.

## <a name="get-an-access-token"></a>Een toegangstoken opvragen

Voor dit eind punt is een toegangs token voor verificatie vereist. Voor het verkrijgen van een toegangs token is een uitwisseling vereist. In dit voor beeld wordt uw abonnements sleutel voor spraak Services voor een toegangs token met behulp van het eind punt uitgewisseld `issueToken` .

In dit voor beeld wordt ervan uitgegaan dat uw speech service-abonnement zich in de regio vs-West bevindt. Als u een andere regio gebruikt, werkt u de waarde voor bij `fetch_token_url` . Zie [regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)voor een volledige lijst.

Kopieer deze code naar de `GetVoices` klasse:

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

Hier gaat u de aanvraag maken en de lijst met geretourneerde stemmen opslaan. Eerst moet u de `base_url` en opgeven `path` . In dit voor beeld wordt ervan uitgegaan dat u gebruikmaakt van het eind punt vs West. Als uw resource is geregistreerd in een andere regio, moet u ervoor zorgen dat u de hebt bijgewerkt `base_url` . Zie [Speech Service regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)(Engelstalig) voor meer informatie.

Voeg vervolgens de vereiste headers voor de aanvraag toe. Ten slotte maakt u een aanvraag voor de service. Als de aanvraag is gelukt en er een 200-status code wordt geretourneerd, wordt het antwoord naar het bestand geschreven.

Kopieer deze code naar de `GetVoices` klasse:

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

U bent bijna klaar. De laatste stap is het instantiëren van uw klasse en het aanroepen van uw functies.

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

* [Naslag informatie over de tekst-naar-spraak-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Aangepaste spraak lettertypen maken](how-to-customize-voice-font.md)
* [Spraak voorbeelden vastleggen om een aangepaste spraak te maken](record-custom-voice-samples.md)
