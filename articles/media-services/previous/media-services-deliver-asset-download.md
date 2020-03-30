---
title: Media Services-elementen downloaden naar uw computer - Azure | Microsoft Documenten
description: Meer informatie over het downloaden van assets naar uw computer. Codevoorbeelden zijn geschreven in C# en gebruiken de Media Services SDK voor .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 21fcc6ae09718ffbb22e1d438926586dd3cde71d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61465657"
---
# <a name="how-to-deliver-an-asset-by-download"></a>How to: Een asset leveren door te downloaden  
In dit artikel worden opties besproken voor het leveren van mediaassets die zijn geüpload naar Media Services. U Media Services-inhoud leveren in tal van toepassingsscenario's. Download na het coderen de gegenereerde media-assets of krijg toegang tot deze met behulp van een streaminglocator. Voor betere prestaties en schaalbaarheid u ook inhoud leveren met behulp van een Content Delivery Network (CDN).

In dit voorbeeld ziet u hoe u mediaassets van Media Services naar uw lokale computer downloaden. De code vraagt de taken op die zijn gekoppeld aan het Media Services-account op taak-ID en toegang tot de **outputMediaAssets-verzameling** (de set van een of meer uitvoermedia-elementen die het resultaat zijn van het uitvoeren van een taak). In dit voorbeeld ziet u hoe u uitvoermediaassets van een taak downloadt, maar u dezelfde benadering toepassen om andere elementen te downloaden.

>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). Gebruik dezelfde beleids-ID als u altijd dezelfde dagen / toegangsmachtigingen gebruikt, bijvoorbeeld beleid voor locators die bedoeld zijn om lang te blijven bestaan (niet-uploadbeleid). Zie voor meer informatie [dit](media-services-dotnet-manage-entities.md#limit-access-policies) artikel.

```csharp
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }
```


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Streamingcontent leveren](media-services-deliver-streaming-content.md)

