---
title: Gegevens voorbereiden op aangepaste spraak - Spraakservice
titleSuffix: Azure Cognitive Services
description: Maak een aangepaste stem voor uw merk met de Spraakservice. U zorgt voor studio-opnames en de bijbehorende scripts, de dienst genereert een uniek stemmodel afgestemd op de opgenomen stem. Gebruik deze stem om spraak in uw producten, hulpprogramma's en toepassingen te synthetiseren.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 5427e9f996fb77d455aa8064fc7cb1c65e1fcf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805974"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Gegevens voorbereiden om een aangepaste stem te maken

Wanneer u klaar bent om een aangepaste tekst-naar-spraakstem voor uw toepassing te maken, is de eerste stap het verzamelen van audio-opnamen en bijbehorende scripts om te beginnen met het trainen van het spraakmodel. De Spraakservice gebruikt deze gegevens om een unieke stem te maken die is afgestemd op de stem in de opnamen. Nadat u de stem hebt getraind, u beginnen met het synthetiseren van spraak in uw toepassingen.

U beginnen met een kleine hoeveelheid gegevens om een proof of concept te maken. Echter, hoe meer gegevens die u verstrekt, hoe natuurlijker uw aangepaste stem zal klinken. Voordat u uw eigen tekst-naar-spraak-spraakmodel trainen, hebt u audio-opnamen en de bijbehorende teksttranscripties nodig. Op deze pagina bekijken we gegevenstypen, hoe ze worden gebruikt en hoe ze elk worden beheren.

## <a name="data-types"></a>Gegevenstypen

Een gegevensset voor spraaktraining bevat audio-opnamen en een tekstbestand met de bijbehorende transcripties. Elk audiobestand moet één utterance bevatten (één zin of één draai voor een dialoogvenster) en minder dan 15 seconden lang zijn.

In sommige gevallen hebt u mogelijk niet de juiste gegevensset klaar en wilt u de aangepaste spraaktraining testen met beschikbare audiobestanden, kort of lang, met of zonder transcripties. We bieden tools (bèta) om u te helpen uw audio te segmenteren in uitingen en transcripties voor te bereiden met behulp van de [Batch Transcription API.](batch-transcription.md)

In deze tabel worden gegevenstypen weergegeven en hoe elk wordt gebruikt om een aangepast tekst-naar-spraakspraakmodel te maken.

| Gegevenstype | Beschrijving | Wanneer gebruikt u dit? | Extra service vereist | Hoeveelheid voor het trainen van een model | Landinplaats(en) |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Individuele uitingen + overeenkomend transcript** | Een verzameling (.zip) van audiobestanden (.wav) als afzonderlijke uitingen. Elk audiobestand moet 15 seconden of minder lang zijn, gekoppeld aan een geformatteerde transcriptie (.txt). | Professionele opnames met bijpassende transcripties | Klaar voor training. | Geen harde eis voor en-US en zh-CN. Meer dan 2.000 meer verschillende uitingen voor andere landlocaties. | [Alle aangepaste spraaklandlocaties](language-support.md#customization) |
| **Lange audio + transcript (bèta)** | Een verzameling (.zip) van lange, niet-gesegmenteerde audiobestanden (langer dan 20 seconden), gekoppeld aan een transcript (.txt) dat alle gesproken woorden bevat. | U hebt audiobestanden en overeenkomende transcripties, maar deze zijn niet gesegmenteerd in uitingen. | Segmentatie (met batchtranscriptie).<br>Transformatie van audioformaat waar nodig. | Geen harde eis  | [Alle aangepaste spraaklandlocaties](language-support.md#customization) |
| **Alleen audio (bèta)** | Een verzameling (.zip) audiobestanden zonder transcript. | U hebt alleen audiobestanden beschikbaar, zonder transcripties. | Segmentatie + transcriptie (met batchtranscriptie).<br>Transformatie van audioformaat waar nodig.| Geen harde eis | [Alle aangepaste spraaklandlocaties](language-support.md#customization) |

Bestanden moeten worden gegroepeerd op type in een gegevensset en worden geüpload als een zip-bestand. Elke gegevensset kan slechts één gegevenstype bevatten.

> [!NOTE]
> Het maximum aantal gegevenssets dat per abonnement mag worden geïmporteerd, is 10 .zip-bestanden voor gratis abonnementsgebruikers (F0) en 500 voor gebruikers met een standaardabonnement (S0).

## <a name="individual-utterances--matching-transcript"></a>Individuele uitingen + overeenkomend transcript

U opnamen van afzonderlijke uitingen en het overeenkomende transcript op twee manieren voorbereiden. Ofwel schrijf een script en hebben het lezen door een stem talent of gebruik openbaar beschikbare audio en transcriberen naar tekst. Als u dit laatste doet, bewerk de disfluencies van de audiobestanden, zoals "um" en andere vulgeluiden, stottert, mompelde woorden, of verkeerde uitspraken.

Om een goed spraaklettertype te produceren, maakt u de opnamen in een rustige ruimte met een microfoon van hoge kwaliteit. Consistent volume, spreeksnelheid, spreektoonhoogte en expressieve maniertjes van spraak zijn essentieel.

> [!TIP]
> Om een stem te maken voor productiegebruik, raden we je aan een professionele opnamestudio en stemtalent te gebruiken. Zie [Spraakvoorbeelden opnemen voor een aangepaste stem voor](record-custom-voice-samples.md)meer informatie.

### <a name="audio-files"></a>Audiobestanden

Elk audiobestand moet één utterance bevatten (één zin of één draai van een dialoogvenster), minder dan 15 seconden lang. Alle bestanden moeten in dezelfde gesproken taal zijn. Meertalige aangepaste tekst-naar-spraakstemmen worden niet ondersteund, met uitzondering van de Chinees-Engelse tweetalige. Elk audiobestand moet een unieke numerieke bestandsnaam hebben met de bestandsnaamextensie .wav.

Volg deze richtlijnen bij het voorbereiden van audio.

| Eigenschap | Waarde |
| -------- | ----- |
| Bestandsindeling | RIFF (.wav), gegroepeerd in een .zip-bestand |
| Bemonsteringsfrequentie | Minimaal 16.000 Hz |
| Voorbeeldnotatie | PCM, 16-bits |
| Bestandsnaam | Numeriek, met .wav extensie. Geen dubbele bestandsnamen toegestaan. |
| Audiolengte | Korter dan 15 seconden |
| Archiefindeling | .zip |
| Maximale archiefgrootte | 2048 MB |

> [!NOTE]
> .wav-bestanden met een bemonsteringssnelheid lager dan 16.000 Hz worden afgewezen. Als een .zip-bestand .wav-bestanden met verschillende samplerates bevat, worden alleen bestanden van 16.000 Hz geïmporteerd. Het portaal importeert momenteel .zip-archieven tot 200 MB. Er kunnen echter meerdere archieven worden geüpload.

### <a name="transcripts"></a>Transcripten

Het transcriptiebestand is een bestand met platte tekst. Gebruik deze richtlijnen om uw transcripties voor te bereiden.

| Eigenschap | Waarde |
| -------- | ----- |
| Bestandsindeling | Platte tekst (.txt) |
| Coderingsindeling | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE of UTF-16-BE. Voor zh-CN worden ANSI/ASCII- en UTF-8-coderingen niet ondersteund. |
| Aantal utterances per regel | **Eén** : Elke regel van het transcriptiebestand moet de naam van een van de audiobestanden bevatten, gevolgd door de bijbehorende transcriptie. De bestandsnaam en transcriptie moeten worden gescheiden door een tab (\t). |
| Maximale bestandsgrootte | 2048 MB |

Hieronder vindt u een voorbeeld van hoe de transcripties zijn georganiseerd uiting door uiting in een .txt bestand:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Het is belangrijk dat de transcripties zijn 100% nauwkeurige transcripties van de bijbehorende audio. Fouten in de transcripties zal leiden tot kwaliteitsverlies tijdens de training.

> [!TIP]
> Selecteer bij het bouwen van tekst-naar-spraakstemmen van productie uitingen (of schrijfscripts) die rekening houden met zowel fonetische dekking als efficiëntie. Heb je moeite om de gewenste resultaten te krijgen? [Neem contact op met het Custom Voice-team](mailto:speechsupport@microsoft.com) om meer te weten te komen over het raadplegen van ons.

## <a name="long-audio--transcript-beta"></a>Lange audio + transcript (bèta)

In sommige gevallen hebt u mogelijk geen gesegmenteerde audio beschikbaar. We bieden een service (bèta) via de aangepaste spraakportal om u te helpen lange audiobestanden te segmenteren en transcripties te maken. Houd er rekening mee dat deze service in rekening wordt gebracht in de richting van uw spraak-naar-tekst-abonnement.

> [!NOTE]
> De long-audio segmentatie service maakt gebruik van de batch transcriptie functie van spraak-naar-tekst, die alleen standaard abonnement (S0) gebruikers ondersteunt. Tijdens de verwerking van de segmentatie worden uw audiobestanden en de transcripties ook naar de service Aangepaste spraak gestuurd om het herkenningsmodel te verfijnen, zodat de nauwkeurigheid voor uw gegevens kan worden verbeterd. Tijdens dit proces worden geen gegevens bewaard. Nadat de segmentatie is gedaan, worden alleen de uitingen gesegmenteerd en hun toewijzingstranscripties opgeslagen voor het downloaden en trainen.

### <a name="audio-files"></a>Audiobestanden

Volg deze richtlijnen bij het voorbereiden van audio op segmentatie.

| Eigenschap | Waarde |
| -------- | ----- |
| Bestandsindeling | RIFF (.wav) met een sampling rate van ten minste 16 khz-16-bit in PCM of .mp3 met een bitsnelheid van ten minste 256 KBps, gegroepeerd in een .zip-bestand |
| Bestandsnaam | Ondersteunde ASCII- en Unicode-tekens. Geen dubbele namen toegestaan. |
| Audiolengte | Langer dan 20 seconden |
| Archiefindeling | .zip |
| Maximale archiefgrootte | 2048 MB |

Alle audiobestanden moeten worden gegroepeerd in een zip-bestand. Het is ok om .wav-bestanden en .mp3-bestanden in één audiozip te plaatsen. U bijvoorbeeld een zip-bestand uploaden met een audiobestand met de naam 'kingstory.wav', 45 seconden lang en een andere audio met de naam 'queenstory.mp3', 200 seconden lang. Alle .mp3-bestanden worden na verwerking omgezet in het .wav-formaat.

### <a name="transcripts"></a>Transcripten

Transcripties moeten worden voorbereid op de specificaties in deze tabel. Elk audiobestand moet worden gekoppeld aan een transcript.

| Eigenschap | Waarde |
| -------- | ----- |
| Bestandsindeling | Platte tekst (.txt), gegroepeerd in een .zip |
| Bestandsnaam | Dezelfde naam gebruiken als het overeenkomende audiobestand |
| Coderingsindeling | Alleen UTF-8-BOM |
| Aantal utterances per regel | Geen limiet |
| Maximale bestandsgrootte | 2048 MB |

Alle transcriptiebestanden in dit gegevenstype moeten worden gegroepeerd in een zip-bestand. U hebt bijvoorbeeld een zip-bestand geüpload met een audiobestand met de naam 'kingstory.wav', 45 seconden lang, en een ander bestand met de naam 'queenstory.mp3', 200 seconden lang. Je moet een ander zip-bestand uploaden met twee transcripties, een genaamd 'kingstory.txt', de andere 'queenstory.txt'. Binnen elk plain text bestand, zult u de volledige juiste transcriptie voor de overeenkomende audio.

Nadat uw gegevensset is geüpload, helpen we u het audiobestand te segmenteren in uitingen op basis van het transcript dat is verstrekt. U de gesegmenteerde uitingen en de overeenkomende transcripties controleren door de gegevensset te downloaden. Unieke id's worden automatisch toegewezen aan de gesegmenteerde uitingen. Het is belangrijk dat u ervoor zorgt dat de transcripties die u verstrekt 100% nauwkeurig zijn. Fouten in de transcripties kunnen de nauwkeurigheid tijdens de audiosegmentatie verminderen en kwaliteitsverlies verder introduceren in de trainingsfase die later komt.

## <a name="audio-only-beta"></a>Alleen audio (bèta)

Als u geen transcripties voor uw audio-opnamen hebt, gebruikt u de **optie Audio als enige** om uw gegevens te uploaden. Ons systeem kan u helpen bij het segmenteren en transcriberen van uw audiobestanden. Houd er rekening mee dat deze service meetelt voor het gebruik van uw spraak-naar-tekst-abonnement.

Volg deze richtlijnen bij het voorbereiden van audio.

> [!NOTE]
> De long-audio segmentatie service maakt gebruik van de batch transcriptie functie van spraak-naar-tekst, die alleen standaard abonnement (S0) gebruikers ondersteunt.

| Eigenschap | Waarde |
| -------- | ----- |
| Bestandsindeling | RIFF (.wav) met een sampling rate van ten minste 16 khz-16-bit in PCM of .mp3 met een bitsnelheid van ten minste 256 KBps, gegroepeerd in een .zip-bestand |
| Bestandsnaam | Ondersteunde ASCII- en Unicode-tekens. Geen dubbele naam toegestaan. |
| Audiolengte | Langer dan 20 seconden |
| Archiefindeling | .zip |
| Maximale archiefgrootte | 2048 MB |

Alle audiobestanden moeten worden gegroepeerd in een zip-bestand. Zodra uw gegevensset is geüpload, helpen we u het audiobestand te segmenteren in uitingen op basis van onze spraakbatchtranscriptieservice. Unieke id's worden automatisch toegewezen aan de gesegmenteerde uitingen. Overeenkomende transcripties worden gegenereerd door middel van spraakherkenning. Alle .mp3-bestanden worden na verwerking omgezet in het .wav-formaat. U de gesegmenteerde uitingen en de overeenkomende transcripties controleren door de gegevensset te downloaden.

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste stem maken](how-to-custom-voice-create-voice.md)
- [Gids: Neem uw stemvoorbeelden op](record-custom-voice-samples.md)
