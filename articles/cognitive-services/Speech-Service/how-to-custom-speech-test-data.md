---
title: Test gegevens voorbereiden voor de Custom Speech-Speech-Service
titleSuffix: Azure Cognitive Services
description: Of u nu test om te zien hoe nauw keurige spraak herkenning van micro soft is of uw eigen modellen trainen, u hebt gegevens nodig (in de vorm van audio en/of tekst). Op deze pagina hebben we betrekking op de typen gegevens, hoe ze worden gebruikt en hoe ze kunnen worden beheerd.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: b18e1b755b4e1339bf00380d8228fc28e355d3e1
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802511"
---
# <a name="prepare-data-for-custom-speech"></a>Gegevens voorbereiden voor Custom Speech

Of u nu test om te zien hoe nauw keurige spraak herkenning van micro soft is of uw eigen modellen trainen, u hebt gegevens nodig in de vorm van audio en tekst. Op deze pagina hebben we betrekking op de typen gegevens, hoe ze worden gebruikt en hoe ze kunnen worden beheerd.

## <a name="data-types"></a>Gegevenstypen

In deze tabel worden de geaccepteerde gegevens typen vermeld, wanneer elk gegevens type moet worden gebruikt en de aanbevolen hoeveelheid. Niet elk gegevens type is vereist voor het maken van een model. De gegevens vereisten variëren, afhankelijk van het feit of u een model maakt of een training uitvoert.

| Gegevenstype | Testen gebruiken | Hoeveelheid | Gebruikt voor training | Hoeveelheid |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Ja<br>Gebruikt voor visuele inspectie | 5 + audio bestanden | Nee | N.v.t. |
| [Audio en Transcripten met menselijke labels](#audio--human-labeled-transcript-data-for-testingtraining) | Ja<br>Wordt gebruikt om de nauw keurigheid te evalueren | 0,5-5 uur audio | Ja | 1-1.000 uur audio |
| [Gerelateerde tekst](#related-text-data-for-training) | Nee | N.v.t. | Ja | 1-200 MB aan Verwante tekst |

Bestanden moeten worden gegroepeerd door te typen in een gegevensset en worden geüpload als een zip-bestand. Elke gegevensset kan slechts één gegevens type bevatten.

## <a name="upload-data"></a>Gegevens uploaden

Wanneer u klaar bent om uw gegevens te uploaden, gaat u naar de [Custom speech Portal](https://speech.microsoft.com/customspeech)en klikt u vervolgens op **gegevens uploaden** om de wizard te starten en uw eerste gegevensset te maken. U wordt gevraagd een type spraak gegevens voor uw gegevensset te selecteren voordat u uw gegevens kunt uploaden.

![Audio selecteren in de spraak Portal](./media/custom-speech/custom-speech-select-audio.png)

Elke gegevensset die u uploadt, moet voldoen aan de vereisten voor het gegevens type dat u kiest. Het is belang rijk dat u uw gegevens op de juiste wijze opmaakt voordat deze worden geüpload. Dit zorgt ervoor dat de gegevens correct worden verwerkt door de Custom Speech-Service. De vereisten worden weer gegeven in de volgende secties.

Nadat uw gegevensset is geüpload, hebt u een aantal opties:

* U kunt naar het tabblad **testen** navigeren en alleen audio visueel controleren of audio + Transcriptie-gegevens met menselijke labels.
* U kunt naar het tabblad **training** gaan en audio en gegevens van menselijk transcriptie of gerelateerde tekst gegevens gebruiken om een aangepast model te trainen.

## <a name="audio-data-for-testing"></a>Audio gegevens voor testen

Audio gegevens zijn optimaal voor het testen van de nauw keurigheid van het spraak-naar-tekst model van micro soft of een aangepast model. Houd er rekening mee dat audio gegevens worden gebruikt voor het inspecteren van de nauw keurigheid van spraak met betrekking tot de prestaties van een specifiek model. Als u wilt weten wat de nauw keurigheid van een model is, gebruik dan [audio + met Human labels transcriptie-gegevens](#audio--human-labeled-transcript-data-for-testingtraining).

Gebruik deze tabel om ervoor te zorgen dat uw audio bestanden correct zijn ingedeeld voor gebruik met Custom Speech:

| Eigenschap | Value |
|----------|-------|
| Bestands indeling | RIFF (WAV) |
| Steekproeffrequentie | 8\.000 Hz of 16.000 Hz |
| Kanalen | 1 (mono) |
| Maximum lengte per audio | 2 uur |
| Voorbeeld indeling | PCM, 16-bits |
| Archief indeling | .zip |
| Maximale archief grootte | 2 GB |

Als uw audio niet voldoet aan deze eigenschappen of als u wilt controleren of dit het geval is, wordt u aangeraden [Sox](http://sox.sourceforge.net) te downloaden om de audio te controleren of te converteren. Hieronder ziet u enkele voor beelden van de manier waarop elk van deze activiteiten kan worden uitgevoerd via de opdracht regel:

| Activiteit | Description | Sox-opdracht |
|----------|-------------|-------------|
| Audio-indeling controleren | Gebruik deze opdracht om de indeling van het audio bestand te controleren. | `sox --i <filename>` |
| Audio-indeling converteren | Gebruik deze opdracht om het audio bestand te converteren naar één kanaal, 16-bits, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Audio en transcriptie gegevens met menselijke labels voor testen/training

Als u de nauw keurigheid van de spraak-naar-tekst nauwkeurigheid van micro soft tijdens het verwerken van uw audio bestanden wilt meten, moet u transcripties (woord voor woord) van de mens voorzien voor vergelijking. Hoewel menselijke labels transcriptie vaak tijdrovend zijn, is het nood zakelijk om nauw keurigheid te evalueren en het model te trainen voor uw gebruiks voorbeelden. Houd er rekening mee dat de verbeteringen in de herkenning alleen van belang zijn voor de gegevens. Daarom is het belang rijk dat alleen transcripten van hoogwaardige kwaliteit worden geüpload.  

| Eigenschap | Value |
|----------|-------|
| Bestands indeling | RIFF (WAV) |
| Steekproeffrequentie | 8\.000 Hz of 16.000 Hz |
| Kanalen | 1 (mono) |
| Maximum lengte per audio | 60 s |
| Voorbeeld indeling | PCM, 16-bits |
| Archief indeling | .zip |
| Maximale grootte van zip | 2 GB |

Voor het oplossen van problemen zoals het verwijderen of vervangen van woorden, is een aanzienlijke hoeveelheid gegevens vereist om de herkenning te verbeteren. Over het algemeen is het raadzaam om per woord transcripties te bieden voor ongeveer 10 tot 1.000 uur aan audio. De transcripties voor alle WAV-bestanden moeten worden opgenomen in één bestand met tekst zonder opmaak. Elke regel van het transcriptiebestand moet de naam van een van de audiobestanden bevatten, gevolgd door de bijbehorende transcriptie. De bestandsnaam en transcriptie moeten worden gescheiden door een tab (\t).

  Bijvoorbeeld:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Transcriptie moet worden gecodeerd als UTF-8 BOM (Byte Order Mark).

De tekst van de transcripties wordt genormaliseerd zodat ze door het systeem kunnen worden verwerkt. Er zijn echter enkele belangrijke normalisaties die door de gebruiker moeten worden uitgevoerd _voordat_ de gegevens worden geüpload naar de Custom Speech Service. Voor de juiste taal die moet worden gebruikt wanneer u uw transcripties voorbereidt, Zie [How to Create a Human-gelabeld transcriptie](how-to-custom-speech-human-labeled-transcriptions.md)

Nadat u de audio bestanden en de bijbehorende transcripties hebt verzameld, moeten ze worden ingepakt als één ZIP-bestand voordat ze naar de [Custom speech-Portal](https://speech.microsoft.com/customspeech)kunnen worden geüpload. Dit is een voor beeld van een gegevensset met drie audio bestanden en een transcriptie-bestand met menselijke Labels:

![Audio selecteren in de spraak Portal](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Gerelateerde tekst gegevens voor training

Als u product namen of-onderdelen hebt die uniek zijn, en u ervoor wilt zorgen dat ze correct worden herkend, is het belang rijk om gerelateerde tekst gegevens op te nemen voor training. Er kunnen twee typen gerelateerde tekst gegevens worden gegeven om de herkenning te verbeteren:

| Gegevenstype | Hoe deze gegevens de herkenning verbeteren |
|-----------|------------------------------------|
| Uitingen en/of zinnen | Deze kunnen nauw keuriger worden bij het herkennen van product namen of branchespecifieke vocabulaire in de context van een zin. |
| Uitspraak | Dit kan de uitspraak van ongebruikelijke termen, acroniemen of andere woorden met niet-gedefinieerde uitspraak verbeteren. |

Uitingen kan worden gegeven als één of meer tekst bestanden. Hoe dichter de tekst gegevens zijn voor wat wordt gesp roken, des te groter is de kans dat de nauw keurigheid is verbeterd. Uitspraak moet worden gegeven als één tekst bestand. Alles kan worden verpakt als één ZIP-bestand en worden geüpload naar de [Custom speech Portal](https://speech.microsoft.com/customspeech).

### <a name="guidelines-to-create-an-utterances-file"></a>Richt lijnen voor het maken van een uitingen-bestand

Als u een aangepast model met behulp van gerelateerde tekst wilt maken, moet u een lijst met voorbeeld uitingen opgeven. Deze uitingen hoeven niet te worden ingevuld of moeten grammaticaal correct zijn, maar ze moeten nauw keurig overeenkomen met de gesp roken invoer die u verwacht tijdens de productie. Als u wilt dat bepaalde voor waarden meer gewicht hebben, kunt u verschillende zinnen toevoegen aan het gerelateerde gegevens bestand dat deze specifieke voor waarden bevat.

Gebruik deze tabel om ervoor te zorgen dat het gerelateerde gegevens bestand voor uitingen correct is ingedeeld:

| Eigenschap | Value |
|----------|-------|
| Tekstcodering | UTF-8 BOM |
| Aantal utterances per regel | 1 |
| Maximale bestandsgrootte | 200 MB |

Daarnaast wilt u rekening met de volgende beperkingen:

* Vermijd meer dan vier keer herhaalde tekens. Bijvoorbeeld: ' AAAA ' of ' UUUU '.
* Gebruik geen speciale tekens of UTF-8 tekens boven U en 00A1.
* Uri's worden geweigerd.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Richt lijnen voor het maken van een uitspraak bestand

Als er ongebruikelijke voor waarden zijn zonder standaard uitspraak dat uw gebruikers zich kunnen voordoen of gebruiken, kunt u een aangepast uitspraak bestand opgeven om de herkenning te verbeteren.

> [!IMPORTANT]
> Het is niet raadzaam om deze functie te gebruiken om de uitspraak van veelvoorkomende woorden te wijzigen.

Dit omvat voor beelden van een gesp roken utterance en een aangepaste uitspraak voor elk:

| Herkend/weer gegeven formulier | Gesproken formulier |
|--------------|--------------------------|
| 3CPO | drie c p |  
| CNTK | c n t k |
| IEEE | drievoudige e |

Het gesp roken formulier is de fonetische volg orde die is gespeld. Het kan bestaan uit letter, woorden, letter grepen of een combi natie van alle drie.

Aangepaste uitspraak is beschikbaar in het Engels (nl-nl) en Duits (de-DE). In deze tabel worden de ondersteunde tekens per taal weer gegeven:

| Taal | Landinstelling | Tekens |
|----------|--------|------------|
| Engels | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Duits | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Gebruik deze tabel om ervoor te zorgen dat het gerelateerde gegevens bestand voor uitspraak juist is ingedeeld. De uitspraak bestanden zijn klein en mogen niet meer dan een paar Kb's overschrijden.

| Eigenschap | Value |
|----------|-------|
| Tekstcodering | UTF-8-stuk lijst (ANSI wordt ook ondersteund voor Engels) |
| aantal uitspraakingen per regel | 1 |
| Maximale bestandsgrootte | 1 MB (1 KB voor gratis laag) |

## <a name="next-steps"></a>Volgende stappen

* [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)
* [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)
* [Uw model trainen](how-to-custom-speech-train-model.md)
* [Uw model implementeren](how-to-custom-speech-deploy-model.md)
