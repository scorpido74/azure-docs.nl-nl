---
title: Filters maken met Azure Media Services v3 .NET SDK
description: In dit onderwerp wordt beschreven hoe u filters maakt, zodat uw client deze kan gebruiken om specifieke secties van een stroom te streamen. Media Services maakt dynamische manifesten om deze selectief streaming te verzorgen.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75779243"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Filters maken met Media Services .NET SDK

Bij het leveren van uw inhoud aan klanten (het streamen van Live-gebeurtenissen of video op aanvraag), heeft uw client mogelijk meer flexibiliteit nodig dan is beschreven in het manifest bestand van het standaard activum. Met Azure Media Services kunt u account filters en activa filters definiëren voor uw inhoud. 

Zie [dynamische manifesten](filters-dynamic-manifest-overview.md) en [filters](filters-concept.md)voor een gedetailleerde beschrijving van deze functie en scenario's waarin deze wordt gebruikt.

In dit onderwerp wordt beschreven hoe u Media Services .NET SDK gebruikt om een filter te definiëren voor een video op aanvraag-Asset en om [account filters](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) en- [activa filters](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet)te maken. 

> [!NOTE]
> Zorg ervoor dat u de [presentationTimeRange](filters-concept.md#presentationtimerange)controleert.

## <a name="prerequisites"></a>Vereisten 

- Controleer [filters en dynamische manifesten](filters-dynamic-manifest-overview.md).
- [Een Azure Media Services-account maken](create-account-cli-how-to.md). Zorg ervoor dat u de naam van de resource groep en de naam van het Media Services account vergeet. 
- Gegevens ophalen die nodig zijn voor [toegang tot api's](access-api-cli-how-to.md)
- Bekijk het [uploaden, coderen en streamen met behulp van Azure Media Services](stream-files-tutorial-with-api.md) om te zien hoe u [.NET SDK kunt gaan gebruiken](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Een filter definiëren  

In .NET configureert u het bijhouden van selecties met de klassen [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) en [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) . 

Met de volgende code wordt een filter gedefinieerd dat audio nummers bevat die zijn opgenomen in EC-3 en video tracks met bitsnelheid in het 0-1000000-bereik.

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

## <a name="create-account-filters"></a>Account filters maken

De volgende code laat zien hoe u .NET gebruikt om een account filter te maken dat alle [hierboven gedefinieerde](#define-a-filter)track selecties bevat. 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Activa filters maken

De volgende code laat zien hoe u .NET gebruikt voor het maken van een activa filter dat alle [hierboven gedefinieerde](#define-a-filter)track selecties omvat. 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Filters koppelen aan streaming-Locator

U kunt een lijst opgeven met activa of account filters die van toepassing zijn op uw streaming-Locator. Met de [dynamische pakket (streaming-eind punt)](dynamic-packaging-overview.md) wordt deze lijst met filters toegepast, samen met de gegevens die door uw client zijn opgegeven in de URL. Deze combi natie genereert een [dynamisch manifest](filters-dynamic-manifest-overview.md)dat is gebaseerd op filters in de URL + filters die u opgeeft in de streaming-Locator. U wordt aangeraden deze functie te gebruiken als u filters wilt Toep assen, maar niet de filter namen in de URL wilt weer geven.

De volgende C#-code laat zien hoe u een streaming-Locator `StreamingLocator.Filters`maakt en opgeeft. Dit is een optionele eigenschap die een `IList<string>` filter naam in beslag neemt.

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

Zodra u filters hebt gedefinieerd, kunnen uw clients deze gebruiken in de streaming-URL. Filters kunnen worden toegepast op Adaptive Bitrate Streaming protocollen: Apple HTTP Live Streaming (HLS), MPEG-DASH en Smooth Streaming.

In de volgende tabel ziet u enkele voor beelden van Url's met filters:

|Protocol|Voorbeeld|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Volgende stappen

[Video streamen](stream-files-tutorial-with-api.md) 


