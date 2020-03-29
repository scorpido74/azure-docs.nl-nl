---
title: Overzicht van Mediaverwerking schalen | Microsoft Documenten
description: Dit onderwerp is een overzicht van het schalen van mediaverwerking met Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: 780d3ab5047bff321d0c554880ba2995bcf25524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70102913"
---
# <a name="scaling-media-processing-overview"></a>Overzicht mediaverwerkingsschaling 
Deze pagina geeft een overzicht van hoe en waarom mediaverwerking kan worden opgeschaald. 

## <a name="overview"></a>Overzicht
Media Services-accounts worden gekoppeld aan een gereserveerde-eenheidstype, waarmee wordt bepaald hoe snel de mediaverwerkingstaken worden verwerkt. U kunt kiezen uit de volgende gereserveerde-eenheidstypen: **S1**, **S2** en **S3**. Een coderingstaak wordt bijvoorbeeld sneller uitgevoerd wanneer u het gereserveerde-eenheidstype **S2** gebruikt (in vergelijking met het type **S1**). Zie de typen [gereserveerde eenheden voor](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/)meer informatie.

Naast het opgeven van het type gereserveerde eenheid, u opgeven om uw account in te richten bij gereserveerde eenheden. Op basis van het aantal ingerichte, gereserveerde eenheden wordt bepaald hoeveel mediataken tegelijk kunnen worden verwerkt voor een bepaald account. Als uw account bijvoorbeeld vijf gereserveerde eenheden heeft, worden vijf mediataken gelijktijdig uitgevoerd zolang er taken zijn die moeten worden verwerkt. De overige taken wachten in de wachtrij en worden opgehaald voor het opeenvolgend verwerken wanneer een lopende taak is voltooid. Als een account geen gereserveerde eenheden heeft ingericht, worden taken achtereenvolgens opgehaald. In dit geval is de wachttijd tussen de afwerking van de ene taak en de volgende taak die wordt gestart, afhankelijk van de beschikbaarheid van resources in het systeem.

## <a name="choosing-between-different-reserved-unit-types"></a>Kiezen tussen verschillende gereserveerde eenheidstypen
Met de volgende tabel u een beslissing nemen bij het kiezen tussen verschillende coderingssnelheden. Het biedt ook een paar benchmark cases op [een video die u downloaden](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) om uw eigen tests uit te voeren:

|RU-type|Scenario|Voorbeeldresultaten voor de [7 min 1080p-video](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Enkele bitrate codering. <br/>Bestanden op SD of onder resoluties, niet tijdgevoelig, lage kosten.|Codering voor een enkel bitrate SD-resolutie MP4-bestand met behulp van "H264 Single Bitrate SD 16x9" duurt ongeveer 7 minuten.|
| **S2**|Enkele bitrate en meerdere bitrate codering.<br/>Normaal gebruik voor zowel SD- als HD-codering.|Codering met de voorinstelling "H264 Single Bitrate 720p" duurt ongeveer 6 minuten.<br/><br/>Codering met de voorinstelling "H264 Multiple Bitrate 720p" duurt ongeveer 12 minuten.|
| **S3**|Enkele bitrate en meerdere bitrate codering.<br/>Full HD- en 4K-resolutievideo's. Tijdsgevoelige, snellere doorlooptijdcodering.|Codering met de voorinstelling "H264 Single Bitrate 1080p" duurt ongeveer 3 minuten.<br/><br/>Codering met de voorinstelling "H264 Multiple Bitrate 1080p" duurt ongeveer 8 minuten.|

## <a name="considerations"></a>Overwegingen
> [!IMPORTANT]
> Bekijk overwegingen die in deze sectie worden beschreven.  
> 
> 

* Voor de taken Audioanalyse en Videoanalyse die worden geactiveerd door Media Services v3 of Video Indexer, wordt s3-eenheidstype ten zeerste aanbevolen.
* Als u de gedeelde groep gebruikt, dat wil zeggen, zonder gereserveerde eenheden, hebben uw codeertaken dezelfde prestaties als bij S1-EENHEDEN. Er is echter geen bovengrens aan de tijd die uw taken in de wachtrij kunnen doorbrengen en op een bepaald moment wordt er hooguit één taak uitgevoerd.

## <a name="billing"></a>Billing

Er worden kosten in rekening gebracht op basis van het aantal minuten dat de gereserveerde mediain uw account zijn ingericht. Dit gebeurt onafhankelijk van of er taken worden uitgevoerd in uw account. Zie de veelgestelde vragen van de [prijspagina Media Services](https://azure.microsoft.com/pricing/details/media-services/) voor een gedetailleerde uitleg.   

## <a name="quotas-and-limitations"></a>Quota en beperkingen
Zie [Quota en beperkingen](media-services-quotas-and-limitations.md)voor informatie over quota en beperkingen en het openen van een ondersteuningsticket.

## <a name="next-step"></a>Volgende stap
Bereik de schaalbewerking met een van deze technologieën: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [Rest](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Zie [Aan de slag met de SDK voor Java-clients voor Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use) om de nieuwste versie van de Java-SDK op te halen en te ontwikkelen met Java. <br/>
> Als u de nieuwste PHP-SDK voor Media Services wilt downloaden, zoekt u versie 0.5.7 van het Microsoft/WindowAzure-pakket in de [Packagist-opslagplaats](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

