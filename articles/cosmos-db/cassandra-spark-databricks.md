---
title: Toegang tot Azure Cosmos DB Cassandra API vanuit Azure Databricks
description: In dit artikel wordt ingegaan op het werken met Azure Cosmos DB Cassandra API van Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 37a06b19285c1196b5d87830ea176d4bd0d4eade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60894008"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Toegang tot Azure Cosmos DB Cassandra API-gegevens van Azure Databricks

In dit artikel wordt beschreven hoe u werken met Azure Cosmos DB Cassandra API van Spark op [Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)

## <a name="prerequisites"></a>Vereisten

* [Een Azure Cosmos DB Cassandra API-account inrichten](create-cassandra-dotnet.md#create-a-database-account)

* [Bekijk de basisprincipes van verbinding maken met Azure Cosmos DB Cassandra API](cassandra-spark-generic.md)

* [Een Azure Databricks-cluster inrichten](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Bekijk de codevoorbeelden voor het werken met Cassandra API](cassandra-spark-generic.md#next-steps)

* [Gebruik cqlsh voor validatie als u dat liever hebt](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Cassandra API-instantieconfiguratie voor Cassandra-connector:**

  De connector voor Cassandra API vereist dat de Cassandra-verbindingsgegevens worden geïnitialiseerd als onderdeel van de vonkcontext. Wanneer u een Databricks-notitieblok start, is de vonkcontext al geïnitialiseerd en is het niet raadzaam om deze te stoppen en opnieuw te initialiseren. Een oplossing is het toevoegen van de Cassandra API-instantieconfiguratie op clusterniveau in de clustervonkconfiguratie. Dit is een eenmalige activiteit per cluster. Voeg de volgende code toe aan de Spark-configuratie als een spatiegescheiden sleutelwaardepaar:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>De vereiste afhankelijkheden toevoegen

* **Cassandra Spark-connector:** - Om Azure Cosmos DB Cassandra API te integreren met Spark, moet de Cassandra-connector worden gekoppeld aan het Azure Databricks-cluster. Ga als u het cluster wilt koppelen:

  * Bekijk de Databricks runtime-versie, de Spark-versie. Zoek vervolgens de [maven-coördinaten](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) die compatibel zijn met de Cassandra Spark-connector en bevestig deze aan het cluster. Zie het artikel ['Een Maven-pakket of Spark-pakket uploaden'](https://docs.databricks.com/user-guide/libraries.html) om de connectorbibliotheek aan het cluster te koppelen. Maven coördinaat voor 'Databricks Runtime versie 4.3', 'Spark 2.3.1' en 'Scala 2.11' is bijvoorbeeld`spark-cassandra-connector_2.11-2.3.1`

* **Azure Cosmos DB Cassandra API-specifieke bibliotheek:** - Er is een aangepaste verbindingsfabriek vereist om het beleid voor opnieuw proberen te configureren van de Cassandra Spark-connector naar Azure Cosmos DB Cassandra API. Voeg `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0`de [maven-coördinaten](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) toe om de bibliotheek aan het cluster te koppelen.

## <a name="sample-notebooks"></a>Voorbeelden van notebooks

Een lijst met [voorbeeldnotitieblokken](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) van Azure Databricks is beschikbaar in GitHub repo die u downloaden. Deze voorbeelden omvatten hoe u verbinding maken met Azure Cosmos DB Cassandra API van Spark en verschillende CRUD-bewerkingen op de gegevens uitvoeren. U ook [alle notitieblokken importeren](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) in uw Databricks-clusterwerkruimte en deze uitvoeren. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Toegang tot Azure Cosmos DB Cassandra API van Spark Scala-programma's

Spark-programma's die moeten worden uitgevoerd als geautomatiseerde processen op Azure Databricks, worden naar het cluster verzonden met behulp van [spark-submit)](https://spark.apache.org/docs/latest/submitting-applications.html)en moeten worden uitgevoerd via de Azure Databricks-taken.

Hieronder vindt u koppelingen om aan de slag te gaan met het bouwen van Spark Scala-programma's om te communiceren met Azure Cosmos DB Cassandra API.
* [Verbinding maken met Azure Cosmos DB Cassandra API vanuit een Spark Scala-programma](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Een Spark Scala-programma uitvoeren als een geautomatiseerde taak op Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Volledige lijst met codevoorbeelden voor het werken met Cassandra API](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met [het maken van een Cassandra API-account, database en een tabel](create-cassandra-api-account-java.md) met behulp van een Java-toepassing.
