---
title: Streams wijzigen in de API van Azure Cosmos DB voor MongoDB
description: Meer informatie over het gebruik van wijzigingsstromen n Azure Cosmos DB's API voor MongoDB om de wijzigingen in uw gegevens op te halen.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: ec1ec1a8a80953f8988355341ee7128bd29b982d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467774"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Streams wijzigen in de API van Azure Cosmos DB voor MongoDB

[De feedondersteuning](change-feed.md) wijzigen in de API van Azure Cosmos DB voor MongoDB is beschikbaar met behulp van de API voor wijzigingsstreams. Door de API voor wijzigingsstreams te gebruiken, kunnen uw toepassingen de wijzigingen in de verzameling of de items in één shard krijgen. Later u verdere acties ondernemen op basis van de resultaten. Wijzigingen in de items in de collectie worden vastgelegd in de volgorde van hun wijzigingstijd en de sorteervolgorde is gegarandeerd per shardsleutel.

> [!NOTE]
> Als u wijzigingsstreams wilt gebruiken, maakt u het account met versie 3.6 van de API van Azure Cosmos DB voor MongoDB of een latere versie. Als u de voorbeelden van wijzigingsstromen uitvoert ten `Unrecognized pipeline stage name: $changeStream` opzichte van een eerdere versie, ziet u mogelijk de fout. 

In het volgende voorbeeld ziet u hoe u wijzigingsstreams ontvangen op alle items in de verzameling. In dit voorbeeld wordt een cursor ontworpen om items te bekijken wanneer ze worden ingevoegd, bijgewerkt of vervangen. De $match fase, $project fase en de optie FullDocument zijn vereist om de wijzigingsstreams op te halen. Het bekijken van verwijderingsbewerkingen met behulp van wijzigingsstreams wordt momenteel niet ondersteund. Als tijdelijke oplossing u een zachte markering toevoegen aan de items die worden verwijderd. U bijvoorbeeld een kenmerk toevoegen aan het item 'verwijderd' en het instellen op 'true' en een TTL instellen op het item, zodat u het automatisch verwijderen en bijhouden.

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

In het volgende voorbeeld ziet u hoe u wijzigingen in de items in één shard krijgen. In dit voorbeeld worden de wijzigingen van items met een shardtoets gelijk aan "a" en de shardsleutelwaarde gelijk aan "1".

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

De volgende beperkingen zijn van toepassing bij het gebruik van wijzigingsstromen:

* De `operationType` `updateDescription` eigenschappen en eigenschappen worden nog niet ondersteund in het uitvoerdocument.
* De `insert` `update`typen `replace` , en bewerkingen worden momenteel ondersteund. Verwijderbewerking of andere gebeurtenissen worden nog niet ondersteund.

Vanwege deze beperkingen zijn de $match fase, $project fase en volledigeDocument-opties vereist, zoals in de vorige voorbeelden wordt weergegeven.

## <a name="error-handling"></a>Foutafhandeling

De volgende foutcodes en berichten worden ondersteund bij het gebruik van wijzigingsstreams:

* **HTTP-foutcode 429** - Wanneer de wijzigingsstroom wordt beperkt, wordt een lege pagina geretourneerd.

* **NamespaceNotFound (OperationType ongeldig)** - Als u wijzigingsstroom uitvoert op de verzameling die `NamespaceNotFound` niet bestaat of als de verzameling wordt verwijderd, wordt een fout geretourneerd. Omdat `operationType` de eigenschap niet kan worden geretourneerd in `operationType Invalidate` het uitvoerdocument, wordt de `NamespaceNotFound` fout geretourneerd in plaats van de fout.

## <a name="next-steps"></a>Volgende stappen

* [Gebruik de tijd om te leven om gegevens automatisch te verlopen in de API van Azure Cosmos DB voor MongoDB](mongodb-time-to-live.md)
* [Indexering in azure cosmos DB's API voor MongoDB](mongodb-indexing.md)
