---
title: MongoDB-extensie opdrachten voor het beheren van gegevens in de API van Azure Cosmos DB voor MongoDB
description: In dit artikel wordt beschreven hoe u MongoDB-extensie opdrachten gebruikt voor het beheren van gegevens die zijn opgeslagen in de API van Azure Cosmos DB voor MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f99c4d096bcbe1fbdc42cac80a491d6017266cb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80583586"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Gebruik MongoDB-extensie opdrachten voor het beheren van gegevens die zijn opgeslagen in de API van Azure Cosmos DB voor MongoDB 

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt communiceren met de API van de Azure Cosmos DB voor MongoDB door gebruik te maken van de open-source [MongoDb-client Stuur Programma's](https://docs.mongodb.org/ecosystem/drivers). De API van Azure Cosmos DB voor MongoDB maakt het gebruik van bestaande client Stuur Programma's mogelijk door te voldoen aan het [MongoDb wire-protocol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Met de API van de Azure Cosmos DB voor MongoDB kunt u profiteren van de voor delen Cosmos DB zoals wereld wijde distributie, automatische sharding, hoge Beschik baarheid, latentie garanties, Automatische versleuteling op rest, back-ups en nog veel meer, terwijl uw investeringen in uw MongoDB-app behouden blijven.

## <a name="mongodb-protocol-support"></a>Ondersteuning voor MongoDB-Protocol

De Azure Cosmos DB-API voor MongoDB is standaard compatibel met MongoDB Server versie 3,2, Zie [ondersteunde functies en syntaxis](mongodb-feature-support.md)voor meer informatie. De functies of query operators die zijn toegevoegd in MongoDB versie 3,4 zijn momenteel beschikbaar als een preview in de API van Azure Cosmos DB voor MongoDB. De volgende extensie opdrachten ondersteunen specifieke Azure Cosmos DB functionaliteit voor het uitvoeren van ruwe bewerkingen op de gegevens die zijn opgeslagen in de Azure Cosmos DB-API voor MongoDB:

* [Data base maken](#create-database)
* [Data base bijwerken](#update-database)
* [Data base ophalen](#get-database)
* [Verzameling maken](#create-collection)
* [Verzameling bijwerken](#update-collection)
* [Verzameling ophalen](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>Data base maken

Met de opdracht Create Data Base extension maakt u een nieuwe MongoDB-data base. De database naam wordt gebruikt vanuit de data bases-context waartegen de opdracht wordt uitgevoerd. De notatie van de opdracht CreateDatabase is als volgt:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

In de volgende tabel worden de para meters in de opdracht beschreven:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
| Circulatie   |  tekenreeks  |   De naam van de aangepaste opdracht moet ' CreateDatabase ' zijn.      |
| offerThroughput | int  | Ingerichte door Voer die u hebt ingesteld voor de data base. Deze parameter is optioneel. |

### <a name="output"></a>Uitvoer

Retourneert een standaard aangepast opdracht antwoord. Zie de [standaard uitvoer](#default-output) van een aangepaste opdracht voor de para meters in de uitvoer.

### <a name="examples"></a>Voorbeelden

**Een database maken**

Als u een Data Base met de naam ' test ' wilt maken, gebruikt u de volgende opdracht:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Een Data Base maken met door Voer**

Gebruik de volgende opdracht om een Data Base met de naam ' test ' en ingerichte door Voer van 1000 RUs te maken:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>Data base bijwerken

Met de opdracht data base-extensie bijwerken worden de eigenschappen bijgewerkt die zijn gekoppeld aan de opgegeven Data Base. Op dit moment kunt u alleen de eigenschap ' offerThroughput ' bijwerken.

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

In de volgende tabel worden de para meters in de opdracht beschreven:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
| Circulatie    |    tekenreeks     |   De naam van de aangepaste opdracht. Moet ' UpdateDatabase ' zijn.      |
|  offerThroughput   |  int       |     Nieuwe ingerichte door Voer die u wilt instellen voor de data base.    |

### <a name="output"></a>Uitvoer

Retourneert een standaard aangepast opdracht antwoord. Zie de [standaard uitvoer](#default-output) van een aangepaste opdracht voor de para meters in de uitvoer.

### <a name="examples"></a>Voorbeelden

**De ingerichte door Voer bijwerken die is gekoppeld aan een Data Base**

Als u de ingerichte door Voer van een Data Base met de naam test naar 1200 RUs wilt bijwerken, gebruikt u de volgende opdracht:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>Data base ophalen

De opdracht data base-extensie ophalen retourneert het database object. De database naam wordt gebruikt vanuit de database context waartegen de opdracht wordt uitgevoerd.

```
{
  customAction: "GetDatabase"
}
```

In de volgende tabel worden de para meters in de opdracht beschreven:


|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  Circulatie   |   tekenreeks      |   De naam van de aangepaste opdracht. Moet ' GetDatabase ' zijn|
        
### <a name="output"></a>Uitvoer

Als de opdracht is geslaagd, bevat het antwoord een document met de volgende velden:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  `ok`   |   `int`     |   De status van het antwoord. 1 = = geslaagd. 0 = = mislukt.      |
| `database`    |    `string`        |   De naam van de data base.      |
|   `provisionedThroughput`  |    `int`      |    Ingerichte door Voer die is ingesteld voor de data base. Dit is een optionele antwoord parameter.     |

Als de opdracht mislukt, wordt een standaard antwoord voor een aangepaste opdracht geretourneerd. Zie de [standaard uitvoer](#default-output) van een aangepaste opdracht voor de para meters in de uitvoer.

### <a name="examples"></a>Voorbeelden

**De data base ophalen**

Als u het database object voor een Data Base met de naam ' test ' wilt ophalen, gebruikt u de volgende opdracht:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>Verzameling maken

Met de opdracht verzamelings extensie maken maakt u een nieuwe MongoDB-verzameling. De database naam wordt gebruikt vanuit de data bases-context waartegen de opdracht wordt uitgevoerd. De indeling van de CreateCollection opdracht is als volgt:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

In de volgende tabel worden de para meters in de opdracht beschreven:

| **Veld** | **Type** | **Vereist** | **Beschrijving** |
|---------|---------|---------|---------|
| Circulatie | tekenreeks | Vereist | De naam van de aangepaste opdracht. Moet ' CreateCollection ' zijn.|
| verzameling | tekenreeks | Vereist | De naam van de verzameling. Speciale tekens zijn niet toegestaan.|
| offerThroughput | int | Beschrijving | Ingerichte door Voer om in te stellen op de data base. Als deze para meter niet wordt gegeven, wordt standaard de minimum versie 400 RU/s. * Als u de door voer wilt opgeven na meer dan `shardKey` 10.000 ru/s, is de para meter vereist.|
| shardKey | tekenreeks | Beschrijving | Het pad naar de Shard-sleutel voor de Shard-verzameling. Deze para meter is vereist als u meer dan 10.000 RU/s in `offerThroughput`hebt ingesteld.  Als deze is opgegeven, is deze waarde vereist voor alle documenten die zijn ingevoegd. |

### <a name="output"></a>Uitvoer

Retourneert een standaard aangepast opdracht antwoord. Zie de [standaard uitvoer](#default-output) van een aangepaste opdracht voor de para meters in de uitvoer.

### <a name="examples"></a>Voorbeelden

**Een unsharded-verzameling maken**

Als u een unsharded-verzameling met de naam ' testCollection ' en de ingerichte door Voer van 1000 RUs wilt maken, gebruikt u de volgende opdracht: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Een Shard-verzameling maken**

Als u een Shard-verzameling met de naam ' testCollection ' en de ingerichte door Voer van 1000 RUs en een shardkey-eigenschap ' a. b ' wilt maken, gebruikt u de volgende opdracht:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>Verzameling bijwerken

Met de opdracht verzamelings extensie bijwerken worden de eigenschappen bijgewerkt die zijn gekoppeld aan de opgegeven verzameling.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

In de volgende tabel worden de para meters in de opdracht beschreven:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  Circulatie   |   tekenreeks      |   De naam van de aangepaste opdracht. Moet ' UpdateCollection ' zijn.      |
|  verzameling   |   tekenreeks      |   De naam van de verzameling.       |
| offerThroughput   |int|   Ingerichte door Voer om in te stellen voor de verzameling.|

## <a name="output"></a>Uitvoer

Retourneert een standaard aangepast opdracht antwoord. Zie de [standaard uitvoer](#default-output) van een aangepaste opdracht voor de para meters in de uitvoer.

### <a name="examples"></a>Voorbeelden

**De ingerichte door Voer bijwerken die is gekoppeld aan een verzameling**

Als u de ingerichte door Voer van een verzameling met de naam ' testCollection ' wilt bijwerken naar 1200 RUs, gebruikt u de volgende opdracht:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>Verzameling ophalen

De opdracht verzameling aangepaste ophalen retourneert het verzamelings object.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

In de volgende tabel worden de para meters in de opdracht beschreven:


|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
| Circulatie    |   tekenreeks      |   De naam van de aangepaste opdracht. Moet ' GetCollection ' zijn.      |
| verzameling    |    tekenreeks     |    De naam van de verzameling.     |

### <a name="output"></a>Uitvoer

Als de opdracht is geslaagd, bevat het antwoord een document met de volgende velden:


|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  `ok`   |    `int`     |   De status van het antwoord. 1 = = geslaagd. 0 = = mislukt.      |
| `database`    |    `string`     |   De naam van de data base.      |
| `collection`    |    `string`     |    De naam van de verzameling.     |
|  `shardKeyDefinition`   |   `document`      |  Index specificatie document dat wordt gebruikt als een Shard-sleutel. Dit is een optionele antwoord parameter.       |
|  `provisionedThroughput`   |   `int`      |    Ingerichte door Voer om in te stellen voor de verzameling. Dit is een optionele antwoord parameter.     |

Als de opdracht mislukt, wordt een standaard antwoord voor een aangepaste opdracht geretourneerd. Zie de [standaard uitvoer](#default-output) van een aangepaste opdracht voor de para meters in de uitvoer.

### <a name="examples"></a>Voorbeelden

**De verzameling ophalen**

Als u het verzamelings object wilt ophalen voor een verzameling met de naam ' testCollection ', gebruikt u de volgende opdracht:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>Standaard uitvoer van een aangepaste opdracht

Als deze niet is opgegeven, bevat een aangepast antwoord een document met de volgende velden:

|**Veld**|**Type** |**Beschrijving** |
|---------|---------|---------|
|  `ok`   |    `int`     |   De status van het antwoord. 1 = = geslaagd. 0 = = mislukt.      |
| `code`    |   `int`      |   Wordt alleen geretourneerd als de opdracht is mislukt (bijvoorbeeld OK = = 0). Bevat de fout code MongoDB. Dit is een optionele antwoord parameter.      |
|  `errMsg`   |  `string`      |    Wordt alleen geretourneerd als de opdracht is mislukt (bijvoorbeeld OK = = 0). Bevat een gebruikers vriendelijk fout bericht. Dit is een optionele antwoord parameter.      |

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met de volgende Azure Cosmos DB concepten: 

* [Indexering in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Gegevens in Azure Cosmos DB automatisch laten verlopen met TTL](../cosmos-db/time-to-live.md)
