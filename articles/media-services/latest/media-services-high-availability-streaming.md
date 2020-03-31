---
title: Azure Media Services met hoge beschikbaarheid streamen
description: Meer informatie over het mislukken van een secundair Media Services-account als er een regionale datacenterstoring of -storing optreedt.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899228"
---
# <a name="media-services-high-availability-streaming"></a>Media Services met hoge beschikbaarheid streaming

Azure Media Services biedt momenteel geen directe failover van de service als er een regionale datacenterstoring of storing van onderliggende component- of afhankelijke services optreedt. In dit artikel wordt uitgelegd hoe u video-on-demand cross-region streaming bouwen.

## <a name="prerequisites"></a>Vereisten

Review [How to build a cross-regional encoding system Review How To build a cross-regional encoding system Review How To build a cross-regional encoding system](media-services-high-availability-encoding.md) Review How

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Video-on-demand cross-region streaming bouwen 

* Video-on-demand cross region streaming omvat het dupliceren van [assets,](assets-concept.md) [Content Key Policies](content-key-policy-concept.md) (indien gebruikt), Streaming [Policies](streaming-policy-concept.md)en [Streaming Locators](streaming-locators-concept.md). 
* U moet het beleid in beide regio's maken en up-to-date houden. 
* Wanneer u de streaminglocators maakt, wilt u dezelfde locator-id-waarde, ContentKey ID-waarde en ContentKey-waarde gebruiken.  
* Als u de inhoud codeert, wordt geadviseerd om de inhoud in regio A te coderen en te publiceren, de gecodeerde inhoud vervolgens te kopiëren naar regio B en deze te publiceren met dezelfde waarden als uit regio A.
* U Traffic Manager gebruiken op de hostnamen voor de oorsprong en de key delivery service (in de mediaservice ziet dit eruit als een URL van een aangepaste sleutelserver).

## <a name="next-steps"></a>Volgende stappen

Uitchecken:

* [Zelfstudie: Een extern bestand coderen op basis van URL en de video streamen](stream-files-dotnet-quickstart.md)
* [codemonsters](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
