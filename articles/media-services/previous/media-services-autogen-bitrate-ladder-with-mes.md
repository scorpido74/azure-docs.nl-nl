---
title: Media Encoder Standard gebruiken om een bitrate ladder automatisch te genereren-Azure | Microsoft Docs
description: In dit onderwerp wordt uitgelegd hoe u Media Encoder Standard (MES) kunt gebruiken om automatisch een bitrate ladder te genereren op basis van de invoer resolutie en bitrate.
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
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: b7f0b77ba11a0c9c1670ec240caf45fcf61a934d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896019"
---
#  <a name="use-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Media Encoder Standard gebruiken om een bitrate ladder automatisch te genereren  

## <a name="overview"></a>Overzicht

In dit artikel wordt uitgelegd hoe u Media Encoder Standard (MES) kunt gebruiken om automatisch een bitrate ladder te genereren (paren voor de omzetting van de bitsnelheid) op basis van de invoer resolutie en bitrate. De automatisch gegenereerde voor instelling overschrijdt nooit de invoer resolutie en bitrate. Bijvoorbeeld, als de invoer 720p met 3 Mbps is, uitvoer blijft 720p met de beste en tarieven lager is dan 3 Mbps wordt gestart.

### <a name="encoding-for-streaming-only"></a>Code ring voor alleen streaming

Als u uw bron video alleen wilt coderen voor streaming, moet u de voor instelling ' adaptieve streaming ' gebruiken bij het maken van een coderings taak. Wanneer u de voor instelling voor **adaptieve streaming** gebruikt, zal het mes-coderings programma op een slimme manier een bitrate voor de bitsnelheid maken. U kunt de coderings kosten echter niet beheren omdat de service bepaalt hoeveel lagen er moeten worden gebruikt en met welke resolutie. Aan het einde van dit artikel ziet u voor beelden van uitvoer lagen die worden geproduceerd door MES als resultaat van code ring met de voor instelling **adaptief streamen** . Het uitvoer element bevat MP4-bestanden waarbij audio en video niet Interleaved zijn.

### <a name="encoding-for-streaming-and-progressive-download"></a>Code ring voor streaming en progressief downloaden

Als uw intentie is om uw bron video te coderen voor streaming en voor het maken van MP4-bestanden voor progressief downloaden, moet u de voor instelling ' adaptieve meerdere bitrate MP4-inhoud ' gebruiken wanneer u een coderings taak maakt. Wanneer u de voor instelling voor de **inhouds adaptieve meerdere bitrate MP4** gebruikt, past de methode mes dezelfde coderings logica toe als hierboven. de uitvoer Asset bevat nu MP4-bestanden waarbij audio en Video Interleaved zijn. U kunt een van deze MP4-bestanden (bijvoorbeeld de versie met de hoogste bitrate) gebruiken als een progressief Download bestand.

## <a id="encoding_with_dotnet"></a>Code ring met Media Services .NET SDK

In het volgende code voorbeeld wordt Media Services .NET SDK gebruikt om de volgende taken uit te voeren:

- Maak een coderings taak.
- Een verwijzing naar de Media Encoder Standard encoder ophalen.
- Voeg een coderings taak aan de taak toe en geef op dat u de voor instelling voor **adaptieve streaming** wilt gebruiken. 
- Maak een uitvoer activum dat het gecodeerde activum bevat.
- Voeg een gebeurtenis-handler toe om de voortgang van de taak te controleren.
- Verzend de taak.

#### <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

Stel uw ontwikkelomgeving in en vul in het bestand app.config de verbindingsinformatie in, zoals beschreven in [Media Services ontwikkelen met .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Voorbeeld

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");

            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a id="output"></a>Uitvoer

In deze sectie worden drie voor beelden van uitvoer lagen geproduceerd door MES als resultaat van de code ring met de vooraf ingestelde voor instelling van de **adaptieve streaming** . 

### <a name="example-1"></a>Voorbeeld 1
Bron met hoogte "1080" en "29.970" framesnelheid produceert 6 video lagen:

|Laag|Hoogte|Breedte|Bitrate (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Voorbeeld 2
Bron met hoogte "720" en "23.970" framesnelheid produceert 5 video lagen:

|Laag|Hoogte|Breedte|Bitrate (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Voorbeeld 3
Bron met hoogte "360" en "29.970" framesnelheid produceert 3 video lagen:

|Laag|Hoogte|Breedte|Bitrate (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Overzicht van Media Services encoding](media-services-encode-asset.md)

