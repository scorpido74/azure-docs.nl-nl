---
title: Gegevens importeren en exporteren tussen Spark-Pools (preview) en SQL-groepen
description: Dit artikel bevat informatie over het gebruik van de aangepaste connector voor het verplaatsen van gegevens tussen SQL-groepen en Spark-Pools (preview).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424291"
---
# <a name="introduction"></a>Inleiding

De Spark SQL Analytics-connector is ontworpen om op efficiënte wijze gegevens over te dragen tussen Spark-pool (preview) en SQL-groepen in azure Synapse. De Spark SQL Analytics-connector werkt alleen in SQL-groepen, maar werkt niet met SQL op aanvraag.

## <a name="design"></a>Ontwerpen

Het overbrengen van gegevens tussen Spark-Pools en SQL-groepen kan worden uitgevoerd met JDBC. Maar op twee gedistribueerde systemen, zoals Spark en SQL-Pools, is JDBC meestal een knel punt met een seriële gegevens overdracht.

De Spark-Pools naar de SQL Analytics-connector is een gegevens bron implementatie voor Apache Spark. Hierbij worden de Azure Data Lake Storage gen 2 en poly base in SQL-groepen gebruikt om efficiënt gegevens over te dragen tussen het Spark-cluster en het SQL Analytics-exemplaar.

![Architectuur van connector](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Verificatie in azure Synapse Analytics

Verificatie tussen systemen wordt naadloos in azure Synapse Analytics gemaakt. Er is een token service die verbinding maakt met Azure Active Directory om beveiligings tokens te verkrijgen die kunnen worden gebruikt bij het openen van het opslag account of de Data Warehouse-server. Daarom is het niet nodig om referenties te maken of op te geven in de connector-API zolang AAD-auth is geconfigureerd op het opslag account en de Data Warehouse-server. Als dat niet het geval is, kan de SQL-verificatie worden opgegeven. Meer informatie vindt u in de sectie [gebruik](#usage) .

## <a name="constraints"></a>Beperkingen

- Deze connector werkt alleen in scala.

## <a name="prerequisites"></a>Vereisten

- Hebben **db_exporter** rol in de data base/SQL-groep waarnaar u gegevens wilt overdragen.

Als u gebruikers wilt maken, maakt u verbinding met de data base en volgt u deze voor beelden:

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Een rol toewijzen:

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Gebruik

De import instructies hoeven niet te worden vermeld, ze worden vooraf geïmporteerd voor de notitieblok ervaring.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Gegevens overdragen van of naar een SQL-groep in de logische server (DW-instantie) die is gekoppeld aan de werk ruimte

> [!NOTE]
> **Invoer niet vereist in laptop ervaring**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API lezen

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

De bovenstaande API werkt zowel voor intern (beheerd) als voor externe tabellen in de SQL-groep.

#### <a name="write-api"></a>API schrijven

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

waarbij TableType constanten kan zijn. intern of constanten. EXTERNAL

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

De verificatie voor opslag en het SQL Server is voltooid

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>Als u gegevens overbrengt naar of van een SQL-groep of Data Base op een logische server buiten de werk ruimte

> [!NOTE]
> Invoer niet vereist in laptop ervaring

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

### <a name="using-sql-auth-instead-of-aad"></a>SQL-verificatie gebruiken in plaats van AAD

#### <a name="read-api"></a>API lezen

De connector biedt momenteel geen ondersteuning voor verificatie op basis van tokens naar een SQL-groep die zich buiten de werk ruimte bevindt. U moet SQL-verificatie gebruiken.

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
> In dit voor beeld wordt alleen de ervaring van het notitie blok gegeven.

Stel dat u een data frame ' pyspark_df ' hebt die u naar de DW wilt schrijven.

Een tijdelijke tabel maken met behulp van de data frame in PySpark

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Een scala-cel in de PySpark-notebook uitvoeren met behulp van magics

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
Lees in het Lees scenario de gegevens met behulp van scala en schrijf deze in een tijdelijke tabel, en gebruik Spark SQL in PySpark om de tijdelijke tabel in een data frame op te vragen.

## <a name="next-steps"></a>Volgende stappen

- [Een SQL-groep maken]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Een nieuwe Apache Spark groep maken voor een Azure Synapse Analytics-werk ruimte](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 