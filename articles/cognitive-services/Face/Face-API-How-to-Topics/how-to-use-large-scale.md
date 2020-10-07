---
title: 'Voorbeeld: De functie voor grootschalige verwerking gebruiken - Face'
titleSuffix: Azure Cognitive Services
description: Deze handleiding is een artikel over het omhoog schalen van bestaande PersonGroup- en FaceList-objecten naar LargePersonGroup- en LargeFaceList-objecten.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 5341c2613624c6a52f1649dcd8a64b6746b84f67
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332386"
---
# <a name="example-use-the-large-scale-feature"></a>Voorbeeld: De functie Grootschalig gebruiken

Deze handleiding is een geavanceerd artikel over het respectievelijk omhoog schalen van bestaande PersonGroup- en FaceList-objecten naar LargePersonGroup- en LargeFaceList-objecten. In deze handleiding wordt het migratieproces gedemonstreerd. We gaan ervan uit dat u een elementaire kennis van de PersonGroup- en FaceList-objecten, de [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)-bewerking en de functies voor gezichtsherkenning hebt. Zie de conceptuele handleiding [Gezichtsherkenning](../concepts/face-recognition.md) voor meer informatie over deze onderwerpen.

LargePersonGroup en LargeFaceList worden gezamenlijk aangeduid als grootschalige bewerkingen. LargePersonGroup kan maximaal 1 miljoen personen bevatten, met elk maximaal 248 gezichten. LargeFaceList kan maximaal 1 miljoen gezichten bevatten. De grootschalige bewerkingen zijn vergelijkbaar met de conventionele PersonGroup en FaceList, maar er zijn enkele verschillen vanwege de nieuwe architectuur. 

De voorbeelden worden geschreven in C# met behulp van de Face-clientbibliotheek van Azure Cognitive Services.

> [!NOTE]
> Als u Face-zoekprestaties voor Identification en FindSimilar op grote schaal wilt inschakelen, moet u LargeFaceList en LargePersonGroup voorbereiden door een Train-bewerking uit te voeren. De trainingstijd varieert van seconden tot ongeveer een half uur, al naargelang de werkelijke capaciteit. Tijdens de trainingsperiode is het mogelijk Identification en FindSimilar uit te voeren als er al eerder een geslaagde training is uitgevoerd. Het nadeel is dat de nieuw toegevoegde personen/gezichten pas worden weergegeven in het resultaat als er een nieuwe grootschalige training na de migratie is uitgevoerd.

## <a name="step-1-initialize-the-client-object"></a>Stap 1: Het clientobject initialiseren

Als u de Face-clientbibliotheek gebruikt, worden de abonnementssleutel en het abonnementseindpunt doorgegeven via de constructor van de klasse FaceClient. Bijvoorbeeld:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Als u de abonnementssleutel en het bijbehorende eindpunt wilt ophalen, gaat u vanuit Azure Portal naar Azure Marketplace.
Zie [Abonnementen](https://azure.microsoft.com/services/cognitive-services/directory/vision/) voor meer informatie.

## <a name="step-2-code-migration"></a>Stap 2: Codemigratie

In deze sectie richten we ons alleen op de migratie van de PersonGroup- of FaceList-implementatie naar LargePersonGroup of LargeFaceList. Hoewel het ontwerp en de interne implementatie van LargePersonGroup of LargeFaceList en PersonGroup of FaceList verschillen, zijn de API-interfaces vergelijkbaar voor compatibiliteit met eerdere versies.

Gegevensmigratie wordt niet ondersteund. In plaats daarvan maakt u de LargePersonGroup of LargeFaceList opnieuw.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Een PersonGroup migreren naar een LargePersonGroup

Het migreren van een PersonGroup naar een LargePersonGroup is eenvoudig. Ze delen precies dezelfde bewerkingen op groepsniveau.

Voor de implementatie van PersonGroup of Person hoeft u alleen de API-paden of de SDK-klasse/module te wijzigen van LargePersonGroup en LargePersonGroup Person.

Voeg alle gezichten en personen van de PersonGroup toe aan de nieuwe LargePersonGroup. Zie [Gezichten toevoegen](how-to-add-faces.md) voor meer informatie.

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Een FaceList migreren naar een LargeFaceList

| FaceList-API's | LargeFaceList-API's |
|:---:|:---:|
| Maken | Maken |
| Verwijderen | Verwijderen |
| Ophalen | Ophalen |
| Lijst | Lijst |
| Bijwerken | Bijwerken |
| - | Trainen |
| - | Trainingsstatus ophalen |

De bovenstaande tabel bevat een vergelijking van bewerkingen tussen FaceList en LargeFaceList op lijstniveau. Zoals weergegeven, wordt LargeFaceList geleverd met nieuwe bewerkingen, Train en Get Training Status (Trainen en Trainingsstatus ophalen) als uitbreiding op FaceList. Het trainen van de LargeFaceList is een hoofdvoorwaarde voor de [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)-bewerking. Training is niet vereist voor FaceList. Het volgende codefragment is een Help-functie tijdens het wachten op de training van een LargeFaceList:

```csharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

Voorheen zag een typisch gebruik van FaceList met toegevoegde gezichten en FindSimilar er als volgt uit:

```csharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Na de migratie naar LargeFaceList ziet het er als volgt uit:

```csharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Zoals eerder beschreven, worden het gegevensbeheer en het onderdeel FindSimilar bijna op dezelfde manier uitgevoerd. De enige uitzondering hierop is dat eerst een nieuwe Train-bewerking moet worden voltooid in LargeFaceList voordat FindSimilar werkt.

## <a name="step-3-train-suggestions"></a>Stap 3: Suggesties trainen

Hoewel door de Train-bewerking [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) en [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) sneller worden uitgevoerd, is de trainingstijd aanzienlijk langer bij een grote schaal. De geschatte trainingstijd in verschillende schalen wordt in de volgende tabel vermeld.

| Schaal voor gezichten of personen | Geschatte trainingstijd |
|:---:|:---:|
| 1000 | 1-2 sec. |
| 10.000 | 5-10 sec. |
| 100.000 | 1-2 min. |
| 1.000.000 | 10-30 min. |

We raden u aan de volgende strategieën te gebruiken om de functie voor een grote schaal beter te benutten.

### <a name="step-31-customize-time-interval"></a>Stap 3.1: Tijdsinterval aanpassen

Zoals wordt weergegeven in `TrainLargeFaceList()`, is er een tijdsinterval in milliseconden om het oneindige proces voor controle van de trainingsstatus uit te stellen. Als u een langer interval gebruikt voor LargeFaceList met meer gezichten, vermindert u het aantal oproepen en de kosten. Pas het tijdsinterval aan op basis van de verwachte capaciteit van LargeFaceList.

Dezelfde strategie geldt ook voor LargePersonGroup. Wanneer u bijvoorbeeld een LargePersonGroup traint met 1 miljoen personen, kan `timeIntervalInMilliseconds` 60.000 zijn. Dit is een interval van 1 minuut.

### <a name="step-32-small-scale-buffer"></a>Stap 3.2: Kleinschalige buffer

Personen of gezichten in een LargePersonGroup of LargeFaceList zijn pas doorzoekbaar wanneer ze zijn getraind. In een dynamisch scenario worden voortdurend nieuwe personen of gezichten toegevoegd, die onmiddellijk doorzoekbaar moeten zijn, maar de training kan langer duren dan gewenst. 

Als u dit probleem wilt verhelpen, gebruikt u een extra kleinschalige LargePersonGroup of LargeFaceList voor alleen de toegevoegde vermeldingen als buffer. Deze buffer kan sneller worden getraind vanwege de beperkte grootte. De functie voor onmiddellijke doorzoekbaarheid zou moeten werken voor deze tijdelijke buffer. Gebruik deze buffer in combinatie met de training van de master-LargePersonGroup of -LargeFaceList door de mastertraining met een langer interval uit te voeren. Bijvoorbeeld dagelijks om middernacht.

Hier volgt een voorbeeld van een werkstroom:

1. Maak een master-LargePersonGroup of -LargeFaceList. Dit is de masterverzameling. Maak een buffer-LargePersonGroup of -LargeFaceList. Dit is de bufferverzameling. De bufferverzameling is alleen voor nieuw toegevoegde personen of gezichten.
1. Voeg nieuwe personen of gezichten toe aan zowel de masterverzameling als de bufferverzameling.
1. Train alleen de bufferverzameling met een kort interval om te waarborgen dat de zojuist toegevoegde vermeldingen bruikbaar zijn.
1. Roep Identification/FindSimilar aan voor zowel de masterverzameling als de bufferverzameling. Voeg de resultaten samen.
1. Wanneer de omvang van de bufferverzameling een drempelwaarde bereikt, of op een tijd dat het systeem niet actief is, maakt u een nieuwe bufferverzameling. Trigger de Train-bewerking voor de masterverzameling.
1. Verwijder de oude bufferverzameling wanneer de Train-bewerking van de masterverzameling is voltooid.

### <a name="step-33-standalone-training"></a>Stap 3.3: Zelfstandige training

Als een relatief lange latentie aanvaardbaar is, is het niet nodig om de Train-bewerking te triggeren direct nadat u nieuwe gegevens hebt toegevoegd. De Train-bewerking kan ook worden gescheiden van de hoofdlogica en op regelmatige tijden worden geactiveerd. Deze strategie is geschikt voor dynamische scenario's met een acceptabele latentie. Deze kan worden toegepast op statische scenario's om de trainingsfrequentie te verlagen.

Stel dat er een `TrainLargePersonGroup`-functie is die vergelijkbaar is met `TrainLargeFaceList`. Een typische implementatie van de zelfstandige training voor LargePersonGroup door het aanroepen van de klasse [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) in `System.Timers` is:

```csharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Zie [Gezichten toevoegen](how-to-add-faces.md) voor meer informatie over gegevensbeheer en aan identificatie gerelateerde implementaties.

## <a name="summary"></a>Samenvatting

In deze handleiding hebt u geleerd hoe u de bestaande PersonGroup- of FaceList-code (geen gegevens) migreert naar LargePersonGroup of LargeFaceList:

- LargePersonGroup en LargeFaceList werken op een manier die vergelijkbaar is met PersonGroup en FaceList, behalve dat de Train-bewerking nodig is voor LargeFaceList.
- Pas de juiste trainingsstrategie toe voor dynamische gegevensupdates van grootschalige gegevenssets.

## <a name="next-steps"></a>Volgende stappen

Volg een instructiegids voor het toevoegen van gezichten aan een PersonGroup of het schrijven van een script om de Identify-bewerking op een PersonGroup uit voeren.

- [Gezichten toevoegen](how-to-add-faces.md)
- [Quickstart voor de Face-clientbibliotheek](../Quickstarts/client-libraries.md)