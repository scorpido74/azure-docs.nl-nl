---
title: Azure Media Services-inhoud publiceren met .NET | Microsoft Documenten
description: Meer informatie over het maken van een locator die wordt gebruikt om een streaming-URL op te bouwen. Codevoorbeelden zijn geschreven in C# en gebruiken de Media Services SDK voor .NET.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 615a6afb0f7a3e133603db10e7c79add3322070c
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476707"
---
# <a name="publish-media-services-content-using-net"></a>Inhoud van Media Services publiceren met .NET  
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Overzicht
U een adaptieve bitrate MP4-set streamen door een OnDemand-streaminglocator te maken en een streaming-URL te bouwen. Het [coderingsonderwerp van een asset](media-services-encode-asset.md) laat zien hoe u coderen in een adaptieve bitrate MP4-set. 

> [!NOTE]
> Als uw inhoud is versleuteld, configureert u het beleid voor het leveren van activa (zoals beschreven in [dit](media-services-dotnet-configure-asset-delivery-policy.md) onderwerp) voordat u een locator maakt. 
> 
> 

U ook een OnDemand-streaminglocator gebruiken om URL's te bouwen die wijzen op MP4-bestanden die geleidelijk kunnen worden gedownload.  

In dit onderwerp ziet u hoe u een OnDemand-streaminglocator maakt om uw asset te publiceren en een streaming-URL's voor vloeiend, MPEG-streepje en HLS te bouwen. Het toont ook warm om progressieve download URL's te bouwen. 

## <a name="create-an-ondemand-streaming-locator"></a>Een OnDemand streaming locator maken
Als u de OnDemand-streaminglocator wilt maken en URL's wilt krijgen, moet u de volgende dingen doen:

1. Als de inhoud is versleuteld, definieert u een toegangsbeleid.
2. Maak een OnDemand streaming locator.
3. Als u van plan bent te streamen, krijgt u het streaming manifestbestand (.ism) in het item. 
   
   Als u van plan bent om geleidelijk te downloaden, krijgt u de namen van MP4-bestanden in het item.  
4. Maak URL's voor het manifestbestand of MP4-bestanden. 


>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). Gebruik dezelfde beleids-ID als u altijd dezelfde dagen / toegangsmachtigingen gebruikt. Bijvoorbeeld beleid voor locators die bedoeld zijn om lang te blijven bestaan (niet-uploadbeleid). Raadpleeg [dit](media-services-dotnet-manage-entities.md#limit-access-policies) onderwerp voor meer informatie.

### <a name="use-media-services-net-sdk"></a>Media Services .NET SDK gebruiken
Url's voor streaming maken 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

De uitgangen:

    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


> [!NOTE]
> U uw inhoud ook streamen via een TLS-verbinding. Als u deze aanpak wilt doen, moet u ervoor zorgen dat uw streaming-URL's beginnen met HTTPS. Op dit moment ondersteunt AMS TLS niet met aangepaste domeinen.
> 
> 

Progressieve download-URL's maken 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
De uitgangen:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Media Services .NET SDK-extensies gebruiken
De volgende codeaanroepen .NET SDK-extensiesmethoden die een locator maken en de ID's voor vloeiend streamen, HLS en MPEG-DASH genereren voor adaptieve streaming.
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
* [Assets downloaden](media-services-deliver-asset-download.md)
* [Beleid voor het leveren van activa configureren](media-services-dotnet-configure-asset-delivery-policy.md)

