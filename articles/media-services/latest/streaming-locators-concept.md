---
title: Streaming locators in Azure Media Services | Microsoft Documenten
description: In dit artikel wordt uitgelegd wat Streaming Locators zijn en hoe ze worden gebruikt door Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/04/2020
ms.author: juliako
ms.openlocfilehash: 3a9568e1a0307cd1713c511ef42c065424306548
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302879"
---
# <a name="streaming-locators"></a>Streaming-locators

Om video's in de uitvoerasset beschikbaar te maken voor clients om af te spelen, moet u een [streaming-locator](https://docs.microsoft.com/rest/api/media/streaminglocators) maken en streaming-URL's maken. Als u een URL wilt samenstellen, moet u de hostnaam van het streaming-eindpunt en het pad van de streaming-locator samenvoegen. Zie [Een streaming-locator ophalen](stream-files-tutorial-with-api.md#get-a-streaming-locator) voor een voorbeeld met .NET.

Het proces van het maken van een **streaming-locator** wordt publiceren genoemd. De **streaming-locator** is standaard onmiddellijk geldig nadat u de API-aanroepen hebt gemaakt en totdat deze wordt verwijderd, tenzij u de optionele start- en eindtijden configureert. 

Wanneer u een **streaminglocator**maakt, moet u een **naam van** het actief en een naam van **het streamingbeleid** opgeven. Zie de volgende onderwerpen voor meer informatie:

* [Activa](assets-concept.md)
* [Beleid voor streaming](streaming-policy-concept.md)
* [Beleid voor inhoudssleutels](content-key-policy-concept.md)

Je ook de begin- en eindtijd opgeven op je Streaming Locator, waardoor je gebruiker de content alleen tussen deze tijden laat afspelen (bijvoorbeeld tussen 5/1/2019 tot 5/5/2019).  

## <a name="considerations"></a>Overwegingen

* **Streaming Locators** zijn niet updatable. 
* Eigenschappen van **Streaming Locators** die van het type Datetime zijn, zijn altijd in UTC-indeling.
* U moet een beperkt aantal beleidsregels voor uw Media Service-account ontwerpen en deze opnieuw gebruiken voor uw streaminglocators wanneer dezelfde opties nodig zijn. Zie [Quota en beperkingen](limits-quotas-constraints.md)voor meer informatie .

## <a name="create-streaming-locators"></a>Streaming locators maken  

### <a name="not-encrypted"></a>Niet versleuteld

Als u uw bestand in-the-clear (niet-versleuteld) wilt streamen, stelt u het vooraf gedefinieerde duidelijke streamingbeleid in: op 'Predefined_ClearStreamingOnly' (in .NET u het vooraf gedefinieerde StreamingPolicy.ClearStreamingOnly enum) gebruiken.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Versleuteld 

Als u uw inhoud wilt versleutelen met de CENC-versleuteling, stelt u uw beleid in op 'Predefined_MultiDrmCencStreaming'. De Widevine-versleuteling wordt toegepast op een DASH-stream en PlayReady naar Smooth. De sleutel wordt geleverd aan een afspeelclient op basis van de geconfigureerde DRM-licenties.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Als u ook uw HLS-stream wilt versleutelen met CBCS (FairPlay), gebruikt u 'Predefined_MultiDrmStreaming'.

> [!NOTE]
> Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="associate-filters-with-streaming-locators"></a>Filters koppelen aan Streaming Locators

Zie [Filters: associëren met Streaming Locators](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Entiteiten voor filteren, bestellen, streamen van pagina's

Zie [Het filteren, bestellen, paging van mediaservices entiteiten](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Streaminglocators aanbieden op naam van activa

Als u Streaming Locators wilt krijgen op basis van de bijbehorende assetnaam, gebruikt u de volgende bewerkingen:

|Taal|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[LijstStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[lijstStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Zie ook

* [Activa](assets-concept.md)
* [Beleid voor streaming](streaming-policy-concept.md)
* [Beleid voor inhoudssleutels](content-key-policy-concept.md)
* [Zelfstudie: Video's uploaden, coderen en streamen met .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Volgende stappen

[Een streaminglocator maken en URL's maken](create-streaming-locator-build-url.md)
