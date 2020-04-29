---
title: Video toezicht met menselijke beoordeling-Content Moderator
titleSuffix: Azure Cognitive Services
description: Gebruik machine-ondersteunde video toezicht en het beoordelings programma voor gepaste inhoud
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 0c031a890efc7fad7e5d9caefce3b0e66c515d90
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81404239"
---
# <a name="video-moderation-with-human-review"></a>Video toezicht met menselijke beoordeling

Content Moderator gebruik de functie voor door de machine ondersteunde video-en [beoordelings hulpmiddelen](Review-Tool-User-Guide/human-in-the-loop.md) voor [gelaagde](video-moderation-api.md) Video's en geautomatiseerde transcripten voor volwassene (expliciete) en ongepaste (suggestief) om de beste resultaten voor uw bedrijf te krijgen.

## <a name="video-trained-classifier-preview"></a>Video-getrainde classificatie (preview-versie)

De door de machine ondersteunde video classificatie is behaald met afbeeldingen getrainde modellen of modellen die zijn getraind. In tegens telling tot video classificaties die zijn getraind, wordt de video classificatie voor volwassenen en ongepaste van micro soft getraind met Video's. Deze methode resulteert in een beter resultaat van kwaliteit.

## <a name="shot-detection"></a>Opname detectie

Bij het uitvoeren van de classificatie Details helpt aanvullende video-informatie over meer flexibiliteit bij het analyseren van Video's. In plaats van alleen de frames uit te voeren, biedt de video moderator service van micro soft ook informatie over het niveau van de opname. U hebt nu de mogelijkheid om uw Video's op het niveau van de afbeelding en het frame niveau te analyseren.

## <a name="key-frame-detection"></a>Belangrijkste frame detectie

In plaats van frames met regel matige tussen pozen te laten uitvoeren, identificeert de video moderator-service alleen mogelijke voltooide (goede) frames. Met deze functie kunt u efficiënte frames genereren voor de analyse op frame niveau voor volwassenen en ongepaste.

De volgende extractie toont een gedeeltelijk antwoord met potentiële opnamen, keyframes en volwassene en ongepastee scores:

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

## <a name="visualization-for-human-reviews"></a>Visualisatie voor mensen beoordelingen

Voor meer gesmelte gevallen hebben bedrijven een menselijke beoordelings oplossing nodig voor het renderen van de video, de frames en de door de machine toegewezen tags. De menselijke moderators die Video's en frames bekijken, krijgen een volledig overzicht van de inzichten, wijzigingen aanbrengen en hun beslissingen verzenden.

![standaard weergave voor video revisie programma](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Player-weer gave voor beoordeling op video niveau

Binaire beslissingen op video niveau worden mogelijk gemaakt met een video speler-weer gave waarin potentiële volwassene en ongepaste-frames worden weer gegeven. De menselijke revisoren navigeren de video met verschillende snelheids opties om de scènes te onderzoeken. Ze bevestigen hun beslissingen door de tags in te scha kelen.

![weer gave video revisie programma Player](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Frame weergave voor gedetailleerde Recensies

Een gedetailleerde video beoordeling voor frame-voor-frame analyse wordt mogelijk gemaakt met een weer gave op basis van een frame. De menselijke revisoren beoordelen en selecteren een of meer frames en wisselen Tags in om hun beslissingen te bevestigen. Een optionele volgende stap is redactie van de aanstootgevende frames of inhoud.

![frame weergave van video-revisie programma](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Transcriptie beoordelen

Video's hebben normaal gesp roken Voice-over die gematigd moeten worden gemoderator en aanstootgevende spraak. U gebruikt de Azure Media Indexer-service om spraak naar tekst te converteren en de beoordelings-API van Content Moderator te gebruiken om de transcriptie voor tekst toezicht in het beoordelings programma in te dienen.

![weer gave voor de transcriptie van video-revisie Programma's](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met de [Snelstartgids voor video toezicht](video-moderation-api.md).
- Meer informatie over het genereren van [video beoordelingen](video-reviews-quickstart-dotnet.md) voor uw menselijke revisoren vanuit uw gecontroleerde uitvoer.
- Voeg [Video-Transcript beoordelingen](video-transcript-reviews-quickstart-dotnet.md) toe aan uw video Beoordelingen.
- Bekijk de gedetailleerde zelf studie over het ontwikkelen van een [complete oplossing voor video toezicht](video-transcript-moderation-review-tutorial-dotnet.md).