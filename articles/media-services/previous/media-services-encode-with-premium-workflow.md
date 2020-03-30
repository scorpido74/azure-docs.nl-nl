---
title: Geavanceerde codering met Media Encoder Premium Workflow | Microsoft Documenten
description: Meer informatie over het coderen met Media Encoder Premium Workflow. Codevoorbeelden zijn geschreven in C# en gebruiken de Media Services SDK voor .NET.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ca5de657ad45f53cff0cb01d5fe9cc412baf4533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792303"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Geavanceerd coderen met Media Encoder Premium Workflow
> [!NOTE]
> Media Encoder Premium Workflow media processor besproken in dit artikel is niet beschikbaar in China.
>
>

## <a name="overview"></a>Overzicht
Microsoft Azure Media Services introduceert de **Media Encoder Premium Workflow-mediaprocessor.** Deze processor biedt geavanceerde coderingsmogelijkheden voor uw premium on-demand workflows.

De volgende onderwerpen beschrijven details met betrekking tot **Media Encoder Premium Workflow:**

* [Indelingen die worden ondersteund door de Media Encoder Premium-workflow](media-services-premium-workflow-encoder-formats.md) – bespreekt de bestandsindelingen en codecs die worden ondersteund door **Media Encoder Premium Workflow.**
* [Overzicht en vergelijking van Azure on-demand media encoders](media-services-encode-asset.md) vergelijkt de coderingsmogelijkheden van **Media Encoder Premium Workflow** en Media **Encoder Standard**.

In dit artikel wordt uitgelegd hoe u coderen met **Media Encoder Premium Workflow** met behulp van .NET.

Voor coderingstaken voor de **Media Encoder Premium-werkstroom** is een afzonderlijk configuratiebestand vereist, een werkstroombestand genaamd. Deze bestanden hebben een .workflow-extensie en worden gemaakt met het gereedschap [Workflow Designer.](media-services-workflow-designer.md)

U [hier](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)ook de standaardwerkstroombestanden krijgen. De map bevat ook de beschrijving van deze bestanden.

De werkstroombestanden moeten als asset worden geüpload naar uw Media Services-account en dit item moet worden doorgegeven aan de coderingstaak.

## <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

Stel uw ontwikkelomgeving in en vul het app.config-bestand in met verbindingsgegevens, zoals beschreven in [de ontwikkeling van Media Services met .NET](media-services-dotnet-how-to-use.md). 

## <a name="encoding-example"></a>Voorbeeld van codering

In het volgende voorbeeld wordt uitgelegd hoe u coderen met **Media Encoder Premium Workflow.**

De volgende stappen worden uitgevoerd:

1. Maak een asset en upload een werkstroombestand.
2. Maak een asset en upload een bronmediabestand.
3. Download de mediaprocessor "Media Encoder Premium Workflow".
4. Maak een taak en een taak.

    In de meeste gevallen is de configuratietekenreeks voor de taak leeg (zoals in het volgende voorbeeld). Er zijn enkele geavanceerde scenario's (waarvoor u runtime-eigenschappen dynamisch moet instellen) in welk geval u een XML-tekenreeks aan de coderingstaak zou verstrekken. Voorbeelden van dergelijke scenario's zijn: het maken van een overlay, parallelle of sequentiële media stiksels, ondertiteling.
5. Voeg twee invoerelementen toe aan de taak.

   1. Eerste – de workflow asset.
   2. 2e – de video-asset.

      >[!NOTE]
      >Het werkstroomitem moet vóór het media-element aan de taak worden toegevoegd.
      De configuratietekenreeks voor deze taak moet leeg zijn.
   
6. Dien de coderingstaak in.

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderPremiumWorkflowSample
{
    class Program
    {
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _workflowFilePath =
            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

        private static readonly string _singleMP4InputFilePath =
            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");

        static void Main(string[] args)
        {

            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset);

        }

        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                processor,
                "",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(workflow);
            task.InputAssets.Add(video); // we add one asset
                                         // Add an output asset to contain the results of the job.
                                         // This output is specified as AssetCreationOptions.None, which
                                         // means the output asset is not encrypted.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                throw new Exception("\nExiting method due to job error.");
            }

            return job.OutputMediaAssets[0];
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
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
                    // LogJobStop(job.Id);
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

## <a name="need-help"></a>Hebt u hulp nodig?

U een ondersteuningsticket openen door te navigeren naar [nieuw ondersteuningsverzoek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
