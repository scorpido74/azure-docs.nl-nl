---
title: CLI gebruiken voor het schalen van gereserveerde Media-eenheden - Azure | Microsoft Docs
description: Dit onderwerp leest hoe u met CLI mediaverwerking schalen met Azure Media Services.
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
ms.custom: seodec18
ms.openlocfilehash: 13fa733417558ab8be9ff1e5a9f1e484fb40f445
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102949"
---
# <a name="scaling-media-processing"></a>Mediaverwerking schalen

Azure Media Services kunt u mediaverwerking schalen in uw account door het beheer van gereserveerde Media-eenheden (groepsbeleidsinstelling). MRUs bepalen de snelheid waarmee uw media verwerkings taken worden verwerkt. U kunt kiezen uit de volgende gereserveerde-eenheidstypen: **S1**, **S2**, of **S3**. Een coderingstaak wordt bijvoorbeeld sneller uitgevoerd wanneer u het gereserveerde-eenheidstype **S2** gebruikt (in vergelijking met het type **S1**). 

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
> Voor de analyse van Audio en Video Analysis-taken die worden geactiveerd door Media Services v3 of Video Indexer, is het raadzaam om in te richten van uw account met 10 S3 groepsbeleidsinstelling. Als u meer dan 10 S3 groepsbeleidsinstelling nodig hebt, opent u een ondersteuning ticket met de [Azure-portal](https://portal.azure.com/).
>
> Op dit moment kunt u niet de Azure Portal gebruiken om andere v3-resources te beheren. Gebruik de [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) of een van de ondersteunde [SDK's](media-services-apis-overview.md#sdks).

## <a name="prerequisites"></a>Vereisten 

[Een Azure Media Services-account maken](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Gereserveerde schaal Media-eenheden met CLI

Voer de opdracht `mru` uit.

De volgende [az ams-account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) sets gereserveerde Media-eenheden op de 'amsaccount' rekening met de opdracht de **aantal** en **type** parameters.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Billing

Er worden kosten in rekening gebracht op basis van het aantal minuten dat de gereserveerde media-eenheden in uw account zijn ingericht. Dit gebeurt onafhankelijk van het feit of er taken worden uitgevoerd in uw account. Voor een gedetailleerde uitleg, Zie de sectie Veelgestelde vragen over van de [prijzen van Media Services](https://azure.microsoft.com/pricing/details/media-services/) pagina.   

## <a name="next-step"></a>Volgende stap

[Video's analyseren](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Zie ook

* [Quota en beperkingen](limits-quotas-constraints.md)
* [Azure-CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
