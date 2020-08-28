---
title: Media Services assets downloaden naar uw computer-Azure | Microsoft Docs
description: Meer informatie over het downloaden van assets naar uw computer. Code voorbeelden zijn geschreven in C# en gebruiken de Media Services SDK voor .NET.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: f22d2022ae095ea7178859b1c036f78e39c10152
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019450"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Procedure: een Asset leveren door te downloaden  
In dit artikel worden opties beschreven voor het leveren van media-assets die zijn geüpload naar Media Services. U kunt Media Services-inhoud leveren in verschillende toepassings scenario's. Down load na het coderen de gegenereerde media-assets of open ze met behulp van een streaming-Locator. Voor betere prestaties en schaal baarheid kunt u ook inhoud leveren met behulp van een Content Delivery Network (CDN).

In dit voor beeld ziet u hoe u media-assets van Media Services naar uw lokale computer kunt downloaden. De code voert een query uit op de taken die zijn gekoppeld aan het Media Services-account op taak-ID en opent de **OutputMediaAssets** -verzameling (dat wil zeggen de set van een of meer uitvoer media-assets die het resultaat zijn van het uitvoeren van een taak). In dit voor beeld ziet u hoe uitvoer media-assets van een taak kunnen worden gedownload, maar u kunt dezelfde benadering Toep assen om andere assets te downloaden.

>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). Gebruik dezelfde beleids-ID als u altijd dezelfde dagen/toegangs machtigingen gebruikt, bijvoorbeeld beleids regels voor Locators die zijn bedoeld om gedurende een lange periode te blijven (niet-upload beleid). Zie [Dit](media-services-dotnet-manage-entities.md#limit-access-policies) artikel voor meer informatie.

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
[Streaming-inhoud leveren](media-services-deliver-streaming-content.md)

