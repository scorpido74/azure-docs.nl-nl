---
title: Werken met Azure Cosmos DB Cassandra API van Spark
description: Dit artikel is de hoofdpagina voor Cosmos DB Cassandra API-integratie van Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: cb34ea44c069f067d13a6480531a94a1a515f380
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70241246"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Verbinding maken met de Azure Cosmos DB Cassandra-API vanuit Spark

Dit artikel is een van een reeks artikelen over Azure Cosmos DB Cassandra API-integratie van Spark. De artikelen hebben betrekking op connectiviteit, DDL-bewerkingen (Data Definition Language), dml-bewerkingen (Basic Data Manipulation Language) en geavanceerde Azure Cosmos DB Cassandra API-integratie van Spark. 

## <a name="prerequisites"></a>Vereisten
* [Een Azure Cosmos DB Cassandra API-account inrichten.](create-cassandra-dotnet.md#create-a-database-account)

* Uw keuze van de Spark-omgeving inrichten [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Anderen].

## <a name="dependencies-for-connectivity"></a>Afhankelijkheden voor connectiviteit
* **Vonkconnector voor Cassandra:** Spark-connector wordt gebruikt om verbinding te maken met Azure Cosmos DB Cassandra API.  Identificeer en gebruik de versie van de connector in [Maven central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) die compatibel is met de Spark- en Scala-versies van uw Spark-omgeving.

* **Azure Cosmos DB helperbibliotheek voor Cassandra API:** Naast de Spark-connector hebt u een andere bibliotheek nodig, [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) van Azure Cosmos DB. Deze bibliotheek bevat aangepaste verbindingsfabrieks- en beleidsklassen opnieuw proberen.

  Het beleid voor opnieuw proberen in Azure Cosmos DB is geconfigureerd voor het verwerken van HTTP-statuscode 429('Aanvraagsnelheid groot') uitzonderingen. De Azure Cosmos DB Cassandra API vertaalt deze uitzonderingen in overbelaste fouten op het Cassandra native protocol en u het opnieuw proberen met back-offs. Omdat Azure Cosmos DB een ingerichte doorvoermodel gebruikt, treden de uitzonderingen op de aanvraagsnelheid beperkt wanneer de invallen/uitgangssnelheden toenemen. Het beleid voor opnieuw proberen beschermt uw spark-taken tegen gegevenspieken die de doorvoer die voor uw container is toegewezen, tijdelijk overschrijden.

  > [!NOTE] 
  > Het beleid voor nieuwe apparaten kan uw spark-taken alleen beschermen tegen tijdelijke pieken. Als u niet voldoende R's hebt geconfigureerd die nodig zijn om uw werkbelasting uit te voeren, is het beleid voor opnieuw proberen niet van toepassing en wordt de uitzondering opnieuw uitgevoerd door de beleidsklasse opnieuw proberen.

* **Verbindingsgegevens van Azure Cosmos DB-account:** Uw Azure Cassandra API-accountnaam, accounteindpunt en sleutel.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Configuratieparameters voor spark-connectordoorvoer

In de volgende tabel worden Azure Cosmos DB Cassandra API-specifieke doorvoerconfiguratieparameters weergegeven die door de connector worden geleverd. Zie [de referentiepagina](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) van de configuratie van de Spark Cassandra Connector GitHub-opslagplaats voor een gedetailleerde lijst met alle configuratieparameters.

| **Naam van eigenschap** | **Standaardwaarde** | **Beschrijving** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rijen |  1 |Aantal rijen per enkele batch. Stel deze parameter in op 1. Deze parameter wordt gebruikt om een hogere doorvoer voor zware workloads te bereiken. |
| spark.cassandra.connection.connections_per_executor_max  | Geen | Maximaal aantal verbindingen per knooppunt per executeur. 10*n is gelijk aan 10 verbindingen per knooppunt in een n-node Cassandra-cluster. Dus, als u 5 verbindingen per knooppunt per executeur nodig hebt voor een 5-node Cassandra-cluster, moet u deze configuratie instellen op 25. Wijzig deze waarde op basis van de mate van parallellisme of het aantal uitvoerders waarvoor uw spark-taken zijn geconfigureerd.   |
| spark.cassandra.output.concurrent.writes  |  100 | Hiermee definieert u het aantal parallelle schrijfbewerkingen dat per uitvoerder kan optreden. Omdat u 'batch.size.rows' op 1 instelt, moet u deze waarde dienovereenkomstig opschalen. Wijzig deze waarde op basis van de mate van parallellisme of de doorvoer die u wilt bereiken voor uw werkbelasting. |
| spark.cassandra.concurrent.leest |  512 | Hiermee definieert u het aantal parallelle leest dat per executeur kan optreden. Wijzig deze waarde op basis van de mate van parallellisme of de doorvoer die u wilt bereiken voor uw werkbelasting  |
| spark.cassandra.output.throughput_mb_per_sec  | Geen | Hiermee definieert u de totale schrijfdoorvoer per executeur. Deze parameter kan worden gebruikt als bovengrens voor de doorvoer van uw vonktaak en baseren deze op de ingerichte doorvoer van uw Cosmos-container.   |
| spark.cassandra.input.reads_per_sec| Geen   | Hiermee definieert u de totale leesdoorvoer per executeur. Deze parameter kan worden gebruikt als bovengrens voor de doorvoer van uw vonktaak en baseren deze op de ingerichte doorvoer van uw Cosmos-container.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Hiermee definieert u het aantal batches per enkele vonktaak dat in het geheugen kan worden opgeslagen voordat u naar de Cassandra API wordt verzonden |
| spark.cassandra.connection.keep_alive_ms | 60000 | Hiermee bepaalt u de periode totdat ongebruikte verbindingen beschikbaar zijn. | 

Pas de doorvoer en mate van parallellisme van deze parameters aan op basis van de werkbelasting die u verwacht voor uw spark-taken en de doorvoer die u hebt ingericht voor uw Cosmos DB-account.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Verbinding maken met Azure Cosmos DB Cassandra API vanuit Spark

### <a name="cqlsh"></a>cqlsh cqlsh
In de volgende opdrachten wordt beschreven hoe u verbinding maken met Azure CosmosDB Cassandra API van cqlsh.  Dit is handig voor validatie als u door de monsters in Spark loopt.<br>
**Van Linux/Unix/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1. Azure-gegevensstenen
Het onderstaande artikel heeft betrekking op Azure Databricks-clusterinrichting, clusterconfiguratie voor verbinding maken met Azure Cosmos DB Cassandra API en verschillende voorbeeldnotitieblokken die betrekking hebben op DDL-bewerkingen, DML-bewerkingen en meer.<BR>
[Werken met Azure Cosmos DB Cassandra API vanuit Azure-databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. Azure HDInsight-Spark
Het onderstaande artikel heeft betrekking op de HDinsight-Spark-service, provisioning, clusterconfiguratie voor verbinding maken met Azure Cosmos DB Cassandra API en verschillende voorbeeldnotitieblokken die betrekking hebben op DDL-bewerkingen, DML-bewerkingen en meer.<BR>
[Werken met Azure Cosmos DB Cassandra API van Azure HDInsight-Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3. Spark-omgeving in het algemeen
Hoewel de bovenstaande secties specifiek waren voor Azure Spark-gebaseerde PaaS-services, behandelt deze sectie elke algemene Spark-omgeving.  Verbindingsafhankelijkheden, import en Spark-sessieconfiguratie worden hieronder beschreven. De sectie "Volgende stappen" omvat codevoorbeelden voor DDL-bewerkingen, DML-bewerkingen en meer.  

#### <a name="connector-dependencies"></a>Afhankelijkheden van connectoren:

1. Voeg de maven-coördinaten toe om de [Cassandra-connector voor Spark](cassandra-spark-generic.md#dependencies-for-connectivity) te krijgen
2. Voeg de maven-coördinaten toe voor de [Azure Cosmos DB-helperbibliotheek](cassandra-spark-generic.md#dependencies-for-connectivity) voor Cassandra API

#### <a name="imports"></a>Invoer:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Spark-sessieconfiguratie:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Volgende stappen

De volgende artikelen tonen Spark-integratie met Azure Cosmos DB Cassandra API. 
 
* [DDL-bewerkingen](cassandra-spark-ddl-ops.md)
* [Bewerkingen maken/invoegen](cassandra-spark-create-ops.md)
* [Bewerkingen lezen](cassandra-spark-read-ops.md)
* [Upsert-activiteiten](cassandra-spark-upsert-ops.md)
* [Bewerkingen verwijderen](cassandra-spark-delete-ops.md)
* [Aggregatiebewerkingen](cassandra-spark-aggregation-ops.md)
* [Bewerkingen voor tabelkopiëren](cassandra-spark-table-copy-ops.md)
