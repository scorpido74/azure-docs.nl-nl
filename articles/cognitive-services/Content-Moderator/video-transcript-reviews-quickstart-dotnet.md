---
title: Videotranscriptiebeoordelingen maken met .NET - ContentModerator
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van videotranscriptiebeoordelingen met behulp van de Azure Cognitive Services Content Moderator SDK voor .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: b2d763454b86570b57a16fb9ae2107a2a2bcd23d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73744391"
---
# <a name="create-video-transcript-reviews-using-net"></a>Videotranscriptiebeoordelingen maken met .NET

In dit artikel vindt u informatie en codevoorbeelden waarmee u snel aan de slag met de [Content Moderator SDK met C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) om:

- Een videoreview maken voor menselijke moderators
- Een gemodereerd transcript toevoegen aan de beoordeling
- De beoordeling publiceren

## <a name="prerequisites"></a>Vereisten

- Meld u aan of maak een account aan op de site van het hulpprogramma Inhoudmoderator [als](https://contentmoderator.cognitive.microsoft.com/) u dit nog niet hebt gedaan.
- In dit artikel wordt ervan uitgegaan dat u de video hebt [gemodereerd](video-moderation-api.md) en [de videobeoordeling](video-reviews-quickstart-dotnet.md) hebt gemaakt in de beoordelingstool voor menselijke besluitvorming. U wilt nu gemodereerde videotranscripties toevoegen aan de beoordelingstool.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Ervoor zorgen dat uw API-sleutel de beoordelings-API kan aanroepen (Taakmaken)

Nadat u de vorige stappen hebt uitgevoerd, zou u twee Content Moderator-sleutels kunnen hebben als u vanuit de Azure-portal bent gestart.

Als u van plan bent de door Azure verstrekte API-sleutel in uw SDK-voorbeeld te gebruiken, volgt u de stappen in de sectie [De Azure-sleutel met de beoordelings-API gebruiken](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) zodat uw app de beoordelings-API kan aanroepen en beoordelingen kan maken.

Als u de gratis proefversie van de sleutel gebruikt die wordt gegenereerd door het beoordelingsprogramma, is uw account van het beoordelingsprogramma al op de hoogte van de sleutel, zodat er geen extra stappen zijn vereist.

## <a name="prepare-your-video-for-review"></a>Je video voorbereiden op beoordeling

Voeg het transcript toe aan een videobeoordeling. De video moet online worden gepubliceerd. Je hebt het streaming eindpunt nodig. Met het streaming-eindpunt kan de videospeler van de beoordelingstool de video afspelen.

![Miniatuur videodemo](images/ams-video-demo-view.PNG)

- Kopieer de **URL** op deze [demopagina van Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) voor de manifest-URL.

## <a name="create-your-visual-studio-project"></a>Het Visual Studio-project maken

1. Voeg een nieuw project van het type **Console-app (.NET Framework)** toe aan uw oplossing.

1. Geef het project **VideoTranscriptReviews**een naam .

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

Voeg de volgende privé-eigenschappen toe aan namespace **VideoTranscriptReviews**, **klasseprogramma**. Werk `AzureEndpoint` de `CMSubscriptionKey` velden en velden bij met de waarden van uw URL voor eindpunt en abonnementssleutel. Deze vindt u op het tabblad **Snel starten** van uw resource in de Azure-portal.

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

Voeg de volgende methodedefinitie toe aan namespace VideoTranscriptReviews, klasseprogramma.

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
> Er geldt een limiet voor het aantal aanvragen per seconde (RPS) voor de sleutel van de Content Moderator-service. Als u de limiet overschrijdt, veroorzaakt dit een uitzondering met foutcode 429 in de SDK.
>
> Een sleutel voor de gratis laag heeft een limiet van één RPS.

## <a name="add-transcript-to-video-review"></a>Transcript toevoegen aan videobeoordeling

U voegt een transcript toe aan een videobeoordeling met **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** heeft de volgende vereiste parameters:
1. Je contentmoderatorteam-id.
1. De video review ID terug door **CreateVideoReviews**.
1. Een **streamobject** dat het transcript bevat.

Het transcript moet in de WebVTT-indeling staan. Zie [WebVTT: De indeling voor webvideoteksttracks](https://www.w3.org/TR/webvtt1/)voor meer informatie.

> [!NOTE]
> Het programma maakt gebruik van een voorbeeldtranscript in de VTT-indeling. In een echte oplossing gebruikt u de Azure Media Indexer-service om een transcriptie van een video te [genereren.](https://docs.microsoft.com/azure/media-services/media-services-index-content)

Voeg de volgende methodedefinitie toe aan namespace VideotranscriptReviews, klasseprogramma.

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Een resultaat voor transcriptiemoderatie toevoegen aan videobeoordeling

Naast het toevoegen van een transcript aan een videoreview, voegt u ook het resultaat toe van het modereren van dat transcript. Dit doe je met **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Zie voor meer informatie de [API-naslaghandleiding](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** heeft de volgende vereiste parameters:
1. Een tekenreeks die een MIME-type bevat, dat moet worden "toepassing / json." 
1. De teamnaam van je Content Moderator.Your Content Moderator team name.
1. De video review ID terug door **CreateVideoReviews**.
1. Een IList\<TranscriptModerationBodyItem>. Een **TranscriptModerationBodyItem** heeft de volgende eigenschappen:
1. **Voorwaarden**. Een IList\<TranscriptModerationBodyItemTermsItemitem>. Een **TranscriptModerationBodyItemTermsItem** heeft de volgende eigenschappen:
1. **Index**. De nul-gebaseerde index van de term.
1. **Term**. Een tekenreeks die de term bevat.
1. **Tijdstempel.** Een tekenreeks die in enkele seconden de tijd bevat in het transcript waarop de termen worden gevonden.

Het transcript moet in de WebVTT-indeling staan. Zie [WebVTT: De indeling voor webvideoteksttracks](https://www.w3.org/TR/webvtt1/)voor meer informatie.

Voeg de volgende methodedefinitie toe aan namespace VideoTranscriptReviews, klasseprogramma. Met deze methode wordt een transcript verzonden naar de methode **ContentModeratorClient.TextModeration.ScreenText.** Het vertaalt ook het resultaat\<in een IList TranscriptModerationBodyItem>, en legt zich voor aan **AddVideoTranscriptModerationResult**.

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

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

Voeg de definitie van de **hoofdmethode** toe aan namespace VideoTranscriptReviews, klasseprogramma. Sluit ten slotte de klasse Programma en de naamruimte VideoTranscriptReviews.

> [!NOTE]
> Het programma maakt gebruik van een voorbeeldtranscript in de VTT-indeling. In een echte oplossing gebruikt u de Azure Media Indexer-service om een transcriptie van een video te [genereren.](https://docs.microsoft.com/azure/media-services/media-services-index-content)

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

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

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Navigeren naar uw videotranscriptiebeoordeling

Ga naar de videotranscriptiebeoordeling in je beoordelingstool voor inhoudsmoderator op het scherm>**Videotranscriptie**>**Transcript** **bekijken.**

U ziet de volgende functies:
- De twee regels transcript die u hebt toegevoegd
- De godslasteringsterm die is gevonden en gemarkeerd door de tekstmoderatieservice
- Als u een transcriptietekst selecteert, wordt de video gestart vanuit die tijdstempel

![Video transcript review voor menselijke moderators](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Volgende stappen

Download de [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) en de Visual [Studio-oplossing](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) voor deze en andere Content Moderator snelstarts voor .NET.

Meer informatie over het genereren van [videorecensies](video-reviews-quickstart-dotnet.md) in de beoordelingstool.

Bekijk de gedetailleerde tutorial over het ontwikkelen van een [complete video moderatie oplossing.](video-transcript-moderation-review-tutorial-dotnet.md)
