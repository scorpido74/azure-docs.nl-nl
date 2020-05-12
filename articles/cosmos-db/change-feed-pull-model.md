---
title: Pull-model voor feed wijzigen
description: Meer informatie over het gebruik van het Azure Cosmos DB model voor het activeren van wijzigingen in feed voor het lezen van de wijzigings feed en de verschillen tussen het pull-model en de wijzigings processor
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2020
ms.reviewer: sngun
ms.openlocfilehash: 0e6e243ceb73ca2a1180e59ba6c6b4095ed6069a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116710"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Pull-model voor feed wijzigen in Azure Cosmos DB

Met het pull-model voor wijzigings invoer kunt u de Azure Cosmos DB wijzigings feed gebruiken in uw eigen tempo. Zoals u al kunt doen met de [Change feed-processor](change-feed-processor.md), kunt u het pull-model wijzigings feed gebruiken om de verwerking van wijzigingen in meerdere parallelliseren te verwerken.

> [!NOTE]
> Het pull-model voor de wijzigings feed is momenteel in [de preview-versie van de Azure Cosmos db .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) . De preview-versie is nog niet beschikbaar voor andere SDK-versies.

## <a name="consuming-an-entire-containers-changes"></a>De wijzigingen van een hele container gebruiken

U kunt een maken `FeedIterator` om de wijzigings feed te verwerken met het pull-model. Wanneer u voor het eerst een maakt `FeedIterator` , kunt u een optioneel `StartTime` binnen opgeven `ChangeFeedRequestOptions` . Als u niets opgeeft, `StartTime` wordt de huidige tijd.

De `FeedIterator` is beschikbaar in twee soorten. Naast de voor beelden hieronder die entiteits objecten retour neren, kunt u ook het antwoord verkrijgen met `Stream` ondersteuning. Met streams kunt u gegevens lezen zonder dat deze eerst worden gedeserialiseerd, op client bronnen worden opgeslagen.

Hier volgt een voor beeld voor het verkrijgen van het `FeedIterator` retour neren van entiteits objecten, in dit geval een `User` object:

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

Hier volgt een voor beeld voor het verkrijgen van een `FeedIterator` `Stream` :

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

Met behulp `FeedIterator` van een kunt u eenvoudig de wijzigings feed van een hele container in uw eigen tempo verwerken. Hier volgt een voorbeeld:

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator(new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>De wijzigingen van een partitie sleutel gebruiken

In sommige gevallen wilt u mogelijk alleen de wijzigingen van een specifieke partitie sleutel verwerken. U kunt een `FeedIterator` voor een specifieke partitie sleutel verkrijgen en de wijzigingen op dezelfde manier verwerken als voor een hele container:

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator(new PartitionKey("myPartitionKeyValueToRead"), new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForThePartitionKey.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>FeedRange gebruiken voor parallel Lise ring

In de [Change feed-processor](change-feed-processor.md)wordt werk automatisch verspreid over meerdere gebruikers. In het pull-model voor het wijzigen van de feed kunt u de gebruiken `FeedRange` om de parallelliseren van de wijzigings feed te verwerken. Een `FeedRange` vertegenwoordigt een bereik van partitie sleutel waarden.

Hier volgt een voor beeld waarin wordt getoond hoe u een lijst met bereiken voor uw container kunt verkrijgen:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Wanneer u een lijst met FeedRanges voor uw container ontvangt, krijgt u één `FeedRange` per [fysieke partitie](partition-data.md#physical-partitions).

Met behulp `FeedRange` van een kunt u vervolgens een `FeedIterator` parallelliseren maken om de verwerking van de wijzigings feed op meerdere computers of threads te verwerken. In tegens telling tot het vorige voor beeld dat laat zien hoe `FeedIterator` u één voor de hele container kunt verkrijgen, kunt u de gebruiken `FeedRange` om meerdere FeedIterators te verkrijgen die de wijzigings feed parallel kunnen verwerken.

In het geval waar u FeedRanges wilt gebruiken, moet u een Orchestrator-proces hebben dat FeedRanges verkrijgt en distribueert naar deze machines. Deze distributie kan het volgende zijn:

* `FeedRange.ToJsonString`Deze teken reeks waarde gebruiken en distribueren. De consumenten kunnen deze waarde gebruiken met`FeedRange.FromJsonString`
* Als de distributie in het proces wordt uitgevoerd, wordt de `FeedRange` object verwijzing door gegeven.

Hier volgt een voor beeld waarin wordt uitgelegd hoe u vanaf het begin van de wijzigings feed van de container kunt lezen met behulp van twee hypothetische afzonderlijke machines die parallel worden gelezen:

Machine 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<Person>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorA.HasMoreResults)
{
   FeedResponse<User> users = await iteratorA.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

Machine 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ranges[1], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorB.HasMoreResults)
{
   FeedResponse<User> users = await iteratorB.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="saving-continuation-tokens"></a>Bezig met opslaan van vervolg tokens

U kunt de positie van uw opslaan `FeedIterator` door een vervolg token te maken. Een vervolg token is een teken reeks waarde waarmee de laatst verwerkte wijzigingen van de FeedIterator worden bijgehouden. Hierdoor kan de service `FeedIterator` op dit moment later worden hervat. Met de volgende code wordt de wijzigings feed gelezen sinds het maken van de container. Nadat er geen wijzigingen meer beschikbaar zijn, wordt er een vervolg token bewaard, zodat het gebruik van de wijzigings toevoer later kan worden hervat.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

string continuation = null;

while (iterator.HasMoreResults)
{
   FeedResponse<User> users = await iterator.ReadNextAsync();
   continuation = users.ContinuationToken;

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(continuation);
```

## <a name="comparing-with-change-feed-processor"></a>Vergelijken met de processor voor wijzigings invoer

In veel scenario's kan de wijzigings feed worden verwerkt met behulp van de [Change feed-processor](change-feed-processor.md) of het pull-model. De vervolg tokens van het pull-model en de lease-container van de wijzigings feed zijn beide ' blad wijzers ' voor het laatst verwerkte item (of een batch met items) in de wijzigings feed.
U kunt vervolg tokens echter niet converteren naar een lease container (of andersom).

Overweeg het gebruik van het pull-model in de volgende scenario's:

- U wilt een eenmalige Lees bewerking uitvoeren van de bestaande gegevens in de wijzigings feed
- U wilt alleen wijzigingen van een bepaalde partitie sleutel lezen
- U wilt geen push model en wilt u de wijzigings feed in uw eigen tempo gebruiken

Hier volgen enkele belang rijke verschillen tussen de Change feed-processor en het pull-model:

|  | Verwerker van wijzigingenfeed| Pull-model |
| --- | --- | --- |
| Bijhouden van huidige punt in verwerking wijzigings feed | Lease (opgeslagen in een Azure Cosmos DB container) | Vervolg token (opgeslagen in het geheugen of hand matig bewaard) |
| Mogelijkheid om eerdere wijzigingen opnieuw af te spelen | Ja, met push model | Ja, met pull-model|
| Navragen voor toekomstige wijzigingen | Controleert automatisch op wijzigingen op basis van door de gebruiker opgegeven`WithPollInterval` | Handmatig |
| Wijzigingen van de hele container verwerken | Ja, en automatisch geparallelleerd voor meerdere threads/machine die uit dezelfde container worden verbruikt| Ja, en hand matig geparallelleerd met behulp van FeedTokens |
| Wijzigingen van slechts één partitie sleutel verwerken | Niet ondersteund | Ja|
| Ondersteunings niveau | Algemeen verkrijgbaar | Preview |

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van wijzigings feed](change-feed.md)
* [De Change feed-processor gebruiken](change-feed-processor.md)
* [Azure Functions activeren](change-feed-functions.md)