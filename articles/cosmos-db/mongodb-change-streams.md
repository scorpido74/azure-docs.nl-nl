---
title: Streams wijzigen in de API van Azure Cosmos DB voor MongoDB
description: Meer informatie over het gebruik van wijzigingsstromen in de API van Azure Cosmos DB voor MongoDB om de wijzigingen in uw gegevens op te halen.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: 38e262abefe5444c1fe7586810f4b971cc7baf6c
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114151"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Streams wijzigen in de API van Azure Cosmos DB voor MongoDB

[De feedondersteuning](change-feed.md) wijzigen in de API van Azure Cosmos DB voor MongoDB is beschikbaar met behulp van de API voor wijzigingsstreams. Door de API voor wijzigingsstreams te gebruiken, kunnen uw toepassingen de wijzigingen in de verzameling of de items in één shard krijgen. Later u verdere acties ondernemen op basis van de resultaten. Wijzigingen in de items in de collectie worden vastgelegd in de volgorde van hun wijzigingstijd en de sorteervolgorde is gegarandeerd per shardsleutel.

> [!NOTE]
> Als u wijzigingsstreams wilt gebruiken, maakt u het account met versie 3.6 van de API van Azure Cosmos DB voor MongoDB of een latere versie. Als u de voorbeelden van wijzigingsstromen uitvoert ten `Unrecognized pipeline stage name: $changeStream` opzichte van een eerdere versie, ziet u mogelijk de fout.

## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen zijn van toepassing bij het gebruik van wijzigingsstromen:

* De `operationType` `updateDescription` eigenschappen en eigenschappen worden nog niet ondersteund in het uitvoerdocument.
* De `insert` `update`typen `replace` , en bewerkingen worden momenteel ondersteund. 
* Verwijderbewerking of andere gebeurtenissen worden nog niet ondersteund.

Vanwege deze beperkingen zijn de $match fase, $project fase en volledigeDocument-opties vereist, zoals in de vorige voorbeelden wordt weergegeven.

In tegenstelling tot de wijzigingsfeed in de SQL API van Azure Cosmos DB, is er geen aparte [Change Feed Processor Library](change-feed-processor.md) om wijzigingsstreams te verbruiken of een noodzaak voor een leasecontainer. Er is momenteel geen ondersteuning voor [Azure Functions-triggers](change-feed-functions.md) om wijzigingsstromen te verwerken.

## <a name="error-handling"></a>Foutafhandeling

De volgende foutcodes en berichten worden ondersteund bij het gebruik van wijzigingsstreams:

* **HTTP-foutcode 16500** - Wanneer de wijzigingsstroom wordt beperkt, wordt een lege pagina geretourneerd.

* **NamespaceNotFound (OperationType ongeldig)** - Als u wijzigingsstroom uitvoert op de verzameling die `NamespaceNotFound` niet bestaat of als de verzameling wordt verwijderd, wordt een fout geretourneerd. Omdat `operationType` de eigenschap niet kan worden geretourneerd in `operationType Invalidate` het uitvoerdocument, wordt de `NamespaceNotFound` fout geretourneerd in plaats van de fout.

## <a name="examples"></a>Voorbeelden

In het volgende voorbeeld ziet u hoe u wijzigingsstreams ontvangen op alle items in de verzameling. In dit voorbeeld wordt een cursor ontworpen om items te bekijken wanneer ze worden ingevoegd, bijgewerkt of vervangen. De `$match` fase, `$project` het `fullDocument` podium en de optie zijn vereist om de wijzigingsstreams te krijgen. Het bekijken van verwijderingsbewerkingen met behulp van wijzigingsstreams wordt momenteel niet ondersteund. Als tijdelijke oplossing u een zachte markering toevoegen aan de items die worden verwijderd. U bijvoorbeeld een kenmerk toevoegen aan het item 'verwijderd'. Wanneer u het item wilt verwijderen, u 'verwijderd' instellen op `true` en een TTL instellen op het item. Aangezien het bijwerken van `true` "verwijderd" naar een update is, is deze wijziging zichtbaar in de wijzigingsstroom.

### <a name="javascript"></a>Javascript:

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

### <a name="c"></a>C#:

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

## <a name="changes-within-a-single-shard"></a>Wijzigingen binnen één shard

In het volgende voorbeeld ziet u hoe u wijzigingen in de items in één shard krijgen. In dit voorbeeld worden de wijzigingen van items met een shardtoets gelijk aan "a" en de shardsleutelwaarde gelijk aan "1". Het is mogelijk om verschillende clients te lezen veranderingen van verschillende scherven in parallel.

```javascript
var cursor = db.coll.watch(
    [
        {
            $match: {
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="next-steps"></a>Volgende stappen

* [Gebruik de tijd om te leven om gegevens automatisch te verlopen in de API van Azure Cosmos DB voor MongoDB](mongodb-time-to-live.md)
* [Indexering in azure cosmos DB's API voor MongoDB](mongodb-indexing.md)
