---
title: De gedeelde metadatatabellen van Azure Synapse Analytics
description: Azure Synapse Analytics biedt een gedeeld metadatamodel waarbij het maken van een tabel in Apache Spark het toegankelijk maakt vanuit de SQL on-demand (preview) en SQL-poolengines zonder de gegevens te dupliceren.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 7c1951c772dcd2f49f4f7c09021f69193af0a87e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424578"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Gedeelde metagegevenstabellen voor Azure Synapse Analytics

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Azure Synapse Analytics stelt de verschillende computerengines voor werkruimtes in staat om databases en door parket gesteunde tabellen te delen tussen de Apache Spark-pools (preview), SQL on-demand (preview)-engine en SQL-pools.

Zodra een database is gemaakt door een Spark-taak, u er tabellen in maken met Spark die Parket als opslagindeling gebruiken. Deze tabellen worden onmiddellijk beschikbaar voor query's door een van de Spark-pools van Azure Synapse-werkruimte. Ze kunnen ook worden gebruikt vanaf een van de Spark-taken onder voorbehoud van machtigingen.

De spark-gemaakte, beheerde en externe tabellen worden ook beschikbaar gesteld als externe tabellen met dezelfde naam `$`in de bijbehorende gesynchroniseerde database in SQL on-demand en in de bijbehorende vooraf vastgestelde schema's in de SQL-groepen waarop hun synchronisatie metagegevens is ingeschakeld. [Het blootstellen van een Spark-tabel in SQL](#exposing-a-spark-table-in-sql) biedt meer details over de tabelsynchronisatie.

Aangezien de tabellen worden gesynchroniseerd met SQL on-demand en de SQL-pools asynchroon, is er een vertraging totdat ze worden weergegeven.

Toewijzing van tabellen aan externe tabellen, gegevensbronnen en bestandsindelingen.

## <a name="manage-a-spark-created-table"></a>Een door Spark gemaakte tabel beheren

Gebruik Spark om spark-databases te beheren. Verwijder deze bijvoorbeeld via een Spark-pooltaak en maak er tabellen in vanuit Spark.

Als u objecten in een dergelijke database maakt vanuit SQL on-demand of probeert de database te laten vallen, wordt de bewerking uitgevoerd, maar wordt de oorspronkelijke Spark-database niet gewijzigd.

Als u het gesynchroniseerde schema in een SQL-groep probeert te laten vallen of een tabel probeert te maken, geeft Azure een fout als resultaat.

## <a name="exposing-a-spark-table-in-sql"></a>Een Spark-tabel in SQL blootstellen

### <a name="which-spark-tables-are-shared"></a>Welke Spark-tabellen worden gedeeld

Spark biedt twee typen tabellen die Azure Synapse automatisch in SQL blootlegt:

- Beheerde tabellen

  Spark biedt veel opties voor het opslaan van gegevens in beheerde tabellen, zoals TEKST, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA en LIBSVM. Deze bestanden worden normaal `warehouse` gesproken opgeslagen in de map waar beheerde tabelgegevens worden opgeslagen.

- Externe tabellen

  Spark biedt ook manieren om externe tabellen over `LOCATION` bestaande gegevens te maken, door de optie te bieden of door de Hive-indeling te gebruiken. Dergelijke externe tabellen kunnen zich over verschillende gegevensformaten bevinden, waaronder parket.

Azure Synapse deelt momenteel alleen beheerde en externe Spark-tabellen die hun gegevens opslaan in parketindeling met de SQL-engines. Tabellen die door andere indelingen worden ondersteund, worden niet automatisch gesynchroniseerd. Mogelijk u dergelijke tabellen expliciet zelf synchroniseren als een externe tabel in uw eigen SQL-database als de SQL-engine de onderliggende indeling van de tabel ondersteunt.

### <a name="how-are-spark-tables-shared"></a>Hoe worden Spark-tabellen gedeeld

De beheerbare beheerde en externe Spark-tabellen die in de SQL-engines worden weergegeven als externe tabellen met de volgende eigenschappen:

- De gegevensbron van de SQL-externe tabel is de gegevensbron die de locatiemap van de Spark-tabel vertegenwoordigt.
- De bestandsindeling van de SQL-externe tabel is Parket.
- De toegangsreferenties van de SQL-externe tabel zijn doorgegeven.

Aangezien alle Spark-tabelnamen geldige SQL-tabelnamen zijn en alle Spark-kolomnamen geldige SQL-kolomnamen zijn, worden de Spark-tabel en kolomnamen gebruikt voor de sql-externe tabel.

Spark-tabellen bieden andere gegevenstypen dan de Synapse SQL-engines. In de volgende tabel worden spark-tabelgegevenstypen toegewezen aan de SQL-typen:

| Spark-gegevenstype | SQL-gegevenstype | Opmerkingen |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | Met collatie`Latin1_General_CP1_CI_AS_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Serialiseert in JSON met collatie`Latin1_General_CP1_CI_AS_UTF8` |
| `map`       |    `varchar(max)`   | Serialiseert in JSON met collatie`Latin1_General_CP1_CI_AS_UTF8` |
| `struct`    |    `varchar(max)`   | Serialiseert in JSON met collatie`Latin1_General_CP1_CI_AS_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Beveiligingsmodel

De Spark-databases en -tabellen en hun gesynchroniseerde representaties in de SQL-engines worden beveiligd op het onderliggende opslagniveau. Omdat ze momenteel geen machtigingen hebben op de objecten zelf, kunnen de objecten worden gezien in de objectverkenner.

De beveiligingsprincipal die een beheerde tabel maakt, wordt beschouwd als de eigenaar van die tabel en heeft alle rechten op de tabel en de onderliggende mappen en bestanden. Bovendien wordt de eigenaar van de database automatisch mede-eigenaar van de tabel.

Als u een Spark- of SQL-externe tabel maakt met verificatiepass-through, worden de gegevens alleen beveiligd op de map- en bestandsniveaus. Als iemand dit type externe tabel opvraagt, wordt de beveiligingsidentiteit van de queryindiener doorgegeven aan het bestandssysteem, waarmee wordt gecontroleerd op toegangsrechten.

Zie [Azure Synapse Analytics shared database](database.md)voor meer informatie over het instellen van machtigingen voor de mappen en bestanden.

## <a name="examples"></a>Voorbeelden

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>Een beheerde tabel maken die wordt ondersteund door Parquet in Spark en query vanuit SQL on-demand

In dit scenario hebt u `mytestdb`een Spark-database met de naam . Zie [& maken verbinding maken met spark-database - SQL on-demand](database.md#create--connect-to-spark-database---sql-on-demand).

Maak een beheerde Spark-tabel met SparkSQL door de volgende opdracht uit te voeren:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

Hiermee wordt `myParquetTable` de tabel `mytestdb`in de database gemaakt. Na een korte vertraging u de tabel in SQL on-demand zien. Voer bijvoorbeeld de volgende instructie uit SQL on-demand uit.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Controleer `myParquetTable` of dit in de resultaten is opgenomen.

>[!NOTE]
>Een tabel die parket niet als opslagformaat gebruikt, wordt niet gesynchroniseerd.

Voeg vervolgens enkele waarden in de tabel van Spark in, bijvoorbeeld met de volgende C# Spark-instructies in een C#-notitieblok:

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myParquetTable");
```

Nu u de gegevens van SQL on-demand als volgt lezen:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

U moet de volgende rij als resultaat krijgen:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="creating-an-external-table-backed-by-parquet-in-spark-and-querying-it-from-sql-on-demand"></a>Een externe tabel maken die wordt ondersteund door Parket in Spark en deze opvraagt vanuit SQL on-demand

Maak in dit voorbeeld een externe Spark-tabel over de parketgegevensbestanden die in het vorige voorbeeld voor de beheerde tabel zijn gemaakt.

Bijvoorbeeld met SparkSQL-run:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Vervang de `<fs>` tijdelijke aanduiding door de bestandssysteemnaam die het `<synapse_ws>` standaardbestandssysteem van de werkruimte is en de tijdelijke aanduiding door de naam van de synapswerkruimte die u gebruikt om dit voorbeeld uit te voeren.

In het vorige `myExtneralParquetTable` voorbeeld wordt `mytestdb`de tabel in de database gemaakt. Na een korte vertraging u de tabel in SQL on-demand zien. Voer bijvoorbeeld de volgende instructie uit SQL on-demand uit.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Controleer `myExternalParquetTable` of dit in de resultaten is opgenomen.

Nu u de gegevens van SQL on-demand als volgt lezen:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

U moet de volgende rij als resultaat krijgen:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="querying-spark-tables-in-a-sql-pool"></a>Spark-tabellen opvragen in een SQL-groep

Met de tabellen die in de vorige voorbeelden zijn gemaakt, maakt u nu een SQL-groep met de naam met de naam `mysqlpool` metagegevens (of gebruikt u de reeds gemaakte groep van Een [Spark-database blootstellen in een SQL-groep.](database.md#exposing-a-spark-database-in-a-sql-pool)

Voer de volgende `mysqlpool` instructie uit tegen de SQL-groep:

```sql
SELECT * FROM sys.tables;
```

Controleer of `myParquetTable` de `myExternalParquetTable` tabellen zichtbaar `$mytestdb`zijn in het schema .

Nu u de gegevens van SQL on-demand als volgt lezen:

```sql
SELECT * FROM [$mytestdb].myParquetTable WHERE name = 'Alice';
SELECT * FROM [$mytestdb].myExternalParquetTable WHERE name = 'Alice';
```

U moet dezelfde resultaten krijgen als met SQL on-demand hierboven.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over de gedeelde metadata van Azure Synapse Analytics](overview.md)
- [Meer informatie over de gedeelde metadatatabellen van Azure Synapse Analytics](table.md)


