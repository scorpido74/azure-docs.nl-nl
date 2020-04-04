---
title: Indexering in azure cosmos DB's API voor MongoDB
description: Geeft een overzicht van de indexeringsmogelijkheden met de API van Azure Cosmos DB voor MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: f3f369928270c77557337bfdb1037cc5174c39f2
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637967"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexering met behulp van azure cosmos DB's API voor MongoDB

De API van Azure Cosmos DB voor MongoDB maakt gebruik van de kernmogelijkheden voor indexbeheer van Azure Cosmos DB. Dit document richt zich op het toevoegen van indexen met behulp van de API van Azure Cosmos DB voor MongoDB. U ook een overzicht lezen [van indexering in Azure Cosmos DB](index-overview.md) dat relevant is voor alle API's.

## <a name="indexing-for-version-36"></a>Indexering voor versie 3.6

Het `_id` veld wordt altijd automatisch geïndexeerd en kan niet worden verwijderd. Azure Cosmos DB's API voor MongoDB dwingt `_id` automatisch uniciteit van het veld per shardtoets af.

Als u extra velden wilt indexeren, moet u de opdrachten voor mongoDB-indexbeheer toepassen. Net als in MongoDB wordt alleen het `_id` veld automatisch geïndexeerd. Dit standaardindexeringsbeleid verschilt van de Azure Cosmos DB SQL API, die standaard alle velden indexeert.

Als u een sortering op een query wilt toepassen, moet een index worden gemaakt op de velden die in de sorteerbewerking worden gebruikt.

## <a name="index-types"></a>Indextypen

### <a name="single-field"></a>Eén veld

U indexen maken op elk veld. De sorteervolgorde van de enkelvoudige veldindex doet er niet toe. Met de volgende opdracht wordt `name`een index op het veld gemaakt:

`db.coll.createIndex({name:1})`

Eén query maakt gebruik van meerdere indexen met één veld, indien beschikbaar. U maximaal 500 indexen per container maken.

### <a name="compound-indexes-36"></a>Samengestelde indexen (3.6)

Samengestelde indexen worden ondersteund voor accounts met behulp van het 3.6-draadprotocol. U maximaal 8 velden opnemen in een samengestelde index. In tegenstelling tot mongoDB moet u alleen een samengestelde index maken als uw query efficiënt moet sorteren op meerdere velden tegelijk. Voor query's met meerdere filters die niet hoeven te sorteren, moet u meerdere enkelvoudige indexen maken in plaats van één samengestelde index.

Met de volgende opdracht wordt een `name` `age`samengestelde index op de velden gemaakt en:

`db.coll.createIndex({name:1,age:1})`

Samengestelde indexen kunnen worden gebruikt om efficiënt te sorteren op meerdere velden tegelijk, zoals:

`db.coll.find().sort({name:1,age:1})`

De bovenstaande samengestelde index kan ook worden gebruikt voor efficiënt sorteren op een query met de tegenovergestelde sorteervolgorde op alle velden. Hier volgt een voorbeeld:

`db.coll.find().sort({name:-1,age:-1})`

De volgorde van de paden in de samengestelde index moet echter precies overeenkomen met de query. Hier is een voorbeeld van een query waarvoor een extra samengestelde index nodig is:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Multikey-indexen

Azure Cosmos DB maakt multikey-indexen om inhoud te indexeren die is opgeslagen in arrays. Als u een veld indexeert met een matrixwaarde, indexeert Azure Cosmos DB automatisch elk element in de array.

### <a name="geospatial-indexes"></a>Georuimtelijke indexen

Veel georuimtelijke operatoren zullen profiteren van georuimtelijke indexen. Momenteel ondersteunt `2dsphere` de API van Azure Cosmos DB voor MongoDB indexen. `2d`indexen worden nog niet ondersteund.

Hier is een voorbeeld voor het maken `location` van een georuimtelijke index op het veld:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Tekstindexen

Tekstindexen worden momenteel niet ondersteund. Voor zoekopdrachten naar tekst op tekenreeksen moet u de integratie [van Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) met Azure Cosmos DB gebruiken.

## <a name="index-properties"></a>Indexeigenschappen

De volgende bewerkingen zijn gebruikelijk voor zowel accounts met draadprotocol versie 3.6 als accounts met eerdere versies van het draadprotocol. U ook meer te weten komen over [ondersteunde indexen en geïndexeerde eigenschappen.](mongodb-feature-support-36.md#indexes-and-index-properties)

### <a name="unique-indexes"></a>Unieke indexen

[Unieke indexen](unique-keys.md) zijn handig om af te dwingen dat twee of meer documenten niet dezelfde waarde bevatten voor een of meer geïndexeerde velden.

>[!Important]
> Unieke indexen kunnen alleen worden gemaakt als de verzameling leeg is (bevat geen documenten).

Met de volgende opdracht wordt een unieke index gemaakt op het veld 'student_id':

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

Voor geshardverzamelingen vereist het maken van een unieke index het leveren van de shard (partitie) sleutel. In andere woorden, alle unieke indexen voor een shard-verzameling zijn samengestelde indexen waarbij een van de velden de partitiesleutel is.

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

### <a name="ttl-indexes"></a>TTL indexen

Als u wilt instellen dat documenten in een bepaalde verzameling verlopen na bepaalde tijd, moet u een [TTL-index (Time-To-Live)](../cosmos-db/time-to-live.md) maken. Een TTL-index is een index van het veld _ts met een waarde voor 'expireAfterSeconds'.

Voorbeeld:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Met de bovenstaande opdracht worden alle documenten in de verzameling ```db.coll``` verwijderd die de afgelopen 10 seconden niet zijn gewijzigd.

> [!NOTE]
> **_ts** is een Azure Cosmos DB-specifiek veld en is niet toegankelijk vanuit MongoDB-clients. Het is een gereserveerde eigenschap (systeemeigenschap) met het tijdstempel van de laatste wijziging van het document.

## <a name="track-the-index-progress"></a>De indexvoortgang bijhouden

De 3.6-versie van de API van Azure Cosmos `currentOp()` DB voor MongoDB-accounts ondersteunt de opdracht om de indexvoortgang van een database-exemplaar bij te houden. Met deze opdracht retourneert een document dat informatie bevat over de lopende bewerkingen in een database-instantie. De `currentOp` opdracht wordt gebruikt om alle lopende bewerkingen in native MongoDB bij te houden, terwijl deze opdracht in de API van Azure Cosmos DB voor MongoDB alleen het bijhouden van de indexbewerking ondersteunt.

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

De details van de indexvoortgang bevatten het voortgangspercentage voor de huidige indexbewerking. In het volgende voorbeeld wordt de uitvoerdocumentnotatie weergegeven voor verschillende stadia van indexvoortgang:

1. Als de indexbewerking op een 'foo'-verzameling en 'balk'-database met een totale indexering van 60 % het volgende uitvoerdocument heeft. `Inprog[0].progress.total`100 als doelvoltooiing.

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

2. Voor een indexbewerking die net is gestart op een 'foo'-verzameling en 'balk'-database, kan het uitvoerdocument 0% voortgang weergeven totdat het een meetbaar niveau bereikt.

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

3. Wanneer de lopende indexbewerking is voltooid, wordt in het uitvoerdocument lege inprog-bewerkingen weergegeven.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Updates voor achtergrondindex

Ongeacht de waarde die is opgegeven voor de eigenschap **Achtergrondindex,** worden indexupdates altijd op de achtergrond uitgevoerd. Indexupdates verbruiken RU's met een lagere prioriteit dan andere databasebewerkingen. Daarom leiden indexwijzigingen niet tot downtime voor schrijf-, updates- of verwijderingen.

Bij het toevoegen van een nieuwe index zullen query's deze onmiddellijk gebruiken. Dit betekent dat query's mogelijk niet alle overeenkomende resultaten retourneren en dit doen zonder fouten terug te sturen. Zodra de indextransformatie is voltooid, zijn de queryresultaten consistent. U [de voortgang van de index bijhouden.](#track-the-index-progress)

## <a name="migrating-collections-with-indexes"></a>Verzamelingen migreren met indexen

Op dit moment kunnen alleen unieke indexen worden gemaakt wanneer de verzameling geen documenten bevat. Veelgebruikte MongoDB-migratieprogramma's proberen de unieke indexen te maken nadat de gegevens zijn geïmporteerd. Om dit probleem te omzeilen, wordt voorgesteld dat gebruikers handmatig de bijbehorende verzamelingen ```mongorestore``` en unieke indexen `--noIndexRestore` maken, in plaats van het migratieprogramma toe te staan (voor dit gedrag wordt bereikt met behulp van de vlag in de opdrachtregel).

## <a name="indexing-for-version-32"></a>Indexering voor versie 3.2

Voor Azure Cosmos DB-accounts die compatibel zijn met de 3.2-versie van het MongoDB-draadprotocol, zijn de beschikbare indexeringsfuncties en standaardinstellingen verschillend. U [de versie van uw account controleren.](mongodb-feature-support-36.md#protocol-support) U upgraden naar de 3.6-versie door een ondersteuningsverzoek in te [dienen.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

Als u versie 3.2 gebruikt, worden in deze sectie belangrijke verschillen met versie 3.6 beschreven.

### <a name="dropping-the-default-indexes-32"></a>De standaardindexen laten vallen (3.2)

In tegenstelling tot de 3.6-versie van de API van Azure Cosmos DB voor MongoDB, indexeert 3.2-versie elke eigenschap standaard. De volgende opdracht kan worden gebruikt om deze ```coll```standaardindexen voor een verzameling te laten vallen:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Nadat u de standaardindexen hebt laten vallen, u in versie 3.6 extra indexen toevoegen.

### <a name="compound-indexes-32"></a>Samengestelde indexen (3.2)

Samengestelde indexen bevatten verwijzingen naar meerdere velden van een document. Als u een samengestelde index wilt maken, u upgraden naar 3.6-versie door een ondersteuningsverzoek in te [dienen.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="next-steps"></a>Volgende stappen

* [Indexering in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Gegevens in Azure Cosmos DB automatisch laten verlopen met TTL](../cosmos-db/time-to-live.md)
