---
title: Een actief coderen met Media Encoder Standard met .NET | Microsoft Documenten
description: In dit artikel ziet u hoe u .NET gebruikt om een asset te coderen met Behulp van Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 259e32d55f25c4a146b7ff358eb503763dd5fab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016593"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Een asset coderen met Media Encoder Standard met .NET  

Coderingstaken zijn een van de meest voorkomende verwerkingsbewerkingen in Media Services. U maakt coderingstaken om mediabestanden van de ene codering naar de andere te converteren. Wanneer u codeert, u de ingebouwde Media Encoder van Media Services gebruiken. U ook gebruik maken van een encoder die wordt geleverd door een Media Services-partner; externe encoders zijn beschikbaar via de Azure Marketplace. 

In dit artikel ziet u hoe u .NET gebruiken om uw assets te coderen met Media Encoder Standard (MES). Media Encoder Standard is geconfigureerd met behulp van een van de encoders presets [hier](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)beschreven.

Het wordt aanbevolen om uw bronbestanden altijd te coderen in een adaptieve bitrate MP4-set en vervolgens de set om te zetten naar het gewenste formaat met behulp van de [Dynamische verpakking.](media-services-dynamic-packaging-overview.md) 

Als uw uitvoerasset is versleuteld met opslag, moet u het beleid voor het leveren van activa configureren. Zie [Beleid voor het configureren van asset delivery voor](media-services-dotnet-configure-asset-delivery-policy.md)meer informatie .

> [!NOTE]
> MES produceert een uitvoerbestand met een naam die de eerste 32 tekens van de invoerbestandsnaam bevat. De naam is gebaseerd op wat is opgegeven in het voorinstellingsbestand. Bijvoorbeeld 'Bestandsnaam': "{Basename}_{Index}{Extension}". {Basename} wordt vervangen door de eerste 32 tekens van de invoerbestandsnaam.
> 
> 

### <a name="mes-formats"></a>MES-indelingen
[Indelingen en codecs](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>MES-standaardinstellingen
Media Encoder Standard is geconfigureerd met behulp van een van de encoders presets [hier](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)beschreven.

### <a name="input-and-output-metadata"></a>Metagegevens voor invoer en uitvoer
Wanneer u een invoerelement (of activa) codeert met MES, krijgt u een uitvoerasset bij de succesvolle voltooiing van die codetaak. Het uitvoerelement bevat video, audio, miniaturen, manifest, enz.

Het uitvoerelement bevat ook een bestand met metagegevens over het invoerelement. De naam van het XML-bestand metagegevens heeft de volgende indeling: <asset_id>_metadata.xml (bijvoorbeeld 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), waarbij <asset_id> de AssetId-waarde van het invoeractief is. Het schema van deze invoermetagegevens XML wordt [hier](media-services-input-metadata-schema.md)beschreven.

Het uitvoerelement bevat ook een bestand met metagegevens over het uitvoerelement. De naam van het XML-bestand met ametjes heeft de volgende indeling: <source_file_name>_manifest.xml (bijvoorbeeld BigBuckBunny_manifest.xml). Het schema van deze uitvoermetagegevens XML wordt [hier](media-services-output-metadata-schema.md)beschreven .

Als u een van de twee metagegevensbestanden wilt onderzoeken, u een SAS-locator maken en het bestand downloaden naar uw lokale computer. U een voorbeeld vinden over het maken van een SAS-locator en het downloaden van een bestand met behulp van de Media Services .NET SDK-extensies.

## <a name="download-sample"></a>Voorbeeld downloaden
U een voorbeeld krijgen en uitvoeren dat laat zien hoe u vanaf [hier](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/)met MES coderen.

## <a name="net-sample-code"></a>.NET-voorbeeldcode

In het volgende codevoorbeeld wordt Media Services .NET SDK gebruikt om de volgende taken uit te voeren:

* Maak een coderingstaak.
* Hier krijg je een verwijzing naar de Media Encoder Standard encoder.
* Geef op om de [voorinstelling Adaptieve streaming](media-services-autogen-bitrate-ladder-with-mes.md) te gebruiken. 
* Voeg één coderingstaak toe aan de taak. 
* Geef het invoerelement op dat moet worden gecodeerd.
* Maak een uitvoerelement dat het gecodeerde element bevat.
* Voeg een gebeurtenishandler toe om de voortgang van de taak te controleren.
* Verzend de taak.

#### <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

Stel uw ontwikkelomgeving in en vul het app.config-bestand in met verbindingsgegevens, zoals beschreven in [de ontwikkeling van Media Services met .NET](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Voorbeeld 

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderStandardSample
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

            // Create a task with the encoding details, using a string preset.
            // In this case "Adaptive Streaming" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
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

## <a name="advanced-encoding-features-to-explore"></a>Geavanceerde coderingsfuncties om te verkennen
* [Miniaturen genereren](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Miniaturen genereren tijdens het coderen](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Video's bijsnijden tijdens het coderen](media-services-crop-video.md)
* [Coderingsvoorinstellingen aanpassen](media-services-custom-mes-presets-with-dotnet.md)
* [Een video met een afbeelding bedekken of markeren met een afbeelding](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
[Miniatuur genereren met Media Encoder Standard met .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Media Services Encoding Overview](media-services-encode-asset.md)

