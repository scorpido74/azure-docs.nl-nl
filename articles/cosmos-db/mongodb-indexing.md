---
title: Indexering in de API van Azure Cosmos DB voor MongoDB
description: Geeft een overzicht van de mogelijkheden voor het indexeren met de API van Azure Cosmos DB voor MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: e51e96c0c553bcf37284878cab11f3ec592ddd05
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753380"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexeren met de API van Azure Cosmos DB voor MongoDB

Azure Cosmos DB-API voor MongoDB maakt gebruik van automatische index beheer mogelijkheden van Cosmos DB. Als gevolg hiervan hebben gebruikers toegang tot het standaard indexerings beleid van Cosmos DB. Als er dus geen indexen zijn gedefinieerd door de gebruiker, of als er geen indexen zijn verwijderd, worden alle velden standaard automatisch geïndexeerd wanneer deze in een verzameling worden ingevoegd. Voor de meeste scenario's wordt het aangeraden om het standaardindexeringsbeleid te gebruiken dat is ingesteld voor het account.

## <a name="indexing-for-version-36"></a>Indexering voor versie 3,6

Accounts voor wire-protocol versie 3,6 bieden een ander standaard indexerings beleid dan het beleid van eerdere versies. Standaard wordt alleen het veld _id geïndexeerd. Voor het indexeren van extra velden moet de gebruiker de MongoDB-index beheer opdrachten Toep assen. Als u een sortering op een query wilt Toep assen, moet er momenteel een index worden gemaakt op basis van de velden die in de sorteer bewerking worden gebruikt.

### <a name="dropping-the-default-indexes-36"></a>Het verwijderen van de standaard indexen (3,6)

Voor accounts voor wire-protocol versie 3,6 is de enige standaard index _id, die niet kan worden verwijderd.

### <a name="creating-a-compound-index-36"></a>Een samengestelde index maken (3,6)

Echte samengestelde indexen worden ondersteund voor accounts met het 3,6-bedradings protocol. Met de volgende opdracht wordt een samengestelde index gemaakt op de velden ' a ' en ' b ': `db.coll.createIndex({a:1,b:1})`

Samengestelde indexen kunnen worden gebruikt om efficiënt te sorteren op meerdere velden tegelijk, zoals: `db.coll.find().sort({a:1,b:1})`

## <a name="indexing-for-version-32"></a>Indexering voor versie 3,2

### <a name="dropping-the-default-indexes-32"></a>Het verwijderen van de standaard indexen (3,2)

De volgende opdracht kan worden gebruikt voor het verwijderen van de standaardindexen voor een verzameling ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

### <a name="creating-a-compound-index-32"></a>Een samengestelde index maken (3,2)

Samengestelde indexen bevatten verwijzingen naar meerdere velden van een document. Logisch gezien zijn ze te vergelijken met het maken van meerdere afzonderlijke indexen per veld. Als u gebruik wilt maken van de optimalisaties die het resultaat zijn van de indexeringstechnieken van Cosmos DB, adviseren we om meerdere afzonderlijke indexen te maken in plaats van één (niet-unieke) samengestelde index.

## <a name="common-indexing-operations"></a>Algemene indexerings bewerkingen

De volgende bewerkingen zijn gebruikelijk voor beide accounts voor wire-protocol versie 3,6 en accounts die eerdere wire-protocol versies bedienen. 

## <a name="creating-unique-indexes"></a>Unieke indexen maken

[Unieke indexen](unique-keys.md) zijn handig om af te dwingen dat twee of meer documenten niet dezelfde waarde bevatten voor een of meer geïndexeerde velden.

>[!Important]
> Op dit moment kunnen alleen unieke indexen worden gemaakt wanneer de verzameling leeg is (bevat geen documenten).

Met de volgende opdracht maakt u een unieke index voor het veld ' student_id ':

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Overeenkomstig het gedrag van MongoDB is voor het maken van een unieke index voor shard-verzamelingen de shard-sleutel (partitiesleutel) nodig. In andere woorden, alle unieke indexen voor een shard-verzameling zijn samengestelde indexen waarbij een van de velden de partitiesleutel is.

Met de volgende opdrachten maakt u een shard verzameling ```coll``` (de shard-sleutel is ```university```) met een unieke index voor de velden student_id en university:

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

Als in het bovenstaande voorbeeld de component ```"university":1``` wordt weggelaten, treedt er een fout op en ziet u dit foutbericht:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>TTL indexen

Als u wilt instellen dat documenten in een bepaalde verzameling verlopen na bepaalde tijd, moet u een [TTL-index (Time-To-Live)](../cosmos-db/time-to-live.md) maken. Een TTL-index is een index van het veld _ts met een waarde voor 'expireAfterSeconds'.

Voorbeeld:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Met de bovenstaande opdracht worden alle documenten in de verzameling ```db.coll``` verwijderd die de afgelopen 10 seconden niet zijn gewijzigd.

> [!NOTE]
> **_ts** is een veld dat specifiek is voor Cosmos DB en dat niet toegankelijk is vanuit MongoDB-clients. Het is een gereserveerde eigenschap (systeemeigenschap) met het tijdstempel van de laatste wijziging van het document.

## <a name="migrating-collections-with-indexes"></a>Verzamelingen migreren met indexen

Op dit moment kunnen alleen unieke indexen worden gemaakt wanneer de verzameling geen documenten bevat. Veelgebruikte MongoDB-migratieprogramma's proberen de unieke indexen te maken nadat de gegevens zijn geïmporteerd. Om dit probleem te omzeilen, wordt aangeraden dat gebruikers hand matig de overeenkomstige verzamelingen en unieke indexen maken, in plaats van het migratie hulpprogramma toe te staan (voor ```mongorestore``` dit gedrag wordt bereikt met behulp van de `--noIndexRestore` markering in de opdracht regel).

## <a name="next-steps"></a>Volgende stappen

* [Indexering in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Gegevens in Azure Cosmos DB automatisch laten verlopen met TTL](../cosmos-db/time-to-live.md)
