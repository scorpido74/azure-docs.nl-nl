---
title: 'Quick Start: asynchrone synthese voor lange-vorm audio (preview)-spraak service'
titleSuffix: Azure Cognitive Services
description: Gebruik de lange audio-API om tekst asynchroon te converteren naar spraak en de audio-uitvoer op te halen uit een URI die door de service wordt verschaft. Deze REST API is ideaal voor inhouds providers die tekst bestanden moeten converteren die groter zijn dan 10.000 tekens of 50 alinea's in gesynthesizerde spraak.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: d3cd330001bcf53e7bd4fb9e6955c76a9ef20511
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331073"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Quick Start: asynchrone synthese voor lange-vorm audio in python (preview-versie)

In deze Quick Start gebruikt u de lange audio-API om tekst asynchroon te converteren naar spraak en de audio-uitvoer op te halen uit een URI die door de service wordt verschaft. Deze REST API is ideaal voor inhouds providers waarvoor audio moet worden gesynthesizerd van tekst die groter is dan 5.000 tekens (of meer dan 10 minuten lang). Zie voor meer informatie [Long audio API](../../long-audio-api.md)(Engelstalig).

> [!NOTE]
> Asynchrone synthese voor lange-vorm audio kan alleen worden gebruikt met [aangepaste Neural stemmen](../../how-to-custom-voice.md#custom-neural-voices).

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* Python 2.7. x of 3. x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio code](https://code.visualstudio.com/download)of uw favoriete tekst editor.
* Een Azure-abonnement en een sleutel voor spraak service-abonnement. [Maak een Azure-account](../../get-started.md#new-resource) en [Maak een spraak bron](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) om de sleutel op te halen. Wanneer u de spraak bron maakt, moet u ervoor zorgen dat uw prijs categorie is ingesteld op **s0**en dat de locatie is ingesteld op een [ondersteunde regio](../../regions.md#standard-and-neural-voices).

## <a name="create-a-project-and-import-required-modules"></a>Een project maken en de vereiste modules importeren

Maak een nieuw Python-project met uw favoriete IDE of editor. Kopieer vervolgens dit code fragment naar een bestand met de naam `voice_synthesis_client.py`.

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

> [!NOTE]
> Als u deze modules niet hebt gebruikt, moet u deze installeren voordat u het programma uitvoert. Voer voor het installeren van deze pakketten voert u `pip install requests urllib3` uit.

Deze modules worden gebruikt voor het parseren van argumenten, het samen stellen van de HTTP-aanvraag en het aanroepen van de tekst-naar-spraak-audio REST API.

## <a name="get-a-list-of-supported-voices"></a>Een lijst met ondersteunde stemmen ophalen

Met deze code wordt een lijst met beschik bare stemmen opgehaald die u kunt gebruiken om tekst naar spraak te converteren. Voeg de code toe aan `voice_synthesis_client.py`:

```python
parser = argparse.ArgumentParser(description='Cris client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the cris subscription key, like ff1eb62d06d34767bda0207acb1da7d7 ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.cris.ai/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

### <a name="test-your-code"></a>Uw code testen

Laten we eens testen wat u tot nu toe hebt gedaan. U moet een paar dingen bijwerken in de onderstaande aanvraag:

* Vervang `<your_key>` door de abonnements sleutel van uw speech-service. Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).
* Vervang `<region>` door de regio waarin uw spraak bron is gemaakt (bijvoorbeeld: `eastus` of `westus`). Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).

Voer deze opdracht uit:

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

U ziet een uitvoer die er als volgt uitziet:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>Invoer bestanden voorbereiden

Een invoer tekst bestand voorbereiden. Dit kan onbewerkte tekst of SSML tekst zijn. Zie [inhoud voorbereiden voor synthese](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis)voor de vereisten voor het invoer bestand.

## <a name="convert-text-to-speech"></a>Tekst naar spraak converteren

Nadat u het invoer tekst bestand hebt voor bereid, voegt u deze code voor spraak synthese toe aan `voice_synthesis_client.py`:

> [!NOTE]
> ' concatenateResult ' is een optionele para meter. Als deze para meter niet is ingesteld, worden de audio-uitvoer per alinea gegenereerd. U kunt de audio waarden ook samen voegen in 1 uitvoer door de para meter in te stellen. De audio-uitvoer wordt standaard ingesteld op RIFF-16khz-16-mono-PCM. Zie [audio-uitvoer indelingen](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats)voor meer informatie over ondersteunde audio-uitvoer.

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

### <a name="test-your-code"></a>Uw code testen

Laten we een aanvraag indienen voor het samen stellen van tekst met behulp van uw invoer bestand als bron. U moet een paar dingen bijwerken in de onderstaande aanvraag:

* Vervang `<your_key>` door de abonnements sleutel van uw speech-service. Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).
* Vervang `<region>` door de regio waarin uw spraak bron is gemaakt (bijvoorbeeld: `eastus` of `westus`). Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).
* Vervang `<input>` door het pad naar het tekst bestand dat u voor tekst naar spraak hebt voor bereid.
* Vervang `<locale>` door de gewenste land instelling voor uitvoer. Zie [taal ondersteuning](../../language-support.md#neural-voices)voor meer informatie.
* Vervang `<voice_guid>` door de gewenste uitvoer stem. Gebruik een van de stemmen die worden geretourneerd door [een lijst met ondersteunde stemmen](#get-a-list-of-supported-voices)op te halen.

Converteer tekst naar spraak met deze opdracht:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Als u meer dan 1 invoer bestanden hebt, moet u meerdere aanvragen indienen. Er zijn enkele beperkingen die u moet kennen. 
> * De client mag Maxi maal **5** aanvragen voor de server per seconde indienen voor elk account van een Azure-abonnement. Als deze de beperking overschrijdt, krijgt de client een fout code van 429 (te veel aanvragen). Verminder de aanvraag hoeveelheid per seconde
> * De server mag Maxi maal **120** aanvragen voor elk account van een Azure-abonnement uitvoeren en in de wachtrij plaatsen. Als de limiet wordt overschreden, retourneert de server een fout code van 429 (te veel aanvragen). Een ogen blik geduld en het verzenden van een nieuwe aanvraag voor komen totdat sommige aanvragen zijn voltooid

U ziet een uitvoer die er als volgt uitziet:

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

Het resultaat bevat de invoer tekst en de audio-uitvoer bestanden die door de service worden gegenereerd. U kunt deze bestanden downloaden in een zip-bestand.

## <a name="remove-previous-requests"></a>Eerdere aanvragen verwijderen

De server bewaart Maxi maal **20.000** aanvragen voor elk account van een Azure-abonnement. Als uw aanvraag bedrag deze beperking overschrijdt, moet u de vorige aanvragen verwijderen voordat u er nieuwe maakt. Als u geen bestaande aanvragen verwijdert, ontvangt u een fout melding.

Voeg de code toe aan `voice_synthesis_client.py`:

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

### <a name="test-your-code"></a>Uw code testen

We gaan nu kijken welke aanvragen u eerder hebt verzonden. Voordat u doorgaat, moet u enkele dingen in deze aanvraag bijwerken:

* Vervang `<your_key>` door de abonnements sleutel van uw speech-service. Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).
* Vervang `<region>` door de regio waarin uw spraak bron is gemaakt (bijvoorbeeld: `eastus` of `westus`). Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).

Voer deze opdracht uit:

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Hiermee wordt een lijst geretourneerd met de synthese aanvragen die u hebt gemaakt. U ziet een uitvoer als volgt:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Nu gaan we een eerder ingediende aanvraag verwijderen. U moet een paar dingen bijwerken in de onderstaande code:

* Vervang `<your_key>` door de abonnements sleutel van uw speech-service. Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).
* Vervang `<region>` door de regio waarin uw spraak bron is gemaakt (bijvoorbeeld: `eastus` of `westus`). Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).
* Vervang `<synthesis_id>` door de waarde die in de vorige aanvraag is geretourneerd.

> [!NOTE]
> Aanvragen met de status Running/Waiting kunnen niet worden verwijderd of verwijderd.

Voer deze opdracht uit:

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

U ziet een uitvoer als volgt:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>De volledige client ophalen

De voltooide `voice_synthesis_client.py` is beschikbaar voor downloaden op [github](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de API voor lange audio](../../long-audio-api.md)
