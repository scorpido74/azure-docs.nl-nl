---
title: Spark-connector met Azure SQL Database en SQL Server
description: Meer informatie over het gebruik van de Spark-connector voor Azure SQL Database en SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: b22ec475c0281a54d65921bc450b35723aa23219
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471649"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Versnel real-time big data Analytics met Spark-connector voor Azure SQL Database en SQL Server

Met de Spark-connector voor Azure SQL Database en SQL Server kunnen SQL-data bases, waaronder Azure SQL Database en SQL Server, fungeren als invoer gegevens bron of uitvoer gegevens Sink voor Spark-taken. Hiermee kunt u realtime transactionele gegevens gebruiken in big data Analytics en de resultaten van ad hoc query's of rapportages behouden. Vergeleken met de ingebouwde JDBC-connector biedt deze connector de mogelijkheid om gegevens bulksgewijs in te voegen in SQL-data bases. De leverde kan rij by Row invoeging met 10x voor 20x snellere prestaties. De Spark-connector voor Azure SQL Database en SQL Server biedt ook ondersteuning voor AAD-verificatie. Hiermee kunt u veilig verbinding maken met uw Azure-SQL database van Azure Databricks met behulp van uw AAD-account. Het biedt vergelijk bare interfaces met de ingebouwde JDBC-connector. Het is eenvoudig om uw bestaande Spark-taken te migreren om deze nieuwe connector te gebruiken.

## <a name="download"></a>Download
Om aan de slag te gaan, downloadt u de Spark naar SQL DB-connector vanuit de [Azure-sqldb-Spark-opslag plaats](https://github.com/Azure/azure-sqldb-spark) op github.

## <a name="official-supported-versions"></a>Officiële ondersteunde versies

| Onderdeel                            | Versie                  |
| :----------------------------------- | :----------------------- |
| Apache Spark                         | 2.0.2 of hoger           |
| Scala                                | 2,10 of hoger            |
| Micro soft JDBC-stuur programma voor SQL Server | 6,2 of hoger             |
| Microsoft SQL Server                 | SQL Server 2008 of hoger |
| Azure SQL Database                   | Ondersteund                |

De Spark-connector voor Azure SQL Database en SQL Server maakt gebruik van het micro soft JDBC-stuur programma voor SQL Server voor het verplaatsen van gegevens tussen Spark-werk knooppunten en SQL-data bases:
 
De gegevens stroom is als volgt:
1. Het Spark-hoofd knooppunt maakt verbinding met SQL Server of Azure SQL Database en laadt gegevens uit een specifieke tabel of gebruikt een specifieke SQL-query
2. Het Spark-hoofd knooppunt distribueert gegevens naar worker-knoop punten voor trans formatie. 
3. Het worker-knoop punt maakt verbinding met SQL Server of Azure SQL Database en schrijft gegevens naar de data base. De gebruiker kan ervoor kiezen om rijen in te voegen of bulksgewijs in te voegen.

In het volgende diagram ziet u de gegevens stroom.

   ![architectuur](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>De Spark-verbinding maken met de SQL DB-connector
Op dit moment gebruikt het connector project maven. Als u de connector zonder afhankelijkheden wilt maken, kunt u het volgende uitvoeren:

- MVN opschone pakket
- De nieuwste versie van het JAR-bestand downloaden vanuit de map uitgeven
- De SQL-data base Spark JAR toevoegen

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Spark verbinden met SQL DB met behulp van de connector
U kunt verbinding maken met Azure SQL Database of SQL Server vanuit Spark-taken, gegevens lezen of schrijven. U kunt ook een DML-of DDL-query uitvoeren in een Azure SQL database-of SQL Server-Data Base.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Gegevens lezen van Azure SQL Database of SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Gegevens lezen van Azure SQL Database of SQL Server met de opgegeven SQL-query
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Gegevens schrijven naar Azure SQL Database of SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>DML-of DDL-query uitvoeren in Azure SQL Database of SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Spark verbinden met Azure SQL Database met AAD-verificatie
U kunt verbinding maken met Azure SQL Database met behulp van Azure Active Directory-verificatie (AAD). Gebruik AAD-verificatie voor het centraal beheren van identiteiten van database gebruikers en als alternatief voor SQL Server-verificatie.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Verbinding maken met behulp van de ActiveDirectoryPassword-verificatie modus
#### <a name="setup-requirement"></a>Installatie vereiste
Als u de ActiveDirectoryPassword-verificatie modus gebruikt, moet u [Azure-ActiveDirectory-Library-voor-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) en de bijbehorende afhankelijkheden downloaden en in het pad Java build toevoegen.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Verbinding maken met behulp van toegangs token
#### <a name="setup-requirement"></a>Installatie vereiste
Als u de verificatie modus op basis van toegangs tokens gebruikt, moet u [Azure-ActiveDirectory-library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) en de bijbehorende afhankelijkheden downloaden en in het pad Java build toevoegen.

Zie [Azure Active Directory verificatie gebruiken voor verificatie met SQL database](sql-database-aad-authentication.md) voor meer informatie over het verkrijgen van een toegangs token voor uw Azure SQL database.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Gegevens schrijven naar Azure SQL database of SQL Server met bulksgewijs invoegen
De traditionele JDBC-connector schrijft gegevens naar Azure SQL database of SQL Server met behulp van een rij-voor-rij-invoeging. U kunt Spark naar SQL DB-connector gebruiken om gegevens naar SQL database te schrijven met bulksgewijs invoegen. De schrijf prestaties worden aanzienlijk verbeterd bij het laden van grote gegevens sets of het laden van gegevens in tabellen waarin een column store-index wordt gebruikt.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Volgende stappen
Als u dat nog niet hebt gedaan, downloadt u de Spark-connector voor Azure SQL Database en SQL Server van de [Azure-sqldb-Spark github-opslag plaats](https://github.com/Azure/azure-sqldb-spark) en bekijkt u de aanvullende bronnen in de opslag plaats:

- [Voor beeld Azure Databricks-notebooks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Voorbeeld scripts (scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Mogelijk wilt u ook de [Apache Spark SQL-, DataFrames-en gegevens sets gids](https://spark.apache.org/docs/latest/sql-programming-guide.html) en de [Azure Databricks documentatie](https://docs.microsoft.com/azure/azure-databricks/)bekijken.

