---
title: 'Snelstart: Asynchrone synthese voor lange audio (Preview) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Gebruik de LANGE Audio-API om tekst asynchroon om te zetten in spraak en de audio-uitvoer op te halen uit een URI die door de service wordt geleverd. Deze REST API is ideaal voor contentproviders die tekstbestanden van meer dan 10.000 tekens of 50 alinea's moeten converteren naar gesynthetiseerde spraak.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: d3cd330001bcf53e7bd4fb9e6955c76a9ef20511
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331073"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Snelstart: Asynchrone synthese voor lange audio in Python (Preview)

In deze snelstart gebruikt u de API Voor lange audio om tekst asynchroon om te zetten in spraak en de audio-uitvoer op te halen uit een URI die door de service wordt geleverd. Deze REST API is ideaal voor contentproviders die audio moeten synthetiseren uit tekst van meer dan 5.000 tekens (of meer dan 10 minuten lang). Zie [Long Audio API](../../long-audio-api.md)voor meer informatie.

> [!NOTE]
> Asynchrone synthese voor lange-vorm audio kan alleen worden gebruikt met [Custom Neural Voices](../../how-to-custom-voice.md#custom-neural-voices).

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* Python 2.7.x of 3.x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)of uw favoriete teksteditor.
* Een Azure-abonnement en een abonnementssleutel voor spraakservice. [Maak een Azure-account](../../get-started.md#new-resource) en [maak een spraakbron](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) om de sleutel te krijgen. Controleer bij het maken van de spraakbron of uw prijslaag is ingesteld op **S0**en dat de locatie is ingesteld op een [ondersteunde regio](../../regions.md#standard-and-neural-voices).

## <a name="create-a-project-and-import-required-modules"></a>Een project maken en de vereiste modules importeren

Maak een nieuw Python-project met uw favoriete IDE of editor. Kopieer dit codefragment vervolgens `voice_synthesis_client.py`naar een bestand met de naam .

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
> Als u deze modules niet hebt gebruikt, moet u ze installeren voordat u uw programma uitvoert. Voer voor het installeren van deze pakketten voert u `pip install requests urllib3` uit.

Deze modules worden gebruikt om argumenten te ontlopen, de HTTP-aanvraag te construeren en de text-to-speech long audio REST API aan te roepen.

## <a name="get-a-list-of-supported-voices"></a>Een lijst met ondersteunde stemmen

Deze code krijgt een lijst met beschikbare stemmen die u gebruiken om tekst-naar-spraak om te zetten. Voeg de `voice_synthesis_client.py`code toe aan:

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

Laten we testen wat je tot nu toe hebt gedaan. Je moet een paar dingen bijwerken in het onderstaande verzoek:

* Vervang `<your_key>` door de abonnementssleutel spraakservice. Deze informatie is beschikbaar op het tabblad **Overzicht** voor uw resource in de [Azure-portal.](https://aka.ms/azureportal)
* Vervang `<region>` door het gebied waar uw spraakbron `eastus` `westus`is gemaakt (bijvoorbeeld: of ). Deze informatie is beschikbaar op het tabblad **Overzicht** voor uw resource in de [Azure-portal.](https://aka.ms/azureportal)

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

## <a name="prepare-input-files"></a>Invoerbestanden voorbereiden

Een invoertekstbestand voorbereiden. Het kan platte tekst of SSML-tekst zijn. Zie voor de vereisten van het invoerbestand hoe u [inhoud voorbereidt op synthese](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis).

## <a name="convert-text-to-speech"></a>Tekst converteren naar spraak

Voeg na het voorbereiden van het invoertekstbestand `voice_synthesis_client.py`deze code toe voor spraaksynthese aan:

> [!NOTE]
> 'concatenateResult' is een optionele parameter. Als deze parameter niet is ingesteld, worden de audio-uitgangen per alinea gegenereerd. U de audio ook in 1 uitvoer toevoegen door de parameter in te stellen. Standaard is de audio-uitgang ingesteld op riff-16khz-16bit-mono-pcm. Zie [Audio-uitvoerindelingen](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats)voor meer informatie over ondersteunde audio-uitgangen.

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

Laten we een verzoek indienen om tekst te synthetiseren met behulp van uw invoerbestand als bron. Je moet een paar dingen bijwerken in het onderstaande verzoek:

* Vervang `<your_key>` door de abonnementssleutel spraakservice. Deze informatie is beschikbaar op het tabblad **Overzicht** voor uw resource in de [Azure-portal.](https://aka.ms/azureportal)
* Vervang `<region>` door het gebied waar uw spraakbron `eastus` `westus`is gemaakt (bijvoorbeeld: of ). Deze informatie is beschikbaar op het tabblad **Overzicht** voor uw resource in de [Azure-portal.](https://aka.ms/azureportal)
* Vervang `<input>` het pad naar het tekstbestand dat u hebt voorbereid voor tekst-naar-spraak.
* Vervang `<locale>` door de gewenste uitgangsland. Zie [taalondersteuning](../../language-support.md#neural-voices)voor meer informatie.
* Vervang `<voice_guid>` door de gewenste uitvoerstem. Gebruik een van de stemmen die worden geretourneerd door [Get a list of supported voices](#get-a-list-of-supported-voices).

Tekst converteren naar spraak met deze opdracht:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Als u meer dan 1 invoerbestanden hebt, moet u meerdere aanvragen indienen. Er zijn een aantal beperkingen die moet worden bewust. 
> * De client mag maximaal **5** aanvragen indienen bij server per seconde voor elk Azure-abonnementsaccount. Als de beperking wordt beperkt, krijgt de client een foutcode van 429 (te veel aanvragen). Verlaag het aanvraagbedrag per seconde
> * De server mag maximaal **120** aanvragen uitvoeren en in de wachtrij staan voor elk Azure-abonnementsaccount. Als de beperking wordt beperkt, retourneert de server een foutcode van 429 (te veel aanvragen). Wacht en vermijd het indienen van nieuwe aanvraag totdat sommige aanvragen zijn voltooid

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

Het resultaat bevat de invoertekst en de audio-uitvoerbestanden die door de service worden gegenereerd. U deze bestanden downloaden in een zip.

## <a name="remove-previous-requests"></a>Eerdere aanvragen verwijderen

De server houdt maximaal **20.000** aanvragen bij voor elk Azure-abonnementsaccount. Als uw aanvraagbedrag deze beperking overschrijdt, verwijdert u eerdere aanvragen voordat u nieuwe aanvragen maakt. Als u bestaande aanvragen niet verwijdert, ontvangt u een foutmelding.

Voeg de `voice_synthesis_client.py`code toe aan:

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

Laten we nu controleren welke aanvragen u eerder hebt ingediend. Voordat u verdergaat, moet u een paar dingen in dit verzoek bijwerken:

* Vervang `<your_key>` door de abonnementssleutel spraakservice. Deze informatie is beschikbaar op het tabblad **Overzicht** voor uw resource in de [Azure-portal.](https://aka.ms/azureportal)
* Vervang `<region>` door het gebied waar uw spraakbron `eastus` `westus`is gemaakt (bijvoorbeeld: of ). Deze informatie is beschikbaar op het tabblad **Overzicht** voor uw resource in de [Azure-portal.](https://aka.ms/azureportal)

Voer deze opdracht uit:

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Hiermee wordt een lijst met syntheseverzoeken die u hebt ingediend, retourneren. U ziet een uitvoer als volgt:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Laten we nu een eerder ingediende aanvraag verwijderen. Je moet een paar dingen bijwerken in de onderstaande code:

* Vervang `<your_key>` door de abonnementssleutel spraakservice. Deze informatie is beschikbaar op het tabblad **Overzicht** voor uw resource in de [Azure-portal.](https://aka.ms/azureportal)
* Vervang `<region>` door het gebied waar uw spraakbron `eastus` `westus`is gemaakt (bijvoorbeeld: of ). Deze informatie is beschikbaar op het tabblad **Overzicht** voor uw resource in de [Azure-portal.](https://aka.ms/azureportal)
* Vervang `<synthesis_id>` de waarde die in de vorige aanvraag is geretourneerd.

> [!NOTE]
> Aanvragen met de status 'Hardlopen'/'Wachten' kunnen niet worden verwijderd of verwijderd.

Voer deze opdracht uit:

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

U ziet een uitvoer als volgt:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Krijg de volledige klant

De `voice_synthesis_client.py` voltooide is beschikbaar voor download op [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de Lange Audio API](../../long-audio-api.md)
