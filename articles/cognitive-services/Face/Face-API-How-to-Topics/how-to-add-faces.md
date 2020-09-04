---
title: 'Voorbeeld: Gezichten toevoegen aan een PersonGroup - Face'
titleSuffix: Azure Cognitive Services
description: Deze handleiding laat zien hoe u met de service Azure Cognitive Services Face een groot aantal personen en gezichten aan een PersonGroup-object kunt toevoegen.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 06c353cf5fbd5c2b1058d99e7f91e9fcc3853cb1
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929040"
---
# <a name="add-faces-to-a-persongroup"></a>Gezichten toevoegen aan een PersonGroup

Deze handleiding laat zien hoe u een groot aantal personen en gezichten aan een PersonGroup-object kunt toevoegen. De dezelfde strategie geldt ook voor LargePersonGroup-, FaceList- en LargePersonGroup-objecten. Dit voorbeeld wordt geschreven in C# met behulp van de clientbibliotheek van Azure Cognitive Services Face.

## <a name="step-1-initialization"></a>Stap 1: Initialisatie

De volgende code declareert verschillende variabelen en implementeert een helperfunctie om de aanvragen om gezichten toe te voegen, te plannen:

- `PersonCount` is het totale aantal personen.
- `CallLimitPerSecond` is het maximum aantal aanroepen per seconde op basis van de abonnementslaag.
- `_timeStampQueue` is een wachtrij om aanvraag tijdstempels vast te leggen.
- `await WaitCallLimitPerSecondAsync()` wacht totdat de volgende aanvraag kan worden verzonden.

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

Als u gebruikmaakt van een clientbibliotheek, moet u de abonnementssleutel doorgegeven aan de constructor van de klasse **FaceClient**. Bijvoorbeeld:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Als u de abonnementssleutel wilt ophalen, gaat u vanuit Azure Portal naar Azure Marketplace. Zie [Abonnementen](https://www.microsoft.com/cognitive-services/sign-up) voor meer informatie.

## <a name="step-3-create-the-persongroup"></a>Stap 3: De PersonGroup maken

Een PersonGroup met de naam 'MyPersonGroup' wordt gemaakt om de personen op te slaan.
De aanvraagtijd wordt in de wachtrij `_timeStampQueue` geplaatst om de algehele validatie te controleren.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Stap 4: De personen maken voor de PersonGroup

Personen worden gelijktijdig gemaakt en `await WaitCallLimitPerSecondAsync()` wordt ook toegepast om te voorkomen dat de aanroeplimiet wordt overschreden.

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

Gezichten die aan verschillende personen worden toegevoegd, worden gelijktijdig verwerkt. Gezichten die voor een specifieke persoon worden toegevoegd, worden opeenvolgend verwerkt.
`await WaitCallLimitPerSecondAsync()` wordt opnieuw aangeroepen om ervoor te zorgen dat de frequentie van de aanvraag binnen het bereik van de beperking ligt.

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

In deze handleiding hebt u het proces voor het maken van een PersonGroup met een groot aantal personen en gezichten geleerd. Verschillende herinneringen:

- Deze strategie geldt ook voor FaceLists en LargePersonGroups.
- Het toevoegen van gezichten aan (of het verwijderen ervan uit) verschillende FaceLists of personen in LargePersonGroups wordt gelijktijdig verwerkt.
- Het toevoegen van gezichten aan (of het verwijderen ervan uit) een bepaalde FaceList of persoon in een LargePersonGroup wordt opeenvolgende uitgevoerd.
- Voor het gemak wordt de verwerking van mogelijke uitzonderingen in deze handleiding overgeslagen. Als de robuustheid wilt verbeteren, moet het juiste beleid voor opnieuw proberen worden toegepast.

De volgende functies zijn uitgelegd en gedemonstreerd:

- PersonGroups maken met behulp van de API [PersonGroup - Maken](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244).
- Personen maken met behulp van de API [PersonGroup Person - Maken](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c).
- Gezichten aan personen toevoegen met behulp van de API [PersonGroup Person - Gezicht toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

## <a name="related-topics"></a>Verwante onderwerpen

- [Gezichten in een afbeelding identificeren](HowtoIdentifyFacesinImage.md)
- [Gezichten in een afbeelding detecteren](HowtoDetectFacesinImage.md)
- [De functie Grootschalig gebruiken](how-to-use-large-scale.md)
