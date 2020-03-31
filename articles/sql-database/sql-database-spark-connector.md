---
title: Spark-connector met Azure SQL Database en SQL Server
description: Meer informatie over het gebruik van de Spark Connector voor Azure SQL Database en SQL Server
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
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Versnel realtime big data-analyses met Spark-connector voor Azure SQL Database en SQL Server

Met de Spark-connector voor Azure SQL Database en SQL Server kunnen SQL-databases, waaronder Azure SQL Database en SQL Server, fungeren als invoergegevensbron of uitvoergegevenssink voor Spark-taken. Hiermee u realtime transactionele gegevens gebruiken in big data-analyses en blijvende resultaten voor adhoc-query's of rapportages. In vergelijking met de ingebouwde JDBC-connector biedt deze connector de mogelijkheid om gegevens in sql-databases in bulk in te voegen. Het kan beter presteren dan rij voor rij invoeging met 10x tot 20x snellere prestaties. De Spark-connector voor Azure SQL Database en SQL Server ondersteunt ook AAD-verificatie. Hiermee u veilig verbinding maken met uw Azure SQL-database vanuit Azure Databricks met uw AAD-account. Het biedt vergelijkbare interfaces met de ingebouwde JDBC-connector. Het is eenvoudig om uw bestaande Spark-taken te migreren om deze nieuwe connector te gebruiken.

## <a name="download"></a>Download
Download om te beginnen de Spark naar SQL DB-connector vanuit de [azure-sqldb-spark repository](https://github.com/Azure/azure-sqldb-spark) op GitHub.

## <a name="official-supported-versions"></a>Officiële ondersteunde versies

| Onderdeel                            | Versie                  |
| :----------------------------------- | :----------------------- |
| Apache Spark                         | 2.0.2 of hoger           |
| Scala                                | 2.10 of hoger            |
| Microsoft JDBC-stuurprogramma voor SQL Server | 6.2 of hoger             |
| Microsoft SQL Server                 | SQL Server 2008 of hoger |
| Azure SQL Database                   | Ondersteund                |

De Spark-connector voor Azure SQL Database en SQL Server maakt gebruik van de Microsoft JDBC Driver voor SQL Server om gegevens te verplaatsen tussen Spark worker nodes en SQL databases:
 
De dataflow is als volgt:
1. Het Spark-masterknooppunt maakt verbinding met SQL Server of Azure SQL Database en laadt gegevens uit een specifieke tabel of met behulp van een specifieke SQL-query
2. Het hoofdknooppunt spark distribueert gegevens naar werknemersknooppunten voor transformatie. 
3. Het worker-knooppunt maakt verbinding met SQL Server of Azure SQL Database en schrijft gegevens naar de database. De gebruiker kan ervoor kiezen om rij-voor-rij invoeging of bulk insert te gebruiken.

Het volgende diagram illustreert de gegevensstroom.

   ![architectuur](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>De Spark-naar SQL DB-connector bouwen
Momenteel maakt het connectorproject gebruik van maven. Als u de connector zonder afhankelijkheden wilt maken, u het als:

- mvn schoon pakket
- Download de nieuwste versies van de JAR uit de releasemap
- De SQL DB Spark JAR opnemen

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Spark verbinden met SQL DB met de connector
U verbinding maken met Azure SQL Database of SQL Server vanuit Spark-taken, gegevens lezen of schrijven. U ook een DML- of DDL-query uitvoeren in een Azure SQL-database of SQL Server-database.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Gegevens lezen uit Azure SQL Database of SQL Server

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
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Gegevens uit Azure SQL Database of SQL Server met opgegeven SQL-query lezen
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

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>DML- of DDL-query uitvoeren in Azure SQL Database of SQL Server
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
U verbinding maken met Azure SQL Database met Azure Active Directory (AAD)-verificatie. Aad-verificatie gebruiken om identiteiten van databasegebruikers centraal te beheren en als alternatief voor SQL Server-verificatie.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Verbinding maken met de verificatiemodus van ActiveDirectoryPassword
#### <a name="setup-requirement"></a>Installatievereiste
Als u de ActiveDirectoryPassword-verificatiemodus gebruikt, moet u [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) en de afhankelijkheden ervan downloaden en deze opnemen in het Java-buildpad.

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

### <a name="connecting-using-access-token"></a>Verbinding maken met Access Token
#### <a name="setup-requirement"></a>Installatievereiste
Als u de verificatiemodus voor toegangstokens gebruikt, moet u [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) en de afhankelijkheden ervan downloaden en deze opnemen in het Java-buildpad.

Zie [Azure Active Directory Authentication gebruiken voor verificatie met SQL Database](sql-database-aad-authentication.md) voor meer informatie over het openen van een token voor toegang tot uw Azure SQL-database.

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

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Gegevens schrijven naar Azure SQL-database of SQL Server met bulkinsert
De traditionele jdbc-connector schrijft gegevens in Azure SQL-database of SQL Server met behulp van rij-voor-rij-invoeging. U Spark naar SQL DB-connector gebruiken om gegevens naar SQL-database te schrijven met behulp van bulkinsert. Het verbetert de schrijfprestaties aanzienlijk bij het laden van grote gegevenssets of het laden van gegevens in tabellen waar een kolomarchiefindex wordt gebruikt.

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
Als u dit nog niet hebt gedaan, downloadt u de Spark-connector voor Azure SQL Database en SQL Server uit [de GitHub-opslagplaats azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) en onderzoekt u de extra bronnen in de repo:

- [Voorbeeld van Azure Databricks-notitieblokken](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Voorbeeldscripts (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Mogelijk wilt u ook de [handleiding voor Apache Spark SQL, DataFrames en Datasets](https://spark.apache.org/docs/latest/sql-programming-guide.html) en de Azure [Databricks-documentatie](https://docs.microsoft.com/azure/azure-databricks/)controleren.

