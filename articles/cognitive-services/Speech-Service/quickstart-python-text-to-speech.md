---
title: Tekst-naar-spraak-, python-Speech-Service converteren
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u tekst naar spraak kunt converteren met behulp van python en de tekst-naar-spraak REST API. De voorbeeld tekst in deze hand leiding is gestructureerd als SSML (Speech synthese Markup Language). Hiermee kunt u de stem en de taal van het antwoord op de spraak kiezen.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 171fdb033cba422d8ba580da3ab54db88ca20872
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400820"
---
# <a name="convert-text-to-speech-using-python"></a>Tekst-naar-spraak converteren met behulp van python

In dit artikel leert u hoe u tekst naar spraak kunt converteren met behulp van python en de tekst-naar-spraak REST API. De aanvraag tekst in deze hand leiding is gestructureerd als [SSML (Speech synthese Markup Language)](speech-synthesis-markup.md), waarmee u de stem en de taal van het antwoord kunt kiezen.

Voor dit artikel is een [Azure Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vereist met een speech service-resource. Als u geen account hebt, kunt u de [gratis proefversie](get-started.md) gebruiken om een abonnementssleutel op te halen.

## <a name="prerequisites"></a>Vereisten

* Python 2.7.x of 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>of uw favoriete tekst editor
* Een sleutel van een Azure-abonnement voor de speech-service

## <a name="create-a-project-and-import-required-modules"></a>Een project maken en de vereiste modules importeren

Maak een nieuw Python-project met uw favoriete IDE of editor. Kopieer dit codefragment naar uw project in een bestand met de naam `tts.py`.

```python
import os
import requests
import time
from xml.etree import ElementTree
```

> [!NOTE]
> Als u deze modules nog niet hebt gebruikt, moet u ze installeren voordat u het programma uitvoert. Voer voor het installeren van deze pakketten voert u `pip install requests` uit.

Deze modules worden gebruikt voor het schrijven van het antwoord op een bestand met een tijds tempel, het maken van de HTTP-aanvraag en het aanroepen van de tekst-naar-spraak-API.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>De abonnements sleutel instellen en een prompt voor TTS maken

In de volgende secties maakt u methoden voor het afhandelen van autorisatie, het aanroepen van de tekst-naar-spraak-API en het valideren van het antwoord. Laten we beginnen door een code toe te voegen waarmee wordt gecontroleerd of dit voor beeld werkt met python 2.7. x en 3. x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Vervolgens gaan we een klasse maken. Hier worden onze methoden voor het uitwisselen van tokens en het aanroepen van de tekst-naar-spraak-API.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

De `subscription_key` is uw unieke sleutel van de Azure Portal. `tts`vraagt de gebruiker om tekst in te voeren die naar spraak wordt geconverteerd. Deze invoer is een letterlijke teken reeks, dus tekens moeten dus niet worden voorafgegaan. `timestr` Hiermee haalt u de huidige tijd op die we gebruiken om uw bestand een naam te gegeven.

## <a name="get-an-access-token"></a>Een toegangstoken opvragen

Voor de REST API tekst naar spraak is een toegangs token voor verificatie vereist. Voor het verkrijgen van een toegangs token is een uitwisseling vereist. In dit voor beeld wordt uw abonnements sleutel voor spraak Services voor een toegangs `issueToken` token met behulp van het eind punt uitgewisseld.

In dit voor beeld wordt ervan uitgegaan dat uw speech service-abonnement zich in de regio vs-West bevindt. Als u een andere regio gebruikt, werkt u de waarde voor `fetch_token_url`bij. Zie [regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)voor een volledige lijst.

Kopieer deze code naar de `TextToSpeech` klasse:

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

Hier gaat u de aanvraag maken en het antwoord op de spraak actie Opslaan. Eerst moet u de `base_url` en `path`opgeven. In dit voor beeld wordt ervan uitgegaan dat u gebruikmaakt van het eind punt vs West. Als uw resource is geregistreerd in een andere regio, moet u ervoor zorgen dat `base_url`u de hebt bijgewerkt. Zie [Speech Service regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)(Engelstalig) voor meer informatie.

Vervolgens moet u de vereiste headers voor de aanvraag toevoegen. Zorg ervoor dat u bijwerkt `User-Agent` met de naam van uw resource (in de Azure Portal) en stel `X-Microsoft-OutputFormat` deze in op de audio-uitvoer van uw voor keur. Zie [audio-uitvoer](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)voor een volledige lijst met uitvoer indelingen.

Maak vervolgens de aanvraag tekst met behulp van SSML (Speech synthese Markup Language). In dit voor beeld wordt de structuur gedefinieerd en `tts` wordt de invoer gebruikt die u eerder hebt gemaakt.

>[!NOTE]
> In dit voor beeld `Guy24kRUS` wordt het letter type Voice gebruikt. Zie [taal ondersteuning](language-support.md)voor een volledige lijst van door micro soft geboden stemmen/talen.
> Zie [aangepaste spraak lettertypen maken](how-to-customize-voice-font.md)als u geïnteresseerd bent in het maken van een unieke, herken bare spraak voor uw merk.

Ten slotte maakt u een aanvraag voor de service. Als de aanvraag is gelukt en er een 200-status code wordt geretourneerd, wordt het spraak antwoord naar een tijds tempel bestand geschreven.

Kopieer deze code naar de `TextToSpeech` klasse:

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set(
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) +
              "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Alles samenvoegen

U bent bijna klaar. De laatste stap is het instantiëren van uw klasse en het aanroepen van uw functies.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

Dat is alles. u bent klaar om uw tekst-naar-spraak-voorbeeld-app uit te voeren. Ga vanaf de opdrachtregel (of terminalsessie) naar de projectmap en voer het volgende uit:

```console
python tts.py
```

Wanneer u hierom wordt gevraagd, typt u in datgene wat u wilt converteren van tekst naar spraak. Als dit lukt, bevindt het spraak bestand zich in de projectmap. Speel het af met uw favoriete media speler.

## <a name="clean-up-resources"></a>Resources opschonen

Denk eraan dat u eventuele vertrouwelijke informatie, zoals abonnementssleutels, verwijdert uit de broncode van de voorbeeld-app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Python-voorbeelden in GitHub bekijken](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Zie ook

* [Naslag informatie over de tekst-naar-spraak-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Met python en Speech SDK tekst-naar-spraak converteren](quickstarts/speech-to-text-from-microphone.md)
* [Aangepaste spraak lettertypen maken](how-to-customize-voice-font.md)
* [Spraak voorbeelden vastleggen om een aangepaste spraak te maken](record-custom-voice-samples.md)
