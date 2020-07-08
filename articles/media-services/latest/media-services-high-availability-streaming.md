---
title: Azure Media Services streamen met hoge Beschik baarheid
description: Meer informatie over het uitvoeren van een failover naar een secundaire Media Services-account als er sprake is van een storing in een regionaal Data Center of een storing.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78899228"
---
# <a name="media-services-high-availability-streaming"></a>Media Services streamen met hoge Beschik baarheid

Azure Media Services biedt momenteel geen onmiddellijke failover van de service als er sprake is van een storing in de regionale Data Center of het mislukken van onderliggend onderdeel of afhankelijke services. In dit artikel wordt uitgelegd hoe u video-on-demand streaming voor meerdere regio's kunt bouwen.

## <a name="prerequisites"></a>Vereisten

Lees [hoe u een cross-regionaal coderings systeem bouwt](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Video-on-demand streaming voor meerdere regio's bouwen 

* Video-on-demand streaming voor meerdere regio's omvat het dupliceren van [assets](assets-concept.md), [beleids regels voor inhouds sleutels](content-key-policy-concept.md) (indien gebruikt), [streaming-beleid](streaming-policy-concept.md)en [streaming-Locators](streaming-locators-concept.md). 
* U moet de beleids regels in beide regio's maken en deze up-to-date houden. 
* Wanneer u de streaming-Locators maakt, wilt u dezelfde Locator-ID-waarde, ContentKey-ID-waarde en ContentKey-waarde gebruiken.  
* Als u de inhoud codeert, wordt het aanbevolen om de inhoud in regio A te coderen en te publiceren, vervolgens de versleutelde inhoud naar regio B te kopiëren en deze te publiceren met dezelfde waarden als van regio A.
* U kunt Traffic Manager gebruiken op de hostnamen voor de oorsprong en de key delivery-service (in Media Services configuratie dit lijkt op een aangepaste URL voor de sleutel Server).

## <a name="next-steps"></a>Volgende stappen

Uitchecken:

* [Zelf studie: een extern bestand coderen op basis van URL en de video streamen](stream-files-dotnet-quickstart.md)
* [code voorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
