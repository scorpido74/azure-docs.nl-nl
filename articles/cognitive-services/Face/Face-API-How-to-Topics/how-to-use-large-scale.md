---
title: 'Voorbeeld: Gebruik de functie Grootschalige functie - Face'
titleSuffix: Azure Cognitive Services
description: Deze handleiding is een artikel over hoe u opschalen van bestaande PersonGroup- en FaceList-objecten naar largepersongroup- en largefacelist-objecten.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: dc0964e40e9214e414d865c06006f1d36e97eeb2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169769"
---
# <a name="example-use-the-large-scale-feature"></a>Voorbeeld: Gebruik de functie op grote schaal

Deze handleiding is een geavanceerd artikel over het opschalen van bestaande PersonGroup- en FaceList-objecten naar respectievelijk LargePersonGroup- en LargeFaceList-objecten. Deze gids toont het migratieproces. Het gaat uit van een basisbekendheid met PersonGroup- en FaceList-objecten, de [bewerking Trein](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) en de functies voor gezichtsherkenning. Zie de conceptuele gids [voor gezichtsherkenning](../concepts/face-recognition.md) voor meer informatie over deze onderwerpen.

LargePersonGroup en LargeFaceList worden gezamenlijk grootschalige operaties genoemd. LargePersonGroup kan maximaal 1 miljoen personen bevatten, elk met een maximum van 248 gezichten. LargeFaceList kan maximaal 1 miljoen gezichten bevatten. De grootschalige bewerkingen zijn vergelijkbaar met de conventionele PersonGroup en FaceList, maar hebben een aantal verschillen als gevolg van de nieuwe architectuur. 

De voorbeelden worden geschreven in C# met behulp van de Azure Cognitive Services Face-clientbibliotheek.

> [!NOTE]
> Als u face-zoekprestaties voor identificatie en FindSimilar op grote schaal wilt inschakelen, introduceert u een treinbewerking om de LargeFaceList en LargePersonGroup vooraf te verwerken. De trainingstijd varieert van seconden tot ongeveer een half uur op basis van de werkelijke capaciteit. Tijdens de trainingsperiode is het mogelijk om Identification en FindSimilar uit te voeren als er eerder een succesvolle training is uitgevoerd. Het nadeel is dat de nieuwe toegevoegde personen en gezichten niet in het resultaat verschijnen totdat een nieuwe postmigratie naar grootschalige training is voltooid.

## <a name="step-1-initialize-the-client-object"></a>Stap 1: Het clientobject initialiseren

Wanneer u de Face-clientbibliotheek gebruikt, worden de abonnementssleutel en het eindpunt van het abonnement doorgegeven via de constructeur van de klasse FaceClient. Bijvoorbeeld:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Als u de abonnementssleutel met het bijbehorende eindpunt wilt ophalen, gaat u vanuit de Azure-portal naar de Azure Marketplace.
Zie [Abonnementen voor](https://azure.microsoft.com/services/cognitive-services/directory/vision/)meer informatie.

## <a name="step-2-code-migration"></a>Stap 2: Codemigratie

Deze sectie richt zich op het migreren van PersonGroup of FaceList-implementatie naar LargePersonGroup of LargeFaceList. Hoewel LargePersonGroup of LargeFaceList qua ontwerp en interne implementatie verschilt van PersonGroup of FaceList, zijn de API-interfaces vergelijkbaar voor achterwaartse compatibiliteit.

Gegevensmigratie wordt niet ondersteund. U maakt in plaats daarvan de LargePersonGroup of LargeFaceList opnieuw.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Een persoonsgroep migreren naar een largepersongroep

Migratie van een persoonsgroep naar een LargePersonGroup is eenvoudig. Ze delen precies dezelfde groepsoperaties.

Voor persongroup- of persoonsgerelateerde implementatie is het noodzakelijk om alleen de API-paden of SDK-klasse/module te wijzigen in LargePersonGroup en LargePersonGroup Person.

Voeg alle gezichten en personen uit de persoonsgroep toe aan de nieuwe LargePersonGroup. Zie [Gezichten toevoegen voor](how-to-add-faces.md)meer informatie .

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Een FaceList migreren naar een LargeFaceList

| FaceList-API's | LargeFaceList-API's |
|:---:|:---:|
| Maken | Maken |
| Verwijderen | Verwijderen |
| Ophalen | Ophalen |
| Lijst | Lijst |
| Update | Update |
| - | Trainen |
| - | Trainingsstatus ophalen |

De bovenstaande tabel bevat een vergelijking van bewerkingen tussen FaceList en LargeFaceList op lijstniveau. Zoals wordt getoond, largefacelist wordt geleverd met nieuwe operaties, Trein en Get Training Status, in vergelijking met FaceList. Het trainen van de LargeFaceList is een voorwaarde voor de [FindSimilar-bewerking.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) Training is niet vereist voor FaceList. Het volgende fragment is een helperfunctie om te wachten op de training van een LargeFaceList:

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

Voorheen leek een typisch gebruik van FaceList met toegevoegde gezichten en FindSimilar als volgt:

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

Wanneer u deze naar LargeFaceList migreert, wordt het de volgende:

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

Zoals eerder getoond, zijn het gegevensbeheer en het FindSimilar-gedeelte bijna hetzelfde. De enige uitzondering is dat een nieuwe voorbewerking trein bewerking moet voltooien in de LargeFaceList voordat FindSimilar werkt.

## <a name="step-3-train-suggestions"></a>Stap 3: Treinsuggesties

Hoewel de trein operatie versnelt [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) en [Identificatie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), de opleiding tijd lijdt, vooral wanneer het komen op grote schaal. De geschatte trainingstijd in verschillende schalen wordt vermeld in de volgende tabel.

| Schaal voor gezichten of personen | Geschatte trainingstijd |
|:---:|:---:|
| 1000 | 1-2 sec |
| 10.000 | 5-10 sec |
| 100.000 | 1-2 min |
| 1.000.000 | 10-30 min |

Om de grootschalige functie beter te benutten, raden we de volgende strategieën aan.

### <a name="step-31-customize-time-interval"></a>Stap 3.1: Tijdsinterval aanpassen

Zoals wordt `TrainLargeFaceList()`weergegeven in , is er een tijdsinterval in milliseconden om de oneindige training status controle proces uit te stellen. Als u een langer interval gebruikt voor LargeFaceList met meer gezichten, vermindert u het aantal oproepen en de kosten. Pas het tijdsinterval aan op basis van de verwachte capaciteit van de LargeFaceList.

Dezelfde strategie geldt ook voor LargePersonGroup. Wanneer u bijvoorbeeld een LargePersonGroup met 1 `timeIntervalInMilliseconds` miljoen personen traint, kan dit 60.000 zijn, wat een interval van 1 minuut is.

### <a name="step-32-small-scale-buffer"></a>Stap 3.2: Kleinschalige buffer

Personen of gezichten in een LargePersonGroup of een LargeFaceList kunnen alleen worden doorzocht nadat ze zijn opgeleid. In een dynamisch scenario worden voortdurend nieuwe personen of gezichten toegevoegd en moeten ze onmiddellijk doorzoekbaar zijn, maar de training kan langer duren dan gewenst. 

Gebruik een extra kleinschalige LargePersonGroup of LargeFaceList als buffer alleen voor de nieuw toegevoegde items om dit probleem te verhelpen. Deze buffer neemt een kortere tijd om te trainen vanwege de kleinere omvang. De onmiddellijke zoekmogelijkheid op deze tijdelijke buffer zou moeten werken. Gebruik deze buffer in combinatie met training op de master LargePersonGroup of LargeFaceList door de mastertraining op een spaarzame interval uit te voeren. Voorbeelden zijn in het midden van de nacht en dagelijks.

Voorbeeld van een werkstroom:

1. Maak een master LargePersonGroup of LargeFaceList, de hoofdverzameling. Maak een buffer LargePersonGroup of LargeFaceList, de bufferverzameling. De bufferverzameling is alleen voor nieuw toegevoegde personen of gezichten.
1. Voeg nieuwe personen of gezichten toe aan zowel de hoofdverzameling als de bufferverzameling.
1. Train de bufferverzameling alleen met een kort tijdsinterval om ervoor te zorgen dat de nieuw toegevoegde items van kracht worden.
1. Oproep identificatie of FindSimilar tegen zowel de hoofdverzameling als de bufferverzameling. Voeg de resultaten samen.
1. Wanneer de grootte van de bufferverzameling toeneemt tot een drempelwaarde of op een inactieve tijd van het systeem, maakt u een nieuwe bufferverzameling. Activeer de treinbewerking op de hoofdverzameling.
1. Verwijder de oude bufferverzameling nadat de bewerking Trein is voltooid in de hoofdverzameling.

### <a name="step-33-standalone-training"></a>Stap 3.3: Zelfstandige training

Als een relatief lange latentie acceptabel is, is het niet nodig om de bewerking Trein direct nadat u nieuwe gegevens hebt toegevoegd, te activeren. De Train-bewerking kan ook worden gescheiden van de hoofdlogica en op regelmatige tijden worden geactiveerd. Deze strategie is geschikt voor dynamische scenario's met aanvaardbare latentie. Het kan worden toegepast op statische scenario's om de treinfrequentie verder te verlagen.

Stel dat er `TrainLargePersonGroup` een `TrainLargeFaceList`functie is die lijkt op . Een typische implementatie van de zelfstandige training op [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) een `System.Timers` LargePersonGroup door een beroep te doen op de klasse in is:

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

Zie [Gezichten toevoegen](how-to-add-faces.md) en Gezichten in een [afbeelding identificeren](HowtoIdentifyFacesinImage.md)voor meer informatie over gegevensbeheer en identificatiegerelateerde implementaties.

## <a name="summary"></a>Samenvatting

In deze handleiding hebt u geleerd hoe u de bestaande Persoonsgroep- of FaceList-code, niet naar gegevens, migreren naar de LargePersonGroup of LargeFaceList:

- LargePersonGroup en LargeFaceList werken vergelijkbaar met PersonGroup of FaceList, behalve dat de treinbewerking vereist is door LargeFaceList.
- Neem de juiste treinstrategie om dynamische gegevensupdate voor grootschalige gegevenssets te dynamisch.

## <a name="next-steps"></a>Volgende stappen

Volg een handleiding voor meer informatie over het toevoegen van gezichten aan een persoonsgroep of het uitvoeren van de bewerking Identificeren op een persoonsgroep.

- [Gezichten toevoegen](how-to-add-faces.md)
- [Gezichten in een afbeelding identificeren](HowtoIdentifyFacesinImage.md)
