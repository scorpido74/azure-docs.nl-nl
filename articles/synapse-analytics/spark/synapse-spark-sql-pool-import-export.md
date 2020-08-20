---
title: Gegevens importeren en exporteren tussen Spark-pools (preview) en SQL-pools
description: Dit artikel bevat informatie over het gebruik van de aangepaste connector voor het verplaatsen van gegevens tussen SQL-pools en Spark-pools (preview).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: 58c52649750ae03f19188a025fa4baa16a55ae05
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590078"
---
# <a name="introduction"></a>Inleiding

De Azure Synapse Apache Spark naar Synapse SQL-connector is ontworpen om efficiënt gegevens over te dragen tussen Spark-pools (preview) en SQL-pools in Azure Synapse. De Azure Synapse Apache Spark naar Synapse SQL-connector werkt alleen in SQL-pools, maar werkt niet met SQL on-demand.

## <a name="design"></a>Ontwerp

Het overbrengen van gegevens tussen Spark-pools en SQL-pools kan worden uitgevoerd met JDBC. Maar op twee gedistribueerde systemen, zoals Spark en SQL-pools, is JDBC meestal een knelpunt wat betreft seriële gegevensoverdracht.

De Azure Synapse Apache Spark pool naar Synapse SQL-connector is een gegevensbronimplementatie voor Apache Spark. De Azure Data Lake Storage Gen2 en Polybase in SQL-pools worden gebruikt om efficiënt gegevens over te dragen tussen het Spark-cluster en het Synapse SQL-exemplaar.

![Connectorarchitectuur](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Verificatie in Azure Synapse Analytics

Verificatie tussen systemen wordt in Azure Synapse Analytics naadloos gemaakt. Er is een tokenservice die verbinding maakt met Azure Active Directory om beveiligingstokens te verkrijgen die kunnen worden gebruikt bij het openen van het opslagaccount of de datawarehouse-server.

Daarom is het niet nodig om referenties te maken of op te geven in de connector-API zolang AAD-verificatie is geconfigureerd op het opslagaccount en de datawarehouse-server. Als dat niet het geval is, kan de SQL-verificatie worden opgegeven. Meer informatie vindt u in de sectie [Gebruik](#usage).

## <a name="constraints"></a>Beperkingen

- Deze connector werkt alleen in Scala.

## <a name="prerequisites"></a>Vereisten

- Moet lid zijn van de rol **db_exporter** in de database/SQL-pool waarnaar of waaruit u gegevens wilt overdragen.
- Moet lid zijn van de rol Inzender voor Storage Blob-gegevens voor het standaardopslagaccount.

Als u gebruikers wilt maken, maakt u verbinding met de SQL-pooldatabase en volgt u deze voorbeelden:

```sql
--SQL User
CREATE USER Mary FROM LOGIN Mary;

--Azure Active Directory User
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Om een rol toe te wijzen:

```sql
--SQL User
EXEC sp_addrolemember 'db_exporter', 'Mary';

--Azure Active Directory User
EXEC sp_addrolemember 'db_exporter',[mike@contoso.com]
```

## <a name="usage"></a>Gebruik

De instructies voor importeren zijn niet vereist, ze worden vooraf geïmporteerd voor de notebookervaring.

### <a name="transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace"></a>Gegevens overdragen van of naar een SQL-pool die is gekoppeld aan de werkruimte

> [!NOTE]
> **Importeren niet vereist in notebookervaring**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API lezen

```scala
val df = spark.read.sqlanalytics("<DBName>.<Schema>.<TableName>")
```

De bovenstaande API werkt zowel voor interne (beheerde) als voor externe tabellen in de SQL-pool.

#### <a name="write-api"></a>API schrijven

```scala
df.write.sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

De schrijf-API maakt de tabel in de SQL-pool en roept vervolgens Polybase aan om de gegevens te laden.  De tabel mag niet voorkomen in de SQL-pool, anders wordt er een foutbericht geretourneerd met de mededeling dat er al een object met die naam bestaat.

TableType-waarden

- Constants.INTERNAL: beheerde tabel in SQL-pool
- Constants.EXTERNAL: externe tabel in SQL-pool

Beheerde tabel in SQL-pool

```scala
df.write.sqlanalytics("<DBName>.<Schema>.<TableName>", Constants.INTERNAL)
```

Externe tabel in SQL-pool

Als u naar een externe tabel van een SQL-pool wilt schrijven, moeten er een EXTERNE GEGEVENSBRON en een EXTERNE BESTANDSINDELING bestaan in de SQL-pool.  Lees voor meer informatie [een externe gegevensbron maken](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) en [externe bestandsindelingen](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) in SQL-pool.  Hieronder staan voorbeelden voor het maken van een externe gegevensbron en externe bestandsindelingen in SQL-pool.

```sql
--For an external table, you need to pre-create the data source and file format in SQL pool using SQL queries:
CREATE EXTERNAL DATA SOURCE <DataSourceName>
WITH
  ( LOCATION = 'abfss://...' ,
    TYPE = HADOOP
  ) ;

CREATE EXTERNAL FILE FORMAT <FileFormatName>
WITH (  
    FORMAT_TYPE = PARQUET,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);
```

Een EXTERN REFERENTIEOBJECT is niet nodig bij het gebruik van Azure Active Directory-pass-through-verificatie voor het opslagaccount.  Zorg ervoor dat u lid bent van de rol 'Inzender voor Storage Blob-gegevens' voor het opslagaccount.

```scala

df.write.
    option(Constants.DATA_SOURCE, <DataSourceName>).
    option(Constants.FILE_FORMAT, <FileFormatName>).
    sqlanalytics("<DBName>.<Schema>.<TableName>", Constants.EXTERNAL)

```

### <a name="if-you-transfer-data-to-or-from-a-sql-pool-or-database-outside-the-workspace"></a>Als u gegevens overbrengt naar of van een SQL-pool of database buiten de werkruimte

> [!NOTE]
> Importeren niet vereist in notebookervaring

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API lezen

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API schrijven

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-sql-auth-instead-of-aad"></a>SQL-verificatie gebruiken in plaats van AAD

#### <a name="read-api"></a>API lezen

De connector biedt momenteel geen ondersteuning voor verificatie op basis van tokens naar een SQL-pool die zich buiten de werkruimte bevindt. U moet SQL-verificatie gebruiken.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API schrijven

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-the-pyspark-connector"></a>De PySpark-connector gebruiken

> [!NOTE]
> In dit voorbeeld is alleen gericht op de notebookervaring.

Stel dat u een dataframe "pyspark_df" hebt die u naar de DW wilt schrijven.

Maak een tijdelijke tabel met behulp van de dataframe in PySpark:

```py
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Voer een Scala-cel in het PySpark-notebook uit met behulp van magics:

```scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

scala_df.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

Lees in het leesscenario de gegevens met behulp van Scala en schrijf deze in een tijdelijke tabel, en gebruik Spark SQL in PySpark om de tijdelijke tabel in een dataframe aan te vragen.

## <a name="allow-other-users-to-use-the-azure-synapse-apache-spark-to-synapse-sql-connector-in-your-workspace"></a>Andere gebruikers toestaan om de connector van Azure Synapse Apache Spark naar Synapse SQL in uw werkruimte te gebruiken

U moet de eigenaar van de opslagblobgegevens zijn op het ADLS Gen2-opslagaccount dat is verbonden met de werkruimte om ontbrekende machtigingen voor anderen te wijzigen. Zorg ervoor dat de gebruiker toegang heeft tot de werkruimte en de machtigingen voor het uitvoeren van notebooks.

### <a name="option-1"></a>Optie 1

- Maak de gebruiker een Inzender/Eigenaar van opslagblobgegevens

### <a name="option-2"></a>Optie 2

- Geef de volgende ACL's op in de mappenstructuur:

| Map | / | synapse | workspaces  | \<workspacename> | sparkpools | \<sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Toegangsmachtigingen | --X | --X | --X | --X | --X | --X | -WX |
| Standaardmachtigingen | ---| ---| ---| ---| ---| ---| ---|

- U moet ACL kunnen toepassen op alle folders van "synapse" en omlaag vanuit het Azure-portal. Om ACL toe te passen op de hoofdmap "/" folder, volgt u de onderstaande instructies.

- Verbinding maken met het opslagaccount dat is verbonden met de werkruimte vanuit Storage Explorer met AAD
- Selecteer uw account en geef de ADLS Gen2 URL en het standaardbestandssysteem voor de werkruimte op
- Zodra u het vermelde opslagaccount kunt zien, klikt u met de rechtermuisknop op de vermelding werkruimte en selecteert u "Toegang beheren"
- Voeg de Gebruiker toe aan de /-map met de toegangsmachtiging "Uitvoeren". Selecteer "OK"

> [!IMPORTANT]
> Zorg ervoor dat u niet "Standaard" selecteert als u dit niet wilt.

## <a name="next-steps"></a>Volgende stappen

- [Een SQL-pool maken met behulp van de Azure-portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Een nieuwe Apache Spark-pool maken met behulp van de Azure-portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
