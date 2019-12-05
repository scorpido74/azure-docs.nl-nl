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
ms.openlocfilehash: 1558b2eb12b1d4745cdfeab41fc2d1bd829b3d9c
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816685"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Quick Start: asynchrone synthese voor lange-vorm audio in python (preview-versie)

In deze Quick Start gebruikt u de lange audio-API om tekst asynchroon te converteren naar spraak en de audio-uitvoer op te halen uit een URI die door de service wordt verschaft. Deze REST API is ideaal voor inhouds providers die tekst bestanden moeten converteren die groter zijn dan 10.000 tekens of 50 alinea's in gesynthesizerde spraak. Zie voor meer informatie [Long audio API](../../long-audio-api.md)(Engelstalig).

> [!NOTE]
> Asynchrone synthese voor lange-vorm audio kan alleen worden gebruikt met [aangepaste Neural stemmen](../../how-to-custom-voice.md#custom-neural-voices).

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* Python 2.7. x of 3. x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio code](https://code.visualstudio.com/download)of uw favoriete tekst editor.
* Een Azure-abonnement en een sleutel voor spraak service-abonnement. [Maak een Azure-account](../../get-started.md#try-the-speech-service-using-a-new-azure-account) en [Maak een spraak bron](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) om de sleutel op te halen. Wanneer u de spraak bron maakt, moet u ervoor zorgen dat uw prijs categorie is ingesteld op **s0**en dat de locatie is ingesteld op een [ondersteunde regio](../../regions.md#standard-and-neural-voices).

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
> Als u deze modules nog niet hebt gebruikt, moet u ze installeren voordat u het programma uitvoert. Voer voor het installeren van deze pakketten voert u `pip install requests urllib3` uit.

Deze modules worden gebruikt voor het parseren van argumenten, het samen stellen van de HTTP-aanvraag en het aanroepen van de tekst-naar-spraak-audio REST API.

## <a name="get-a-list-of-supported-voices"></a>Een lijst met ondersteunde stemmen ophalen

Met deze code wordt een lijst met beschik bare stemmen opgehaald die u kunt gebruiken om tekst naar spraak te converteren. Voeg deze code toe `voice_synthesis_client.py`:

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

Laten we eens testen wat u tot nu toe hebt gedaan. Voer deze opdracht uit, vervang `<your_key>` door de sleutel van uw spraak abonnement en `<region>` met de regio waar uw spraak bron is gemaakt (bijvoorbeeld: `eastus` of `westus`). Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

U moet een uitvoer krijgen die er als volgt uitziet:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="convert-text-to-speech"></a>Tekst naar spraak converteren

De volgende stap is het voorbereiden van een invoer tekst bestand. Dit kan onbewerkte tekst of SSML zijn, maar moet groter zijn dan 10.000 tekens of 50 alinea's. Zie voor een volledige lijst met vereisten [lange audio-API](../../long-audio-api.md).

Nadat u het tekst bestand hebt voor bereid. De volgende stap is het toevoegen van code voor spraak synthese aan uw project. Voeg deze code toe aan `voice_synthesis_client.py`:

> [!NOTE]
> De audio-uitvoer wordt standaard ingesteld op RIFF-16khz-16-mono-PCM. Zie voor meer informatie over ondersteunde audio-uitvoer, [Long audio API](../../long-audio-api.md#audio-output-formats).

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
        location = response.headers['Operation-Location']
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

We gaan nu een aanvraag indienen voor het samen stellen van tekst met behulp van uw invoer bestand als bron. U moet een paar dingen bijwerken in de onderstaande aanvraag:

* Vervang `<your_key>` door de abonnements sleutel van uw speech-service. Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).
* Vervang `<region>` door de regio waarin uw spraak bron is gemaakt (bijvoorbeeld: `eastus` of `westus`). Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).
* Vervang `<input>` door het pad naar het tekst bestand dat u wilt converteren van tekst naar spraak.
* Vervang `<locale>` door de gewenste land instelling voor uitvoer. Zie [taal ondersteuning](../../language-support.md#neural-voices)voor meer informatie.
* Vervang `<voice_guid>` door de gewenste stem voor de audio-uitvoer. Gebruik een van de stemmen die worden geretourneerd door [een lijst met ondersteunde stemmen](#get-a-list-of-supported-voices) op te halen of gebruik de lijst met Neural stemmen die worden geboden in [taal ondersteuning](../../language-support.md#neural-voices).

Converteer tekst naar spraak met deze opdracht:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> ' concatenateResult ' is een optionele para meter als deze para meter niet is opgenomen, wordt de uitvoer als meerdere Wave-bestanden weer gegeven, één voor elke regel.

U moet een uitvoer krijgen die er als volgt uitziet:

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

Het gegeven resultaat bevat de invoer tekst en de audio-uitvoer bestanden die door de service zijn gegenereerd. Deze worden als een zip gedownload.

## <a name="remove-previous-requests"></a>Eerdere aanvragen verwijderen

Er geldt een limiet van 2.000 aanvragen voor elk abonnement. Daarom moeten er eerder ingediende aanvragen worden verwijderd voordat u er nieuwe kunt maken. Als u geen bestaande aanvragen verwijdert, ontvangt u een fout melding wanneer u 2.000 overschrijdt.

Voeg deze code toe aan `voice_synthesis_client.py`:

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

Voer deze opdracht uit, vervang `<your_key>` door de sleutel van uw spraak abonnement en `<region>` met de regio waar uw spraak bron is gemaakt (bijvoorbeeld: `eastus` of `westus`). Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).

```console
python voice_synthesis_client.py – syntheses -key <your_key> -region <Region>
```

Hiermee wordt een lijst geretourneerd met syntheses die u hebt aangevraagd. U moet een uitvoer krijgen die er als volgt uitziet:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

We gaan nu enkele van deze waarden gebruiken om eerder ingediende aanvragen te verwijderen/verwijderen. Voer deze opdracht uit, vervang `<your_key>` door de sleutel van uw spraak abonnement en `<region>` met de regio waar uw spraak bron is gemaakt (bijvoorbeeld: `eastus` of `westus`). Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal). De `<synthesis_id>` moet een van de waarden zijn die in de vorige aanvraag zijn geretourneerd.

> [!NOTE]
> Aanvragen met de status Running/Waiting kunnen niet worden verwijderd of verwijderd.

```console
python voice_synthesis_client.py – delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

U moet een uitvoer krijgen die er als volgt uitziet:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>De volledige client ophalen

De volledige `voice_synthesis_client.py` is beschikbaar voor downloaden op [github](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de API voor lange audio](../../long-audio-api.md)
