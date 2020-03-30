---
title: Meertalige inhoud automatisch identificeren en transcriberen met Video Indexer
titleSuffix: Azure Media Services
description: In dit onderwerp wordt uitgelegd hoe u meertalige inhoud automatisch identificeren en transcriberen met Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: f0dede42891069bb5d01ddc33f3797c20c5493d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72968744"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>Meertalige inhoud automatisch identificeren en transcriberen (voorbeeld)

Video Indexer ondersteunt automatische taalidentificatie en transcriptie in meertalige inhoud. Dit proces omvat het automatisch identificeren van de gesproken taal in verschillende segmenten van audio, het verzenden van elk segment van het mediabestand te transcriberen en combineren van de transcriptie terug naar een uniforme transcriptie. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Kiezen voor meertalige identificatie op indexering met portal

U **taaldetectie** kiezen bij het uploaden en indexeren van uw video. U ook **kiezen** voor detectie in meerdere talen wanneer u uw video opnieuw indexeert. In de volgende stappen wordt beschreven hoe u opnieuw indexeren:

1. Ga naar de [Video Indexer](https://vi.microsoft.com/)-website en meld u aan.
1. Ga naar de **pagina Bibliotheek** en plaats de gewenste video die u opnieuw wilt indexeren. 
1. Klik in de rechterbenedenhoek op de **videoknop opnieuw indexeren.** 
1. Kies in het dialoogvenster **Video opnieuw indexeren** de optie **voor meertalige detectie** in het vervolgkeuzevak **Videobrontaal.**

    * Wanneer een video als meertalig wordt geïndexeerd, bevat de inzichtspagina die optie en verschijnt er een extra inzichtstype, zodat de gebruiker kan zien welk segment is getranscribeerd in welke taal "Gesproken taal".
    * Vertaling naar alle talen is volledig beschikbaar via het meertalige transcript.
    * Alle andere inzichten worden weergegeven in de gedetecteerde hoofdtaal - dat is de taal die het meest in de audio verscheen.
    * Ondertiteling op de speler is ook beschikbaar in meertalig.

![Portalervaring](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Kiezen voor meertalige identificatie bij indexering met API

Wanneer u een video indexeert of [opnieuw indexeert](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) met de API, kiest u de `multi-language detection` optie in de `sourceLanguage` parameter.

### <a name="model-output"></a>Uitvoer van model

Het model haalt alle talen op die in de video in één lijst worden gedetecteerd

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Bovendien bevat elk exemplaar in de transcriptiesectie de taal waarin het is getranscribeerd

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Richtlijnen en beperkingen

* Reeks ondersteunde talen: Engels, Frans, Duits, Spaans.
* Ondersteuning voor meertalige inhoud met maximaal drie ondersteunde talen.
* Als de audio andere talen bevat dan de bovenstaande ondersteunde lijst, is het resultaat onverwacht.
* Minimale segmentlengte om voor elke taal te detecteren – 15 seconden.
* Taaldetectie offset is 3 seconden gemiddeld.
* Spraak zal naar verwachting continu zijn. Frequente wisselingen tussen talen kunnen van invloed zijn op de prestaties van de modellen.
* Spraak van anderstaligen kan van invloed zijn op de prestaties van het model (bijvoorbeeld wanneer sprekers hun moedertaal gebruiken en ze overschakelen naar een andere taal).
* Het model is ontworpen om een spontane gesprekstoespraak te herkennen met een redelijke audioakoestiek (geen spraakopdrachten, zang, enz.).
* Het maken en bewerken van projecten is momenteel niet beschikbaar voor video's in meerdere talen.
* Aangepaste taalmodellen zijn niet beschikbaar bij het gebruik van detectie in meerdere talen.
* Het toevoegen van zoekwoorden wordt niet ondersteund.
* Bij het exporteren van bestanden met ondertiteling wordt de taalaanduiding niet weergegeven.
* De updatetranscript-API biedt geen ondersteuning voor bestanden in meerdere talen.

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Video Indexer](video-indexer-overview.md)
