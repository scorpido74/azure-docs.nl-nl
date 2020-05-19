---
title: SPX-basis beginselen-spraak service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van het opdracht regel programma SPX om met de spraak-SDK te werken zonder code en minimale Setup.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 68947895891b4875ef4c57355f1236afdb8c2c7d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83204819"
---
# <a name="learn-the-basics-of-spx"></a>Meer informatie over de basis principes van SPX

In dit artikel leert u de basis gebruiks patronen van SPX, een opdracht regel programma voor het gebruik van de spraak service zonder code te schrijven. U kunt snel de belangrijkste functies van de spraak service testen zonder ontwikkel omgevingen te maken of code te schrijven om te controleren of er voldoende problemen zijn met het gebruik van uw gebruiks voorbeelden. Daarnaast is SPX gereed voor productie en kan worden gebruikt voor het automatiseren van eenvoudige werk stromen in de spraak service, met behulp van `.bat` of shell-scripts.

## <a name="prerequisites"></a>Vereisten

De enige vereiste is een Azure speech-abonnement. Raadpleeg de [hand leiding](get-started.md#new-resource) voor het maken van een nieuw abonnement als u er nog geen hebt.

## <a name="download-and-install"></a>Downloaden en installeren

SPX is beschikbaar in Windows en Linux. Begin met het downloaden van het [zip-archief](https://aka.ms/speech/spx-zips.zip)en pak het uit. SPX vereist de .NET core-of .NET Framework runtime en de volgende versies worden ondersteund door het platform:

* Windows: [.NET Framework 4,7](https://dotnet.microsoft.com/download/dotnet-framework/net471), [.net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
* Linux: [.net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)

Nadat u een runtime hebt geïnstalleerd, gaat u naar de hoofdmap `spx-zips` die u hebt geëxtraheerd uit de down load en extraheert u de submap die u nodig hebt ( `spx-net471` bijvoorbeeld). In een opdracht prompt wijzigt u de map in deze locatie en voert `spx` u uit om de toepassing te starten.

## <a name="create-subscription-config"></a>Abonnements configuratie maken

Als u SPX wilt gebruiken, moet u eerst de sleutel voor het spraak abonnement en de regio gegevens invoeren. Bekijk de [ondersteunings](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) pagina voor regio's om uw regio-id te vinden. Zodra u uw abonnements sleutel en regio-id hebt (bijvoorbeeld `eastus`, `westus` ) voert u de volgende opdrachten uit.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

Uw abonnements verificatie wordt nu opgeslagen voor toekomstige SPX-aanvragen. Als u een van deze opgeslagen waarden wilt verwijderen, voert u `spx config @region --clear` of uit `spx config @key --clear` .

## <a name="basic-usage"></a>Basisgebruik

In deze sectie worden enkele eenvoudige SPX-opdrachten weer gegeven die vaak nuttig zijn voor de eerste keer testen en experimenteren. Voer de volgende opdracht uit om te beginnen met het uitvoeren van een spraak herkenning met behulp van de standaard microfoon.

```shell
spx recognize --microphone
```

Nadat u de opdracht hebt ingevoerd, luistert SPX naar audio op het huidige actieve invoer apparaat en stopt het nadat u hebt geklikt `ENTER` . De opgenomen spraak wordt vervolgens herkend en geconverteerd naar tekst in de console-uitvoer. De tekst-naar-spraak-synthese is ook eenvoudig te gebruiken met behulp van SPX. 

Als u de volgende opdracht uitvoert, wordt de ingevoerde tekst als invoer uitgevoerd en wordt de gesynthesizerde spraak naar het huidige actieve uitvoer apparaat uitgevoerd.

```shell
spx synthesize --text "Testing synthesis using SPX" --speakers
```

Naast spraak herkenning en synthese kunt u ook spraak omzetting met SPX uitvoeren. Net als bij de opdracht voor spraak herkenning hierboven voert u de volgende opdracht uit voor het vastleggen van audio van de standaard microfoon en voert u een vertaling uit naar tekst in de doel taal.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

In deze opdracht geeft u zowel de bron (taal om **van**) op, en het doel (taal om **naar**) talen te converteren. Het gebruik van het `--microphone` argument luistert naar audio op het huidige actieve invoer apparaat en stopt nadat u hebt geklikt `ENTER` . De uitvoer is een tekst omzetting naar de doel taal, geschreven naar een tekst bestand.

> [!NOTE]
> Zie het [artikel taal en land instelling](language-support.md) voor een lijst met alle ondersteunde talen met de bijbehorende land instellingen.

## <a name="batch-operations"></a>Batch bewerkingen

De opdrachten in de vorige sectie zijn handig om snel te zien hoe de speech-service werkt. Als u echter nagaat of uw gebruik al dan niet kan worden bereikt, moet u waarschijnlijk batch bewerkingen uitvoeren op basis van een bereik met ingevoerde gegevens. zo kunt u zien hoe de service een verscheidenheid aan scenario's afhandelt. In deze sectie wordt beschreven hoe u:

* Batch spraak herkenning uitvoeren op een map met audio bestanden
* Een bestand sequentieel door lopen `.tsv` en batch tekst-naar-spraak-synthese uitvoeren

## <a name="batch-speech-recognition"></a>Spraak herkenning voor batch

Als u een map met audio bestanden hebt, is het eenvoudig met SPX om snel batch-spraak herkenning uit te voeren. Voer gewoon de volgende opdracht uit en wijs naar uw map met de `--files` opdracht. In dit voor beeld voegt u `\*.wav` toe aan de Directory om alle `.wav` bestanden in de map te herkennen. Daarnaast moet u het `--threads` argument opgeven om de herkenning uit te voeren op 10 parallelle threads.

> [!NOTE]
> Het `--threads` argument kan ook worden gebruikt in de volgende sectie voor `spx synthesize` -opdrachten en de beschik bare threads zijn afhankelijk van de CPU en het huidige belasting percentage.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

De herkende spraak uitvoer wordt geschreven naar `speech_output.tsv` met behulp van het `--output file` argument. Hier volgt een voor beeld van de structuur van het uitvoer bestand.

    audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
    sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
    sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.

## <a name="batch-text-to-speech-synthesis"></a>Batch-naar-spraak-synthese

De eenvoudigste manier om batch tekst-naar-spraak uit te voeren is het maken `.tsv` van een nieuw bestand (door tabs gescheiden waarden) en het gebruik `--foreach` van de opdracht in SPX. Houd rekening met het volgende bestand `text_synthesis.tsv` :

    audio.output    text
    C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
    C:\batch_wav_output\wav_2.wav    Using SPX to run batch-synthesis.
    C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.

 Vervolgens voert u een opdracht uit om naar te verwijzen `text_synthesis.tsv` , op elk veld synthese uit te voeren `text` en het resultaat naar het bijbehorende pad te schrijven `audio.output` als een `.wav` bestand. 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Met deze opdracht wordt het equivalent van uitvoeren `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` **voor elke** record in het `.tsv` bestand. Een paar dingen die u moet weten:

* De kolom koppen `audio.output` en `text` komen overeen met de opdracht regel argumenten `--audio output` en `--text` respectievelijk. Opdracht regel argumenten met meerdere delen `--audio output` , zoals moet worden opgemaakt in het bestand zonder spaties, geen voorloop streepjes en punten die teken reeksen scheiden, bijvoorbeeld `audio.output` . Andere bestaande opdracht regel argumenten kunnen worden toegevoegd aan het bestand als aanvullende kolommen met dit patroon.
* Wanneer het bestand op deze manier wordt geformatteerd, moeten er geen aanvullende argumenten worden door gegeven aan `--foreach` .
* Zorg ervoor dat elke waarde wordt gescheiden van `.tsv` een **tabblad**.

Als u echter een bestand hebt `.tsv` zoals in het volgende voor beeld, met kolom koppen die **niet overeenkomen** met opdracht regel argumenten:

    wav_path    str_text
    C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
    C:\batch_wav_output\wav_2.wav    Using SPX to run batch-synthesis.
    C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.

U kunt deze veld namen vervangen door de juiste argumenten met de volgende syntaxis in de `--foreach` aanroep. Dit is dezelfde aanroep als hierboven.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Volgende stappen

* Voltooi de Snelstartgids voor [spraak herkenning](./quickstarts/speech-to-text-from-microphone.md) of [spraak synthese](./quickstarts/text-to-speech.md) met de SDK.
