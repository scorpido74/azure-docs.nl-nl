---
title: Gebruik Azure Media Content Moderator om mogelijke inhoud voor volwassenen en ongepaste te detecteren | Microsoft Docs
description: Met de media processor van Azure Media Content Moderator kunt u potentiële inhoud voor volwassenen en ongepaste in Video's detecteren.
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
ms.openlocfilehash: 70d824522e1ae71bd49050779ff37e821d560783
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954702"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Azure Media Content Moderator gebruiken om mogelijke inhoud voor volwassenen en ongepaste te detecteren 

> [!NOTE]
> De media processor van **Azure media content moderator** wordt buiten gebruik gesteld. Zie het onderwerp [oudere onderdelen](legacy-components.md) voor de datum van beëindiging.

## <a name="overview"></a>Overzicht
Met de **Azure media content moderator** media processor (MP) kunt u op uw Video's machine-ondersteunde toezicht gebruiken. Stel, u wilt mogelijk erotische en racistische inhoud detecteren in video's en de gemarkeerde inhoud laten controleren door uw team.

De **Azure Media Content moderator** -MP is momenteel beschikbaar als preview-versie.

Dit artikel bevat informatie over **Azure Media Content moderator** en laat zien hoe u deze kunt gebruiken met Media Services SDK voor .net.

## <a name="content-moderator-input-files"></a>Content Moderator invoer bestanden
Video bestanden. Momenteel worden de volgende indelingen ondersteund: MP4, MOV en WMV.

## <a name="content-moderator-output-files"></a>Uitvoer bestanden Content Moderator
De gecontroleerde uitvoer in de JSON-indeling omvat automatisch gedetecteerde opnamen en keyframes. De keyframes worden geretourneerd met betrouwbaarheids scores voor mogelijke inhoud voor volwassenen of ongepaste. Ze bevatten ook een Booleaanse vlag die aangeeft of een beoordeling wordt aanbevolen. Met de vlag beoordelings aanbeveling worden waarden toegewezen op basis van de interne drempel waarden voor volwassene en ongepaste scores.

## <a name="elements-of-the-output-json-file"></a>Elementen van het JSON-uitvoer bestand

De taak produceert een JSON-uitvoer bestand dat meta gegevens bevat over gedetecteerde opnamen en keyframes en of ze inhoud voor volwassenen of ongepaste bevatten.

De JSON van de uitvoer bevat de volgende elementen:

### <a name="root-json-elements"></a>Wortel-JSON-elementen

| Element | Description |
| --- | --- |
| versie |De versie van Content Moderator. |
| lijnen |' Ticks ' per seconde van de video. |
| offset |De tijd-offset voor tijdstempels. In versie 1,0 van video-Api's is deze waarde altijd 0. Deze waarde kan in de toekomst worden gewijzigd. |
| snelheid |Aantal frames per seconde video. |
| breedte |De breedte van het video kader voor de uitvoer, in pixels.|
| hoogte |De hoogte van het video kader voor de uitvoer, in pixels.|
| totalDuration |De duur van de invoer video, in Ticks. |
| [fragmenten](#fragments-json-elements) |De meta gegevens worden gesegmenteerd in verschillende segmenten die fragmenten worden genoemd. Elk fragment is een automatisch gedetecteerde opname met een start, duur, interval nummer en gebeurtenis (sen). |

### <a name="fragments-json-elements"></a>Fragmenten JSON-elementen

|Element|Description|
|---|---|
| start |De begin tijd van de eerste gebeurtenis in Ticks. |
| duur |De lengte van het fragment, in Ticks. |
| interval |Het interval van elke gebeurtenis vermelding in het fragment, in Ticks. |
| [evenementen](#events-json-elements) |Elke gebeurtenis vertegenwoordigt een clip en elke clip bevat keyframes die binnen deze tijds duur zijn gedetecteerd en bijgehouden. Het is een matrix met gebeurtenissen. De buitenste matrix vertegenwoordigt een tijdsinterval. De binnenste matrix bestaat uit 0 of meer gebeurtenissen die hebben plaatsgevonden gedurende de interval.|

### <a name="events-json-elements"></a>JSON-elementen van gebeurtenissen

|Element|Description|
|---|---|
| reviewRecommended | `true`of `false` afhankelijk van of de **AdultScore** of **racyScore** de interne drempel waarden overschrijdt. |
| adultScore | Betrouwbaarheids score voor mogelijke inhoud voor volwassenen, op een schaal van 0,00 tot 0,99. |
| racyScore | Betrouw bare score voor mogelijke ongepaste-inhoud, op een schaal van 0,00 tot 0,99. |
| TabIndex | index van het frame op een schaal van de eerste frame-index naar de laatste frame-index. |
| tijdstempel | De locatie van het frame in Ticks. |
| shotIndex | De index van de bovenliggende opname. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Snelstartgids voor content toezicht en voorbeeld uitvoer

### <a name="task-configuration-preset"></a>Taak configuratie (voor instelling)
Wanneer u een taak met **Azure Media Content moderator**maakt, moet u een voor instelling voor de configuratie opgeven. De volgende configuratie voorinstelling is alleen bedoeld voor inhouds toezicht.

```json
{
    "version":"2.0"
}
```

### <a name="net-code-sample"></a>Voor beeld van .NET-code

In het volgende voor beeld van een .NET-code wordt gebruikgemaakt van de Media Services .NET SDK om een Content Moderator taak uit te voeren. Er wordt een Asset van Media Services gebruikt als invoer die de video bevat die moet worden gematigd.
Bekijk de [Content moderator video Quick](../../cognitive-services/Content-Moderator/video-moderation-api.md) start voor de volledige bron code en het Visual Studio-project.


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
```

Bekijk de Quick Start van [Content moderator video](../../cognitive-services/Content-Moderator/video-moderation-api.md)voor de volledige bron code en het Visual Studio-project.

### <a name="json-output"></a>JSON-uitvoer

Het volgende voor beeld van een Content Moderator JSON-uitvoer is afgekapt.

> [!NOTE]
> Locatie van een keyframe in seconden = tijds tempel/tijd schaal

```json
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

[Demo's Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de video-moderator van Content Moderator [en de oplossing](../../cognitive-services/Content-Moderator/video-moderation-human-review.md).

Haal de volledige bron code en het Visual Studio-project op uit de [Snelstartgids voor video toezicht](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Meer informatie over het genereren van [video beoordelingen](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) uit uw gecontroleerde uitvoer en [gematige transcripten](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) in .net.

Bekijk de gedetailleerde zelf studie voor .NET- [video toezicht en-beoordeling](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
