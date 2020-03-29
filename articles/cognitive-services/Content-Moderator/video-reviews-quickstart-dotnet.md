---
title: Videoreviews maken met .NET - Content Moderator
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u informatie en codevoorbeelden waarmee u snel aan de slag met de Content Moderator SDK met C# om videobeoordelingen te maken.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 7130ed43183d64b00f8f5ef1697b9a3b456ad396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72931679"
---
# <a name="create-video-reviews-using-net"></a>Videorecensies maken met .NET

In dit artikel vindt u informatie en codevoorbeelden waarmee u snel aan de slag met de [Content Moderator SDK met C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) om:

- Een videoreview maken voor menselijke moderators
- Frames toevoegen aan een beoordeling
- Download de frames voor de beoordeling
- De status en details van de beoordeling opvragen
- De beoordeling publiceren

## <a name="prerequisites"></a>Vereisten

- Meld u aan of maak een account aan op de site van het [hulpprogramma Inhoudsmoderator.](https://contentmoderator.cognitive.microsoft.com/)
- In dit artikel wordt ervan uitgegaan dat u de video hebt [gemodereerd (zie snel starten)](video-moderation-api.md) en de reactiegegevens hebt. Je hebt het nodig voor het maken van frame-gebaseerde beoordelingen voor menselijke moderators.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Instellen dat uw API-sleutel de beoordelings-API kan aanroepen voor het maken van de beoordeling

Nadat u de vorige stappen hebt uitgevoerd, zou u twee Content Moderator-sleutels kunnen hebben als u vanuit de Azure-portal bent gestart.

Als u van plan bent de door Azure verstrekte API-sleutel in uw SDK-voorbeeld te gebruiken, volgt u de stappen in de sectie [De Azure-sleutel met de beoordelings-API gebruiken](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) zodat uw app de beoordelings-API kan aanroepen en beoordelingen kan maken.

Als u de gratis proefversie van de sleutel gebruikt die wordt gegenereerd door het beoordelingsprogramma, is uw account van het beoordelingsprogramma al op de hoogte van de sleutel, zodat er geen extra stappen zijn vereist.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Uw video en de videoframes voorbereiden voor beoordeling

De video- en voorbeeldvideoframes die u wilt bekijken, moeten online worden gepubliceerd omdat u hun URL's nodig hebt.

> [!NOTE]
> Het programma maakt gebruik van handmatig opgeslagen screenshots van de video met willekeurige adult / racy scores om het gebruik van de review API te illustreren. In een echte situatie gebruikt u de [videomoderatie-uitvoer](video-moderation-api.md#run-the-program-and-review-the-output) om afbeeldingen te maken en scores toe te wijzen. 

Voor de video hebt u een streaming-eindpunt nodig, zodat de beoordelingstool de video afspeelt in de spelersweergave.

![Miniatuur videodemo](images/ams-video-demo-view.PNG)

- Kopieer de **URL** op deze [demopagina van Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) voor de manifest-URL.

Gebruik voor de videoframes (afbeeldingen) de volgende afbeeldingen:

![Miniatuur van videolijsten 1](images/ams-video-frame-thumbnails-1.PNG) | ![Miniatuur van videolijsten 2](images/ams-video-frame-thumbnails-2.PNG) | ![Miniatuur van videolijsten 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Frame 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Frame 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Frame 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Het Visual Studio-project maken

1. Voeg een nieuw project van het type **Console-app (.NET Framework)** toe aan uw oplossing.

1. Geef het project **VideoReviews**een naam .

1. Selecteer dit project als het enige opstartproject voor de oplossing.

### <a name="install-required-packages"></a>De vereiste pakketten installeren

Installeer de volgende NuGet-pakketten voor het TermLists-project.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>De using-instructies van het programma bijwerken

Wijzig het programma met behulp van verklaringen als volgt.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Private-eigenschappen toevoegen

Voeg de volgende privé-eigenschappen toe aan namespace **VideoReviews**, **klasseprogramma**. Werk `AzureEndpoint` de `CMSubscriptionKey` velden en velden bij met de waarden van uw URL voor eindpunt en abonnementssleutel. Deze vindt u op het tabblad **Snel starten** van uw resource in de Azure-portal.


```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Object Inhoudmoderator-client maken

Voeg de volgende methodedefinitie toe aan namespace **VideoReviews**, **klasseprogramma**.

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>Een videobeoordeling maken

Maak een videoreview met **ContentModeratorClient.Reviews.CreateVideoReviews**. Zie voor meer informatie de [API-naslaghandleiding](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** heeft de volgende vereiste parameters:
1. Een tekenreeks die een MIME-type bevat, dat moet worden "toepassing / json." 
1. De teamnaam van je Content Moderator.Your Content Moderator team name.
1. Een **iList\<CreateVideoReviewsBodyItem>** object. Elk **object CreateVideoReviewsBodyItem** vertegenwoordigt een videobeoordeling. Met deze quickstart wordt één beoordeling tegelijk gemaakt.

**CreateVideoReviewsBodyItem** heeft verschillende eigenschappen. U stelt ten minste de volgende eigenschappen in:
- **Inhoud**. De URL van de video die moet worden beoordeeld.
- **ContentId**. Een id die u wilt toewijzen aan de videobeoordeling.
- **Status**. Stel de waarde in op 'Niet gepubliceerd'. Als u deze niet instelt, wordt deze standaard ingesteld op 'In behandeling', wat betekent dat de videobeoordeling wordt gepubliceerd en in behandeling is bij een menselijke beoordeling. Zodra een videobeoordeling is gepubliceerd, u er geen videoframes, een transcript of een transcriptmoderatieresultaat meer aan toevoegen.

> [!NOTE]
> **CreateVideoReviews** retourneert\<een iList-tekenreeks>. Elk van deze tekenreeksen bevat een ID voor een videobeoordeling. Deze id's zijn GUIDs en zijn niet hetzelfde als de waarde van de eigenschap **ContentId.** 

Voeg de volgende methodedefinitie toe aan namespace VideoReviews, klasseprogramma.

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> De sleutel van uw Content Moderator-service heeft een limiet voor het aantal aanvragen per seconde (RPS) en als u die limiet overschrijdt, genereert de SDK een uitzondering met foutcode 429.
>
> Een sleutel voor de gratis laag heeft een limiet van één RPS.

## <a name="add-video-frames-to-the-video-review"></a>Videoframes toevoegen aan de videobeoordeling

U voegt videoframes toe aan een videobeoordeling met **ContentModeratorClient.Reviews.AddVideoFrameUrl** (als uw videoframes online worden gehost) of **ContentModeratorClient.Reviews.AddVideoFrameStream** (als uw videoframes lokaal worden gehost). Deze quickstart gaat ervan uit dat uw videoframes online worden gehost en maakt daarom gebruik van **AddVideoFrameUrl**. Zie voor meer informatie de [API-naslaghandleiding](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** heeft de volgende vereiste parameters:
1. Een tekenreeks die een MIME-type bevat, dat moet worden "toepassing / json."
1. De teamnaam van je Content Moderator.Your Content Moderator team name.
1. De video review ID terug door **CreateVideoReviews**.
1. Een **iList\<VideoFrameBodyItem>** object. Elk **Object VideoFrameBodyItem** vertegenwoordigt een videoframe.

**VideoFrameBodyItem** heeft de volgende eigenschappen:
- **Tijdstempel.** Een tekenreeks die in enkele seconden de tijd bevat in de video waaruit het videoframe is genomen.
- **FrameImage**. De URL van het videoframe.
- **Metagegevens**. Een ilist\<videoframebodyitemmetadataitem>. **VideoFrameBodyItemMetadataItem** is gewoon een sleutel/waardepaar. Geldige sleutels zijn:
- **reviewAanbevolen**. True als een menselijke beoordeling van het videoframe wordt aanbevolen.
- **adultScore**. Een waarde van 0 tot 1 die de ernst van inhoud voor volwassenen in het videoframe beoordeelt.
- **a**. True als de video inhoud voor volwassenen bevat.
- **racyScore**. Een waarde van 0 tot 1 die de ernst van pikante inhoud in het videoframe beoordeelt.
- **r**. True als het videoframe pikante inhoud bevat.
- **ReviewerResultTags**. Een iList\<videoframebodyitemreviewerresulttagsitem>. **VideoFrameBodyItemReviewerResultTagsItem** is gewoon een sleutel/waardepaar. Een toepassing kan deze tags gebruiken om videoframes te ordenen.

> [!NOTE]
> Deze quickstart genereert willekeurige waarden voor de eigenschappen **adultScore** en **racyScore.** In een productietoepassing haalt u deze waarden uit de [videomoderatieservice](video-moderation-api.md)die is geïmplementeerd als Azure Media Service.

Voeg de volgende methodedefinities toe aan namespace VideoReviews, klasseprogramma.

```csharp
<summary>
/// Create a video frame to add to a video review after the video review is created.
/// </summary>
/// <param name="url">The URL of the video frame image.</param>
/// <returns>The video frame.</returns>
private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
{
    // We generate random "adult" and "racy" scores for the video frame.
    Random rand = new Random();

    var frame = new VideoFrameBodyItem
    {
        // The timestamp is measured in milliseconds. Convert from seconds.
        Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
        FrameImage = url,

        Metadata = new List<VideoFrameBodyItemMetadataItem>
        {
            new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
            new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("a", "false"),
            new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("r", "false")
        },

        ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
        {
            new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
        }
    };

    return frame;
}
```

```csharp
/// <summary>
/// Add a video frame to the indicated video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="url">The URL of the video frame image.</param>
static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
{
    Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

    var frames = new List<VideoFrameBodyItem>()
    {
        CreateFrameToAddToReview(url, timestamp_seconds)
    };
        
    client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

    Thread.Sleep(throttleRate);
```

## <a name="get-video-frames-for-video-review"></a>Videoframes bekijken voor videobeoordeling

U de videoframes voor een videoreview krijgen met **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** heeft de volgende vereiste parameters:
1. De teamnaam van je Content Moderator.Your Content Moderator team name.
1. De video review ID terug door **CreateVideoReviews**.
1. De nul-gebaseerde index van het eerste videoframe te krijgen.
1. Het aantal videoframes dat u moet krijgen.

Voeg de volgende methodedefinitie toe aan namespace VideoReviews, klasseprogramma.

```csharp
/// <summary>
/// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
static void GetFrames(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

    Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="get-video-review-information"></a>Informatie over videobeoordeling

Je krijgt informatie voor een videoreview met **ContentModeratorClient.Reviews.GetReview**. **GetReview** heeft de volgende vereiste parameters:
1. De teamnaam van je Content Moderator.Your Content Moderator team name.
1. De video review ID terug door **CreateVideoReviews**.

Voeg de volgende methodedefinitie toe aan namespace VideoReviews, klasseprogramma.

```csharp
/// <summary>
/// Get the information for the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void GetReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

    var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>Videobeoordeling publiceren

U publiceert een videoreview met **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** heeft de volgende vereiste parameters:
1. De teamnaam van je Content Moderator.Your Content Moderator team name.
1. De video review ID terug door **CreateVideoReviews**.

Voeg de volgende methodedefinitie toe aan namespace VideoReviews, klasseprogramma.

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>Alles samenvoegen

Voeg de definitie van de **hoofdmethode** toe aan namespace VideoReviews, klasseprogramma. Sluit ten slotte de klasse Programma en de naamruimte VideoReviews.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
        var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
        var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

        // Add the frames from 17, 64, and 144 seconds.
        AddFrame(client, review_id, frame1_url, "17");
        AddFrame(client, review_id, frame2_url, "64");
        AddFrame(client, review_id, frame3_url, "144");

        // Get frames information and show
        GetFrames(client, review_id);
        GetReview(client, review_id);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>Het programma uitvoeren en de uitvoer controleren
Wanneer u de toepassing uitvoert, ziet u een uitvoer op de volgende regels:

```json
Creating a video review.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "VideoFrames": [
    {
        "Timestamp": "17000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.808312381528463"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.846378884206702"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "64000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.576078300166912"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.244768953064815"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "144000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.664480847150311"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.933817870418456"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
        ]
    }
    ]
}

Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "SubTeam": "public",
    "Status": "UnPublished",
    "ReviewerResultTags": [],
    "CreatedBy": "testreview6",
    "Metadata": [
    {
        "Key": "FrameCount",
        "Value": "3"
    }
    ],
    "Type": "Video",
    "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    "ContentId": "review1",
    "CallbackEndpoint": null
}

Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="check-out-your-video-review"></a>Bekijk je videoreview

Tot slot zie je de videoreview in je contentmoderator-beoordelingstool-account op het scherm>**Video** **bekijken.**

![Videoreview voor menselijke moderators](images/ams-video-review.PNG)

## <a name="next-steps"></a>Volgende stappen

Download de [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) en de Visual [Studio-oplossing](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) voor deze en andere Content Moderator snelstarts voor .NET.

Meer informatie over het toevoegen van [transcriptiemoderatie](video-transcript-moderation-review-tutorial-dotnet.md) aan de videobeoordeling. 

Bekijk de gedetailleerde tutorial over het ontwikkelen van een [complete video moderatie oplossing.](video-transcript-moderation-review-tutorial-dotnet.md)
