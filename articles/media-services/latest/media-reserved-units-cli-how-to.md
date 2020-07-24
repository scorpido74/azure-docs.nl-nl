---
title: CLI gebruiken om gereserveerde media-eenheden te schalen-Azure | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u CLI gebruikt om media verwerking met Azure Media Services te schalen.
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
ms.openlocfilehash: 6715014485b227713447ce5d552cf7ba79737845
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053212"
---
# <a name="scaling-media-processing"></a>Mediaverwerking schalen

Met Azure Media Services kunt u mediaverwerking schalen in uw account door het beheer van gereserveerde media-eenheden (MRU’s). MRUs bepalen de snelheid waarmee uw media verwerkings taken worden verwerkt. U kunt kiezen uit de volgende gereserveerde-eenheidstypen: **S1**, **S2** en **S3**. Een coderingstaak wordt bijvoorbeeld sneller uitgevoerd wanneer u het gereserveerde-eenheidstype **S2** gebruikt (in vergelijking met het type **S1**). 

Naast het opgeven van het type gereserveerde eenheid kunt u opgeven om uw account in te richten met gereserveerde eenheden. Op basis van het aantal ingerichte, gereserveerde eenheden wordt bepaald hoeveel mediataken tegelijk kunnen worden verwerkt voor een bepaald account. Als uw account bijvoorbeeld vijf gereserveerde eenheden heeft, zullen vijf media taken gelijktijdig worden uitgevoerd, zolang er taken moeten worden verwerkt. De resterende taken wachten in de wachtrij en worden opgehaald voor verwerking opeenvolgend wanneer een actieve taak wordt voltooid. Als er voor een account geen gereserveerde eenheden zijn ingericht, worden de taken sequentieel opgehaald. In dit geval is de wacht tijd tussen het volt ooien van een taak en de volgende begint afhankelijk van de beschik baarheid van resources in het systeem.

## <a name="choosing-between-different-reserved-unit-types"></a>Kiezen tussen verschillende typen gereserveerde eenheden

In de volgende tabel kunt u een beslissing nemen bij het kiezen van verschillende coderings snelheden. Het biedt ook enkele benchmark cases voor [een video die u kunt downloaden](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) om uw eigen tests uit te voeren:

|RU-type|Scenario|Voorbeeld resultaten voor de [7 min 1080p-video](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Code ring met één bitsnelheid. <br/>Bestanden in SD of onder oplossingen, niet tijd gevoelig, lage kosten.|De code ring voor een MP4-bestand van de SD-resolutie met één bitsnelheid met ' H264 single bitrate SD 16x9 ' duurt circa 7 minuten.|
| **S2**|Code ring met één bitrate en meerdere bitrates.<br/>Normaal gebruik voor zowel SD-als HD-code ring.|De code ring met de voor instelling ' H264 single bitrate 720p ' duurt circa 6 minuten.<br/><br/>De code ring met de voor instelling ' H264 multiple bitrate 720p ' duurt ongeveer 12 minuten.|
| **S3**|Code ring met één bitrate en meerdere bitrates.<br/>Volledige HD-en 4.000 video-oplossingen. Tijd gevoelige, snellere aflever code.|De code ring met de voor instelling ' H264 single bitrate 1080p ' duurt ongeveer drie minuten.<br/><br/>De code ring met de voor instelling ' H264 multiple bitrate 1080p ' duurt ongeveer acht minuten.|

## <a name="considerations"></a>Overwegingen

* Voor de taken voor audio analyse en video analyse die worden geactiveerd door Media Services v3 of Video Indexer, wordt het type S3-eenheid nadrukkelijk aanbevolen.
* Als u de gedeelde groep gebruikt, dat wil zeggen, zonder gereserveerde eenheden, hebben uw versleutelings taken dezelfde prestaties als met S1 RUs. Er is echter geen bovengrens voor de tijd dat uw taken in de wachtrij staan en op elk gewenst moment maar één taak wordt uitgevoerd.

In de rest van het artikel ziet u hoe u [Media Services v3 cli](https://aka.ms/ams-v3-cli-ref) kunt gebruiken om MRUs te schalen.

> [!NOTE]
> Voor de analysetaken van audio en video die worden geactiveerd door Media Services v3 of Video Indexer is het raadzaam om uw account in te richten met 10 S3 MRU’s. Als u meer dan 10 S3 MRUs nodig hebt, kunt u een ondersteunings ticket openen met behulp van de [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Vereisten 

[Een Azure Media Services-account maken](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Gereserveerde media-eenheden schalen met CLI

Voer de opdracht `mru` uit.

Met de volgende opdracht [AZ AMS account MRU](/cli/azure/ams/account/mru?view=azure-cli-latest) worden de gereserveerde media-eenheden voor het account ' amsaccount ' ingesteld met behulp van de para meters **Count** en **type** .

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Billing

Er worden kosten in rekening gebracht op basis van het aantal minuten dat de gereserveerde media-eenheden in uw account zijn ingericht. Dit gebeurt onafhankelijk van het feit of er taken worden uitgevoerd in uw account. Zie de sectie Veelgestelde vragen op de pagina met prijzen voor [Media Services](https://azure.microsoft.com/pricing/details/media-services/) voor een gedetailleerde uitleg.   

## <a name="next-step"></a>Volgende stap

[Video's analyseren](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Zie tevens

* [Quota en limieten](limits-quotas-constraints.md)
* [Azure-CLI](/cli/azure/ams?view=azure-cli-latest)
