---
title: Live stream-analyse met video-indexer
titleSuffix: Azure Media Services
description: In dit artikel ziet u hoe u een live stream-analyse uitvoert met Behulp van Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185998"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Live stream-analyse met Video Indexer

Azure Media Services Video Indexer is een Azure-service die is ontworpen om diepgaande inzichten uit video- en audiobestanden offline te halen. Dit is om een bepaald mediabestand te analyseren dat al van tevoren is gemaakt. Voor sommige use cases is het echter belangrijk om de media-inzichten zo snel mogelijk uit een live feed te halen om operationele en andere use cases te ontgrendelen die op tijd zijn ingedrukt. Dergelijke rijke metadata op een live stream kan bijvoorbeeld door contentproducenten worden gebruikt om de tv-productie te automatiseren.

Een oplossing beschreven in dit artikel, stelt klanten in staat om Video Indexer te gebruiken in de buurt van real-time resoluties op live feeds. De vertraging in het indexeren kan zo laag zijn als vier minuten met behulp van deze oplossing, afhankelijk van de brokken gegevens die worden geïndexeerd, de inputresolutie, het type inhoud en de rekenkracht die voor dit proces wordt gebruikt.

![De metagegevens van de video-indexer op de live stream](./media/live-stream-analysis/live-stream-analysis01.png)

*Figuur 1 – Voorbeeldspeler die de metagegevens van de Video-indexer weergeeft op de live stream*

De [streamanalyseoplossing](https://aka.ms/livestreamanalysis) bij de hand, maakt gebruik van Azure Functions en twee Logic Apps om een live-programma te verwerken vanaf een live-kanaal in Azure Media Services met Video Indexer en geeft het resultaat weer met Azure Media Player met de bijna realtime resulterende stream.

Op hoog niveau bestaat het uit twee hoofdstappen. De eerste stap loopt elke 60 seconden, en neemt een subclip van de laatste 60 seconden gespeeld, creëert een actief van het en indexeert het via Video Indexer. Vervolgens wordt de tweede stap aangeroepen zodra de indexering is voltooid. De vastgelegde inzichten worden verwerkt, verzonden naar Azure Cosmos DB en de subclip geïndexeerd wordt verwijderd.

De voorbeeldspeler speelt de livestream af en krijgt de inzichten van Azure Cosmos DB met behulp van een speciale Azure-functie. Het toont de metadata en miniaturen in sync met de live video.

![De twee logische apps verwerken de livestream elke minuut in de cloud](./media/live-stream-analysis/live-stream-analysis02.png)

*Figuur 2 – De twee logische apps verwerken de livestream elke minuut in de cloud.*

## <a name="step-by-step-guide"></a>Stapsgewijze handleiding 

De volledige code en een stapsgewijze handleiding voor het implementeren van de resultaten zijn te vinden in [GitHub project voor Live media analytics met Video Indexer.](https://aka.ms/livestreamanalysis) 

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Video Indexer](video-indexer-overview.md)
