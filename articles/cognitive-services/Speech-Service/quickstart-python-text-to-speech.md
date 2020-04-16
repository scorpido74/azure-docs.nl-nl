---
title: Tekst-naar-spraak, Python - Spraakservice converteren
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u tekst-naar-spraak converteert met Python en de API Voor tekst-naar-spraakrest. De voorbeeldtekst in deze handleiding is gestructureerd als Spraaksyntheseopmaaktaal (SSML). Hiermee u de stem en taal van de spraakrespons kiezen.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 171fdb033cba422d8ba580da3ab54db88ca20872
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400820"
---
# <a name="convert-text-to-speech-using-python"></a>Tekst-naar-spraak converteren met Python

In dit artikel leert u hoe u tekst-naar-spraak converteert met Python en de REST-API voor tekst naar spraak. De aanvraaginstantie in deze handleiding is gestructureerd als [Speech Synthesis Markup Language (SSML),](speech-synthesis-markup.md)waarmee u de stem en taal van het antwoord kiezen.

Voor dit artikel is een [Azure Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vereist met een Spraakservicebron. Als u geen account hebt, kunt u de [gratis proefversie](get-started.md) gebruiken om een abonnementssleutel op te halen.

## <a name="prerequisites"></a>Vereisten

* Python 2.7.x of 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>of uw favoriete teksteditor
* Een Azure-abonnementssleutel voor de spraakservice

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

Deze modules worden gebruikt om het spraakantwoord op een bestand te schrijven met een tijdstempel, de HTTP-aanvraag te construeren en de text-to-speech-API aan te roepen.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>De abonnementssleutel instellen en een prompt maken voor TTS

In de volgende secties maakt u methoden voor het afhandelen van autorisatie, het aanroepen van de API voor tekst naar spraak en het valideren van het antwoord. Laten we beginnen met het toevoegen van een aantal code die ervoor zorgt dat dit voorbeeld zal werken met Python 2.7.x en 3.x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Laten we vervolgens een klasse maken. Dit is waar we onze methoden voor token-uitwisseling en het aanroepen van de text-to-speech-API plaatsen.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

Het `subscription_key` is uw unieke sleutel van de Azure-portal. `tts`vraagt de gebruiker om tekst in te voeren die wordt omgezet in spraak. Deze invoer is een tekenreeks letterlijk, zodat tekens niet hoeven te worden ontsnapt. Tot `timestr` slot, krijgt de huidige tijd, die we zullen gebruiken om uw bestand een naam.

## <a name="get-an-access-token"></a>Een toegangstoken opvragen

De rest-to-speech REST API vereist een toegangstoken voor verificatie. Om een toegangstoken te krijgen, is een uitwisseling vereist. In dit voorbeeld wordt uw abonnementssleutel voor `issueToken` spraakservice uitgewisseld voor een toegangstoken met behulp van het eindpunt.

In dit voorbeeld wordt ervan uitgegaan dat uw spraakserviceabonnement zich in de regio West-VS bevindt. Als u een ander gebied gebruikt, `fetch_token_url`werkt u de waarde bij voor . Zie [Regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)voor een volledige lijst .

Kopieer deze code `TextToSpeech` naar de klasse:

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

Hier ga je het verzoek bouwen en de spraakreactie opslaan. Eerst moet je de `base_url` `path`en. In dit voorbeeld wordt ervan uitgegaan dat u het Eindpunt van west-VS gebruikt. Als uw resource is geregistreerd in een andere `base_url`regio, moet u de . Zie [Spraakserviceregio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)voor meer informatie .

Vervolgens moet u vereiste kopteksten voor de aanvraag toevoegen. Zorg ervoor dat `User-Agent` u de naam van uw bron bijwerkt `X-Microsoft-OutputFormat` (in de Azure-portal) en ingesteld op de audio-uitvoer van uw voorkeur. Zie [Audio-uitvoer](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)voor een volledige lijst met uitvoerindelingen .

Bouw vervolgens de aanvraaginstantie met behulp van Spraaksynthese-opmaaktaal (SSML). In dit voorbeeld wordt de `tts` structuur gedefinieerd en wordt de invoer gebruikt die u eerder hebt gemaakt.

>[!NOTE]
> In dit `Guy24kRUS` voorbeeld wordt het spraaklettertype gebruikt. Zie [Taalondersteuning](language-support.md)voor een volledige lijst met door Microsoft geleverde stemmen/talen.
> Zie [Aangepaste spraaklettertypen maken](how-to-customize-voice-font.md)als u geïnteresseerd bent in het maken van een unieke, herkenbare stem voor uw merk.

Tot slot doet u een verzoek aan de service. Als de aanvraag is geslaagd en een statuscode van 200 wordt geretourneerd, wordt het spraakantwoord naar een getimestamped bestand geschreven.

Kopieer deze code `TextToSpeech` naar de klasse:

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

U bent bijna klaar. De laatste stap is om uw klas te instantiëren en uw functies te bellen.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

Dat is het, je bent klaar om je text-to-speech sample app uit te voeren. Ga vanaf de opdrachtregel (of terminalsessie) naar de projectmap en voer het volgende uit:

```console
python tts.py
```

Wanneer u daarom wordt gevraagd, typt u alles in wat u wilt converteren van tekst naar toespraak. Als dit is gelukt, bevindt het spraakbestand zich in uw projectmap. Speel het met je favoriete mediaspeler.

## <a name="clean-up-resources"></a>Resources opschonen

Denk eraan dat u eventuele vertrouwelijke informatie, zoals abonnementssleutels, verwijdert uit de broncode van de voorbeeld-app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Python-voorbeelden in GitHub bekijken](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Zie ook

* [API-verwijzing naar tekst naar spraak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Python en Speech SDK gebruiken om tekst-naar-spraak te converteren](quickstarts/speech-to-text-from-microphone.md)
* [Aangepaste spraaklettertypen maken](how-to-customize-voice-font.md)
* [Spraakvoorbeelden opnemen om een aangepaste stem te maken](record-custom-voice-samples.md)
