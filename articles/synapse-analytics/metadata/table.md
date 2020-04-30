---
title: Gedeelde meta gegevens tabellen van Azure Synapse Analytics
description: Azure Synapse Analytics biedt een gemeen schappelijk meta gegevens model waarmee u een tabel in Apache Spark maakt, toegankelijk is vanuit de SQL-service on-demand (preview) en de SQL-groeps engines zonder de gegevens te dupliceren.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 7c1951c772dcd2f49f4f7c09021f69193af0a87e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424578"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Gedeelde meta gegevens tabellen van Azure Synapse Analytics

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Met Azure Synapse Analytics kunt u de verschillende reken kundige engines voor werk ruimten gebruiken om data bases en tabellen met Parquet-ondersteuning te delen tussen de Apache Spark Pools (preview), de SQL on-demand-Engine (preview) en SQL-groepen.

Wanneer een Data Base is gemaakt met een Spark-taak, kunt u er tabellen in maken met Spark waarin Parquet als opslag indeling wordt gebruikt. Deze tabellen worden direct beschikbaar voor het uitvoeren van query's in een van de opslag ruimten van de Azure Synapse-werk ruimte. Ze kunnen ook worden gebruikt vanuit een van de Spark-taken waarvoor machtigingen gelden.

De Spark gemaakte, beheerde en externe tabellen worden ook beschikbaar gemaakt als externe tabellen met dezelfde naam in de bijbehorende gesynchroniseerde data base in SQL on-demand en in de bijbehorende `$`vooraf vastgestelde schema's in de SQL-groepen waarvoor de synchronisatie van meta gegevens is ingeschakeld. Het beschikbaar maken van [een Spark-tabel in SQL](#exposing-a-spark-table-in-sql) biedt meer informatie over de tabel synchronisatie.

Aangezien de tabellen op aanvraag en de SQL-groepen asynchroon worden gesynchroniseerd met SQL, is er een vertraging tot ze worden weer gegeven.

Toewijzing van tabellen aan externe tabellen, gegevens bronnen en bestands indelingen.

## <a name="manage-a-spark-created-table"></a>Een door Spark gemaakte tabel beheren

Gebruik Spark voor het beheren van Spark gemaakte data bases. U kunt dit bijvoorbeeld verwijderen via een Spark-pool taak en er tabellen in maken vanuit Spark.

Als u objecten in een dergelijke data base van SQL on-demand maakt of de data base probeert te verwijderen, wordt de bewerking uitgevoerd, maar wordt de oorspronkelijke Spark-data base niet gewijzigd.

Als u probeert het gesynchroniseerde schema in een SQL-groep te verwijderen of een tabel erin te maken, wordt een fout geretourneerd door Azure.

## <a name="exposing-a-spark-table-in-sql"></a>Een Spark-tabel in SQL weer geven

### <a name="which-spark-tables-are-shared"></a>Welke Spark-tabellen worden gedeeld

Spark biedt twee typen tabellen die Azure Synapse automatisch beschikbaar maakt in SQL:

- Beheerde tabellen

  Spark biedt veel opties voor het opslaan van gegevens in beheerde tabellen, zoals TEXT, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA en LIBSVM. Deze bestanden worden normaal gesp roken opgeslagen `warehouse` in de map waarin de beheerde-tabel gegevens worden opgeslagen.

- Externe tabellen

  Spark biedt ook manieren om externe tabellen te maken op basis van bestaande gegevens, door `LOCATION` de optie te bieden of de Hive-indeling te gebruiken. Dergelijke externe tabellen kunnen meer dan een groot aantal gegevens indelingen hebben, waaronder Parquet.

Azure Synapse deelt momenteel alleen beheerde en externe Spark-tabellen op waarin hun gegevens worden opgeslagen in de Parquet-indeling met de SQL-engines. Tabellen die worden ondersteund door andere indelingen, worden niet automatisch gesynchroniseerd. U kunt dergelijke tabellen mogelijk expliciet als een externe tabel in uw eigen SQL database synchroniseren als de SQL-engine de onderliggende indeling van de tabel ondersteunt.

### <a name="how-are-spark-tables-shared"></a>Hoe worden Spark-tabellen gedeeld

De deelbaar beheerde en externe Spark-tabellen die worden weer gegeven in de SQL-engines als externe tabellen met de volgende eigenschappen:

- De gegevens bron van de externe SQL-tabel is de gegevens bron die de map location van de Spark-tabel voor stelt.
- De bestands indeling van de externe SQL-tabel is Parquet.
- De toegangs referenties van de externe SQL-tabel is Pass-through.

Aangezien alle Spark-tabel namen geldige SQL-tabel namen zijn en alle Spark-kolom namen geldige SQL-kolom namen zijn, worden de Spark-tabel-en kolom namen gebruikt voor de externe SQL-tabel.

Spark-tabellen bieden verschillende gegevens typen dan de Synapse SQL-engines. In de volgende tabel worden de gegevens typen van Spark-tabel toegewezen aan de SQL-typen:

| Spark-gegevens type | SQL-gegevens type | Opmerkingen |
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
| `string`    |    `varchar(max)`   | Met sortering`Latin1_General_CP1_CI_AS_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Serialisatie naar JSON met sortering`Latin1_General_CP1_CI_AS_UTF8` |
| `map`       |    `varchar(max)`   | Serialisatie naar JSON met sortering`Latin1_General_CP1_CI_AS_UTF8` |
| `struct`    |    `varchar(max)`   | Serialisatie naar JSON met sortering`Latin1_General_CP1_CI_AS_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Beveiligingsmodel

De Spark-data bases en-tabellen, evenals hun gesynchroniseerde representaties in de SQL-engines, worden beveiligd op het onderliggende opslag niveau. Omdat deze momenteel geen machtigingen hebben voor de objecten zelf, kunnen de objecten worden weer gegeven in de object Verkenner.

De beveiligingsprincipal die een beheerde tabel maakt, wordt beschouwd als de eigenaar van de tabel en heeft alle rechten voor de tabel, evenals de onderliggende mappen en bestanden. Daarnaast wordt de eigenaar van de data base automatisch mede-eigenaar van de tabel.

Als u een externe Spark-of SQL-tabel met verificatie doorvoer maakt, worden de gegevens alleen beveiligd op de map-en bestands niveaus. Als iemand dit type externe tabel opvraagt, wordt de beveiligings identiteit van de query indiener door gegeven aan het bestands systeem, waarmee de toegangs rechten worden gecontroleerd.

Zie voor meer informatie over het instellen van machtigingen voor de mappen en bestanden [Azure Synapse Analytics gedeelde data base](database.md).

## <a name="examples"></a>Voorbeelden

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>Een beheerde tabel maken die wordt ondersteund door Parquet in Spark en een query van SQL op aanvraag

In dit scenario hebt u een Spark-Data Base `mytestdb`met de naam. Zie [Create & verbinding maken met Spark data base-SQL op aanvraag](database.md#create--connect-to-spark-database---sql-on-demand).

Maak een beheerde Spark-tabel met SparkSQL door de volgende opdracht uit te voeren:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

Hiermee maakt u de `myParquetTable` tabel in de `mytestdb`data base. Na een korte vertraging kunt u de tabel in SQL op aanvraag bekijken. Voer bijvoorbeeld de volgende instructie uit op SQL op aanvraag.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Controleer of `myParquetTable` het is opgenomen in de resultaten.

>[!NOTE]
>Een tabel die geen Parquet gebruikt als de opslag indeling, wordt niet gesynchroniseerd.

Voeg vervolgens enkele waarden in de tabel in vanuit Spark, bijvoorbeeld met de volgende C# Spark-instructies in een C#-notebook:

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

U kunt de gegevens van SQL op aanvraag nu als volgt lezen:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

U moet de volgende rij als resultaat ophalen:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="creating-an-external-table-backed-by-parquet-in-spark-and-querying-it-from-sql-on-demand"></a>Een externe tabel maken die wordt ondersteund door Parquet in Spark en query's uitvoert op SQL op aanvraag

In dit voor beeld maakt u een externe Spark-tabel over de Parquet-gegevens bestanden die zijn gemaakt in het vorige voor beeld voor de beheerde tabel.

Bijvoorbeeld met SparkSQL-uitvoering:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Vervang de tijdelijke `<fs>` aanduiding door de naam van het bestands systeem dat het standaard bestandssysteem voor de `<synapse_ws>` werk ruimte is en de tijdelijke aanduiding met de naam van de Synapse-werk ruimte die u gebruikt om dit voor beeld uit te voeren.

In het vorige voor beeld wordt `myExtneralParquetTable` de tabel in `mytestdb`de-data base gemaakt. Na een korte vertraging kunt u de tabel in SQL op aanvraag bekijken. Voer bijvoorbeeld de volgende instructie uit op SQL op aanvraag.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Controleer of `myExternalParquetTable` het is opgenomen in de resultaten.

U kunt de gegevens van SQL op aanvraag nu als volgt lezen:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

U moet de volgende rij als resultaat ophalen:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="querying-spark-tables-in-a-sql-pool"></a>Query's uitvoeren voor Spark-tabellen in een SQL-groep

Met de tabellen die in de voor gaande voor beelden zijn gemaakt, maakt u nu een SQL `mysqlpool` -groep in uw werk ruimte met de naam die de synchronisatie van meta gegevens mogelijk maakt (of gebruikt u de reeds gemaakte groep om [een Spark-data base in een SQL-groep beschikbaar](database.md#exposing-a-spark-database-in-a-sql-pool)te maken.

Voer de volgende instructie uit op `mysqlpool` de SQL-groep:

```sql
SELECT * FROM sys.tables;
```

Controleer of de tabellen `myParquetTable` en `myExternalParquetTable` zichtbaar zijn in het schema `$mytestdb`.

U kunt de gegevens van SQL op aanvraag nu als volgt lezen:

```sql
SELECT * FROM [$mytestdb].myParquetTable WHERE name = 'Alice';
SELECT * FROM [$mytestdb].myExternalParquetTable WHERE name = 'Alice';
```

U moet dezelfde resultaten krijgen als met bovenstaande SQL op aanvraag.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over gedeelde meta gegevens van Azure Synapse Analytics](overview.md)
- [Meer informatie over gedeelde meta gegevens tabellen van Azure Synapse Analytics](table.md)


