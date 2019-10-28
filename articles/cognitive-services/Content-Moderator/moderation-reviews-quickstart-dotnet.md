---
title: Beoordelingen maken met behulp van .NET - Content Moderator
titleSuffix: Azure Cognitive Services
description: Beoordelingen maken met behulp van de Azure Content Moderator SDK voor .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 38e4d33d453bae4e6dbb5b87ae9a5023cc8aa671
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931714"
---
# <a name="create-human-reviews-net"></a>Mensen beoordelingen maken (.NET)

Beoordelingen van de winkel en de weer gave van inhoud voor menselijke moderators om te beoordelen. Wanneer een gebruiker een beoordeling voltooit, worden de resultaten verzonden naar een opgegeven eind punt voor terugbellen. Deze hand leiding bevat informatie en code voorbeelden waarmee u aan de slag kunt met de [Content moderator SDK voor .net voor](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) het volgende:

- Een set beoordelingen voor menselijke moderators maken
- De status van bestaande beoordelingen voor menselijke moderators ophalen

## <a name="prerequisites"></a>Vereisten

- Meld u aan of maak een account op de site van het Content Moderator [controle programma](https://contentmoderator.cognitive.microsoft.com/) .

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Instellen dat uw API-sleutel de beoordelings-API kan aanroepen voor het maken van de beoordeling

Nadat u de vorige stappen hebt uitgevoerd, zou u twee Content Moderator-sleutels kunnen hebben als u vanuit de Azure-portal bent gestart.

Als u van plan bent de door Azure verstrekte API-sleutel in uw SDK-voorbeeld te gebruiken, volgt u de stappen in de sectie [De Azure-sleutel met de beoordelings-API gebruiken](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) zodat uw app de beoordelings-API kan aanroepen en beoordelingen kan maken.

Als u de gratis proefversie van de sleutel gebruikt die wordt gegenereerd door het beoordelingsprogramma, is uw account van het beoordelingsprogramma al op de hoogte van de sleutel, zodat er geen extra stappen zijn vereist.

## <a name="create-your-visual-studio-project"></a>Het Visual Studio-project maken

1. Voeg een nieuw project van het type **Console-app (.NET Framework)** toe aan uw oplossing.

   Geef het project de naam **CreateReviews** in de voorbeeldcode.

1. Selecteer dit project als het enige opstartproject voor de oplossing.

### <a name="install-required-packages"></a>De vereiste pakketten installeren

Installeer de volgende NuGet-pakketten:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>De using-instructies van het programma bijwerken

Pas de using-instructies van het programma aan.


```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>De Content Moderator-client maken

Voeg de volgende code toe om een Content Moderator-client voor uw abonnement te maken. Werk de velden `AzureEndpoint` en `CMSubscriptionKey` bij met de waarden van uw eind punt-URL en abonnements sleutel. U vindt deze in het Azure Portal op het tabblad **Quick Start** van uw resource.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureEndpoint;
        return client;
    }
}
```

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Maak een klasse om informatie over interne inhoud te koppelen aan een beoordelings-id

Voeg de volgende klasse toe aan de klasse **Program**. Met deze klasse kunt u de beoordelings-id koppelen aan de id van uw interne inhoud voor het item.

```csharp
/// <summary>
    /// Associates the review ID (assigned by the service) to the internal
    /// content ID of the item.
    /// </summary>
    public class ReviewItem
    {
        /// <summary>
        /// The media type for the item to review.
        /// </summary>
        public string Type;

        /// <summary>
        /// The URL of the item to review.
        /// </summary>
        public string Url;

        /// <summary>
        /// The internal content ID for the item to review.
        /// </summary>
        public string ContentId;

        /// <summary>
        /// The ID that the service assigned to the review.
        /// </summary>
        public string ReviewId;
    }
```

### <a name="initialize-application-specific-settings"></a>Toepassingsspecifieke instellingen initialiseren

> [!NOTE]
> De sleutel van uw Content Moderator-service heeft een limiet voor het aantal aanvragen per seconde (RPS). Als u die limiet overschrijdt, genereert de SDK een uitzondering met foutcode 429.
>
> Een sleutel voor de gratis laag heeft een limiet van één RPS.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Voeg de volgende constanten toe aan de klasse **Program** in Program.cs

```csharp
/// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Image List API.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const double latencyDelay = 45;

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";
```

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Voeg de volgende constanten en statische velden toe aan de klasse **Program** in Program.cs

Werk deze waarden bij met informatie die specifiek is voor uw abonnement en team.

> [!NOTE]
> U stelt de constante TeamName in op de naam die u hebt gebruikt tijdens het maken van uw abonnement op het [Content Moderator-beoordelingsprogramma](https://contentmoderator.cognitive.microsoft.com/). U haalt de TeamName op uit de sectie **Credentials** in het menu **Instellingen** (het tandwielpictogram).
>
> De naam van uw team is de waarde van het veld **Id** veld in de sectie **API**.

```csharp
/// <summary>
/// The name of the team to assign the review to.
/// </summary>
/// <remarks>This must be the team name you used to create your
/// Content Moderator account. You can retrieve your team name from
/// the Content Moderator web site. Your team name is the Id associated
/// with your subscription.</remarks>
private const string TeamName = "YOUR REVIEW TEAM ID";

/// <summary>
/// The optional name of the subteam to assign the review to.
/// </summary>
private const string Subteam = null;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team. 
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "YOUR API ENDPOINT";

/// <summary>
/// The media type for the item to review.
/// </summary>
/// <remarks>Valid values are "image", "text", and "video".</remarks>
private const string MediaType = "image";

/// <summary>
/// The URLs of the images to create review jobs for.
/// </summary>
private static readonly string[] ImageUrls = new string[] {
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg"
    // add more if you want
};

/// <summary>
/// The metadata key to initially add to each review item.
/// </summary>
private const string MetadataKey = "sc";

/// <summary>
/// The metadata value to initially add to each review item.
/// </summary>
private const string MetadataValue = "true";
```

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Voeg de volgende statische velden toe aan de klasse **Program** in Program.cs

Gebruik deze velden voor het volgen van de status van de toepassing.

```csharp
/// <summary>
/// A static reference to the text writer to use for logging.
/// </summary>
private static TextWriter writer;

/// <summary>
/// The cached review information, associating a local content ID
/// to the created review ID for each item.
/// </summary>
private static List<ReviewItem> reviewItems =
    new List<ReviewItem>();
```

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>Een methode definiëren voor het schrijven van berichten naar het logboekbestand

Voeg de volgende methode toe aan de klasse **Program**.

```csharp
/// <summary>
/// Writes a message to the log file, and optionally to the console.
/// </summary>
/// <param name="message">The message.</param>
/// <param name="echo">if set to <c>true</c>, write the message to the console.</param>
private static void WriteLine(string message = null, bool echo = false)
{
    writer.WriteLine(message ?? String.Empty);

    if (echo)
    {
        Console.WriteLine(message ?? String.Empty);
    }
}
```

## <a name="create-a-method-to-create-a-set-of-reviews"></a>Een methode definiëren voor het maken van een reeks beoordelingen

Normaal gesproken beschikt u over bedrijfslogica voor het identificeren van binnenkomende afbeeldingen, tekst of video die moeten worden beoordeeld. Hier gebruikt u echter alleen een vaste lijst met afbeeldingen.

Voeg de volgende methode toe aan de klasse **Program**.

```csharp
/// <summary>
/// Create the reviews using the fixed list of images.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
private static void CreateReviews(ContentModeratorClient client)
{
    WriteLine(null, true);
    WriteLine("Creating reviews for the following images:", true);

    // Create the structure to hold the request body information.
    List<CreateReviewBodyItem> requestInfo =
        new List<CreateReviewBodyItem>();

    // Create some standard metadata to add to each item.
    List<CreateReviewBodyItemMetadataItem> metadata =
        new List<CreateReviewBodyItemMetadataItem>(
        new CreateReviewBodyItemMetadataItem[] {
            new CreateReviewBodyItemMetadataItem(
                MetadataKey, MetadataValue)
        });

    // Populate the request body information and the initial cached review information.
    for (int i = 0; i < ImageUrls.Length; i++)
    {
        // Cache the local information with which to create the review.
        var itemInfo = new ReviewItem()
        {
            Type = MediaType,
            ContentId = i.ToString(),
            Url = ImageUrls[i],
            ReviewId = null
        };

        WriteLine($" - {itemInfo.Url}; with id = {itemInfo.ContentId}.", true);

        // Add the item informaton to the request information.
        requestInfo.Add(new CreateReviewBodyItem(
            itemInfo.Type, itemInfo.Url, itemInfo.ContentId,
            CallbackEndpoint, metadata));

        // Cache the review creation information.
        reviewItems.Add(itemInfo);
    }

    var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync(
        "application/json", TeamName, requestInfo);

    // Update the local cache to associate the created review IDs with
    // the associated content.
    var reviewIds = reviewResponse.Result.Body;
    for (int i = 0; i < reviewIds.Count; i++)
    {
        Program.reviewItems[i].ReviewId = reviewIds[i];
    }

    WriteLine(JsonConvert.SerializeObject(
    reviewIds, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>Een methode definiëren om de status van bestaande beoordelingen op te halen

Voeg de volgende methode toe aan de klasse **Program**.

> [!Note]
> In de praktijk stelt u de callback-URL `CallbackEndpoint` in op de URL die de resultaten van de handmatige beoordeling ontvangt (via een HTTP POST-aanvraag). U kunt deze methode wijzigen om de status van in behandeling zijnde beoordelingen te controleren.

```csharp
/// <summary>
    /// Gets the review details from the server.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void GetReviewDetails(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Getting review details:", true);
        foreach (var item in reviewItems)
        {
            var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(
                TeamName, item.ReviewId);

            WriteLine(
                $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                $"{reviewDetail.Result.Body.Status}.", true);
            WriteLine(JsonConvert.SerializeObject(
                reviewDetail.Result.Body, Formatting.Indented));

            Thread.Sleep(throttleRate);
        }
    }
```

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Code toevoegen om een reeks beoordelingen te maken en de status ervan te controleren

Voeg de volgende code aan de methode **Hoofd**.

Deze code simuleert veel van de bewerkingen die u uitvoert bij het definiëren en beheren van de lijst en bij het gebruik van de lijst voor schermafbeeldingen. Met de functie voor logboek registratie kunt u de reactie objecten zien die zijn gegenereerd door de SDK-aanroepen naar de content mModerator-service.

```csharp
using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
{
    writer = outputWriter;
    using (var client = Clients.NewClient())
    {
        CreateReviews(client);
        GetReviewDetails(client);

        Console.WriteLine();
        Console.WriteLine("Perform manual reviews on the Content Moderator site.");
        Console.WriteLine("Then, press any key to continue.");
        Console.ReadKey();

        Console.WriteLine();
        Console.WriteLine($"Waiting {latencyDelay} seconds for results to propigate.");
        Thread.Sleep(latencyDelay * 1000);

        GetReviewDetails(client);
    }

    writer = null;
    outputWriter.Flush();
    outputWriter.Close();
}

Console.WriteLine();
Console.WriteLine("Press any key to exit...");
Console.ReadKey();
```

## <a name="run-the-program-and-review-the-output"></a>Voer het programma uit en controleer de uitvoer

U krijgt de volgende voorbeelduitvoer te zien:

```console
Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
```

Meld u aan bij het Content Moderator-beoordelingsprogramma om de in behandeling zijnde beoordeling van afbeeldingen te zien met het **sc**-label ingesteld op **true**. U ziet ook de standaard **a**- en **r**-tags en eventuele aangepaste tags die u mogelijk hebt gedefinieerd binnen het beoordelingsprogramma.

Gebruik de knop **Volgende** om de code te verzenden.

![Beoordeling van afbeeldingen voor menselijke moderators](images/moderation-reviews-quickstart-dotnet.PNG)

Druk vervolgens op een willekeurige toets om door te gaan.

```console
Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...
```

## <a name="check-out-the-following-output-in-the-log-file"></a>Bekijk de volgende uitvoer in het logboekbestand.

> [!NOTE]
> In het uitvoerbestand geven de tekenreeksen "\{teamname} ' en '\{callbackUrl}" de waarden aan voor respectievelijk de velden `TeamName` en `CallbackEndpoint`.

De beoordelings-id's en de URL's van de afbeeldingsinhoud verschillen iedere keer wanneer u de toepassing uitvoert. Wanneer een beoordeling is voltooid, geeft het veld `reviewerResultTags` aan hoe de beoordelaar het item gemarkeerd heeft.

```json
Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.
    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Pending",
        "reviewerResultTags": [],
        "createdBy": "{teamname}",
        "metadata": [
        {
              "key": "sc",
              "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
              "key": "a",
              "value": "False"
        },
        {
              "key": "r",
              "value": "True"
        },
        {
              "key": "sc",
              "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
              "key": "sc",
              "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>Indien opgegeven ontvangt de URL voor terugbellen deze reactie

U ziet een reactie zoals in het volgende voorbeeld:

```json
{
    "ReviewId": "201801i48a2937e679a41c7966e838c92f5e649",
    "ModifiedOn": "2018-01-06T05:04:40.5525865Z",
    "ModifiedBy": "yourusername",
    "CallBackType": "Review",
    "ContentId": "0",
    "ContentType": "Image",
    "Metadata": {
        "sc": "true"
        },
    "ReviewerResultTags": {
        "a": "False",
        "r": "False",
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Haal de [Content moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) op en down load de [Visual Studio-oplossing](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) voor deze en andere content moderator Snelstartgids voor .net, en ga aan de slag met uw integratie.