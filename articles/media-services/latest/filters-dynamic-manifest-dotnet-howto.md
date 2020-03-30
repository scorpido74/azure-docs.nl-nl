---
title: Filters maken met Azure Media Services v3 .NET SDK
description: In dit onderwerp wordt beschreven hoe u filters maakt, zodat uw client deze kan gebruiken om specifieke secties van een stream te streamen. Media Services creëert dynamische manifesten om deze selectieve streaming te bereiken.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: ef04b1b7b5030189482e89e26e4565397cbdd7c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779243"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Filters maken met Media Services .NET SDK

Wanneer uw klant uw inhoud aan klanten levert (live-gebeurtenissen streamen of Video on Demand) heeft uw klant mogelijk meer flexibiliteit nodig dan wat wordt beschreven in het manifestbestand van het standaardassetbestand. Met Azure Media Services u accountfilters en assetfilters voor uw inhoud definiëren. 

Zie [Dynamische manifesten](filters-dynamic-manifest-overview.md) en [filters](filters-concept.md)voor een gedetailleerde beschrijving van deze functie en scenario's waar deze wordt gebruikt.

In dit onderwerp wordt uitgelegd hoe u Media Services .NET SDK gebruiken om een filter voor een Video on Demand-asset te definiëren en [accountfilters](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) en [-activafilters te](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet)maken. 

> [!NOTE]
> Controleer de [presentatieTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Vereisten 

- [Filters en dynamische manifesten bekijken](filters-dynamic-manifest-overview.md).
- [Een Azure Media Services-account maken](create-account-cli-how-to.md). Zorg ervoor dat u de naam van de brongroep en de naam van het Media Services-account onthoudt. 
- Informatie krijgen die nodig is om toegang te [krijgen tot API's](access-api-cli-how-to.md)
- [Uploaden, coderen en streamen met Azure Media Services](stream-files-tutorial-with-api.md) controleren om te zien hoe u [.NET SDK gebruiken](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Een filter definiëren  

In .NET configureert u trackselecties met klassen [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) en [FilterTrackPropertyCondition.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) 

De volgende code definieert een filter dat alle audiotracks bevat die EC-3 zijn en alle videotracks die bitrate hebben in het 0-1000000-bereik.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Accountfilters maken

In de volgende code ziet u hoe u .NET gebruikt om een accountfilter te maken dat alle [hierboven gedefinieerde](#define-a-filter)trackselecties bevat. 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Assetfilters maken

In de volgende code ziet u hoe u .NET gebruikt om een assetfilter te maken dat alle [hierboven gedefinieerde](#define-a-filter)trackselecties bevat. 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Filters koppelen aan Streaming Locator

U een lijst met activa- of accountfilters opgeven, die van toepassing zou zijn op uw streaminglocator. De [Dynamic Packager (Streaming Endpoint)](dynamic-packaging-overview.md) past deze lijst met filters toe samen met de filters die uw client in de URL opgeeft. Deze combinatie genereert een [dynamisch manifest](filters-dynamic-manifest-overview.md), dat is gebaseerd op filters in de URL + filters die u opgeeft op Streaming Locator. We raden u aan deze functie te gebruiken als u filters wilt toepassen, maar de filternamen in de URL niet wilt blootleggen.

In de volgende C#-code ziet u `StreamingLocator.Filters`hoe u een streaminglocator maakt en opgeeft . Dit is een optionele `IList<string>` eigenschap die een van filternamen neemt.

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>Streamen met filters

Zodra u filters hebt gedefinieerd, kunnen uw klanten deze gebruiken in de streaming-URL. Filters kunnen worden toegepast op adaptieve bitrate streaming protocollen: Apple HTTP Live Streaming (HLS), MPEG-DASH en Smooth Streaming.

In de volgende tabel worden enkele voorbeelden van URL's met filters weergegeven:

|Protocol|Voorbeeld|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Volgende stappen

[Video's streamen](stream-files-tutorial-with-api.md) 


