---
title: Streams wijzigen in de API van Azure Cosmos DB voor MongoDB
description: Meer informatie over het gebruik van wijzigings stromen in de API van Azure Cosmos DB voor MongoDB om de wijzigingen op te halen die u hebt aangebracht in uw gegevens.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: 7a6060448175530ada5ba95ceda470056a7be002
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872153"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Streams wijzigen in de API van Azure Cosmos DB voor MongoDB

[Change feed](change-feed.md) -ondersteuning in de Azure Cosmos DB-API voor MongoDb is beschikbaar via de Change streams-API. Door gebruik te maken van de Change streams-API, kunnen uw toepassingen de wijzigingen die zijn aangebracht aan de verzameling of aan de items in één Shard ophalen. Later kunt u verdere acties uitvoeren op basis van de resultaten. Wijzigingen in de items in de verzameling worden vastgelegd in de volg orde van hun wijzigings tijd en de sorteer volgorde wordt gegarandeerd per Shard-sleutel.

> [!NOTE]
> Als u een wijzigings stroom wilt gebruiken, maakt u het account met versie 3,6 van de API van Azure Cosmos DB voor MongoDB of een latere versie. Als u de voor beelden van de wijzigings stroom uitvoert voor een eerdere versie, `Unrecognized pipeline stage name: $changeStream` ziet u mogelijk de fout.

## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen zijn van toepassing wanneer u een wijzigings stroom gebruikt:

* De `operationType` eigenschappen `updateDescription` en worden nog niet ondersteund in het uitvoer document.
* De `insert`typen `update`, en `replace` bewerkingen worden momenteel ondersteund. 
* De Verwijder bewerking of andere gebeurtenissen worden nog niet ondersteund.

Als gevolg van deze beperkingen zijn de opties $match fase, $project fase en fullDocument vereist, zoals in de vorige voor beelden wordt weer gegeven.

In tegens telling tot de wijzigings feed in de SQL-API van Azure Cosmos DB, is er geen afzonderlijke [feed voor wijzigings doorvoer](change-feed-processor.md) stroom voor het gebruik van wijzigings stromen of de behoefte aan een lease-container. Er is momenteel geen ondersteuning voor [Azure functions-triggers](change-feed-functions.md) om veranderingen stromen te verwerken.

## <a name="error-handling"></a>Foutafhandeling

De volgende fout codes en-berichten worden ondersteund bij het gebruik van wijzigings stromen:

* **HTTP-fout code 16500** -wanneer de wijzigings stroom wordt beperkt, wordt een lege pagina geretourneerd.

* **NamespaceNotFound (OperationType ongeldig)** : als u een wijzigings stroom uitvoert voor de verzameling die niet bestaat of als de verzameling wordt verwijderd, wordt een `NamespaceNotFound` fout geretourneerd. Omdat de `operationType` eigenschap niet kan worden geretourneerd in het uitvoer document, in plaats `operationType Invalidate` van de fout `NamespaceNotFound` , wordt de fout geretourneerd.

## <a name="examples"></a>Voorbeelden

In het volgende voor beeld ziet u hoe u een wijzigings stroom kunt ophalen voor alle items in de verzameling. In dit voor beeld wordt een cursor gemaakt om items te bekijken wanneer ze worden ingevoegd, bijgewerkt of vervangen. Het `$match` stadium, `$project` de fase en `fullDocument` de optie zijn vereist voor het ophalen van de wijzigings stromen. Het is niet mogelijk om delete-bewerkingen te volgen met behulp van wijzigings stromen. Als tijdelijke oplossing kunt u een zachte markering toevoegen voor de items die worden verwijderd. U kunt bijvoorbeeld een kenmerk toevoegen aan het item met de naam ' verwijderd '. Wanneer u het item wilt verwijderen, kunt u ' verwijderd ' instellen `true` en een TTL instellen voor het item. Sinds het bijwerken van ' verwijderd `true` ' naar is een update, is deze wijziging zichtbaar in de wijzigings stroom.

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
# <a name="c"></a>[G #](#tab/csharp)

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

## <a name="next-steps"></a>Volgende stappen

* [Time to Live gebruiken om gegevens automatisch te laten verlopen in de API van Azure Cosmos DB voor MongoDB](mongodb-time-to-live.md)
* [Indexering in de API van Azure Cosmos DB voor MongoDB](mongodb-indexing.md)
