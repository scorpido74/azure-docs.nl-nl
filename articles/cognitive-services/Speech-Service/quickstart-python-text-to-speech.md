---
title: 'Quickstart: Tekst-naar-spraak-, python-Speech-Service converteren'
titleSuffix: Azure Cognitive Services
description: In deze snelstartgids leert u hoe u om te converteren tekst naar spraak met behulp van Python en de tekst naar spraak REST-API. De voorbeeldtekst opgenomen in deze handleiding is opgebouwd als spraak synthese Markup Language (SSML). Hiermee kunt u de spraak en taal van het antwoord spraak te kiezen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: a2e43b72bda65c5f1e7515888ac33b0ac806e64c
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803248"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>Quickstart: Tekst-naar-spraak converteren met behulp van python

In deze snelstartgids leert u hoe u om te converteren tekst naar spraak met behulp van Python en de Text to Speech REST-API. De hoofdtekst van de aanvraag in deze handleiding is opgebouwd als [spraak synthese Markup Language (SSML)](speech-synthesis-markup.md), waarmee u de spraak en taal van het antwoord te kiezen.

Voor deze Snelstartgids is een [Azure Cognitive Services-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vereist met een speech Services-resource. Als u geen account hebt, kunt u de [gratis proefversie](get-started.md) gebruiken om een abonnementssleutel op te halen.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* Python 2.7.x of 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) of uw favoriete teksteditor
* Een sleutel van een Azure-abonnement voor de spraak Services

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

Deze modules worden gebruikt voor het schrijven van het antwoord spraak als een bestand met een tijdstempel, de HTTP-aanvraag maken en aanroepen van de Text to Speech-API.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Stel de abonnementssleutel en een prompt voor TTS maken

In de volgende gedeelten maakt u methoden voor verwerking van autorisatie, de Text to Speech-API aanroepen en valideren van het antwoord. Laten we beginnen met het toevoegen van code die ervoor dat in dit voorbeeld zorgt werkt met Python 2.7.x en 3.x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Vervolgens maken we een klasse. Dit is waar gaan we onze methoden voor token exchange en het aanroepen van de Text to Speech-API.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

De `subscription_key` is de unieke sleutel vanuit de Azure-portal. `tts` vraagt de gebruiker tekst die wordt geconverteerd naar spraak in te voeren. Deze invoer is een tekenreeks, zodat tekens niet hoeft te worden weergegeven. Ten slotte `timestr` opgehaald van de huidige tijd, die we gebruiken om de naam van het bestand.

## <a name="get-an-access-token"></a>Een toegangstoken opvragen

De Text to Speech REST-API is een toegangstoken voor verificatie vereist. Als u een toegangstoken, is een exchange vereist. In dit voor beeld wordt uw abonnements sleutel voor spraak Services voor een toegangs token uitgewisseld met behulp van het eind punt `issueToken`.

In dit voor beeld wordt ervan uitgegaan dat uw speech Services-abonnement zich in de regio vs-West bevindt. Als u een andere regio, werk de waarde voor `fetch_token_url`. Zie voor een volledige lijst [regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Kopieer deze code in de `TextToSpeech` klasse:

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

Hier gaat u naar de aanvraag voor het samenstellen en sla het antwoord spraak. Eerst moet u om in te stellen de `base_url` en `path`. In dit voorbeeld wordt ervan uitgegaan dat u het eindpunt van de VS-West. Als de bron naar een andere regio is geregistreerd, moet u bijwerken de `base_url`. Zie voor meer informatie [spraak Services regio's](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Vervolgens moet u vereiste headers voor de aanvraag toevoegen. Zorg ervoor dat bij te werken `User-Agent` met de naam van uw resource (te vinden in Azure portal) en stel `X-Microsoft-OutputFormat` aan uw gewenste audio-uitvoer. Zie voor een volledige lijst van de uitvoerindeling op te geven, [Audio-uitvoer](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Vervolgens maken de hoofdtekst van de aanvraag met behulp van spraak synthese Markup Language (SSML). In dit voorbeeld definieert u de structuur en maakt gebruik van de `tts` invoer u eerder hebt gemaakt.

>[!NOTE]
> In dit voorbeeld wordt de `Guy24KRUS` spraakstijl. Voor een volledige lijst van door Microsoft geleverd stemmen/talen, Zie [taalondersteuning](language-support.md).
> Als u geïnteresseerd bent in het maken van een unieke, herkenbare stem voor uw merk, Zie [het maken van aangepaste spraakstijlen](how-to-customize-voice-font.md).

Ten slotte maakt u een aanvraag naar de service. Als de aanvraag geslaagd is, en een 200-statuscode is geretourneerd, wordt het antwoord spraak geschreven naar een bestand voorzien van een tijdstempel.

Kopieer deze code in de `TextToSpeech` klasse:

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
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
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

U bent bijna klaar. De laatste stap is het instantiëren van de klasse en aanroepen van uw functies.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

Dat is alles, u kunt nu uw Text to Speech voorbeeldapp uit te voeren. Ga vanaf de opdrachtregel (of terminalsessie) naar de projectmap en voer het volgende uit:

```console
python tts.py
```

Wanneer u hierom wordt gevraagd, typt u in alles wat u wilt converteren van tekst naar spraak. Als dit lukt, wordt de spraak-bestand bevindt zich in de projectmap. Spelen met behulp van uw favoriete MediaPlayer.

## <a name="clean-up-resources"></a>Resources opschonen

Denk eraan dat u eventuele vertrouwelijke informatie, zoals abonnementssleutels, verwijdert uit de broncode van de voorbeeld-app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Python-voorbeelden in GitHub bekijken](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Zie ook

* [Naslaginformatie voor de Text to Speech-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Met python en Speech SDK tekst-naar-spraak converteren](quickstart-text-to-speech-python.md)
* [Het maken van aangepaste spraakstijlen](how-to-customize-voice-font.md)
* [Record stem voorbeelden voor het maken van een aangepaste spraak](record-custom-voice-samples.md)
