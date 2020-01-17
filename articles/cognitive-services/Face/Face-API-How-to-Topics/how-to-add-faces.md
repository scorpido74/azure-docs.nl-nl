---
title: 'Voor beeld: gezichten toevoegen aan een PersonGroup-Face-API'
titleSuffix: Azure Cognitive Services
description: Deze hand leiding laat zien hoe u een groot aantal personen en gezichten kunt toevoegen aan een PersonGroup-object met de Azure Cognitive Services Face-API.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 891614caddf729acb58bc363df977031ad62fb07
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156706"
---
# <a name="add-faces-to-a-persongroup"></a>Gezichten toevoegen aan een PersonGroup

Deze hand leiding laat zien hoe u een groot aantal personen en gezichten kunt toevoegen aan een PersonGroup-object. Dezelfde strategie geldt ook voor LargePersonGroup-, FaceList-en LargeFaceList-objecten. Dit voor beeld wordt geschreven C# in met behulp van de Azure Cognitive Services Face-API .net-client bibliotheek.

## <a name="step-1-initialization"></a>Stap 1: Initialisatie

De volgende code declareert verschillende variabelen en implementeert een Help-functie om het gezichts toevoeg aanvragen te plannen:

- `PersonCount` is het totale aantal personen.
- `CallLimitPerSecond` is het maximum aantal aanroepen per seconde op basis van de abonnementslaag.
- `_timeStampQueue` is een wachtrij om aanvraag tijdstempels vast te leggen.
- `await WaitCallLimitPerSecondAsync()` wacht totdat het geldig is voor het verzenden van de volgende aanvraag.

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>Stap 2: de API-aanroep autoriseren

Wanneer u een client bibliotheek gebruikt, moet u uw abonnements sleutel door geven aan de constructor van de klasse **FaceClient** . Bijvoorbeeld:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Als u de abonnements sleutel wilt ophalen, gaat u naar de Azure Marketplace vanuit de Azure Portal. Zie [abonnementen](https://www.microsoft.com/cognitive-services/sign-up)voor meer informatie.

## <a name="step-3-create-the-persongroup"></a>Stap 3: De PersonGroup maken

Een PersonGroup met de naam 'MyPersonGroup' wordt gemaakt om de personen op te slaan.
De aanvraagtijd wordt in de wachtrij `_timeStampQueue` geplaatst om de algehele validatie te controleren.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Stap 4: de personen voor de PersonGroup maken

Personen worden gelijktijdig gemaakt en `await WaitCallLimitPerSecondAsync()` wordt ook toegepast om te voor komen dat de aanroep limiet wordt overschreden.

```csharp
Person[] persons = new Person[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Stap 5: Gezichten voor de personen toevoegen

Gezichten die aan verschillende personen worden toegevoegd, worden gelijktijdig verwerkt. Gezichten die worden toegevoegd voor een specifieke persoon, worden opeenvolgend verwerkt.
`await WaitCallLimitPerSecondAsync()` wordt opnieuw gestart om ervoor te zorgen dat de aanvraag frequentie binnen het bereik van de beperking valt.

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Samenvatting

In deze hand leiding hebt u het proces voor het maken van een PersonGroup met een groot aantal personen en gezichten geleerd. Verschillende herinneringen:

- Deze strategie is ook van toepassing op FaceLists en LargePersonGroups.
- Het toevoegen of verwijderen van gezichten aan verschillende FaceLists of personen in LargePersonGroups wordt gelijktijdig verwerkt.
- Het toevoegen of verwijderen van gezichten aan een specifieke FaceList of persoon in een LargePersonGroup wordt opeenvolgend uitgevoerd.
- In deze hand leiding wordt beschreven hoe u een mogelijke uitzonde ring kunt afhandelen. Als u meer robuustheid wilt uitbreiden, moet u het juiste beleid voor opnieuw proberen Toep assen.

De volgende functies zijn uitgelegd en aangetoond:

- PersonGroups maken met behulp van de [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API.
- Personen maken met behulp van de [PersonGroup-API maken](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) .
- Voeg gezichten toe aan personen met behulp van de [PersonGroup persoon-add face-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API.

## <a name="related-topics"></a>Verwante onderwerpen

- [Gezichten identificeren in een installatie kopie](HowtoIdentifyFacesinImage.md)
- [Gezichten detecteren in een installatie kopie](HowtoDetectFacesinImage.md)
- [De grootschalige functie gebruiken](how-to-use-large-scale.md)
