---
title: Video Indexer concepten
titleSuffix: Azure Media Services
description: In dit artikel worden enkele concepten van de Azure Media Services Video Indexer-service beschreven.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 73dad1db4f44134f871c9f3d6e7edcdd3bd1e2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900679"
---
# <a name="video-indexer-concepts"></a>Video Indexer concepten
 
In dit artikel worden enkele concepten van de Video Indexer-service beschreven.
    
## <a name="summarized-insights"></a>Samengevatte inzichten

Samengevatte inzichten bevatten een geaggregeerde weergave van de gegevens: gezichten, onderwerpen, emoties. In plaats van bijvoorbeeld elk van de duizenden tijdsbereiken te doorlopen en te controleren welke vlakken erin staan, bevatten de samengevatte inzichten alle gezichten en voor elk aantal de perioden waarin het wordt weergegeven en het % van de tijd waarin het wordt weergegeven.

## <a name="time-range-vs-adjusted-time-range"></a>tijdbereik versus aangepast tijdbereik

TimeRange is het tijdsbereik in de originele video. AdjustedTimeRange is het tijdsbereik ten opzichte van de huidige afspeellijst. Omdat je een afspeellijst maken van verschillende regels van verschillende video's, kun je een video van 1 uur maken en er slechts 1 regel van gebruiken, bijvoorbeeld 10:00-10:15. In dat geval heb je een afspeellijst met 1 regel, waarbij het tijdsbereik 10:00-10:15 is, maar de aangepaste TimeRange 00:00-00:15 is.
 
## <a name="blocks"></a>Blokken

Blokken zijn bedoeld om het gemakkelijker te maken om door de gegevens te gaan. Een blok kan bijvoorbeeld worden opgesplitst op basis van wanneer er tussen sprekers wordt gewisseld of als er een lange pauze plaatsvindt.

## <a name="next-steps"></a>Volgende stappen

Zie [Hoe u zich aanmeldt en uw eerste video uploadt](video-indexer-get-started.md)voor informatie over hoe u aan de slag.

## <a name="see-also"></a>Zie ook

[Overzicht van Video Indexer](video-indexer-overview.md)
