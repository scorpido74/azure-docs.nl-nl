---
title: Indexering beheren in de API van Azure Cosmos DB voor MongoDB
description: Dit artikel bevat een overzicht van Azure Cosmos DB indexerings mogelijkheden met behulp van de MongoDB-API.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732710"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Indexering beheren in de API van Azure Cosmos DB voor MongoDB

Azure Cosmos DB-API voor MongoDB maakt gebruik van de belangrijkste mogelijkheden voor index beheer van Azure Cosmos DB. In dit artikel wordt uitgelegd hoe u indexen toevoegt met behulp van de API van Azure Cosmos DB voor MongoDB. U kunt ook een [overzicht van het indexeren in azure Cosmos DB](index-overview.md) lezen dat relevant is voor alle api's.

## <a name="indexing-for-mongodb-server-version-36"></a>Indexering voor MongoDB-Server versie 3,6

De API van Azure Cosmos DB voor MongoDB-Server versie 3,6 indexeert automatisch het `_id` veld dat niet kan worden verwijderd. De unieke waarde van het `_id` veld wordt automatisch afgedwongen per Shard-sleutel.

Als u extra velden wilt indexeren, past u de MongoDB-index beheer opdrachten toe. Net als in MongoDB indexeert Azure Cosmos DB-API voor MongoDB automatisch `_id` het veld. Dit standaard indexerings beleid wijkt af van de Azure Cosmos DB SQL-API, waarmee standaard alle velden worden geïndexeerd.

Als u een sortering op een query wilt Toep assen, moet u een index maken voor de velden die in de sorteer bewerking worden gebruikt.

## <a name="index-types"></a>Indextypen

### <a name="single-field"></a>Eén veld

U kunt indexen maken voor één veld. De sorteer volgorde van de enkelvoudige veld index is niet van belang. Met de volgende opdracht maakt u een index voor `name`het veld:

`db.coll.createIndex({name:1})`

Bij een query worden meerdere enkelvoudige veld indexen gebruikt, waar beschikbaar. U kunt Maxi maal 500 enkelvoudige veld indexen per container maken.

### <a name="compound-indexes-mongodb-server-version-36"></a>Samengestelde indexen (MongoDB Server versie 3,6)

Azure Cosmos DB-API voor MongoDB ondersteunt samengestelde indexen voor accounts die gebruikmaken van versie 3,6 wire protocol. U kunt Maxi maal acht velden in een samengestelde index toevoegen. In tegens telling tot in MongoDB moet u alleen een samengestelde index maken als uw query efficiënt moet worden gesorteerd op meerdere velden tegelijk. Voor query's met meerdere filters die niet hoeven te worden gesorteerd, maakt u meerdere enkelvoudige veld indexen in plaats van één samengestelde index.

Met de volgende opdracht maakt u een samengestelde index voor `name` de `age`velden en:

`db.coll.createIndex({name:1,age:1})`

U kunt samengestelde indexen gebruiken om efficiënt te sorteren op meerdere velden tegelijk, zoals wordt weer gegeven in het volgende voor beeld:

`db.coll.find().sort({name:1,age:1})`

U kunt ook de voor gaande samengestelde index gebruiken om efficiënt te sorteren op een query met de tegenovergestelde sorteer volgorde voor alle velden. Hier volgt een voorbeeld:

`db.coll.find().sort({name:-1,age:-1})`

De volg orde van de paden in de samengestelde index moet echter exact overeenkomen met de query. Hier volgt een voor beeld van een query waarvoor een extra samengestelde index is vereist:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>MultiKey-indexen

Azure Cosmos DB maakt MultiKey-indexen om inhoud te indexeren die in matrices is opgeslagen. Als u een veld indexeert met een matrix waarde, Azure Cosmos DB automatisch elk element in de matrix geïndexeerd.

### <a name="geospatial-indexes"></a>Georuimtelijke indexen

Veel georuimtelijke Opera tors profiteren van georuimtelijke indexen. Momenteel biedt de API van Azure Cosmos DB voor MongoDB `2dsphere` ondersteuning voor indexen. De API biedt nog geen ondersteuning `2d` voor indexen.

Hier volgt een voor beeld van het maken van een georuimtelijke index op het `location` veld:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Tekst indexen

De API van Azure Cosmos DB voor MongoDB biedt momenteel geen ondersteuning voor tekst indexen. Voor tekst zoekopdracht query's op teken reeksen moet u [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) -integratie met Azure Cosmos DB gebruiken.

## <a name="index-properties"></a>Eigenschappen van index

De volgende bewerkingen zijn gebruikelijk voor accounts die zijn gereserveerd van wire-protocol versie 3,6 en accounts die eerdere versies bieden. Meer informatie over [ondersteunde indexen en geïndexeerde eigenschappen](mongodb-feature-support-36.md#indexes-and-index-properties)vindt u hier.

### <a name="unique-indexes"></a>Unieke indexen

[Unieke indexen](unique-keys.md) zijn handig voor het afdwingen dat twee of meer documenten niet dezelfde waarde voor geïndexeerde velden bevatten.

> [!IMPORTANT]
> Unieke indexen kunnen alleen worden gemaakt wanneer de verzameling leeg is (geen documenten bevat).

Met de volgende opdracht maakt u een unieke index voor `student_id`het veld:

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

Voor Shard-verzamelingen moet u de Shard-sleutel (Partition) opgeven om een unieke index te maken. In andere woorden, alle unieke indexen voor een shard-verzameling zijn samengestelde indexen waarbij een van de velden de partitiesleutel is.

Met de volgende opdrachten maakt u een ```coll``` Shard-verzameling (de ```university```Shard-sleutel is) met een unieke `student_id` index `university`voor de velden en:

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

In het voor gaande voor beeld wordt met ```"university":1``` het weglaten van de component een fout geretourneerd met het volgende bericht:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL indexen

Als u het verlopen van documenten in een bepaalde verzameling wilt inschakelen, moet u een [time-to-Live-index (TTL)](../cosmos-db/time-to-live.md)maken. Een TTL-index is een index van `_ts` het veld met `expireAfterSeconds` een waarde.

Voorbeeld:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Met de voor gaande opdracht worden alle documenten ```db.coll``` in de verzameling verwijderd die in de afgelopen 10 seconden niet zijn gewijzigd.

> [!NOTE]
> Het veld **_ts** is specifiek voor Azure Cosmos DB en is niet toegankelijk vanuit MongoDb-clients. Het is een gereserveerde eigenschap (systeem) die het tijds tempel van de laatste wijziging van het document bevat.

## <a name="track-index-progress"></a>Voortgang van index bijhouden

Versie 3,6 van de API van Azure Cosmos DB voor MongoDB ondersteunt `currentOp()` de opdracht voor het bijhouden van de voortgang van de index voor een Data Base-exemplaar. Met deze opdracht wordt een document geretourneerd dat informatie bevat over bewerkingen die worden uitgevoerd op een Data Base-exemplaar. U gebruikt de `currentOp` opdracht om alle bewerkingen in uitvoering bij te houden in systeem eigen MongoDb. In de API van Azure Cosmos DB voor MongoDB ondersteunt deze opdracht alleen het volgen van de index bewerking.

Hier volgen enkele voor beelden van het gebruik van de `currentOp` opdracht voor het volgen van de voortgang van de index:

* De voortgang van de index voor een verzameling ophalen:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* De voortgang van de index ophalen voor alle verzamelingen in een Data Base:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* De index voortgang ophalen voor alle data bases en verzamelingen in een Azure Cosmos-account:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>Voor beelden van uitvoer van index voortgang

In de details van de index voortgang wordt het percentage voortgang van de huidige index bewerking weer gegeven. Hier volgt een voor beeld waarin de indeling van het uitvoer document wordt weer gegeven voor de verschillende stadia van de index voortgang:

- Een index bewerking op een ' foo '-verzameling en ' bar '-data base die 60 procent is voltooid, heeft het volgende uitvoer document. In `Inprog[0].progress.total` het veld wordt 100 weer gegeven als het voltooiings percentage van het doel.

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

- Als een index bewerking zojuist is gestart op een ' foo '-verzameling en een staaf-data base, kan het uitvoer document 0 procent van de voortgang weer geven totdat het een meetbaar niveau bereikt.

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

- Wanneer de index bewerking in uitvoering is voltooid, worden in het uitvoer document lege `inprog` bewerkingen weer gegeven.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Achtergrond-index updates

Ongeacht de waarde die is opgegeven voor de eigenschap **achtergrond** index, worden index updates altijd op de achtergrond uitgevoerd. Omdat index updates gebruikmaken van aanvraag eenheden (RUs) met een lagere prioriteit dan andere database bewerkingen, hebben index wijzigingen geen invloed op uitval voor schrijf bewerkingen, updates of verwijderingen.

Wanneer u een nieuwe index toevoegt, zullen query's onmiddellijk de index gebruiken. Dit betekent dat query's mogelijk niet alle overeenkomende resultaten retour neren en dit doen zonder dat er fouten worden geretourneerd. Wanneer de index transformatie is voltooid, zijn de resultaten van de query consistent. U kunt de voortgang van de [index bijhouden](#track-index-progress).

## <a name="migrate-collections-with-indexes"></a>Verzamelingen migreren met indexen

Op dit moment kunt u alleen unieke indexen maken als de verzameling geen documenten bevat. Populaire hulpprogram ma's voor migratie van MongoDB proberen de unieke indexen te maken nadat u de gegevens hebt geïmporteerd. U kunt dit probleem omzeilen door hand matig de overeenkomstige verzamelingen en unieke indexen te maken, in plaats van het hulp programma voor migratie uit te voeren. (U kunt dit gedrag ```mongorestore``` betrekken met behulp `--noIndexRestore` van de vlag op de opdracht regel.)

## <a name="indexing-for-mongodb-version-32"></a>Indexeren voor MongoDB-versie 3,2

Beschik bare indexerings functies en standaard waarden zijn anders voor Azure Cosmos-accounts die compatibel zijn met versie 3,2 van het MongoDB wire-protocol. U kunt [de versie van uw account controleren](mongodb-feature-support-36.md#protocol-support). U kunt een upgrade uitvoeren naar de 3,6-versie door een [ondersteunings aanvraag](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)in te dienen.

Als u versie 3,2 gebruikt, geeft deze sectie een overzicht van de belangrijkste verschillen met versie 3,6.

### <a name="dropping-default-indexes-version-32"></a>Standaard indexen verwijderen (versie 3,2)

In tegens telling tot de 3,6-versie van de API van Azure Cosmos DB voor MongoDB, indexeert versie 3,2 standaard elke eigenschap. U kunt de volgende opdracht gebruiken om deze standaard indexen voor een verzameling te verwijderen```coll```():

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Nadat u de standaard indexen hebt verwijderd, kunt u meer indexen toevoegen zoals u dat zou doen in versie 3,6.

### <a name="compound-indexes-version-32"></a>Samengestelde indexen (versie 3,2)

Samengestelde indexen bevatten verwijzingen naar meerdere velden van een document. Als u een samengestelde index wilt maken, moet u een upgrade uitvoeren naar versie 3,6 door een [ondersteunings aanvraag](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)in te dienen.

## <a name="next-steps"></a>Volgende stappen

* [Indexering in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Gegevens in Azure Cosmos DB automatisch laten verlopen met TTL](../cosmos-db/time-to-live.md)
