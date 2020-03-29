---
title: Azure Media Content Moderator gebruiken om mogelijke inhoud voor volwassenen en racy te detecteren | Microsoft Documenten
description: Azure Media Content Moderator mediaprocessor helpt bij het detecteren van potentiële inhoud voor volwassenen en racy in video's.
services: media-services
documentationcenter: ''
author: sanjeev3
manager: mikemcca
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: 83fe7867a3128ac82597c028452863a1ad681ace
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914310"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Azure Media Content Moderator gebruiken om mogelijke inhoud voor volwassenen en racy te detecteren 

> [!NOTE]
> De **mediaprocessor Azure Media Content Moderator** wordt buiten gebruik gesteld. Zie voor de pensioendatum het onderwerp [oudere onderdelen.](legacy-components.md)

## <a name="overview"></a>Overzicht
Met de **MediaProcessor** (Mp) van Azure Media Content Moderator u machineondersteunde moderatie voor uw video's gebruiken. Stel, u wilt mogelijk erotische en racistische inhoud detecteren in video's en de gemarkeerde inhoud laten controleren door uw team.

Het MP **Voor azure media-inhoudsmoderator** bevindt zich momenteel in Preview.

In dit artikel vindt u informatie over **Azure Media Content Moderator** en wordt uitgelegd hoe u deze gebruiken met Media Services SDK voor .NET.

## <a name="content-moderator-input-files"></a>Invoerbestanden voor inhoudsmoderator
Videobestanden. Momenteel worden de volgende indelingen ondersteund: MP4, MOV en WMV.

## <a name="content-moderator-output-files"></a>Uitvoerbestanden voor inhoudsmoderator
De gemodereerde uitvoer in de JSON-indeling omvat automatisch gedetecteerde opnamen en hoofdframes. De hoofdframes worden geretourneerd met vertrouwen scores voor mogelijke volwassen of racy inhoud. Ze bevatten ook een booleaanse vlag die aangeeft of een beoordeling wordt aanbevolen. De vlag van de aanbeveling voor beoordeling krijgt waarden toegewezen op basis van de interne drempelwaarden voor scores voor volwassenen en racy.

## <a name="elements-of-the-output-json-file"></a>Elementen van het JSON-bestand uitvoer

De taak produceert een JSON-uitvoerbestand met metagegevens over gedetecteerde opnamen en hoofdframes en of deze inhoud voor volwassenen of racy bevatten.

De uitvoer JSON bevat de volgende elementen:

### <a name="root-json-elements"></a>Json-elementen voor root

| Element | Beschrijving |
| --- | --- |
| versie |De versie van Content Moderator. |
| Tijdschaal |"Teken" per seconde van de video. |
| offset |De tijd-offset voor tijdstempels. In versie 1.0 van Video API's is deze waarde altijd 0. Deze waarde kan in de toekomst veranderen. |
| Framerate |Aantal frames per seconde video. |
| breedte |De breedte van het uitvoervideoframe, in pixels.|
| hoogte |De hoogte van het uitvoervideoframe, in pixels.|
| totaalDuur |De duur van de invoervideo, in 'teken'. |
| [Fragmenten](#fragments-json-elements) |De metagegevens worden samengevoegd in verschillende segmenten, fragmenten genaamd. Elk fragment is een automatisch gedetecteerde opname met een begin, duur, intervalnummer en gebeurtenis(en). |

### <a name="fragments-json-elements"></a>Fragmenten JSON-elementen

|Element|Beschrijving|
|---|---|
| start |De begintijd van de eerste gebeurtenis in 'teken'. |
| duur |De lengte van het fragment, in 'teken'. |
| interval |Het interval van elke gebeurtenisvermelding in het fragment, in 'teken'. |
| [Gebeurtenissen](#events-json-elements) |Elke gebeurtenis vertegenwoordigt een clip en elke clip bevat hoofdframes gedetecteerd en bijgehouden binnen die tijdsduur. Het is een scala aan gebeurtenissen. De buitenste matrix vertegenwoordigt een tijdsinterval. De binnenste matrix bestaat uit 0 of meer gebeurtenissen die hebben plaatsgevonden gedurende de interval.|

### <a name="events-json-elements"></a>Evenementen JSON elementen

|Element|Beschrijving|
|---|---|
| reviewAanbevolen | `true`of `false` afhankelijk van of de **adultScore** of **racyScore** de interne drempels overschrijden. |
| adultScore (adultScore) | Betrouwbaarheidsscore voor mogelijke inhoud voor volwassenen, op een schaal van 0,00 tot 0,99. |
| racyScore racyScore | Betrouwbaarheidsscore voor mogelijke pikante inhoud, op een schaal van 0,00 tot 0,99. |
| Index | indexvan het frame op een schaal van de eerste frameindex tot de laatste frameindex. |
| tijdstempel | De locatie van het frame in 'teken'. |
| shotIndex | De index van het ouderschot. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Snel start- en voorbeelduitvoer van inhoudsbeheer

### <a name="task-configuration-preset"></a>Taakconfiguratie (vooraf ingesteld)
Wanneer u een taak maakt met **Azure Media Content Moderator,** moet u een configuratie-voorinstelling opgeven. De volgende configuratie-voorinstelling is alleen voor inhoudsbeheer.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>.NET-codevoorbeeld

In het volgende voorbeeld van .NET-code wordt de Functie Media Services .NET SDK gebruikt om een taak Inhoudsmoderator uit te voeren. Er is een mediaservices-asset nodig als de invoer die de video bevat die moet worden gemodereerd.
Bekijk de [video Content Moderator snel start](../../cognitive-services/Content-Moderator/video-moderation-api.md) voor de volledige broncode en het Visual Studio project.


```csharp
    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            ErrorDetail error = job.Tasks.First().ErrorDetails.First();
            Console.WriteLine(string.Format("Error: {0}. {1}",
            error.Code,
            error.Message));
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

For the full source code and the Visual Studio project, check out the [Content Moderator video quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### JSON output

The following example of a Content Moderator JSON output was truncated.

> [!NOTE]
> Location of a keyframe in seconds = timestamp/timescale

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }
```

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwante koppelingen
[Overzicht van Azure Media Services Analytics](media-services-analytics-overview.md)

[Azure Media Analytics-demo's](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [oplossing voor videobeheer en -beoordeling van](../../cognitive-services/Content-Moderator/video-moderation-human-review.md)contentmoderator .

Haal de volledige broncode en het Visual Studio-project snel van de [videomoderatie.](../../cognitive-services/Content-Moderator/video-moderation-api.md) 

Meer informatie over het genereren van [videorecensies](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) uit uw gemodereerde uitvoer en [matige transcripties](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) in .NET.

Bekijk de gedetailleerde .NET [video moderatie en review tutorial](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
