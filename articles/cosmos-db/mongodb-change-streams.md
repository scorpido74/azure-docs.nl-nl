---
title: Streams wijzigen in de API van Azure Cosmos DB voor MongoDB
description: Meer informatie over het gebruik van Change streams n Azure Cosmos DB API voor MongoDB om de wijzigingen aan te brengen in uw gegevens.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: ec1ec1a8a80953f8988355341ee7128bd29b982d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467774"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Streams wijzigen in de API van Azure Cosmos DB voor MongoDB

[Change feed](change-feed.md) -ondersteuning in de Azure Cosmos DB-API voor MongoDb is beschikbaar via de Change streams-API. Door gebruik te maken van de Change streams-API, kunnen uw toepassingen de wijzigingen die zijn aangebracht aan de verzameling of aan de items in één Shard ophalen. Later kunt u verdere acties uitvoeren op basis van de resultaten. Wijzigingen in de items in de verzameling worden vastgelegd in de volg orde van hun wijzigings tijd en de sorteer volgorde wordt gegarandeerd per Shard-sleutel.

> [!NOTE]
> Als u een wijzigings stroom wilt gebruiken, maakt u het account met versie 3,6 van de API van Azure Cosmos DB voor MongoDB of een latere versie. Als u de voor beelden van de wijzigings stroom uitvoert voor een eerdere versie, ziet u mogelijk de `Unrecognized pipeline stage name: $changeStream` fout. 

In het volgende voor beeld ziet u hoe u een wijzigings stroom kunt ophalen voor alle items in de verzameling. In dit voor beeld wordt een cursor gemaakt om items te bekijken wanneer ze worden ingevoegd, bijgewerkt of vervangen. De $match fase, $project fase en fullDocument-optie zijn vereist voor het ophalen van de wijzigings stromen. Het is niet mogelijk om delete-bewerkingen te volgen met behulp van wijzigings stromen. Als tijdelijke oplossing kunt u een zachte markering toevoegen voor de items die worden verwijderd. U kunt bijvoorbeeld een kenmerk toevoegen aan het item ' verwijderd ' en dit instellen op ' True ' en een TTL instellen voor het item, zodat u het automatisch kunt verwijderen, zodat u het kan registreren.

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

In het volgende voor beeld ziet u hoe u wijzigingen kunt aanbrengen in de items in één Shard. In dit voor beeld worden de wijzigingen van items met de Shard-sleutel gelijk aan "a" en de waarde van de Shard-sleutel gelijk aan "1".

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

* De eigenschappen `operationType` en `updateDescription` worden nog niet ondersteund in het uitvoer document.
* De bewerkings typen `insert`, `update`en `replace` worden momenteel ondersteund. De Verwijder bewerking of andere gebeurtenissen worden nog niet ondersteund.

Als gevolg van deze beperkingen zijn de opties $match fase, $project fase en fullDocument vereist, zoals in de vorige voor beelden wordt weer gegeven.

## <a name="error-handling"></a>Foutafhandeling

De volgende fout codes en-berichten worden ondersteund bij het gebruik van wijzigings stromen:

* **HTTP-fout code 429** -wanneer de wijzigings stroom wordt beperkt, wordt een lege pagina geretourneerd.

* **NamespaceNotFound (OperationType ongeldig)** : als u een wijzigings stroom uitvoert voor de verzameling die niet bestaat of als de verzameling wordt verwijderd, wordt een `NamespaceNotFound` fout geretourneerd. Omdat de eigenschap `operationType` niet kan worden geretourneerd in het uitvoer document, in plaats van de `operationType Invalidate` fout, wordt de `NamespaceNotFound` fout geretourneerd.

## <a name="next-steps"></a>Volgende stappen

* [Time to Live gebruiken om gegevens automatisch te laten verlopen in de API van Azure Cosmos DB voor MongoDB](mongodb-time-to-live.md)
* [Indexering in de API van Azure Cosmos DB voor MongoDB](mongodb-indexing.md)
