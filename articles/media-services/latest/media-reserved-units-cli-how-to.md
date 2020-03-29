---
title: CLI gebruiken om mediagereserveerde eenheden te schalen - Azure | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u CLI gebruiken om mediaverwerking te schalen met Azure Media Services.
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
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9f0a7425fc09d391828a748832f662f02c6022cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970784"
---
# <a name="scaling-media-processing"></a>Mediaverwerking schalen

Met Azure Media Services kunt u mediaverwerking schalen in uw account door het beheer van gereserveerde media-eenheden (MRU’s). MRU's bepalen de snelheid waarmee uw mediaverwerkingstaken worden verwerkt. U kunt kiezen uit de volgende gereserveerde-eenheidstypen: **S1**, **S2** en **S3**. Een coderingstaak wordt bijvoorbeeld sneller uitgevoerd wanneer u het gereserveerde-eenheidstype **S2** gebruikt (in vergelijking met het type **S1**). 

Naast het opgeven van het type gereserveerde eenheid, u opgeven om uw account in te richten bij gereserveerde eenheden. Op basis van het aantal ingerichte, gereserveerde eenheden wordt bepaald hoeveel mediataken tegelijk kunnen worden verwerkt voor een bepaald account. Als uw account bijvoorbeeld vijf gereserveerde eenheden heeft, worden vijf mediataken gelijktijdig uitgevoerd zolang er taken zijn die moeten worden verwerkt. De overige taken wachten in de wachtrij en worden opgehaald voor het opeenvolgend verwerken wanneer een lopende taak is voltooid. Als een account geen gereserveerde eenheden heeft ingericht, worden taken achtereenvolgens opgehaald. In dit geval is de wachttijd tussen de afwerking van de ene taak en de volgende taak die wordt gestart, afhankelijk van de beschikbaarheid van resources in het systeem.

## <a name="choosing-between-different-reserved-unit-types"></a>Kiezen tussen verschillende gereserveerde eenheidstypen

Met de volgende tabel u een beslissing nemen bij het kiezen tussen verschillende coderingssnelheden. Het biedt ook een paar benchmark cases op [een video die u downloaden](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) om uw eigen tests uit te voeren:

|RU-type|Scenario|Voorbeeldresultaten voor de [7 min 1080p-video](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Enkele bitrate codering. <br/>Bestanden op SD of onder resoluties, niet tijdgevoelig, lage kosten.|Codering voor een enkel bitrate SD-resolutie MP4-bestand met behulp van "H264 Single Bitrate SD 16x9" duurt ongeveer 7 minuten.|
| **S2**|Enkele bitrate en meerdere bitrate codering.<br/>Normaal gebruik voor zowel SD- als HD-codering.|Codering met de voorinstelling "H264 Single Bitrate 720p" duurt ongeveer 6 minuten.<br/><br/>Codering met de voorinstelling "H264 Multiple Bitrate 720p" duurt ongeveer 12 minuten.|
| **S3**|Enkele bitrate en meerdere bitrate codering.<br/>Full HD- en 4K-resolutievideo's. Tijdsgevoelige, snellere doorlooptijdcodering.|Codering met de voorinstelling "H264 Single Bitrate 1080p" duurt ongeveer 3 minuten.<br/><br/>Codering met de voorinstelling "H264 Multiple Bitrate 1080p" duurt ongeveer 8 minuten.|

## <a name="considerations"></a>Overwegingen

* Voor de taken Audioanalyse en Videoanalyse die worden geactiveerd door Media Services v3 of Video Indexer, wordt s3-eenheidstype ten zeerste aanbevolen.
* Als u de gedeelde groep gebruikt, dat wil zeggen, zonder gereserveerde eenheden, hebben uw codeertaken dezelfde prestaties als bij S1-EENHEDEN. Er is echter geen bovengrens aan de tijd die uw taken in de wachtrij kunnen doorbrengen en op een bepaald moment wordt er hooguit één taak uitgevoerd.

De rest van het artikel laat zien hoe [mediaservices v3 CLI](https://aka.ms/ams-v3-cli-ref) te gebruiken om MRU's te schalen.

> [!NOTE]
> Voor de analysetaken van audio en video die worden geactiveerd door Media Services v3 of Video Indexer is het raadzaam om uw account in te richten met 10 S3 MRU’s. Als u meer dan 10 S3-MRU's nodig hebt, opent u een ondersteuningsticket via de [Azure-portal.](https://portal.azure.com/)

## <a name="prerequisites"></a>Vereisten 

[Een Azure Media Services-account maken](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Gereserveerde eenheden schalen media met CLI

Voer de opdracht `mru` uit.

De volgende [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) commando sets Media Reserved Units op de "amsaccount" account met behulp van de **telling** en **type** parameters.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Billing

Er worden kosten in rekening gebracht op basis van het aantal minuten dat de gereserveerde mediain uw account zijn ingericht. Dit gebeurt onafhankelijk van of er taken worden uitgevoerd in uw account. Zie de veelgestelde vragen van de [prijspagina Media Services](https://azure.microsoft.com/pricing/details/media-services/) voor een gedetailleerde uitleg.   

## <a name="next-step"></a>Volgende stap

[Video's analyseren](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Zie ook

* [Quota en beperkingen](limits-quotas-constraints.md)
* [Azure-CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
