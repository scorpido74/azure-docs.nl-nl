---
title: Analyse van live streams met behulp van Video Indexer
titleSuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe u een analyse van Live Stream kunt uitvoeren met behulp van Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 0f34aad4a8590c71f926d12d201f9a614afaa127
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114927"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Live Stream-analyse met Video Indexer

Azure Media Services Video Indexer is een Azure-service die is ontworpen om grondige inzichten uit video-en audio bestanden offline te halen. Dit is het analyseren van een bepaald media bestand dat vooraf is gemaakt. In sommige gevallen is het echter belang rijk dat u de media inzichten van een live feed zo snel mogelijk kunt ophalen om operationele en andere use cases op tijd in te scha kelen. Zo kunnen dergelijke uitgebreide meta gegevens in een live stream door producenten van inhoud worden gebruikt om de productie van TV-producten te automatiseren. Bijvoorbeeld, de [groep Endemol schijnt](https://customers.microsoft.com/story/esg-media-telecommunications-azure), waarbij journalisten van een Newsroom de live feeds heeft doorzocht om meldings services te bouwen op basis van de inhoud.

Met een oplossing die in dit artikel wordt beschreven, kunnen klanten Video Indexer in bijna realtime-oplossingen in live feeds gebruiken. De vertraging in het indexeren kan Maxi maal vier minuten duren met behulp van deze oplossing, afhankelijk van de segmenten die worden geïndexeerd, de invoer resolutie, het type inhoud en de reken kracht die voor dit proces wordt gebruikt.

![De Video Indexer meta gegevens voor de Live Stream](./media/live-stream-analysis/live-stream-analysis01.png)

*Afbeelding 1: voor beeld-Player de Video Indexer meta gegevens in de Live Stream weer geven*

De [oplossing voor analyse van streams](https://github.com/Azure-Samples/media-services-dotnet-functions-integration/blob/master/media-functions-for-logic-app/LiveStreamAnalysis.md) maakt gebruik van Azure functions en twee Logic apps om een live-programma van een live kanaal in Azure Media Services met video indexer te verwerken en het resultaat weer te geven met Azure Media Player de stroom van de bijna real-time gegevens stromen weer gegeven.

Op hoog niveau bestaat het uit twee belang rijke stappen. De eerste stap wordt elke 60 seconden uitgevoerd en er wordt een subclip van de laatste 60 seconden afgespeeld, er wordt een activum van gemaakt en geïndexeerd via Video Indexer. Vervolgens wordt de tweede stap aangeroepen wanneer het indexeren is voltooid. De vastgelegde inzichten worden verwerkt, verzonden naar Azure Cosmos DB en de index van het subfragment wordt verwijderd.

De voorbeeld speler speelt de Live Stream af en haalt de inzichten op uit Azure Cosmos DB, met behulp van een speciale Azure-functie. De meta gegevens en miniaturen worden weer gegeven in synchronisatie met de live video.

![De twee Logic apps die elke minuut in de Cloud verwerken van de Live Stream](./media/live-stream-analysis/live-stream-analysis02.png)

*Afbeelding 2: de twee Logic apps die elke minuut in de Cloud verwerken van de live stream.*

## <a name="step-by-step-guide"></a>Stapsgewijze handleiding 

De volledige code en een stapsgewijze hand leiding voor het implementeren van de resultaten vindt u in github- [project voor Live Media Analytics met video indexer](https://aka.ms/livestreamanalysis). 

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Video Indexer](video-indexer-overview.md)
