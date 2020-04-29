---
title: 'Voor beeld: gebruik de grootschalige functie-face'
titleSuffix: Azure Cognitive Services
description: Deze hand leiding is een artikel over het opschalen van bestaande PersonGroup-en FaceList-objecten naar LargePersonGroup-en LargeFaceList-objecten.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: dc0964e40e9214e414d865c06006f1d36e97eeb2
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76169769"
---
# <a name="example-use-the-large-scale-feature"></a>Voor beeld: gebruik de functie grootschalige schaal

Deze hand leiding is een geavanceerd artikel over het opschalen van bestaande PersonGroup-en FaceList-objecten naar respectievelijk LargePersonGroup-en LargeFaceList-objecten. In deze hand leiding wordt het migratie proces gedemonstreerd. Er wordt uitgegaan van een basis kennis met PersonGroup-en FaceList-objecten, de [trein](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) werking en de gezichts herkennings functies. Zie de conceptuele hand leiding voor de [gezichts herkenning](../concepts/face-recognition.md) voor meer informatie over deze onderwerpen.

LargePersonGroup en LargeFaceList worden gezamenlijk aangeduid als grootschalige bewerkingen. LargePersonGroup kan Maxi maal 1.000.000 personen bevatten, elk met een maximum van 248 gezichten. LargeFaceList kan Maxi maal 1.000.000 gezichten bevatten. De grootschalige bewerkingen zijn vergelijkbaar met de conventionele PersonGroup en FaceList, maar hebben een aantal verschillen vanwege de nieuwe architectuur. 

De voor beelden zijn geschreven in C# met behulp van de Azure Cognitive Services Face-client bibliotheek.

> [!NOTE]
> Als u de prestaties van gezichts zoekopdrachten voor het identificeren en FindSimilar op grote schaal wilt inschakelen, moet u een Train bewerking uitvoeren om de LargeFaceList en LargePersonGroup voor te verwerken. De tijd van de training varieert van seconden tot ongeveer een halve uur op basis van de werkelijke capaciteit. Tijdens de cursus periode is het mogelijk om identificatie-en FindSimilar uit te voeren als een geslaagde training eerder is uitgevoerd. Het nadeel is dat de nieuwe toegevoegde personen en gezichten pas in het resultaat worden weer gegeven nadat een nieuwe migratie naar grootschalige trainingen is voltooid.

## <a name="step-1-initialize-the-client-object"></a>Stap 1: het client object initialiseren

Wanneer u de face-client bibliotheek gebruikt, worden de abonnements sleutel en het abonnements eindpunt door gegeven via de constructor van de FaceClient-klasse. Bijvoorbeeld:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Als u de abonnements sleutel met het bijbehorende eind punt wilt ophalen, gaat u naar de Azure Marketplace vanuit de Azure Portal.
Zie [abonnementen](https://azure.microsoft.com/services/cognitive-services/directory/vision/)voor meer informatie.

## <a name="step-2-code-migration"></a>Stap 2: code migratie

In deze sectie wordt uitgelegd hoe u PersonGroup of FaceList implementeert naar LargePersonGroup of LargeFaceList. Hoewel LargePersonGroup of LargeFaceList verschilt van PersonGroup of FaceList in de ontwerp-en interne implementatie, zijn de API-interfaces vergelijkbaar met achterwaartse compatibiliteit.

Gegevens migratie wordt niet ondersteund. U maakt in plaats daarvan de LargePersonGroup of LargeFaceList opnieuw.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Een PersonGroup migreren naar een LargePersonGroup

Migratie van een PersonGroup naar een LargePersonGroup is eenvoudig. Ze delen precies dezelfde bewerkingen op groeps niveau.

Voor PersonGroup-of persoons implementatie hoeft u alleen de API-paden of SDK class/module te wijzigen in LargePersonGroup en LargePersonGroup persoon.

Voeg alle gezichten en personen van de PersonGroup toe aan de nieuwe LargePersonGroup. Zie [gezichten toevoegen](how-to-add-faces.md)voor meer informatie.

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

De bovenstaande tabel bevat een vergelijking van bewerkingen tussen FaceList en LargeFaceList op lijstniveau. Zoals wordt weer gegeven, wordt LargeFaceList geleverd met nieuwe bewerkingen, Train en Get training status, vergeleken met FaceList. Training de LargeFaceList is een voor waarde van de [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) -bewerking. Er is geen training vereist voor FaceList. Het volgende code fragment is een hulp functie om te wachten op de training van een LargeFaceList:

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

Voorheen werd een typisch gebruik van FaceList met toegevoegde gezichten en FindSimilar gezien als het volgende:

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

Wanneer het naar LargeFaceList wordt gemigreerd, wordt het volgende:

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

Zoals eerder weer gegeven, zijn het gegevens beheer en het FindSimilar-deel bijna hetzelfde. De enige uitzonde ring hierop is dat een nieuwe voorverwerkende trein bewerking moet worden voltooid in de LargeFaceList voordat FindSimilar werkt.

## <a name="step-3-train-suggestions"></a>Stap 3: suggesties trainen

Hoewel de trein bewerking [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) en [identificatie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)kan versnellen, is de trainings tijd van belang, vooral wanneer deze op grote schaal komt. De geschatte trainings tijd in verschillende schalen wordt weer gegeven in de volgende tabel.

| Schaal voor gezichten of personen | Geschatte trainings tijd |
|:---:|:---:|
| 1000 | 1-2 sec |
| 10.000 | 5-10 sec |
| 100.000 | 1-2 minuten |
| 1.000.000 | 10-30 minuten |

We raden u aan de volgende strategieën te gebruiken om de grootschalige functie beter te benutten.

### <a name="step-31-customize-time-interval"></a>Stap 3,1: het tijds interval aanpassen

Zoals wordt weer gegeven `TrainLargeFaceList()`in, is er een tijds interval in milliseconden voor het vertragen van het controle proces voor de oneindige trainings status. Als u een langer interval gebruikt voor LargeFaceList met meer gezichten, vermindert u het aantal oproepen en de kosten. Pas het tijds interval aan op basis van de verwachte capaciteit van de LargeFaceList.

Dezelfde strategie geldt ook voor LargePersonGroup. Wanneer u bijvoorbeeld een LargePersonGroup met 1.000.000 personen traint, `timeIntervalInMilliseconds` kan dit 60.000 zijn. Dit is een interval van 1 minuut.

### <a name="step-32-small-scale-buffer"></a>Stap 3,2: kleinschalige buffer

Personen of gezichten in een LargePersonGroup of een LargeFaceList zijn pas te doorzoeken nadat ze zijn getraind. In een dynamisch scenario worden nieuwe personen of gezichten voortdurend toegevoegd en moeten ze direct worden doorzocht, maar de training kan langer duren dan gewenst. 

Als u dit probleem wilt verhelpen, gebruikt u een extra kleinschalige LargePersonGroup of LargeFaceList als buffer voor de zojuist toegevoegde vermeldingen. Deze buffer neemt een kortere tijd in beslag vanwege de kleinere grootte. De onmiddellijke zoek functie voor deze tijdelijke buffer zou moeten werken. Gebruik deze buffer in combi natie met training op het hoofd-LargePersonGroup of LargeFaceList door de hoofd training uit te voeren op een Sparer-interval. Voor beelden zijn in het midden van 's nachts en dagelijks.

Voorbeeld van een werkstroom:

1. Maak een hoofd-LargePersonGroup of-LargeFaceList. Dit is de hoofd verzameling. Maak een buffer LargePersonGroup of LargeFaceList, die de buffer verzameling is. De buffer verzameling is alleen voor nieuw toegevoegde personen of gezichten.
1. Nieuwe personen of gezichten toevoegen aan zowel de hoofd verzameling als de buffer verzameling.
1. Train de buffer verzameling alleen met een korte tijds interval om ervoor te zorgen dat de nieuwe toegevoegde vermeldingen van kracht worden.
1. Roep Identification of FindSimilar aan voor zowel de hoofd verzameling als de buffer verzameling. De resultaten samen voegen.
1. Wanneer de grootte van de buffer verzameling toeneemt aan een drempel waarde of op een systeem niet-actieve tijd, maakt u een nieuwe buffer verzameling. Activeer de Train-bewerking voor de hoofd verzameling.
1. Verwijder de oude buffer verzameling nadat de trein bewerking is voltooid voor de hoofd verzameling.

### <a name="step-33-standalone-training"></a>Stap 3,3: zelfstandige training

Als een relatief lange latentie acceptabel is, is het niet nodig om de trein bewerking te activeren nadat u nieuwe gegevens hebt toegevoegd. De Train-bewerking kan ook worden gescheiden van de hoofdlogica en op regelmatige tijden worden geactiveerd. Deze strategie is geschikt voor dynamische scenario's met een acceptabele latentie. Het kan worden toegepast op statische scenario's om de frequentie van de trein te verlagen.

Stel dat er een `TrainLargePersonGroup` functie is die `TrainLargeFaceList`er ongeveer als volgt uitziet. Een typische implementatie van de zelfstandige training in een LargePersonGroup door het aanroepen [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) van de `System.Timers` klasse in is:

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

Zie [gezichten toevoegen](how-to-add-faces.md) en [gezichten identificeren in een afbeelding](HowtoIdentifyFacesinImage.md)voor meer informatie over gegevens beheer en implementaties met betrekking tot identificatie.

## <a name="summary"></a>Samenvatting

In deze hand leiding hebt u geleerd hoe u de bestaande PersonGroup-of FaceList-code, niet-gegevens, kunt migreren naar de LargePersonGroup of LargeFaceList:

- LargePersonGroup en LargeFaceList werken op vergelijk bare wijze als PersonGroup of FaceList, behalve dat de trein bewerking vereist is door LargeFaceList.
- Neem de juiste Train strategie voor dynamische gegevens update voor grootschalige gegevens sets.

## <a name="next-steps"></a>Volgende stappen

Volg een hand leiding voor het toevoegen van gezichten aan een PersonGroup of het uitvoeren van de bewerking identify op een PersonGroup.

- [Gezichten toevoegen](how-to-add-faces.md)
- [Gezichten identificeren in een installatie kopie](HowtoIdentifyFacesinImage.md)
