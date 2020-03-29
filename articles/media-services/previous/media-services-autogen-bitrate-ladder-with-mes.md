---
title: Media encoderstandaard gebruiken om automatisch een bitrate-ladder te genereren - Azure | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u Media Encoder Standard (MES) gebruiken om een bitrate-ladder automatisch te genereren op basis van de invoerresolutie en bitrate.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896019"
---
#  <a name="use-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Gebruik Media Encoder Standard om automatisch een bitrate ladder te genereren  

## <a name="overview"></a>Overzicht

In dit artikel ziet u hoe u Media Encoder Standard (MES) gebruiken om een bitrateladder (bitrate-resolutieparen) automatisch te genereren op basis van de invoerresolutie en bitrate. De automatisch gegenereerde voorinstelling zal nooit hoger zijn dan de invoerresolutie en bitrate. Als de ingang bijvoorbeeld 720p bij 3 Mbps is, blijft de uitvoer op zijn best 720p en begint het met snelheden lager dan 3 Mbps.

### <a name="encoding-for-streaming-only"></a>Codering voor alleen streaming

Als het uw bedoeling is om uw bronvideo alleen te coderen voor streaming, moet u de voorinstelling 'Adaptieve streaming' gebruiken bij het maken van een coderingstaak. Bij het gebruik van de **Adaptive Streaming** preset zal de MES-encoder op intelligente wijze een bitrate-ladder afdekken. U echter geen controle hebben over de coderingskosten, omdat de service bepaalt hoeveel lagen u moet gebruiken en bij welke resolutie. U voorbeelden zien van uitvoerlagen die door MES zijn geproduceerd als gevolg van codering met de **voorinstelling Adaptieve streaming** aan het einde van dit artikel. De uitvoerasset bevat MP4-bestanden waarbij audio en video niet worden interleaved.

### <a name="encoding-for-streaming-and-progressive-download"></a>Codering voor streaming en progressief downloaden

Als het uw bedoeling is om uw bronvideo te coderen voor streaming en om MP4-bestanden te produceren voor progressief downloaden, moet u de voorinstelling 'Content Adaptive Multiple Bitrate MP4' gebruiken bij het maken van een coderingstaak. Bij het gebruik van de **content adaptive multiple bitrate MP4-voorinstelling** past de MES-encoder dezelfde coderingslogica toe als hierboven, maar nu bevat het uitvoerelement MP4-bestanden waarbij audio en video worden interleaved. U een van deze MP4-bestanden (bijvoorbeeld de hoogste bitrate-versie) gebruiken als een progressief downloadbestand.

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Coderen met Media Services .NET SDK

In het volgende codevoorbeeld wordt Media Services .NET SDK gebruikt om de volgende taken uit te voeren:

- Maak een coderingstaak.
- Hier krijg je een verwijzing naar de Media Encoder Standard encoder.
- Voeg een coderingstaak toe aan de taak en geef op om de **voorinstelling Adaptieve streaming** te gebruiken. 
- Maak een uitvoerelement dat het gecodeerde element bevat.
- Voeg een gebeurtenishandler toe om de voortgang van de taak te controleren.
- Verzend de taak.

#### <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

Stel uw ontwikkelomgeving in en vul het app.config-bestand in met verbindingsgegevens, zoals beschreven in [de ontwikkeling van Media Services met .NET](media-services-dotnet-how-to-use.md). 

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

## <a name="output"></a><a id="output"></a>Output

In deze sectie worden drie voorbeelden weergegeven van uitvoerlagen die door MES zijn geproduceerd als gevolg van codering met de **voorinstelling Adaptive Streaming.** 

### <a name="example-1"></a>Voorbeeld 1
Bron met hoogte "1080" en framerate "29.970" produceert 6 videolagen:

|Laag|Height|Breedte|Bitrate(kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Voorbeeld 2
Bron met hoogte "720" en framerate "23.970" produceert 5 videolagen:

|Laag|Height|Breedte|Bitrate(kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Voorbeeld 3
Bron met hoogte "360" en framerate "29.970" produceert 3 videolagen:

|Laag|Height|Breedte|Bitrate(kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Overzicht van mediaservices codering](media-services-encode-asset.md)

