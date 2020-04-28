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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74900679"
---
# <a name="video-indexer-concepts"></a>Video Indexer concepten
 
In dit artikel worden enkele concepten van de Video Indexer-service beschreven.
    
## <a name="summarized-insights"></a>Overzicht van inzichten

Samenvatd Insights bevatten een geaggregeerde weer gave van de gegevens: gezichten, topics, emoties. Zo kunt u in plaats van elke duizenden Peri odes te gaan gebruiken en te controleren welke gezichten er zich bevinden. het overzicht bevat alle gezichten en voor elk van de getoonde inzichten, het tijds bereik dat wordt weer gegeven in en het percentage van de tijd dat het wordt weer gegeven.

## <a name="time-range-vs-adjusted-time-range"></a>tijds bereik versus gecorrigeerd tijd bereik

Time Range is het tijds bereik in de oorspronkelijke video. AdjustedTimeRange is het tijds bereik ten opzichte van de huidige afspeel lijst. Omdat u een afspeel lijst kunt maken op basis van verschillende lijnen van verschillende Video's, kunt u een video van 1 uur nemen en er slechts één regel van gebruiken, bijvoorbeeld 10:00-10:15. In dat geval hebt u een afspeel lijst met 1 regel, waarbij het tijds bereik 10:00-10:15 is, maar de adjustedTimeRange is 00:00-00:15.
 
## <a name="blocks"></a>Block

Blokken zijn bedoeld om het gemakkelijker te maken om de gegevens te door lopen. Een blok kan bijvoorbeeld worden opgesplitst op basis van wanneer er tussen sprekers wordt gewisseld of als er een lange pauze plaatsvindt.

## <a name="next-steps"></a>Volgende stappen

Voor informatie over hoe u aan de slag gaat, raadpleegt [u hoe u zich kunt registreren en hoe u uw eerste video uploadt](video-indexer-get-started.md).

## <a name="see-also"></a>Zie ook

[Overzicht van Video Indexer](video-indexer-overview.md)
