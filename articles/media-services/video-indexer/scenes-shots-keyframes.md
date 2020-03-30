---
title: Video Indexer-scènes, opnamen en hoofdframes
titleSuffix: Azure Media Services
description: Dit onderwerp geeft een overzicht van de video-indexerscènes, shots en hoofdframes.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: a833fd808049cfce95b182910e50e38d3c39f4e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245939"
---
# <a name="scenes-shots-and-keyframes"></a>Scènes, opnamen en sleutelframes

Video Indexer ondersteunt het segmenteren van video's in tijdelijke eenheden op basis van structurele en semantische eigenschappen. Met deze mogelijkheid kunnen klanten eenvoudig hun video-inhoud bekijken, beheren en bewerken op basis van verschillende granulariteiten. Bijvoorbeeld op basis van scènes, opnamen en hoofdframes, beschreven in dit onderwerp.   

![Scènes, opnamen en sleutelframes](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Scènedetectie  
 
Videoindexer bepaalt wanneer een scène verandert in video op basis van visuele aanwijzingen. Een scène toont een enkele gebeurtenis en het is samengesteld uit een reeks opeenvolgende opnamen, die semantisch gerelateerd zijn. Een scèneminiatuur is het eerste hoofdframe van de onderliggende opname. Video-indexer segmenten van een video in scènes op basis van kleur coherentie over opeenvolgende opnamen en haalt het begin en de eindtijd van elke scène. Scènedetectie wordt beschouwd als een uitdagende taak omdat het gaat om het kwantificeren van semantische aspecten van video's.

> [!NOTE]
> Van toepassing op video's die ten minste 3 scènes bevatten.

## <a name="shot-detection"></a>Shotdetectie

VideoIndexer bepaalt wanneer een opname in de video verandert op basis van visuele aanwijzingen, door zowel abrupte als geleidelijke overgangen in het kleurenschema van aangrenzende frames bij te houden. De metagegevens van de opname bevatten een begin- en eindtijd, evenals de lijst met hoofdframes die in dat schot zijn opgenomen. De opnamen zijn opeenvolgende frames genomen van dezelfde camera op hetzelfde moment.

## <a name="keyframe-detection"></a>Detectie van hoofdframes

VideoIndexer selecteert het frame(s) dat elke opname het beste weergeeft. Hoofdframes zijn de representatieve frames die uit de hele video zijn geselecteerd op basis van esthetische eigenschappen (bijvoorbeeld contrast en stabielheid). Video Indexer haalt een lijst met hoofdframe-id's op als onderdeel van de metagegevens van de opname, op basis waarvan klanten het hoofdframe kunnen extraheren als een afbeelding met een hoge resolutie.  

### <a name="extracting-keyframes"></a>Hoofdframes extraheren

Als u hoofdframes met hoge resolutie voor uw video wilt extraheren, moet u de video eerst uploaden en indexeren.

![Hoofdframes](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>Met de Website van Video Indexer

Als u hoofdframes wilt extraheren met de website Video Indexer, uploadt en indexeert u uw video. Zodra de indexeringstaak is voltooid, klikt u op de knop **Downloaden** en selecteert u **Artefacten (ZIP).** Hiermee wordt de map met artefacten naar uw computer gedownload. 

![Hoofdframes](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
Rits de map uit en open deze. In de *map _KeyframeThumbnail* vindt u alle hoofdframes die uit uw video zijn gehaald. 

#### <a name="with-the-video-indexer-api"></a>Met de API video-indexer

Als u hoofdframes wilt ontvangen met de API voor videoindexer, uploadt en indexeert u uw video met behulp van het gesprek [Video uploaden.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) Zodra de indexeringstaak is voltooid, belt u [Videoindex op.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?) Dit geeft u alle inzichten die Video Indexer uit uw inhoud in een JSON-bestand heeft gehaald.  

U krijgt een lijst van keyframe-id's als onderdeel van de metadata van elk schot. 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

U moet nu elk van deze hoofdframe-id's uitvoeren op het gesprek [Miniaturen weergeven.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) Hiermee worden alle hoofdframeafbeeldingen naar uw computer gedownload. 

## <a name="editorial-shot-type-detection"></a>Detectie van redactioneel schottype

Hoofdframes worden gekoppeld aan opnamen in de uitvoer JSON. 

Het opnametype dat is gekoppeld aan een individuele opname in de inzichten die JSON vertegenwoordigt, vertegenwoordigt het redactionele type. U deze opnametypekenmerken nuttig vinden bij het bewerken van video's in clips, trailers of bij het zoeken naar een specifieke stijl van hoofdframe voor artistieke doeleinden. De verschillende typen worden bepaald op basis van de analyse van het eerste hoofdframe van elke opname. Foto's worden geïdentificeerd door de schaal, grootte en locatie van de gezichten die in hun eerste hoofdframe worden weergegeven. 

De opnamegrootte en -schaal worden bepaald op basis van de afstand tussen de camera en de gezichten die in het frame verschijnen. Met behulp van deze eigenschappen detecteert Video Indexer de volgende opnametypen:

* Breed: toont het lichaam van een hele persoon.
* Medium: toont iemands bovenlichaam en gezicht.
* Close-up: toont vooral iemands gezicht.
* Extreme close-up: toont het gezicht van een persoon die het scherm vult. 

Shottypes kunnen ook worden bepaald door de locatie van de onderwerptekens met betrekking tot het midden van het frame. Met deze eigenschap worden de volgende opnametypen gedefinieerd in Video-indexer:

* Linkergezicht: er verschijnt een persoon aan de linkerkant van het frame.
* Middengezicht: een persoon wordt weergegeven in het centrale gebied van het frame.
* Rechtergezicht: een persoon verschijnt in de rechterkant van het frame.
* Buiten: een persoon verschijnt in een buitenomgeving.
* Binnen: een persoon verschijnt in een binnenomgeving.

Aanvullende kenmerken:

* Twee foto's: toont twee gezichten van gemiddelde grootte.
* Meerdere gezichten: meer dan twee personen.


## <a name="next-steps"></a>Volgende stappen

[De video-indexeruitvoer van de API onderzoeken](video-indexer-output-json-v2.md#scenes)
