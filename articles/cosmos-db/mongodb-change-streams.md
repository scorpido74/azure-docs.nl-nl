---
title: Streams wijzigen in de API van Azure Cosmos DB voor MongoDB
description: Meer informatie over het gebruik van Change streams n Azure Cosmos DB API voor MongoDB om de wijzigingen aan te brengen in uw gegevens.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: cc6b74a56d2a538d35e324090832e6c7e03e609f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647307"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Streams wijzigen in de API van Azure Cosmos DB voor MongoDB

[Change feed](change-feed.md) -ondersteuning in de Azure Cosmos DB-API voor MongoDb is beschikbaar via de Change streams-API. Door gebruik te maken van de Change streams-API, kunnen uw toepassingen de wijzigingen die zijn aangebracht aan de verzameling of aan de items in één Shard ophalen. Later kunt u verdere acties uitvoeren op basis van de resultaten. Wijzigingen in de items in de verzameling worden vastgelegd in de volg orde van hun wijzigings tijd en de sorteer volgorde wordt gegarandeerd per Shard-sleutel.

[!NOTE]
Als u een wijzigings stroom wilt gebruiken, maakt u het account met versie 3,6 van de API van Azure Cosmos DB voor MongoDB of een latere versie. Als u de voor beelden van de wijzigings stroom uitvoert voor een eerdere versie, ziet u mogelijk de `Unrecognized pipeline stage name: $changeStream` fout.

## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen zijn van toepassing wanneer u een wijzigings stroom gebruikt:

* De `operationType` `updateDescription` Eigenschappen en worden nog niet ondersteund in het uitvoer document.
* De `insert` `update` typen, en `replace` bewerkingen worden momenteel ondersteund. 
* De Verwijder bewerking of andere gebeurtenissen worden nog niet ondersteund.

Als gevolg van deze beperkingen zijn de opties $match fase, $project fase en fullDocument vereist, zoals in de vorige voor beelden wordt weer gegeven.

In tegens telling tot de wijzigings feed in de SQL-API van Azure Cosmos DB, is er geen afzonderlijke [feed voor wijzigings doorvoer](change-feed-processor.md) stroom voor het gebruik van wijzigings stromen of de behoefte aan een lease-container. Er is momenteel geen ondersteuning voor [Azure functions-triggers](change-feed-functions.md) om veranderingen stromen te verwerken.

## <a name="error-handling"></a>Foutafhandeling

De volgende fout codes en-berichten worden ondersteund bij het gebruik van wijzigings stromen:

* **HTTP-fout code 16500** -wanneer de wijzigings stroom wordt beperkt, wordt een lege pagina geretourneerd.

* **NamespaceNotFound (OperationType ongeldig)** : als u een wijzigings stroom uitvoert voor de verzameling die niet bestaat of als de verzameling wordt verwijderd, wordt een `NamespaceNotFound` fout geretourneerd. Omdat de `operationType` eigenschap niet kan worden geretourneerd in het uitvoer document, in plaats van de `operationType Invalidate` fout, `NamespaceNotFound` wordt de fout geretourneerd.

## <a name="examples"></a>Voorbeelden

In het volgende voor beeld ziet u hoe u een wijzigings stroom kunt ophalen voor alle items in de verzameling. In dit voor beeld wordt een cursor gemaakt om items te bekijken wanneer ze worden ingevoegd, bijgewerkt of vervangen. Het `$match` stadium, de `$project` fase en de `fullDocument` optie zijn vereist voor het ophalen van de wijzigings stromen. Het is niet mogelijk om delete-bewerkingen te volgen met behulp van wijzigings stromen. Als tijdelijke oplossing kunt u een zachte markering toevoegen voor de items die worden verwijderd. U kunt bijvoorbeeld een kenmerk toevoegen aan het item met de naam ' verwijderd '. Wanneer u het item wilt verwijderen, kunt u ' verwijderd ' instellen `true` en een TTL instellen voor het item. Sinds het bijwerken van ' verwijderd ' naar `true` is een update, is deze wijziging zichtbaar in de wijzigings stroom.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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
# <a name="c"></a>[C#](#tab/csharp)

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

## <a name="changes-within-a-single-shard"></a>Wijzigingen in één Shard

In het volgende voor beeld ziet u hoe u wijzigingen kunt aanbrengen in de items binnen één Shard. In dit voor beeld worden de wijzigingen van items met de Shard-sleutel gelijk aan "a" en de waarde van de Shard-sleutel gelijk aan "1". Het is mogelijk om verschillende clients parallel wijzigingen te laten lezen van verschillende Shards.

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

## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen zijn van toepassing wanneer u een wijzigings stroom gebruikt:

* De `operationType` `updateDescription` Eigenschappen en worden nog niet ondersteund in het uitvoer document.
* De `insert` `update` typen, en `replace` bewerkingen worden momenteel ondersteund. De Verwijder bewerking of andere gebeurtenissen worden nog niet ondersteund.

Als gevolg van deze beperkingen zijn de opties $match fase, $project fase en fullDocument vereist, zoals in de vorige voor beelden wordt weer gegeven.

## <a name="error-handling"></a>Foutafhandeling

De volgende fout codes en-berichten worden ondersteund bij het gebruik van wijzigings stromen:

* **HTTP-fout code 429** -wanneer de wijzigings stroom wordt beperkt, wordt een lege pagina geretourneerd.

* **NamespaceNotFound (OperationType ongeldig)** : als u een wijzigings stroom uitvoert voor de verzameling die niet bestaat of als de verzameling wordt verwijderd, wordt een `NamespaceNotFound` fout geretourneerd. Omdat de `operationType` eigenschap niet kan worden geretourneerd in het uitvoer document, in plaats van de `operationType Invalidate` fout, `NamespaceNotFound` wordt de fout geretourneerd.

## <a name="next-steps"></a>Volgende stappen

* [Time to Live gebruiken om gegevens automatisch te laten verlopen in de API van Azure Cosmos DB voor MongoDB](mongodb-time-to-live.md)
* [Indexering in de API van Azure Cosmos DB voor MongoDB](mongodb-indexing.md)