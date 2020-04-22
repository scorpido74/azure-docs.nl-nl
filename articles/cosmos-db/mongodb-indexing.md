---
title: Indexering beheren in de API van Azure Cosmos DB voor MongoDB
description: In dit artikel vindt u een overzicht van azure cosmos DB-indexeringsmogelijkheden met behulp van de MongoDB API.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732710"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Indexering beheren in de API van Azure Cosmos DB voor MongoDB

De API van Azure Cosmos DB voor MongoDB maakt gebruik van de kernmogelijkheden voor indexbeheer van Azure Cosmos DB. Dit artikel richt zich op het toevoegen van indexen met behulp van azure cosmos DB's API voor MongoDB. U ook een overzicht lezen [van indexering in Azure Cosmos DB](index-overview.md) dat relevant is voor alle API's.

## <a name="indexing-for-mongodb-server-version-36"></a>Indexering voor MongoDB-serverversie 3.6

De API van Azure Cosmos DB voor MongoDB-serverversie `_id` 3.6 indexeert automatisch het veld, dat niet kan worden verwijderd. Het dwingt automatisch de uniciteit `_id` van het veld per shardsleutel af.

Als u extra velden wilt indexeren, past u de opdrachten voor mongoDB-indexbeheer toe. Net als in MongoDB indexeert de API van Azure Cosmos `_id` DB voor MongoDB automatisch alleen het veld. Dit standaardindexeringsbeleid verschilt van de Azure Cosmos DB SQL API, die standaard alle velden indexeert.

Als u een sortering wilt toepassen op een query, moet u een index maken voor de velden die worden gebruikt in de sorteerbewerking.

## <a name="index-types"></a>Indextypen

### <a name="single-field"></a>Eén veld

U indexen maken op elk veld. De sorteervolgorde van de enkelvoudige veldindex doet er niet toe. Met de volgende opdracht wordt `name`een index op het veld geveld:

`db.coll.createIndex({name:1})`

Eén query maakt gebruik van meerdere indexen met één veld waar beschikbaar. U maximaal 500 indexen per container maken.

### <a name="compound-indexes-mongodb-server-version-36"></a>Samengestelde indexen (MongoDB-serverversie 3.6)

Azure Cosmos DB's API voor MongoDB ondersteunt samengestelde indexen voor accounts die het versie 3.6-draadprotocol gebruiken. U maximaal acht velden opnemen in een samengestelde index. In tegenstelling tot mongoDB moet u alleen een samengestelde index maken als uw query efficiënt moet sorteren op meerdere velden tegelijk. Voor query's met meerdere filters die niet hoeven te sorteren, maakt u meerdere enkelvoudige indexen in plaats van één samengestelde index.

Met de volgende opdracht wordt `name` een `age`samengestelde index op de velden gemaakt en:

`db.coll.createIndex({name:1,age:1})`

U samengestelde indexen gebruiken om efficiënt te sorteren op meerdere velden tegelijk, zoals in het volgende voorbeeld wordt weergegeven:

`db.coll.find().sort({name:1,age:1})`

U de voorgaande samengestelde index ook gebruiken om een query efficiënt te sorteren met de tegenovergestelde sorteervolgorde op alle velden. Hier volgt een voorbeeld:

`db.coll.find().sort({name:-1,age:-1})`

De volgorde van de paden in de samengestelde index moet echter precies overeenkomen met de query. Hier is een voorbeeld van een query waarvoor een extra samengestelde index nodig is:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Multikey-indexen

Azure Cosmos DB maakt multikey-indexen om inhoud te indexeren die is opgeslagen in arrays. Als u een veld indexeert met een matrixwaarde, indexeert Azure Cosmos DB automatisch elk element in de array.

### <a name="geospatial-indexes"></a>Georuimtelijke indexen

Veel georuimtelijke operatoren zullen profiteren van georuimtelijke indexen. Momenteel ondersteunt `2dsphere` de API van Azure Cosmos DB voor MongoDB indexen. De API ondersteunt `2d` nog geen indexen.

Hier is een voorbeeld van het maken `location` van een georuimtelijke index op het veld:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Tekstindexen

De API van Azure Cosmos DB voor MongoDB biedt momenteel geen ondersteuning voor tekstindexen. Voor zoekopdrachten naar tekst op tekenreeksen moet u [azure cognitive search-integratie](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) met Azure Cosmos DB gebruiken.

## <a name="index-properties"></a>Indexeigenschappen

De volgende bewerkingen zijn gebruikelijk voor accounts met draadprotocol versie 3.6 en accounts die eerdere versies bevatten. U meer informatie krijgen over [ondersteunde indexen en geïndexeerde eigenschappen](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Unieke indexen

[Unieke indexen](unique-keys.md) zijn handig om af te dwingen dat twee of meer documenten niet dezelfde waarde bevatten voor geïndexeerde velden.

> [!IMPORTANT]
> Unieke indexen kunnen alleen worden gemaakt als de verzameling leeg is (bevat geen documenten).

Met de volgende opdracht wordt `student_id`een unieke index op het veld geveld:

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

Voor geshardverzamelingen moet u de shardtoets (partitie) opgeven om een unieke index te maken. In andere woorden, alle unieke indexen voor een shard-verzameling zijn samengestelde indexen waarbij een van de velden de partitiesleutel is.

Met de volgende opdrachten wordt ```coll``` een geshard ```university```verzameling gemaakt (de shardtoets is ) met een unieke index op de velden `student_id` en: `university`

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

In het voorgaande voorbeeld geeft ```"university":1``` het weglaten van de component een fout met het volgende bericht:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL indexen

Als u het verlopen van documenten in een bepaalde verzameling wilt inschakelen, moet u een [time-to-live (TTL)-index](../cosmos-db/time-to-live.md)maken. Een TTL-index is `_ts` een index `expireAfterSeconds` op het veld met een waarde.

Voorbeeld:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

De vorige opdracht verwijdert alle ```db.coll``` documenten in de verzameling die in de afgelopen 10 seconden niet zijn gewijzigd.

> [!NOTE]
> Het **_ts** veld is specifiek voor Azure Cosmos DB en is niet toegankelijk vanuit MongoDB-clients. Het is een gereserveerde (systeem)eigenschap die de tijdstempel van de laatste wijziging van het document bevat.

## <a name="track-index-progress"></a>Indexvoortgang bijhouden

Versie 3.6 van de API van Azure Cosmos `currentOp()` DB voor MongoDB ondersteunt de opdracht om de indexvoortgang van een database-exemplaar bij te houden. Met deze opdracht retourneert een document dat informatie bevat over lopende bewerkingen in een database-instantie. U gebruikt `currentOp` de opdracht om alle lopende bewerkingen in native MongoDB bij te houden. In de API van Azure Cosmos DB voor MongoDB ondersteunt deze opdracht alleen het bijhouden van de indexbewerking.

Hier volgen enkele voorbeelden die laten `currentOp` zien hoe u de opdracht gebruiken om de voortgang van de index bij te houden:

* De indexvoortgang voor een verzameling ophalen:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* De indexvoortgang voor alle verzamelingen in een database weergeven:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* De indexvoortgang voor alle databases en verzamelingen in een Azure Cosmos-account weergeven:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>Voorbeelden van de uitvoer van indexvoortgang

De details van de indexvoortgang geven het voortgangspercentage voor de huidige indexbewerking weer. Hier is een voorbeeld dat de uitvoerdocumentindeling voor verschillende stadia van indexvoortgang weergeeft:

- Een indexbewerking op een 'foo'-verzameling en 'balk'-database die 60 procent voltooid is, heeft het volgende uitvoerdocument. Het `Inprog[0].progress.total` veld toont 100 als het doelvoltooiingspercentage.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

- Als een indexbewerking net is gestart op een 'foo'-verzameling en 'balk'-database, kan het uitvoerdocument 0 procent voortgang weergeven totdat het een meetbaar niveau bereikt.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

- Wanneer de lopende indexbewerking is voltooid, worden `inprog` in het uitvoerdocument lege bewerkingen weergegeven.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Updates voor achtergrondindex

Ongeacht de waarde die is opgegeven voor de eigenschap **Achtergrondindex,** worden indexupdates altijd op de achtergrond uitgevoerd. Omdat indexupdates Request Units (RU's) verbruiken met een lagere prioriteit dan andere databasebewerkingen, leiden indexwijzigingen niet tot downtime voor schrijft, updates of verwijderingen.

Wanneer u een nieuwe index toevoegt, gebruiken query's onmiddellijk de index. Dit betekent dat query's mogelijk niet alle overeenkomende resultaten retourneren en dit doen zonder fouten terug te sturen. Wanneer de indextransformatie is voltooid, zijn de queryresultaten consistent. U [de voortgang van de index bijhouden.](#track-index-progress)

## <a name="migrate-collections-with-indexes"></a>Verzamelingen migreren met indexen

Momenteel u alleen unieke indexen maken wanneer de verzameling geen documenten bevat. Populaire MongoDB-migratietools proberen de unieke indexen te maken na het importeren van de gegevens. Om dit probleem te omzeilen, u handmatig de bijbehorende verzamelingen en unieke indexen maken in plaats van het migratieprogramma toe te staan om het te proberen. (U dit ```mongorestore``` gedrag bereiken `--noIndexRestore` door de vlag in de opdrachtregel te gebruiken.)

## <a name="indexing-for-mongodb-version-32"></a>Indexering voor MongoDB versie 3.2

Beschikbare indexeringsfuncties en standaardinstellingen zijn verschillend voor Azure Cosmos-accounts die compatibel zijn met versie 3.2 van het MongoDB-draadprotocol. U [de versie van uw account controleren.](mongodb-feature-support-36.md#protocol-support) U upgraden naar de 3.6-versie door een ondersteuningsverzoek in te [dienen.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

Als u versie 3.2 gebruikt, worden in deze sectie de belangrijkste verschillen met versie 3.6 beschreven.

### <a name="dropping-default-indexes-version-32"></a>Standaardindexen laten vallen (versie 3.2)

In tegenstelling tot de 3.6-versie van de API van Azure Cosmos DB voor MongoDB, indexeert versie 3.2 standaard elke eigenschap. U de volgende opdracht gebruiken om deze```coll```standaardindexen voor een verzameling te laten vallen :

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Nadat u de standaardindexen hebt laten vallen, u meer indexen toevoegen zoals in versie 3.6.

### <a name="compound-indexes-version-32"></a>Samengestelde indexen (versie 3.2)

Samengestelde indexen bevatten verwijzingen naar meerdere velden van een document. Als u een samengestelde index wilt maken, u upgraden naar versie 3.6 door een ondersteuningsverzoek in te [dienen.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="next-steps"></a>Volgende stappen

* [Indexering in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Gegevens in Azure Cosmos DB automatisch laten verlopen met TTL](../cosmos-db/time-to-live.md)
