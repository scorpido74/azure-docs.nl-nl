---
title: Gegevens voorbereiden op aangepaste spraak - Spraakservice
titleSuffix: Azure Cognitive Services
description: Wanneer u de nauwkeurigheid van microsoft-spraakherkenning en training van uw aangepaste modellen test, hebt u audio- en tekstgegevens nodig. Op deze pagina behandelen we de soorten gegevens, hoe ze moeten worden gebruikt en beheren we ze.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: d6e377ca4f33c0160267b23daaaaef5e07c271bb
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398687"
---
# <a name="prepare-data-for-custom-speech"></a>Gegevens voorbereiden op aangepaste spraak

Wanneer u de nauwkeurigheid van microsoft-spraakherkenning en training van uw aangepaste modellen test, hebt u audio- en tekstgegevens nodig. Op deze pagina behandelen we de soorten gegevens, hoe ze moeten worden gebruikt en beheren we ze.

## <a name="data-types"></a>Gegevenstypen

In deze tabel worden geaccepteerde gegevenstypen weergegeven, wanneer elk gegevenstype moet worden gebruikt en de aanbevolen hoeveelheid. Niet elk gegevenstype is vereist om een model te maken. De gegevensvereisten zijn afhankelijk van of u een test maakt of een model traint.

| Gegevenstype | Gebruikt voor het testen | Aanbevolen hoeveelheid | Gebruikt voor training | Aanbevolen hoeveelheid |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Ja<br>Gebruikt voor visuele inspectie | 5+ audiobestanden | Nee | N/a |
| [Audio + Human-label transcripties](#audio--human-labeled-transcript-data-for-testingtraining) | Ja<br>Wordt gebruikt om de nauwkeurigheid te evalueren | 0,5-5 uur audio | Ja | 1-1.000 uur audio |
| [Verwante tekst](#related-text-data-for-training) | Nee | N/a | Ja | 1-200 MB gerelateerde tekst |

Bestanden moeten worden gegroepeerd op type in een gegevensset en worden geüpload als een .zip-bestand. Elke gegevensset kan slechts één gegevenstype bevatten.

> [!TIP]
> Om snel aan de slag te gaan, u overwegen voorbeeldgegevens te gebruiken. Bekijk deze GitHub-opslagplaats voor <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">voorbeeldgegevens van aangepaste spraak <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

## <a name="upload-data"></a>Gegevens uploaden

Als u uw gegevens wilt uploaden, navigeert u naar de <a href="https://speech.microsoft.com/customspeech" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"> </span>Aangepaste spraak. </a> Klik in de portal op **Gegevens uploaden** om de wizard te starten en de eerste gegevensset te maken. U wordt gevraagd een spraakgegevenstype voor uw gegevensset te selecteren, voordat u uw gegevens uploaden.

![Audio selecteren in de spraakportal](./media/custom-speech/custom-speech-select-audio.png)

Elke gegevensset die u uploadt, moet voldoen aan de vereisten voor het gegevenstype dat u kiest. Uw gegevens moeten correct worden opgemaakt voordat ze worden geüpload. Correct opgemaakte gegevens zorgen ervoor dat deze nauwkeurig worden verwerkt door de service Aangepaste spraak. Vereisten worden vermeld in de volgende secties.

Nadat je gegevensset is geüpload, heb je een aantal opties:

* U naar het tabblad **Testen** navigeren en alleen audio of audio + transcriptiegegevens met menselijk label bekijken.
* U naar het tabblad **Training** navigeren en audio + menselijke transcriptiegegevens of gerelateerde tekstgegevens gebruiken om een aangepast model te trainen.

## <a name="audio-data-for-testing"></a>Audiogegevens voor het testen

Audiogegevens zijn optimaal voor het testen van de nauwkeurigheid van microsoft's spraak-naar-tekstmodel of een aangepast model. Houd er rekening mee dat audiogegevens worden gebruikt om de nauwkeurigheid van spraak te inspecteren met betrekking tot de prestaties van een specifiek model. Als u de nauwkeurigheid van een model wilt kwantificeren, gebruikt u [audio + transcriptiegegevens met een mens.](#audio--human-labeled-transcript-data-for-testingtraining)

Gebruik deze tabel om ervoor te zorgen dat uw audiobestanden correct zijn opgemaakt voor gebruik met aangepaste spraak:

| Eigenschap                 | Waarde                 |
|--------------------------|-----------------------|
| Bestandsindeling              | RIFF (WAV)            |
| Voorbeeldsnelheid              | 8.000 Hz of 16.000 Hz |
| Kanalen                 | 1 (mono)              |
| Maximale lengte per audio | 2 uur               |
| Voorbeeldnotatie            | PCM, 16-bits           |
| Archiefindeling           | .zip                  |
| Maximale archiefgrootte     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> Bij het uploaden van trainings- en testgegevens mag de .zip-bestandsgrootte niet groter zijn dan 2 GB. Als u meer gegevens nodig hebt voor training, deelt u deze op in verschillende .zip-bestanden en uploadt u deze afzonderlijk. Later u ervoor kiezen om te trainen vanuit *meerdere* gegevenssets. U echter alleen testen vanuit *één* gegevensset.

Gebruik <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX <span class="docon docon-navigate-external x-hidden-focus"></span> </a> om audio-eigenschappen te verifiëren of bestaande audio om te zetten naar de juiste indelingen. Hieronder vindt u enkele voorbeelden van hoe elk van deze activiteiten kan worden gedaan via de SoX-opdrachtregel:

| Activiteit | Beschrijving | SoX, opdracht |
|----------|-------------|-------------|
| Audio-indeling controleren | Deze opdracht gebruiken om<br>de audiobestandsindeling. | `sox --i <filename>` |
| Audio-indeling converteren | Deze opdracht gebruiken om te converteren<br>het audiobestand naar single channel, 16-bits, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Audio + door mensen gelabelde transcriptiegegevens voor het testen/trainen

Om de nauwkeurigheid van de spraak-naar-tekstnauwkeurigheid van Microsoft bij het verwerken van uw audiobestanden te meten, moet u transcripties met menselijk label (van woord voor woord) ter vergelijking opgeven. Hoewel transcriptie met menselijk label vaak tijdrovend is, is het noodzakelijk om de nauwkeurigheid te evalueren en het model voor uw use cases te trainen. Houd er rekening mee dat de verbeteringen in de herkenning alleen zo goed zijn als de verstrekte gegevens. Daarom is het belangrijk dat alleen transcripties van hoge kwaliteit worden geüpload.

| Eigenschap                 | Waarde                               |
|--------------------------|-------------------------------------|
| Bestandsindeling              | RIFF (WAV)                          |
| Voorbeeldsnelheid              | 8.000 Hz of 16.000 Hz               |
| Kanalen                 | 1 (mono)                            |
| Maximale lengte per audio | 2 uur (testen) / 60 s (training) |
| Voorbeeldnotatie            | PCM, 16-bits                         |
| Archiefindeling           | .zip                                |
| Maximale ritsgrootte         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Bij het uploaden van trainings- en testgegevens mag de .zip-bestandsgrootte niet groter zijn dan 2 GB. U alleen testen vanuit *één* gegevensset, zorg ervoor dat u deze binnen de juiste bestandsgrootte houdt. Bovendien mag elk trainingsbestand niet langer zijn dan 60 seconden, anders wordt het fout gemaakt.

Om problemen zoals het verwijderen of vervangen van woorden aan te pakken, is een aanzienlijke hoeveelheid gegevens nodig om de herkenning te verbeteren. Over het algemeen wordt aanbevolen om transcripties van woord tot woord te bieden voor ongeveer 10 tot 1000 uur audio. De transcripties voor alle WAV-bestanden moeten worden opgenomen in één bestand met tekst zonder opmaak. Elke regel van het transcriptiebestand moet de naam van een van de audiobestanden bevatten, gevolgd door de bijbehorende transcriptie. De bestandsnaam en transcriptie moeten worden gescheiden door een tab (\t).

  Bijvoorbeeld:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> Transcriptie moet worden gecodeerd als UTF-8 BOM (Byte Order Mark).

De tekst van de transcripties wordt genormaliseerd zodat ze door het systeem kunnen worden verwerkt. Er zijn echter enkele belangrijke normalisaties die moeten worden gedaan voordat de gegevens naar de Speech Studio worden geüpload. Zie Een transcriptie met het menselijk label [maken](how-to-custom-speech-human-labeled-transcriptions.md) voor de juiste taal wanneer u uw transcripties voorbereidt

Nadat u uw audiobestanden en bijbehorende transcripties hebt verzameld, u ze verpakken als één .zip-bestand voordat u deze uploadt naar de <a href="https://speech.microsoft.com/customspeech" target="_blank">portal Voor aangepaste spraak. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a> Hieronder vindt u een voorbeeldgegevensset met drie audiobestanden en een transcriptiebestand met menselijk label:

> [!div class="mx-imgBorder"]
> ![Audio selecteren in de spraakportal](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Gerelateerde tekstgegevens voor training

Productnamen of functies die uniek zijn, moeten gerelateerde tekstgegevens bevatten voor training. Gerelateerde tekst zorgt voor een correcte herkenning. Er kunnen twee soorten gerelateerde tekstgegevens worden verstrekt om de herkenning te verbeteren:

| Gegevenstype | Hoe deze gegevens de herkenning verbeteren |
|-----------|------------------------------------|
| Zinnen (uitingen) | Verbeter de nauwkeurigheid bij het herkennen van productnamen of branchespecifieke woordenschat in de context van een zin. |
| Uitspraak | Verbeter de uitspraak van ongewone termen, afkortingen of andere woorden met ongedefinieerde uitspraken. |

Zinnen kunnen worden verstrekt als één tekstbestand of meerdere tekstbestanden. Gebruik tekstgegevens die dichter bij de verwachte gesproken uitingen staan om de nauwkeurigheid te verbeteren. Uitspraken moeten worden verstrekt als één tekstbestand. Alles kan worden verpakt als een zip-bestand en geüpload naar de <a href="https://speech.microsoft.com/customspeech" target="_blank">Aangepaste Spraak portal <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

### <a name="guidelines-to-create-a-sentences-file"></a>Richtlijnen voor het maken van een zinnenbestand

Als u een aangepast model wilt maken met zinnen, moet u een lijst met voorbeelduitingen verstrekken. Uitingen _hoeven niet_ volledig of grammaticaal correct te zijn, maar ze moeten nauwkeurig de gesproken invoer weergeven die u in de productie verwacht. Als u wilt dat bepaalde termen een verhoogd gewicht hebben, voegt u verschillende zinnen toe die deze specifieke termen bevatten.

Als algemene leidraad is modelaanpassing het meest effectief wanneer de opleidingstekst zo dicht mogelijk bij de werkelijke tekst ligt die in de productie wordt verwacht. Domeinspecifiek jargon en zinnen die u target om te verbeteren, moeten worden opgenomen in trainingstekst. Probeer indien mogelijk één zin of trefwoord op een aparte regel te laten controleren. Voor zoekwoorden en woordgroepen die belangrijk voor u zijn (bijvoorbeeld productnamen), u ze een paar keer kopiëren. Maar houd in gedachten, niet te veel kopiëren - het kan invloed hebben op de algehele erkenning tarief.

Gebruik deze tabel om ervoor te zorgen dat uw gerelateerde gegevensbestand voor uitingen correct is opgemaakt:

| Eigenschap | Waarde |
|----------|-------|
| Tekstcodering | UTF-8 BOM |
| Aantal utterances per regel | 1 |
| Maximale bestandsgrootte | 200 MB |

Daarnaast wilt u rekening houden met de volgende beperkingen:

* Vermijd het herhalen van tekens meer dan vier keer. Bijvoorbeeld: "aaaa" of "uuuu".
* Gebruik geen speciale tekens of UTF-8-tekens hierboven. `U+00A1`
* URI's worden afgewezen.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Richtlijnen voor het maken van een uitspraakbestand

Als er ongebruikelijke termen zijn zonder standaarduitspraken die uw gebruikers zullen tegenkomen of gebruiken, u een aangepast uitspraakbestand bieden om de herkenning te verbeteren.

> [!IMPORTANT]
> Het wordt afgeraden om aangepaste uitspraakbestanden te gebruiken om de uitspraak van veelvoorkomende woorden te wijzigen.

Dit omvat voorbeelden van een gesproken utterance en een aangepaste uitspraak voor elk:

| Herkend/weergegeven formulier | Gesproken formulier |
|--------------|--------------------------|
| 3CPO | drie c p o |
| CNTK | c n t k |
| Ieee | i triple e |

De gesproken vorm is de fonetische volgorde beschreven. Het kan worden samengesteld uit letter, woorden, lettergrepen, of een combinatie van alle drie.

Aangepaste uitspraak is beschikbaar`en-US`in het`de-DE`Engels ( ) en Duits ( ). In deze tabel worden ondersteunde tekens per taal weergegeven:

| Taal | Landinstelling | Tekens |
|----------|--------|------------|
| Engels | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Duits | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Gebruik de volgende tabel om ervoor te zorgen dat uw gerelateerde gegevensbestand voor uitspraken correct is opgemaakt. Uitspraak bestanden zijn klein, en moet slechts een paar kilobytes in grootte.

| Eigenschap | Waarde |
|----------|-------|
| Tekstcodering | UTF-8 BOM (ANSI wordt ook ondersteund voor Engels) |
| # van de uitspraken per regel | 1 |
| Maximale bestandsgrootte | 1 MB (1 KB gratis laag) |

## <a name="next-steps"></a>Volgende stappen

* [Uw gegevens inspecteren](how-to-custom-speech-inspect-data.md)
* [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)
* [Uw model trainen](how-to-custom-speech-train-model.md)
* [Uw model implementeren](how-to-custom-speech-deploy-model.md)
