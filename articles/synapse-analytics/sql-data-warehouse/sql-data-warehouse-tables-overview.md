---
title: Tabellen ontwerpen
description: Inleiding tot het ontwerpen van tabellen in de Synapse SQL-groep.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2802c62acef0d78f8cfa7dd7f06bc34d8eecca4c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742625"
---
# <a name="design-tables-in-synapse-sql-pool"></a>Tabellen ontwerpen in de Synapse SQL-groep

In dit artikel worden belangrijke inleidende concepten voor het ontwerpen van tabellen in SQL-groep bevat.

## <a name="determine-table-category"></a>Tabelcategorie bepalen

Een [sterschema](https://en.wikipedia.org/wiki/Star_schema) organiseert gegevens in feiten- en dimensietabellen. Sommige tabellen worden gebruikt voor integratie of fasering van gegevens voordat deze worden verplaatst naar een feiten- of dimensietabel. Bepaal bij het ontwerpen van een tabel of de tabelgegevens in een feiten-, dimensie- of integratietabel thuishoren. Dit besluit informeert de juiste tabelstructuur en -verdeling.

- **Feitentabellen** bevatten kwantitatieve gegevens die vaak worden gegenereerd in een transactioneel systeem en vervolgens in de SQL-groep worden geladen. Een retailbedrijf genereert bijvoorbeeld elke dag verkooptransacties en laadt de gegevens vervolgens in een SQL-poolfacttabel voor analyse.

- **Dimensietabellen** bevatten kenmerkgegevens die kunnen veranderen, maar meestal zelden worden gewijzigd. De naam en het adres van een klant worden bijvoorbeeld opgeslagen in een dimensietabel en alleen bijgewerkt wanneer het profiel van de klant verandert. Om de grootte van een grote feitentabel te minimaliseren, hoeven de naam en het adres van de klant niet in elke rij van een feitentabel te staan. In plaats daarvan kunnen de feitentabel en de dimensietabel een klant-id delen. Een query kan deelnemen aan de twee tabellen om het profiel en de transacties van een klant te koppelen.

- **Integratietabellen** bieden een plaats voor het integreren of fasen van gegevens. U een integratietabel maken als een gewone tabel, een externe tabel of een tijdelijke tabel. U bijvoorbeeld gegevens laden naar een faseringstabel, transformaties uitvoeren op de gegevens in fasering en de gegevens vervolgens invoegen in een productietabel.

## <a name="schema-and-table-names"></a>Schema- en tabelnamen

Schema's zijn een goede manier om tabellen, gebruikt op een vergelijkbare manier, samen te groeperen.  Als u meerdere databases migreert van een on-prem-oplossing naar SQL-groep, werkt het het beste om alle feiten-, dimensie- en integratietabellen te migreren naar één schema in SQL-groep.

U bijvoorbeeld alle tabellen opslaan in de [SQL-groep WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) in één schema met de naam wwi. Met de volgende code wordt een [door de gebruiker gedefinieerd schema](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) met de naam wwi.

```sql
CREATE SCHEMA wwi;
```

Als u de organisatie van de tabellen in SQL-groep wilt weergeven, u feiten, dimmen en int gebruiken als voorvoegsels voor de tabelnamen. In de volgende tabel worden enkele schema- en tabelnamen voor WideWorldImportersDW weergegeven.  

| WideWorldImportersDW tabel  | Tabeltype | SQL-pool |
|:-----|:-----|:------|:-----|
| Plaats | Dimensie | wwi. DimCity (DimCity) |
| Bestellen | Fact | wwi. FactOrder (FactOrder) |

## <a name="table-persistence"></a>Tafelpersistentie

Tabellen slaan gegevens permanent op in Azure Storage, tijdelijk in Azure Storage of in een gegevensarchief buiten SQL-groep.

### <a name="regular-table"></a>Gewone tabel

Een gewone tabel slaat gegevens op in Azure Storage als onderdeel van SQL-groep. De tabel en de gegevens blijven bestaan, ongeacht of een sessie is geopend.  In het volgende voorbeeld wordt een gewone tabel met twee kolommen gemaakt.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tijdelijke tabel

Een tijdelijke tabel bestaat alleen voor de duur van de sessie. U een tijdelijke tabel gebruiken om te voorkomen dat andere gebruikers tijdelijke resultaten te zien krijgen en ook om de noodzaak van opruiming te verminderen.  

Tijdelijke tabellen maken gebruik van lokale opslag om snelle prestaties te bieden.  Zie [Tijdelijke tabellen voor](sql-data-warehouse-tables-temporary.md)meer informatie.

### <a name="external-table"></a>Externe tabel

Een externe tabel verwijst naar gegevens in Azure Storage blob of Azure Data Lake Store. Wanneer u deze gebruikt in combinatie met de instructie TABEL MAKEN ALS SELECT, importeert u uit een externe tabel gegevens in SQL-groep.

Externe tabellen zijn daarom handig voor het laden van gegevens. Zie [PolyBase gebruiken om gegevens uit Azure blob-opslag te laden](load-data-from-azure-blob-storage-using-polybase.md)voor een zelfstudie voor het laden.

## <a name="data-types"></a>Gegevenstypen

SQL-groep ondersteunt de meest gebruikte gegevenstypen. Zie [gegevenstypen in Tabelverwijzing MAKEN](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes) in de instructie TABEL MAKEN voor een lijst met ondersteunde gegevenstypen. Zie [Gegevenstypen](sql-data-warehouse-tables-data-types.md)voor richtlijnen voor het gebruik van gegevenstypen .

## <a name="distributed-tables"></a>Gedistribueerde tabellen

Een fundamenteel kenmerk van SQL-pool is de manier waarop het tabellen kan opslaan en bedienen in [verschillende distributies.](massively-parallel-processing-mpp-architecture.md#distributions)  SQL-groep ondersteunt drie methoden voor het distribueren van gegevens: round-robin (standaard), hash en gerepliceerd.

### <a name="hash-distributed-tables"></a>Met hash gedistribueerde tabellen

Een hash gedistribueerde tabel verdeelt rijen op basis van de waarde in de distributiekolom. Een hash distributed table is ontworpen om hoge prestaties te bereiken voor query's op grote tabellen. Er zijn verschillende factoren waarmee rekening moet worden gehouden bij het kiezen van een distributiekolom.

Zie [Ontwerprichtlijnen voor gedistribueerde tabellen voor](sql-data-warehouse-tables-distribute.md)meer informatie .

### <a name="replicated-tables"></a>Gerepliceerde tabellen

Een gerepliceerde tabel bevat een volledige kopie van de tabel die beschikbaar is op elk Compute-knooppunt. Query's worden snel uitgevoerd op gerepliceerde tabellen, omdat joins op gerepliceerde tabellen geen gegevensverplaatsing vereisen. Replicatie vereist echter extra opslagruimte en is niet praktisch voor grote tabellen.

Zie [Ontwerprichtlijnen voor gerepliceerde tabellen voor](design-guidance-for-replicated-tables.md)meer informatie .

### <a name="round-robin-tables"></a>Round-robin tafels

Een ronde-robin tafel verdeelt tafelrijen gelijkmatig over alle distributies. De rijen worden willekeurig verdeeld. Het laden van gegevens in een round-robin tabel is snel.  Houd er rekening mee dat query's meer gegevensverplaatsing kunnen vereisen dan de andere distributiemethoden.

Zie [Ontwerprichtlijnen voor gedistribueerde tabellen voor](sql-data-warehouse-tables-distribute.md)meer informatie .

### <a name="common-distribution-methods-for-tables"></a>Algemene distributiemethoden voor tabellen

De tabelcategorie bepaalt vaak welke optie u wilt kiezen voor het distribueren van de tabel.

| Tabelcategorie | Aanbevolen distributieoptie |
|:---------------|:--------------------|
| Fact           | Gebruik hash-distributie met geclusterde kolomarchiefindex. De prestaties verbeteren wanneer twee hashtabellen worden samengevoegd in dezelfde distributiekolom. |
| Dimensie      | Gebruik gerepliceerd voor kleinere tabellen. Als tabellen te groot zijn om op elk Compute-knooppunt op te slaan, gebruikt u hash-distributed. |
| Faseren        | Gebruik round-robin voor de staging tafel. De belasting met CTAS is snel. Zodra de gegevens zich in de faseringstabel bevindt, gebruikt u INSERT... SELECTEER om de gegevens naar productietabellen te verplaatsen. |

## <a name="table-partitions"></a>Tabelpartities

Een partitietabel slaat bewerkingen op de tabelrijen op en voert deze uit op basis van gegevensbereiken. Een tabel kan bijvoorbeeld worden verdeeld per dag, maand of jaar. U de queryprestaties verbeteren door partitieverwijdering, waardoor een queryscan beperkt tot gegevens binnen een partitie. U de gegevens ook onderhouden door te schakelen met partitie. Aangezien de gegevens in SQL Data Warehouse al zijn gedistribueerd, kunnen te veel partities de queryprestaties vertragen. Zie [Richtlijnen voor het verdelen voor](sql-data-warehouse-tables-partition.md)meer informatie .  Wanneer partitie overschakelt naar tabelpartities die niet leeg zijn, u overwegen de TRUNCATE_TARGET optie in de [wijzigingstabelinstructie](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) te gebruiken als de bestaande gegevens moeten worden afgekapt. De onderstaande code schakelt in de getransformeerde dagelijkse gegevens in de SalesFact overschrijven van bestaande gegevens.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Columnstore-indexen

Sql-groep slaat een tabel standaard op als een geclusterde kolomarchiefindex. Deze vorm van gegevensopslag bereikt hoge gegevenscompressie- en queryprestaties op grote tabellen.  

De geclusterde kolomarchiefindex is meestal de beste keuze, maar in sommige gevallen is een geclusterde index of een heap de juiste opslagstructuur.  

> [!TIP]
> Een heaptabel kan vooral handig zijn voor het laden van tijdelijke gegevens, zoals een faseringstabel die wordt omgezet in een laatste tabel.

Zie [Nieuw voor kolomarchiefindexen voor](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)een lijst met functies voor kolomarchief . Zie De kwaliteit van [de rijgroep maximaliseren voor kolomarchiefindexen](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)als u de indexprestaties van de kolomopslag wilt verbeteren.

## <a name="statistics"></a>statistieken

De queryoptimizer gebruikt statistieken op kolomniveau wanneer het plan voor het uitvoeren van een query wordt gemaakt.

Om de queryprestaties te verbeteren, is het belangrijk om statistieken over afzonderlijke kolommen te hebben, met name kolommen die worden gebruikt in queryjoins. [Het maken van statistieken](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic) gebeurt automatisch.  

Het bijwerken van statistieken gebeurt niet automatisch. Statistieken bijwerken nadat een aanzienlijk aantal rijen zijn toegevoegd of gewijzigd. Werk bijvoorbeeld statistieken na een belasting bij. Zie [Statistiekenrichtlijnen](sql-data-warehouse-tables-statistics.md)voor meer informatie .

## <a name="primary-key-and-unique-key"></a>Primaire sleutel en unieke sleutel

PRIMAIRE SLEUTEL wordt alleen ondersteund wanneer NIET-GECLUSTERD en NIET AFGEDWONGEN worden beide gebruikt.  Unieke beperking wordt alleen ondersteund met NIET AFGEDWONGEN wordt gebruikt.  Schakel [beperkingen voor SQL-pooltabel in](sql-data-warehouse-table-constraints.md).

## <a name="commands-for-creating-tables"></a>Opdrachten voor het maken van tabellen

U een tabel maken als een nieuwe lege tabel. U ook een tabel maken en vullen met de resultaten van een selecte instructie. Hieronder volgen de T-SQL-opdrachten voor het maken van een tabel.

| T-SQL-instructie | Beschrijving |
|:----------------|:------------|
| [TABEL MAKEN](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Hiermee maakt u een lege tabel door alle tabelkolommen en -opties te definiëren. |
| [EXTERNE TABEL MAKEN](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Hiermee maakt u een externe tabel. De definitie van de tabel wordt opgeslagen in SQL-groep. De tabelgegevens worden opgeslagen in Azure Blob-opslag of Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Hiermee wordt een nieuwe tabel gevuld met de resultaten van een selecte instructie. De tabelkolommen en gegevenstypen zijn gebaseerd op de resultaten van de selecte instructie. Als u gegevens wilt importeren, kan deze instructie kiezen uit een externe tabel. |
| [EXTERNE TABEL MAKEN ALS SELECTEREN](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Hiermee maakt u een nieuwe externe tabel door de resultaten van een geselecteerde instructie naar een externe locatie te exporteren.  De locatie is Azure Blob-opslag of Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-sql-pool"></a>Brongegevens afstemmen op de SQL-groep

SQL-pooltabellen worden gevuld door gegevens uit een andere gegevensbron te laden. Als u een geslaagde belasting wilt uitvoeren, moeten het aantal en de gegevenstypen van de kolommen in de brongegevens worden uitgelijnd met de tabeldefinitie in de SQL-groep. Het mogelijk is dat de gegevens worden uitgelijnd, het moeilijkste deel van het ontwerpen van uw tabellen.

Als gegevens afkomstig zijn van meerdere gegevensarchieven, laadt u de gegevens in de SQL-groep en slaat u deze op in een integratietabel. Zodra gegevens zich in de integratietabel bevinden, u de kracht van SQL-pool gebruiken om transformatiebewerkingen uit te voeren. Zodra de gegevens zijn voorbereid, u deze in productietabellen invoegen.

## <a name="unsupported-table-features"></a>Niet-ondersteunde tabelfuncties

SQL-pool ondersteunt veel, maar niet alle, van de tabelfuncties die worden aangeboden door andere databases.  In de volgende lijst worden enkele van de tabelfuncties weergegeven die niet worden ondersteund in SQL-groep:

- Buitenlandse sleutel, [Beperkingen van de tabel controleren](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Berekende kolommen](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Geïndexeerde weergaven](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Reeks](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Schaarse kolommen](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- Surrogaat sleutels. Implementeren met [identiteit](sql-data-warehouse-tables-identity.md).
- [Synoniemen](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Triggers](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Unieke indexen](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Door de gebruiker gedefinieerde typen](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>Query's met tabelgrootte

Een eenvoudige manier om ruimte en rijen verbruikt door een tabel in elk van de 60 distributies te identificeren, is het gebruik van [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Het gebruik van DBCC-opdrachten kan echter behoorlijk beperkend zijn.  Dynamische beheerweergaven (DMVs) tonen meer details dan DBCC-opdrachten. Begin met het maken van deze weergave:

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count]
    + nps.[row_overflow_used_page_count]
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count]
 - (nps.[reserved_page_count] - nps.[used_page_count])
 - ([in_row_data_page_count]
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
WHERE pn.[type] = 'COMPUTE'
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT *
FROM size
;
```

### <a name="table-space-summary"></a>Overzicht van tabelruimte

Met deze query worden de rijen en ruimte voor tabel geretourneerd.  Hiermee u zien welke tabellen uw grootste tabellen zijn en of ze round-robin, gerepliceerd of hash-gedistribueerd zijn.  Voor door hash gedistribueerde tabellen wordt in de query de distributiekolom weergegeven.  

```sql
SELECT
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM
    dbo.vTableSizes
GROUP BY
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Tabelruimte op distributietype

```sql
SELECT
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Tabelruimte op indextype

```sql
SELECT
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Overzicht van distributieruimte

```sql
SELECT
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Volgende stappen

Nadat u de tabellen voor uw SQL-groep hebt maken, moet u gegevens in de tabel laden.  Zie Gegevens laden [in SQL-groep](load-data-wideworldimportersdw.md)voor een zelfstudie voor laden.
