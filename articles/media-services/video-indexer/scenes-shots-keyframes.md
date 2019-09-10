---
title: Video Indexer scènes, afbeeldingen en keyframes
titleSuffix: Azure Media Services
description: In dit onderwerp vindt u een overzicht van de Video Indexer scènes, afbeeldingen en keyframes.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b24778434596f583be44572612c856fa4e0cecde
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860241"
---
# <a name="scenes-shots-and-keyframes"></a>Scènes, opnamen en sleutelframes

Video Indexer ondersteunt het segmenteren van Video's in tijdelijke eenheden op basis van structurele en semantische eigenschappen. Met deze mogelijkheid kunnen klanten eenvoudig hun video-inhoud zoeken, beheren en bewerken op basis van verschillende granulariteit. Bijvoorbeeld, op basis van scènes, afbeeldingen en keyframes, zoals beschreven in dit onderwerp.   

![Scènes, opnamen en sleutelframes](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Scène detectie  
 
Video Indexer bepaalt wanneer een scène wordt gewijzigd in video op basis van visuele hints. In een scène wordt één gebeurtenis weer gegeven en deze bestaat uit een reeks opeenvolgende opnamen, die semantisch verwant zijn. Een scène miniatuur is het eerste keyframe van de onderliggende afbeelding. Video indexer segmenteert een video in scènes op basis van kleur consistentie op opeenvolgende Foto's en haalt de begin-en eind tijd van elke scène op. Scène detectie wordt beschouwd als een uitdagende taak, aangezien het gaat om semantische aspecten van Video's te kwantificeren.

> [!NOTE]
> Van toepassing op Video's die ten minste drie scènes bevatten.

## <a name="shot-detection"></a>Opnamedetectie

Video Indexer bepaalt wanneer een foto in de video wordt gewijzigd op basis van visuele hints door zowel abrupte als geleidelijke overgangen in het kleuren schema van aangrenzende frames bij te houden. De meta gegevens van de opname bevatten een begin-en eind tijd, evenals de lijst met hoofd frames die in de afbeelding zijn opgenomen. De moment opnamen zijn opeenvolgende frames die tegelijkertijd zijn gemaakt van dezelfde camera.

## <a name="keyframe-detection"></a>Detectie van keyframe

Selecteert de frames die het beste de afdruk vertegenwoordigen. Keyframes zijn de representatieve frames die zijn geselecteerd uit de volledige video op basis van esthetische eigenschappen (bijvoorbeeld contrast en stabiele gegevens). Video Indexer haalt een lijst met hoofd frame-Id's op als onderdeel van de meta gegevens van de opname, op basis waarvan klanten de hoofd frame miniatuur kunnen uitpakken. 

Keyframes zijn gekoppeld aan opnamen in de uitvoer-JSON. 

## <a name="editorial-shot-type-detection"></a>Detectie van redactionele type

Het bewerkings type dat is gekoppeld aan een afzonderlijke opname in de JSON van Insights vertegenwoordigt het redactionele type. U kunt deze kenmerken van de afbeeldings typen nuttig vinden bij het bewerken van Video's in clips, Trailers of bij het zoeken naar een specifieke stijl van keyframe voor artistieke doel einden. De verschillende typen worden bepaald op basis van de analyse van het eerste keyframe van elke opname. Foto's worden geïdentificeerd aan de hand van de schaal, de grootte en de locatie van de gezichten die in het eerste keyframe worden weer gegeven. 

De grootte en schaal van de afbeelding worden bepaald op basis van de afstand tussen de camera en de gezichten die in het kader worden weer gegeven. Als u deze eigenschappen gebruikt, detecteert Video Indexer de volgende afbeeldings typen:

* Breed: toont de hoofd tekst van de hele persoon.
* Gemiddeld: toont de hoofd tekst en het gezicht van een persoon.
* Sluiten: voornamelijk toont het gezicht van een persoon.
* Extreem close: toont het gezicht van een persoon die het scherm vult. 

Opname typen kunnen ook worden bepaald door locatie van de onderwerps tekens ten opzichte van het midden van het kader. Deze eigenschap definieert de volgende afbeeldings typen in Video Indexer:

* Links gezicht: er wordt een persoon weer gegeven aan de linkerkant van het kader.
* Midden gezicht: een persoon wordt weer gegeven in de centrale regio van het kader.
* Rechts gezicht: een persoon wordt weer gegeven aan de rechter kant van het kader.
* Buiten: een persoon wordt weer gegeven in een instelling voor buitenste.
* Binnen: een persoon wordt weer gegeven in een instelling in de binnenshuis.

Aanvullende kenmerken:

* Twee opnamen: toont twee personen van de gemiddelde grootte.
* Meerdere gezichten: meer dan twee personen.

## <a name="next-steps"></a>Volgende stappen

[Bekijk de Video Indexer uitvoer die de API produceert](video-indexer-output-json-v2.md#scenes)
