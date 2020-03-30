---
title: Filters maken met Azure Media Services .NET SDK
description: In dit onderwerp wordt beschreven hoe u filters maakt, zodat uw client deze kan gebruiken om specifieke secties van een stream te streamen. Media Services creëert dynamische manifesten om deze selectieve streaming te bereiken.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: cenkdin
ms.openlocfilehash: c60b223f91a151bf63cabc5e95816f2545022503
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016607"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Filters maken met Media Services .NET SDK 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [Rest](media-services-rest-dynamic-manifest.md)
> 
> 

Vanaf 2.17 release u met Media Services filters voor uw assets definiëren. Deze filters zijn regels aan de serverzijde waarmee uw klanten kunnen kiezen om dingen te doen zoals: alleen een gedeelte van een video afspelen (in plaats van de hele video af te spelen), of alleen een subset van audio- en videoweergaven opgeven die het apparaat van uw klant aankan (in plaats van alle weergaven die aan het actief zijn gekoppeld). Deze filtering van uw assets wordt bereikt via **Dynamic Manifest**s die zijn gemaakt op verzoek van uw klant om een video te streamen op basis van opgegeven filter(s).

Zie [Overzicht dynamische manifesten voor](media-services-dynamic-manifest-overview.md)meer gedetailleerde informatie met betrekking tot filters en Dynamisch Manifest.

In dit artikel ziet u hoe u Media Services .NET SDK gebruiken om filters te maken, bij te werken en te verwijderen. 

Als u een filter bijwerkt, kan het tot twee minuten duren voordat streaming-eindpunten de regels vernieuwen. Als de inhoud is weergegeven met dit filter (en in de cache is opgeslagen in proxy's en CDN-caches), kan het bijwerken van dit filter leiden tot player-fouten. Verwijder altijd de cache na het bijwerken van het filter. Als deze optie niet mogelijk is, u overwegen een ander filter te gebruiken. 

## <a name="types-used-to-create-filters"></a>Typen die worden gebruikt om filters te maken
De volgende typen worden gebruikt bij het maken van filters: 

* **IStreamingFilter**.  Dit type is gebaseerd op het volgende REST [API-filter](https://docs.microsoft.com/rest/api/media/operations/filter)
* **iStreamingAssetFilter**. Dit type is gebaseerd op de volgende REST API [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Dit type is gebaseerd op de volgende REST API [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** en **IFilterTrackPropertyCondition**. Deze typen zijn gebaseerd op de volgende REST API's [FilterTrackSelect en FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Globale filters maken/bijwerken/lezen/verwijderen
In de volgende code ziet u hoe u .NET gebruikt om assetfilters te maken, bij te werken, te lezen en te verwijderen.

```csharp
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();
```

## <a name="createupdatereaddelete-asset-filters"></a>Assetfilters maken/bijwerken/lezen/verwijderen
In de volgende code ziet u hoe u .NET gebruikt om assetfilters te maken, bij te werken, te lezen en te verwijderen.

```csharp
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();

```


## <a name="build-streaming-urls-that-use-filters"></a>Streaming-URL's maken die filters gebruiken
Zie [Inhoud leveren aan klantenoverzicht](media-services-deliver-content-overview.md)voor informatie over het publiceren en leveren van uw assets.

In de volgende voorbeelden ziet u hoe u filters toevoegt aan uw streaming-URL's.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Overzicht van dynamische manifesten](media-services-dynamic-manifest-overview.md)

