---
title: Gegevens importeren en exporteren tussen Spark-groepen (voorbeeld) en SQL-groepen
description: In dit artikel vindt u informatie over het gebruik van de aangepaste connector voor het heen en weer verplaatsen van gegevens tussen SQL-pools en Spark-pools (preview).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424291"
---
# <a name="introduction"></a>Inleiding

De Spark SQL Analytics Connector is ontworpen om gegevens efficiënt over te dragen tussen Spark-pool (preview) en SQL-pools in Azure Synapse. De Spark SQL Analytics Connector werkt alleen op SQL-pools, deze werkt niet met SQL on-Demand.

## <a name="design"></a>Ontwerp

Het overbrengen van gegevens tussen Spark-pools en SQL-pools kan met JDBC. Echter, gezien twee gedistribueerde systemen zoals Spark en SQL pools, JDBC heeft de neiging om een knelpunt met seriële gegevensoverdracht.

De Spark-pools naar SQL Analytics Connector is een implementatie van gegevensbronnen voor Apache Spark. Het gebruikt de Azure Data Lake Storage Gen 2 en Polybase in SQL-groepen om gegevens efficiënt over te dragen tussen het Spark-cluster en het SQL Analytics-exemplaar.

![Connectorarchitectuur](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Verificatie in Azure Synapse Analytics

Verificatie tussen systemen wordt naadloos gemaakt in Azure Synapse Analytics. Er is een Token-service die verbinding maakt met Azure Active Directory om beveiligingstokens te verkrijgen voor gebruik bij het openen van het opslagaccount of de gegevensmagazijnserver. Daarom is het niet nodig om referenties te maken of op te geven in de connector-API zolang AAD-Auth is geconfigureerd op het opslagaccount en de gegevensmagazijnserver. Zo niet, dan kan SQL Auth worden opgegeven. Meer informatie vindt u in de sectie [Gebruik.](#usage)

## <a name="constraints"></a>Beperkingen

- Deze connector werkt alleen in Scala.

## <a name="prerequisites"></a>Vereisten

- Heb **db_exporter** rol in de database/SQL-groep waarnaar u gegevens wilt overbrengen naar/van.

Als u gebruikers wilt maken, maakt u verbinding met de database en volgt u de volgende voorbeelden:

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Een rol toewijzen:

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Gebruik

De importverklaringen hoeven niet te worden verstrekt, ze worden vooraf geïmporteerd voor de notebook-ervaring.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Gegevens overbrengen van of naar een SQL-groep in de logische server (DW-instantie) die is gekoppeld aan de werkruimte

> [!NOTE]
> **Importen niet nodig in notebook-ervaring**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API lezen

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

De bovenstaande API werkt zowel voor interne (beheerde) als externe tabellen in de SQL-groep.

#### <a name="write-api"></a>API schrijven

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

waar TableType Constants.INTERNAL of Constants.EXTERNAL kan zijn

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

De verificatie naar opslag en sql-server is uitgevoerd

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>Als u gegevens overzet van of naar een SQL-groep of -database in een logische server buiten de werkruimte

> [!NOTE]
> Importen niet nodig in notebook-ervaring

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API lezen

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API schrijven

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>SQL Auth gebruiken in plaats van AAD

#### <a name="read-api"></a>API lezen

Momenteel ondersteunt de connector op token gebaseerde auth niet naar een SQL-groep die zich buiten de werkruimte bevindt. Je moet SQL Auth gebruiken.

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API schrijven

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>De PySpark-connector gebruiken

> [!NOTE]
> Dit voorbeeld wordt gegeven met alleen de notebook ervaring in gedachten gehouden.

Stel dat u een gegevensframe "pyspark_df" hebt dat u in de DW wilt schrijven.

Een tijdelijke tabel maken met behulp van het gegevensframe in PySpark

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Een Scala-cel uitvoeren in de PySpark-notebook met magie

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
Lees in het leesscenario de gegevens met Scala en schrijf deze in een tijdelijke tabel en gebruik Spark SQL in PySpark om de tijdelijke tabel op te vragen in een gegevensframe.

## <a name="next-steps"></a>Volgende stappen

- [Een SQL-groep maken]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Een nieuwe Apache Spark-pool maken voor een Azure Synapse Analytics-werkruimte](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 