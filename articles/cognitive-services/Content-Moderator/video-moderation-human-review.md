---
title: Videomoderatie met menselijke beoordeling - Content Moderator
titleSuffix: Azure Cognitive Services
description: Gebruik machineondersteunde videomoderatie en tools voor menselijke beoordeling om ongepaste inhoud te matigen
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: pafarley
ms.openlocfilehash: a4e7b079367a4b4dec1d2b3d6c0afde1d8276766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754157"
---
# <a name="video-moderation-with-human-review"></a>Videomoderatie met menselijke beoordeling

Gebruik de machine-ondersteunde [videomoderatie](video-moderation-api.md) en [human review](Review-Tool-User-Guide/human-in-the-loop.md) tool van Content Moderator om video's en transcripties te modereren voor inhoud voor volwassenen (expliciete) en pikante (suggestieve) inhoud om de beste resultaten voor je bedrijf te krijgen.

## <a name="video-trained-classifier-preview"></a>Door video getrainde classifier (preview)

Machine-ondersteunde video classificatie wordt bereikt met beeld getrainde modellen of video getrainde modellen. In tegenstelling tot videoclassificaties die door afbeeldingen zijn opgeleid, wordt de classificatie voor volwassenen en racy video's van Microsoft getraind met video's. Deze methode resulteert in een betere match kwaliteit.

## <a name="shot-detection"></a>Shotdetectie

Bij het uitdelen van de classificatiedetails helpt extra video-intelligentie bij het analyseren van video's. In plaats van alleen de frames te outputting, microsoft's video moderatie service biedt shot-level informatie ook. Je hebt nu de mogelijkheid om je video's te analyseren op het opnameniveau en het frameniveau.

## <a name="key-frame-detection"></a>Detectie van sleutelframes

In plaats van frames op regelmatige tijdstippen uit te voeren, identificeert en produceert de videomoderatieservice alleen potentieel complete (goede) frames. De functie maakt een efficiënte framegeneratie mogelijk voor analyse op frameniveau voor volwassenen en racy.

Het volgende extract toont een gedeeltelijke reactie met potentiële opnamen, hoofdframes en volwassen en pikante scores:

```json
"fragments":[  
  {  
    "start":0,
    "duration":18000
  },
  {  
    "start":18000,
    "duration":3600,
    "interval":3600,
    "events":[  
      [  
        {  
          "reviewRecommended":false,
          "adultScore":0.00001,
          "racyScore":0.03077,
          "index":5,
          "timestamp":18000,
          "shotIndex":0
        }
      ]
    ]
  },
  {  
    "start":18386372,
    "duration":119149,
    "interval":119149,
    "events":[  
      [  
        {  
          "reviewRecommended":true,
          "adultScore":0.00000,
          "racyScore":0.91902,
          "index":5085,
          "timestamp":18386372,
          "shotIndex":62
        }
      ]
    ]
```

## <a name="visualization-for-human-reviews"></a>Visualisatie voor menselijke beoordelingen

Voor meer genuanceerde gevallen hebben bedrijven een oplossing voor menselijke beoordeling nodig voor het renderen van de video, de frames en de door machines toegewezen tags. De menselijke moderators die video's en frames bekijken, krijgen een compleet overzicht van de inzichten, wijzigen tags en dienen hun beslissingen in.

![Standaardweergave voor videocontroletool](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Spelersweergave voor beoordeling op videoniveau

Binaire beslissingen op videoniveau worden mogelijk gemaakt met een videospelerweergave die potentiële volwassen en pikante frames laat zien. De menselijke reviewers navigeren door de video met verschillende snelheidsopties om de scènes te onderzoeken. Ze bevestigen hun beslissingen door de tags te omdraaien.

![video review tool player view video review tool player view video review tool player view video review](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Frames weergave voor gedetailleerde beoordelingen

Een gedetailleerde videoreview voor frame-by-frame analyse wordt mogelijk gemaakt met een frame-based view. De menselijke beoordelaars beoordelen en selecteren een of meer frames en schakeltags om hun beslissingen te bevestigen. Een optionele volgende stap is redactie van de aanstootgevende frames of inhoud.

![weergave videobeoordelingsprogramma's](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Transcriptie beoordelen

Video's hebben meestal voice-over die ook matiging nodig heeft voor aanstootgevende spraak. U gebruikt de Azure Media Indexer-service om spraak om te zetten in tekst en de controle-API van inhoudsmoderator te gebruiken om het transcript in te dienen voor tekstmoderatie in het beoordelingsprogramma.

![transcriptweergave van video-controleprogramma's](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Volgende stappen

- Ga snel aan de slag met de [videomoderatie.](video-moderation-api.md)
- Meer informatie over het genereren van [videorecensies](video-reviews-quickstart-dotnet.md) voor uw menselijke beoordelaars op maat van uw gemodereerde uitvoer.
- Voeg [videotranscriptiebeoordelingen](video-transcript-reviews-quickstart-dotnet.md) toe aan je videorecensies.
- Bekijk de gedetailleerde tutorial over het ontwikkelen van een [complete video moderatie oplossing.](video-transcript-moderation-review-tutorial-dotnet.md)