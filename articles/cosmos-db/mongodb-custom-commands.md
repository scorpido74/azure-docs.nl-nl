---
title: MongoDB-extensieopdrachten voor het beheren van gegevens in de API van Azure Cosmos DB voor MongoDB
description: In dit artikel wordt beschreven hoe u MongoDB-extensieopdrachten gebruiken om gegevens te beheren die zijn opgeslagen in de API van Azure Cosmos DB voor MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f57b274715eb1c8a4d517f5655c09c366574d412
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445207"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-extensieopdrachten gebruiken om gegevens te beheren die zijn opgeslagen in de API van Azure Cosmos DB voor MongoDB 

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U communiceren met de API van Azure Cosmos DB voor MongoDB met behulp van een van de open source [MongoDB-clientstuurprogramma's.](https://docs.mongodb.org/ecosystem/drivers) De API van Azure Cosmos DB voor MongoDB maakt het gebruik van bestaande clientstuurprogramma's mogelijk door vast te houden aan het [MongoDB-draadprotocol.](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)

Door de API van Azure Cosmos DB voor MongoDB te gebruiken, u genieten van de voordelen van Cosmos DB, zoals wereldwijde distributie, automatische sharding, hoge beschikbaarheid, latentiegaranties, automatische, versleuteling in rust, back-ups en nog veel meer, met behoud van uw investeringen in uw MongoDB app.

## <a name="mongodb-protocol-support"></a>Ondersteuning voor MongoDB-protocol

Standaard is de API van azure cosmos DB voor MongoDB compatibel met MongoDB-serverversie 3.2, zie [ondersteunde functies en syntaxis](mongodb-feature-support.md)voor meer informatie. De functies of queryoperators die zijn toegevoegd in MongoDB-versie 3.4 zijn momenteel beschikbaar als voorbeeld in de API van Azure Cosmos DB voor MongoDB. De volgende extensieopdrachten ondersteunen azure cosmos DB-specifieke functionaliteit bij het uitvoeren van CRUD-bewerkingen op de gegevens die zijn opgeslagen in de API van Azure Cosmos DB voor MongoDB:

* [Database maken](#create-database)
* [Database bijwerken](#update-database)
* [Database opdoen](#get-database)
* [Verzameling maken](#create-collection)
* [Verzameling bijwerken](#update-collection)
* [Ophalen ophalen](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>Database maken

Met de opdracht Databaseextensie maken wordt een nieuwe MongoDB-database gemaakt. De databasenaam wordt gebruikt vanuit de context van databases waartegen de opdracht wordt uitgevoerd. De opmaak van de opdracht CreateDatabase is als volgt:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

In de volgende tabel worden de parameters in de opdracht beschreven:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
| aangepaste actie   |  tekenreeks  |   Naam van de aangepaste opdracht, moet het "CreateDatabase".      |
| aanbiedingDoorvoer | int  | Ingerichte doorvoer die u in de database instelt. Deze parameter is optioneel. |

### <a name="output"></a>Uitvoer

Retourneert een standaard aangepast opdrachtantwoord. Zie de [standaarduitvoer](#default-output) van de aangepaste opdracht voor de parameters in de uitvoer.

### <a name="examples"></a>Voorbeelden

**Een database maken**

Als u een database met de naam 'test' wilt maken, gebruikt u de volgende opdracht:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Een database maken met doorvoer**

Als u een database met de naam 'test' en ingerichte doorvoer van 1000 RALLY's wilt maken, gebruikt u de volgende opdracht:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>Database bijwerken

Met de opdracht Update-databaseextensie worden de eigenschappen bijgewerkt die zijn gekoppeld aan de opgegeven database. Momenteel u alleen de eigenschap offerThroughput bijwerken.

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

In de volgende tabel worden de parameters in de opdracht beschreven:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
| aangepaste actie    |    tekenreeks     |   Naam van de aangepaste opdracht. Moet "UpdateDatabase" zijn.      |
|  aanbiedingDoorvoer   |  int       |     Nieuwe ingerichte doorvoer die u in de database wilt instellen.    |

### <a name="output"></a>Uitvoer

Retourneert een standaard aangepast opdrachtantwoord. Zie de [standaarduitvoer](#default-output) van de aangepaste opdracht voor de parameters in de uitvoer.

### <a name="examples"></a>Voorbeelden

**De ingerichte doorvoer bijwerken die is gekoppeld aan een database**

Als u de ingerichte doorvoer van een database met de naam "test" wilt bijwerken naar 1200 RALLY's, gebruikt u de volgende opdracht:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>Database opdoen

Met de opdracht Databaseextensie verwijderen wordt het databaseobject geretourneerd. De databasenaam wordt gebruikt vanuit de databasecontext waartegen de opdracht wordt uitgevoerd.

```
{
  customAction: "GetDatabase"
}
```

In de volgende tabel worden de parameters in de opdracht beschreven:


|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  aangepaste actie   |   tekenreeks      |   Naam van de aangepaste opdracht. Moet "GetDatabase" zijn|
        
### <a name="output"></a>Uitvoer

Als de opdracht slaagt, bevat het antwoord een document met de volgende velden:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Status van de reactie. 1 == succes. 0 == storing.      |
| `database`    |    `string`        |   Naam van de database.      |
|   `provisionedThroughput`  |    `int`      |    Ingerichte doorvoer die is ingesteld op de database. Dit is een optionele reactieparameter.     |

Als de opdracht mislukt, wordt een standaard aangepast opdrachtantwoord geretourneerd. Zie de [standaarduitvoer](#default-output) van de aangepaste opdracht voor de parameters in de uitvoer.

### <a name="examples"></a>Voorbeelden

**Download de database**

Als u het databaseobject voor een database met de naam 'test' wilt opvragen, gebruikt u de volgende opdracht:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>Verzameling maken

Met de opdracht Verzamelingenextensie maken wordt een nieuwe MongoDB-verzameling gemaakt. De databasenaam wordt gebruikt vanuit de context van databases waartegen de opdracht wordt uitgevoerd. De opmaak van de opdracht CreateCollection is als volgt:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

In de volgende tabel worden de parameters in de opdracht beschreven:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
| aangepaste actie    | tekenreeks | Naam van de aangepaste opdracht. Moet "CreateCollection" zijn     |
| verzameling      | tekenreeks | Naam van de collectie                                   |
| aanbiedingDoorvoer | int    | Ingerichte doorvoer die in de database moet worden ingesteld. Het is een optionele parameter |
| shardKey shardKey        | tekenreeks | Shard Key pad om een geshard collectie te maken. Het is een optionele parameter |

### <a name="output"></a>Uitvoer

Retourneert een standaard aangepast opdrachtantwoord. Zie de [standaarduitvoer](#default-output) van de aangepaste opdracht voor de parameters in de uitvoer.

### <a name="examples"></a>Voorbeelden

**Een ongesharde verzameling maken**

Als u een ongeshardverzameling wilt maken met de naam "testCollection" en een ingerichte doorvoer van 1000 RALLY's, gebruikt u de volgende opdracht: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Een geshard verzameling maken**

Als u een geshard verzameling wilt maken met de naam "testCollection" en een ingerichte doorvoer van 1000 RALLY's, gebruikt u de volgende opdracht:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>Verzameling bijwerken

Met de opdracht Updateverzamelingsextensie worden de eigenschappen bijgewerkt die zijn gekoppeld aan de opgegeven verzameling.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

In de volgende tabel worden de parameters in de opdracht beschreven:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  aangepaste actie   |   tekenreeks      |   Naam van de aangepaste opdracht. Moet "UpdateCollection" zijn.      |
|  verzameling   |   tekenreeks      |   Naam van de collectie.       |
| aanbiedingDoorvoer   |int|   Ingerichte doorvoer om in te stellen op de verzameling.|

## <a name="output"></a>Uitvoer

Retourneert een standaard aangepast opdrachtantwoord. Zie de [standaarduitvoer](#default-output) van de aangepaste opdracht voor de parameters in de uitvoer.

### <a name="examples"></a>Voorbeelden

**De ingerichte doorvoer bijwerken die is gekoppeld aan een verzameling**

Als u de ingerichte doorvoer van een verzameling met de naam "testCollection" wilt bijwerken naar 1200 RALLY's, gebruikt u de volgende opdracht:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>Ophalen ophalen

De aangepaste opdracht Verzameling ophalen retourneert het verzamelingsobject.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

In de volgende tabel worden de parameters in de opdracht beschreven:


|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
| aangepaste actie    |   tekenreeks      |   Naam van de aangepaste opdracht. Moet "GetCollection" zijn.      |
| verzameling    |    tekenreeks     |    Naam van de collectie.     |

### <a name="output"></a>Uitvoer

Als de opdracht slaagt, bevat het antwoord een document met de volgende velden


|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status van de reactie. 1 == succes. 0 == storing.      |
| `database`    |    `string`     |   Naam van de database.      |
| `collection`    |    `string`     |    Naam van de collectie.     |
|  `shardKeyDefinition`   |   `document`      |  Indexspecificatiedocument dat wordt gebruikt als een shardtoets. Dit is een optionele reactieparameter.       |
|  `provisionedThroughput`   |   `int`      |    Ingerichte doorvoer om in te stellen op de verzameling. Dit is een optionele reactieparameter.     |

Als de opdracht mislukt, wordt een standaard aangepast opdrachtantwoord geretourneerd. Zie de [standaarduitvoer](#default-output) van de aangepaste opdracht voor de parameters in de uitvoer.

### <a name="examples"></a>Voorbeelden

**Haal de collectie**

Gebruik de volgende opdracht om het verzamelobject voor een verzameling met de naam 'testCollectie' op te halen:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>Standaarduitvoer van een aangepaste opdracht

Als dit niet is opgegeven, bevat een aangepast antwoord een document met de volgende velden:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status van de reactie. 1 == succes. 0 == storing.      |
| `code`    |   `int`      |   Alleen terug wanneer de opdracht is mislukt (ok == 0). Bevat de MongoDB-foutcode. Dit is een optionele reactieparameter.      |
|  `errMsg`   |  `string`      |    Alleen terug wanneer de opdracht is mislukt (ok == 0). Bevat een gebruiksvriendelijke foutmelding. Dit is een optionele reactieparameter.      |

## <a name="next-steps"></a>Volgende stappen

Vervolgens u verder gaan met de volgende Azure Cosmos DB-concepten: 

* [Indexering in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Gegevens in Azure Cosmos DB automatisch laten verlopen met TTL](../cosmos-db/time-to-live.md)
