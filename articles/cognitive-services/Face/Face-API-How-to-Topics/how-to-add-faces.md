---
title: 'Voorbeeld: Gezichten toevoegen aan een persoonsgroep - Gezicht'
titleSuffix: Azure Cognitive Services
description: In deze handleiding wordt uitgelegd hoe u een groot aantal personen en gezichten toevoegt aan een PersonGroup-object met de Azure Cognitive Services Face-service.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 240905d538afc5c0f4b7f0e0bf400fac23c3183f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169828"
---
# <a name="add-faces-to-a-persongroup"></a>Gezichten toevoegen aan een persoonsgroep

Deze handleiding laat zien hoe u een groot aantal personen en gezichten toevoegt aan een object PersonGroup. Dezelfde strategie geldt ook voor largepersongroup-, facelist- en largefacelist-objecten. Dit voorbeeld is geschreven in C# met behulp van de Azure Cognitive Services Face .NET-clientbibliotheek.

## <a name="step-1-initialization"></a>Stap 1: Initialisatie

Met de volgende code worden verschillende variabelen gedeclareerd en wordt een helperfunctie geïmplementeerd om de aanvragen voor het toevoegen van het gezicht te plannen:

- `PersonCount` is het totale aantal personen.
- `CallLimitPerSecond` is het maximum aantal aanroepen per seconde op basis van de abonnementslaag.
- `_timeStampQueue` is een wachtrij om aanvraag tijdstempels vast te leggen.
- `await WaitCallLimitPerSecondAsync()`wacht tot het geldig is om het volgende verzoek te verzenden.

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

Wanneer u een clientbibliotheek gebruikt, moet u uw abonnementssleutel doorgeven aan de constructeur van de **klasse FaceClient.** Bijvoorbeeld:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Als u de abonnementssleutel wilt ophalen, gaat u naar de Azure Marketplace vanuit de Azure-portal. Zie [Abonnementen voor](https://www.microsoft.com/cognitive-services/sign-up)meer informatie.

## <a name="step-3-create-the-persongroup"></a>Stap 3: De PersonGroup maken

Een PersonGroup met de naam 'MyPersonGroup' wordt gemaakt om de personen op te slaan.
De aanvraagtijd wordt in de wachtrij `_timeStampQueue` geplaatst om de algehele validatie te controleren.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Stap 4: De personen voor de persoonsgroep maken

Personen worden gelijktijdig gemaakt `await WaitCallLimitPerSecondAsync()` en worden ook toegepast om te voorkomen dat de gesprekslimiet wordt overschreden.

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

Gezichten die aan verschillende personen worden toegevoegd, worden gelijktijdig verwerkt. Gezichten die voor één specifieke persoon zijn toegevoegd, worden achtereenvolgens verwerkt.
Nogmaals, `await WaitCallLimitPerSecondAsync()` wordt ingeroepen om ervoor te zorgen dat de aanvraag frequentie binnen het bereik van de beperking.

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

In deze gids leerde je het proces van het creëren van een PersonGroup met een enorm aantal personen en gezichten. Verschillende herinneringen:

- Deze strategie is ook van toepassing op FaceLists en LargePersonGroups.
- Het toevoegen of verwijderen van gezichten aan verschillende FaceLists of personen in LargePersonGroups worden gelijktijdig verwerkt.
- Het toevoegen of verwijderen van gezichten aan een specifieke FaceList of persoon in een LargePersonGroup gebeurt achtereenvolgens.
- Voor de eenvoud wordt in deze handleiding verzuimd om met een mogelijke uitzondering om te gaan. Als u de robuustheid wilt vergroten, past u het juiste beleid voor nieuwe proeven toe.

De volgende kenmerken werden uitgelegd en gedemonstreerd:

- Persoonsgroepen maken met behulp van de [Persoonsgroep - API maken.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Personen maken met behulp van de [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API.
- Gezichten toevoegen aan personen met behulp van de [PersonGroup Person - Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API toevoegen.

## <a name="related-topics"></a>Verwante onderwerpen

- [Gezichten in een afbeelding identificeren](HowtoIdentifyFacesinImage.md)
- [Gezichten in een afbeelding detecteren](HowtoDetectFacesinImage.md)
- [De functie Grootschalig gebruiken](how-to-use-large-scale.md)
