---
title: DDL-bewerkingen in Azure Cosmos DB Cassandra API van Spark
description: In dit artikel worden keyspace- en tabelDDL-bewerkingen beschreven tegen Azure Cosmos DB Cassandra API van Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c0df05eff5dc84ef24e1ed5afcaf705d99f447ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622574"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>DDL-bewerkingen in Azure Cosmos DB Cassandra API van Spark

In dit artikel worden keyspace- en tabelDDL-bewerkingen beschreven tegen Azure Cosmos DB Cassandra API van Spark.

## <a name="cassandra-api-related-configuration"></a>Cassandra API-gerelateerde configuratie 

```scala
import org.apache.spark.sql.cassandra._

//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="keyspace-ddl-operations"></a>Keyspace DDL-bewerkingen

### <a name="create-a-keyspace"></a>Een sleutelruimte maken

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Valideren in cqlsh

Voer de volgende opdracht uit in cqlsh en u ziet de keyspace die u eerder hebt gemaakt.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Een sleutelruimte laten vallen

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Valideren in cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Tabel DDL-bewerkingen

**Overwegingen:**  

- Doorvoer kan op tabelniveau worden toegewezen met behulp van de tabelinstructie maken.  
- Eén partitiesleutel kan 20 GB aan gegevens opslaan.  
- Eén record kan maximaal 2 MB aan gegevens opslaan.  
- Eén partitiesleutelbereik kan meerdere partitiesleutels opslaan.

### <a name="create-a-table"></a>Een tabel maken

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Valideren in cqlsh

Voer de volgende opdracht in cqlsh uit en u zou de lijst met de naam "boeken" moeten zien: 

```bash
USE books_ks;
DESCRIBE books;
```

Ingerichte doorvoer- en standaardTTL-waarden worden niet weergegeven in de uitvoer van de vorige opdracht, u deze waarden uit de portal halen.

### <a name="alter-table"></a>Tabel wijzigen

U de volgende waarden wijzigen met de opdracht Tabel wijzigen:

* ingerichte doorvoer 
* time-to-live waarde
<br>Kolomwijzigingen worden momenteel niet ondersteund.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Tabel neerzetten

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Valideren in cqlsh

Voer de volgende opdracht in cqlsh uit en u moet zien dat de tabel "boeken" niet meer beschikbaar is:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Volgende stappen

Ga na het maken van de sleutelruimte en de tabel verder naar de volgende artikelen voor CRUD-bewerkingen en meer:
 
* [Bewerkingen maken/invoegen](cassandra-spark-create-ops.md)  
* [Bewerkingen lezen](cassandra-spark-read-ops.md)  
* [Upsert-activiteiten](cassandra-spark-upsert-ops.md)  
* [Bewerkingen verwijderen](cassandra-spark-delete-ops.md)  
* [Aggregatiebewerkingen](cassandra-spark-aggregation-ops.md)  
* [Bewerkingen voor tabelkopiëren](cassandra-spark-table-copy-ops.md)  
