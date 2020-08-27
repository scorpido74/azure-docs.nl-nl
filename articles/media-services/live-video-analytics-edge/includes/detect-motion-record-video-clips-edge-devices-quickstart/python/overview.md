---
ms.openlocfilehash: 768e79c6a2471715b336f90748ad97ecfcc4bbc2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682164"
---

![overview](../../../media/quickstarts/overview-qs4.png)

Het voorgaande diagram laat zien hoe de signalen in deze quickstart stromen. [Een Edge-module](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuleert een IP-camera die als host fungeert voor een RTSP-server (Real-Time Streaming Protocol). Een [RTSP-bron](../../../media-graph-concept.md#rtsp-source)knooppunt haalt de video-feed van deze server, en verstuurt videoframes naar het knooppunt van de [bewegingsdetectieprocessor](../../../media-graph-concept.md#motion-detection-processor). De RTSP-bron verzendt diezelfde videoframes naar een [signaalpoortprocessor](../../../media-graph-concept.md#signal-gate-processor)knoop punt, dat gesloten blijft totdat het wordt geactiveerd door een gebeurtenis.

Wanneer de bewegingsdetectieprocessor beweging detecteert in de video, verzendt deze een gebeurtenis naar het signaalpoortprocessorknooppunt waardoor dit wordt geactiveerd. De poort wordt geopend gedurende de geconfigureerde tijd, waarin videoframes worden verzonden naar het [bestandssink](../../../media-graph-concept.md#file-sink)knooppunt. Dit sinkknooppunt registreert de video als een MP4-bestand in het lokale bestandssysteem van uw edge-apparaat. Het bestand wordt opgeslagen op de geconfigureerde locatie.

In deze quickstart gaat u het volgende doen:

1. De mediagrafiek maken en implementeren.
1. De resultaten interpreteren.
1. Resources opschonen.
