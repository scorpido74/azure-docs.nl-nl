---
title: Overzicht van media verwerking schalen | Microsoft Docs
description: Dit onderwerp bevat een overzicht van het schalen van media verwerking met Azure Media Services.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "70102913"
---
# <a name="scaling-media-processing-overview"></a>Overzicht mediaverwerkingsschaling 
Deze pagina geeft een overzicht van hoe en waarom media verwerking kan worden geschaald. 

## <a name="overview"></a>Overzicht
Media Services-accounts worden gekoppeld aan een gereserveerde-eenheidstype, waarmee wordt bepaald hoe snel de mediaverwerkingstaken worden verwerkt. U kunt kiezen uit de volgende gereserveerde-eenheidstypen: **S1**, **S2** en **S3**. Een coderingstaak wordt bijvoorbeeld sneller uitgevoerd wanneer u het gereserveerde-eenheidstype **S2** gebruikt (in vergelijking met het type **S1**). Zie de [typen gereserveerde eenheden](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/)voor meer informatie.

Naast het opgeven van het type gereserveerde eenheid kunt u opgeven om uw account in te richten met gereserveerde eenheden. Op basis van het aantal ingerichte, gereserveerde eenheden wordt bepaald hoeveel mediataken tegelijk kunnen worden verwerkt voor een bepaald account. Als uw account bijvoorbeeld vijf gereserveerde eenheden heeft, zullen vijf media taken gelijktijdig worden uitgevoerd, zolang er taken moeten worden verwerkt. De resterende taken wachten in de wachtrij en worden opgehaald voor verwerking opeenvolgend wanneer een actieve taak wordt voltooid. Als er voor een account geen gereserveerde eenheden zijn ingericht, worden de taken sequentieel opgehaald. In dit geval is de wacht tijd tussen het volt ooien van een taak en de volgende begint afhankelijk van de beschik baarheid van resources in het systeem.

## <a name="choosing-between-different-reserved-unit-types"></a>Kiezen tussen verschillende typen gereserveerde eenheden
In de volgende tabel kunt u een beslissing nemen bij het kiezen van verschillende coderings snelheden. Het biedt ook enkele benchmark cases voor [een video die u kunt downloaden](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) om uw eigen tests uit te voeren:

|RU-type|Scenario|Voorbeeld resultaten voor de [7 min 1080p-video](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Code ring met één bitsnelheid. <br/>Bestanden in SD of onder oplossingen, niet tijd gevoelig, lage kosten.|De code ring voor een MP4-bestand van de SD-resolutie met één bitsnelheid met ' H264 single bitrate SD 16x9 ' duurt circa 7 minuten.|
| **S2**|Code ring met één bitrate en meerdere bitrates.<br/>Normaal gebruik voor zowel SD-als HD-code ring.|De code ring met de voor instelling ' H264 single bitrate 720p ' duurt circa 6 minuten.<br/><br/>De code ring met de voor instelling ' H264 multiple bitrate 720p ' duurt ongeveer 12 minuten.|
| **S3**|Code ring met één bitrate en meerdere bitrates.<br/>Volledige HD-en 4.000 video-oplossingen. Tijd gevoelige, snellere aflever code.|De code ring met de voor instelling ' H264 single bitrate 1080p ' duurt ongeveer drie minuten.<br/><br/>De code ring met de voor instelling ' H264 multiple bitrate 1080p ' duurt ongeveer acht minuten.|

## <a name="considerations"></a>Overwegingen
> [!IMPORTANT]
> Bekijk de overwegingen die in deze sectie worden beschreven.  
> 
> 

* Voor de taken voor audio analyse en video analyse die worden geactiveerd door Media Services v3 of Video Indexer, wordt het type S3-eenheid nadrukkelijk aanbevolen.
* Als u de gedeelde groep gebruikt, dat wil zeggen, zonder gereserveerde eenheden, hebben uw versleutelings taken dezelfde prestaties als met S1 RUs. Er is echter geen bovengrens voor de tijd dat uw taken in de wachtrij staan en op elk gewenst moment maar één taak wordt uitgevoerd.

## <a name="billing"></a>Billing

Er worden kosten in rekening gebracht op basis van het aantal minuten dat de gereserveerde media-eenheden in uw account zijn ingericht. Dit gebeurt onafhankelijk van het feit of er taken worden uitgevoerd in uw account. Zie de sectie Veelgestelde vragen op de pagina met prijzen voor [Media Services](https://azure.microsoft.com/pricing/details/media-services/) voor een gedetailleerde uitleg.   

## <a name="quotas-and-limitations"></a>Quota en beperkingen
Zie [quota's en beperkingen](media-services-quotas-and-limitations.md)voor meer informatie over quota en beperkingen en het openen van een ondersteunings ticket.

## <a name="next-step"></a>Volgende stap
Behaal de verwerkings taak voor de schaal van media met een van deze technologieën: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Zie [Aan de slag met de SDK voor Java-clients voor Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use) om de nieuwste versie van de Java-SDK op te halen en te ontwikkelen met Java. <br/>
> Als u de nieuwste PHP-SDK voor Media Services wilt downloaden, zoekt u versie 0.5.7 van het Microsoft/WindowAzure-pakket in de [Packagist-opslagplaats](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

