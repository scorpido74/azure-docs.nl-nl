---
title: Statistieken maken, bijwerken
description: Aanbevelingen en voorbeelden voor het maken en bijwerken van queryoptimalisatiestatistieken op tabellen in Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5fae2bba0acc4ab462c91f7272694d032fc6ceaa
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742660"
---
# <a name="table-statistics-in-synapse-sql-pool"></a>Tabelstatistieken in de Synapse SQL-groep

In dit artikel vindt u aanbevelingen en voorbeelden voor het maken en bijwerken van queryoptimalisatiestatistieken over tabellen in SQL-pool.

## <a name="why-use-statistics"></a>Waarom statistieken gebruiken

Hoe meer SQL-pool weet over uw gegevens, hoe sneller het query's tegen kan uitvoeren. Na het laden van gegevens in SQL-pool is het verzamelen van statistieken over uw gegevens een van de belangrijkste dingen die u doen om uw query's te optimaliseren.

De SQL-groepqueryoptimizer is een op kosten gebaseerde optimizer. Het vergelijkt de kosten van verschillende queryplannen en kiest vervolgens het plan met de laagste kosten. In de meeste gevallen kiest het het plan dat het snelst wordt uitgevoerd.

Als de optimizer bijvoorbeeld schat dat de datum waarop uw query filtert, één rij wordt weergegeven, wordt één plan gekozen. Als de geselecteerde datum 1 miljoen rijen wordt weergegeven, wordt een ander plan weergegeven.

## <a name="automatic-creation-of-statistic"></a>Automatische creatie van statistiek

Wanneer de AUTO_CREATE_STATISTICS-optie voor de database is ingeschakeld, analyseert SQL-groep binnenkomende gebruikersquery's op ontbrekende statistieken.

Als statistieken ontbreken, maakt de queryoptimizer statistieken over afzonderlijke kolommen in het querypredicaat of de join-voorwaarde om de schataldaliteitsschattingen voor het queryplan te verbeteren.

> [!NOTE]
> Het automatisch maken van statistieken is momenteel standaard ingeschakeld.

U controleren of uw SQL-groep is AUTO_CREATE_STATISTICS geconfigureerd door de volgende opdracht uit te voeren:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Als uw SQL-groep niet AUTO_CREATE_STATISTICS geconfigureerd heeft, raden we u aan deze eigenschap in te schakelen door de volgende opdracht uit te voeren:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Deze verklaringen zullen leiden tot het automatisch maken van statistieken:

- SELECT
- INVOEGEN-SELECTEREN
- CTAS
- UPDATE
- DELETE
- LEG uit wanneer een join of de aanwezigheid van een predicaat wordt gedetecteerd

> [!NOTE]
> Automatische creatie van statistieken wordt niet gemaakt op tijdelijke of externe tabellen.

Het automatisch maken van statistieken gebeurt synchroon, zodat u mogelijk enigszins gedegradeerde queryprestaties krijgt als uw kolommen statistieken missen. De tijd om statistieken voor één kolom te maken, is afhankelijk van de grootte van de tabel.

Om meetbare prestatiedegradatie te voorkomen, moet u ervoor zorgen dat statistieken eerst zijn gemaakt door de benchmarkworkload uit te voeren voordat u het systeem profileert.

> [!NOTE]
> Het maken van statistieken wordt aangemeld bij [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) onder een andere gebruikerscontext.

Wanneer automatische statistieken worden gemaakt, nemen ze de vorm aan: _WA_Sys_<8-cijferige kolom-id in Hex>_<8-cijferige tabel-id in Hex>. U statistieken bekijken die al zijn gemaakt door de opdracht [DBCC-SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) uit te voeren:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

De table_name is de naam van de tabel die de statistieken bevat die moeten worden weergegeven. Deze tabel kan geen externe tabel zijn. Het doel is de naam van de doelindex, statistieken of kolom waarvoor statistiekeninformatie kunnen worden weergegeven.

## <a name="update-statistics"></a>Statistieken bijwerken

Een van de beste praktijken is om statistieken over datumkolommen elke dag bij te werken als er nieuwe datums worden toegevoegd. Telkens wanneer nieuwe rijen in de SQL-groep worden geladen, worden nieuwe belastingsdatums of transactiedatums toegevoegd. Deze toevoegingen veranderen de gegevensdistributie en maken de statistieken verouderd.

Statistieken over een kolom land/regio in een klantentabel hoeven mogelijk nooit te worden bijgewerkt, omdat de verdeling van waarden over het algemeen niet verandert. Ervan uitgaande dat de distributie constant is tussen klanten, zal het toevoegen van nieuwe rijen aan de tabelvariatie de gegevensdistributie niet veranderen.

Als uw SQL-groep echter slechts één land/regio bevat en u gegevens uit een nieuw land/regio binnenhaalt, wat resulteert in gegevens uit meerdere landen/regio's die worden opgeslagen, moet u statistieken over de kolom land/regio bijwerken.

Hieronder volgen aanbevelingen voor het bijwerken van statistieken:

|||
|-|-|
| **Frequentie van statistieken updates**  | Conservatief: Dagelijks </br> Na het laden of transformeren van uw gegevens |
| **Steekproeven** |  Gebruik minder dan 1 miljard rijen standaardsampling (20 procent). </br> Met meer dan 1 miljard rijen, gebruik bemonstering van twee procent. |

Een van de eerste vragen die u moet stellen wanneer u een query probleemoplost, **is: "Zijn de statistieken up-to-date?"**

Deze vraag is niet een die kan worden beantwoord door de leeftijd van de gegevens. Een up-to-date statistiekenobject kan oud zijn als er geen wezenlijke wijziging is aangebracht in de onderliggende gegevens.

> [!TIP]
> Wanneer het aantal rijen aanzienlijk is gewijzigd of er een wezenlijke wijziging is in de verdeling van waarden voor een *kolom,* is het tijd om statistieken bij te werken.

Er is geen dynamische beheerweergave om te bepalen of de gegevens in de tabel zijn gewijzigd sinds de laatste keer dat statistieken zijn bijgewerkt. Het kennen van de leeftijd van uw statistieken kan u voorzien van een deel van het beeld.

U de volgende query gebruiken om te bepalen wanneer uw statistieken in elke tabel zijn bijgewerkt.

> [!NOTE]
> Als er een wezenlijke wijziging is in de verdeling van waarden voor een kolom, moet u statistieken bijwerken, ongeacht de laatste keer dat ze zijn bijgewerkt.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

**Datumkolommen** in een SQL-groep hebben bijvoorbeeld meestal frequente statistische updates nodig. Telkens wanneer nieuwe rijen in de SQL-groep worden geladen, worden nieuwe belastingsdatums of transactiedatums toegevoegd. Deze toevoegingen veranderen de gegevensdistributie en maken de statistieken verouderd.

Omgekeerd hoeven statistieken over een genderkolom in een klantentabel mogelijk nooit te worden bijgewerkt. Ervan uitgaande dat de distributie constant is tussen klanten, zal het toevoegen van nieuwe rijen aan de tabelvariatie de gegevensdistributie niet veranderen.

Als uw SQL-groep slechts één geslacht bevat en een nieuwe vereiste resulteert in meerdere geslachten, moet u statistieken over de geslachtkolom bijwerken.

Zie voor meer informatie algemene richtlijnen voor [statistieken](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="implementing-statistics-management"></a>Uitvoering van het beheer van de statistiek

Het is vaak een goed idee om uw gegevenslaadproces uit te breiden om ervoor te zorgen dat statistieken aan het einde van de belasting worden bijgewerkt.

De gegevensbelasting is wanneer tabellen het vaakst hun grootte en/of hun verdeling van waarden wijzigen. Het laden van gegevens is een logische plek om bepaalde beheerprocessen te implementeren.

De volgende leidende principes zijn voorzien voor het bijwerken van uw statistieken tijdens het laadproces:

- Controleer of in elke geladen tabel ten minste één statistisch object is bijgewerkt. Hiermee worden de tabelgrootte (aantal rij's en het aantal pagina's) bijgewerkt als onderdeel van de statistische update.
- Focus op kolommen die deelnemen aan JOIN, GROUP BY, ORDER BY en DISTINCT-clausules.
- Overweeg om kolommen met 'opgaande sleutel' zoals transactiedatums vaker bij te werken, omdat deze waarden niet worden opgenomen in het histogram voor statistieken.
- Overweeg om statische distributiekolommen minder vaak bij te werken.
- Vergeet niet dat elk statistisch object achter elkaar wordt bijgewerkt. Gewoon implementeren `UPDATE STATISTICS <TABLE_NAME>` is niet altijd ideaal, vooral voor brede tabellen met veel statistieken objecten.

Zie [Cardinality Estimation](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)voor meer informatie.

## <a name="examples-create-statistics"></a>Voorbeelden: Statistieken maken

Deze voorbeelden laten zien hoe u verschillende opties gebruiken voor het maken van statistieken. Welke opties u voor elke kolom gebruikt, zijn afhankelijk van de kenmerken van uw gegevens en hoe de kolom in query's wordt gebruikt.

### <a name="create-single-column-statistics-with-default-options"></a>Statistieken met één kolom maken met standaardopties

Als u statistieken over een kolom wilt maken, geeft u een naam op voor het object statistieken en de naam van de kolom.

Met deze syntaxis worden alle standaardopties gebruikt. Sql-groep bemonstert standaard **20 procent** van de tabel wanneer deze statistieken maakt.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Bijvoorbeeld:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Statistieken met één kolom maken door elke rij te onderzoeken

De standaard sampling rate van 20 procent is voldoende voor de meeste situaties. U echter de bemonsteringsfrequentie aanpassen.

Als u de volledige tabel wilt proeven, gebruikt u de volgende syntaxis:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Bijvoorbeeld:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Statistieken met één kolom maken door de steekproefgrootte op te geven

U ook de steekproefgrootte als een percentage opgeven:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Statistieken met één kolom maken voor slechts enkele rijen

U ook statistieken maken over een deel van de rijen in uw tabel. Dit wordt een gefilterde statistiek genoemd.

U bijvoorbeeld gefilterde statistieken gebruiken wanneer u van plan bent een specifieke partitie van een grote partitietabel op te vragen. Door alleen statistieken over de partitiewaarden te maken, verbetert de nauwkeurigheid van de statistieken en verbetert het de queryprestaties.

In dit voorbeeld worden statistieken over een bereik van waarden. De waarden kunnen eenvoudig worden gedefinieerd op basis van het waardenbereik in een partitie.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Als u de queryoptimizer moet overwegen om gefilterde statistieken te gebruiken wanneer deze het gedistribueerde queryplan kiest, moet de query binnen de definitie van het statistiekobject passen. In het vorige voorbeeld moet de WHERE-clausule van de query col1-waarden tussen 2000101 en 20001231 opgeven.

### <a name="create-single-column-statistics-with-all-the-options"></a>Statistieken met één kolom maken met alle opties

U de opties ook combineren. In het volgende voorbeeld wordt een gefilterd statistisch object gemaakt met een aangepaste steekproefgrootte:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Zie STATISTIEKEN MAKEN [voor](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)de volledige referentie .

### <a name="create-multi-column-statistics"></a>Statistieken met meerdere kolommen maken

Als u een object met statistieken met meerdere kolommen wilt maken, gebruikt u de vorige voorbeelden, maar geeft u meer kolommen op.

> [!NOTE]
> Het histogram, dat wordt gebruikt om het aantal rijen in het queryresultaat te schatten, is alleen beschikbaar voor de eerste kolom die wordt vermeld in de definitie van het statistische object.

In dit voorbeeld staat het histogram op *de productcategorie.\_* Statistieken over verschillende kolommen worden berekend op *basis van productcategorie\_* en *productsub_category:\_*

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Omdat er een correlatie is tussen *productcategorie\_* en *\_productsubcategorie,\_* kan een object met statistieken met meerdere kolommen nuttig zijn als deze kolommen tegelijkertijd worden geopend.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Statistieken maken over alle kolommen in een tabel

Een manier om statistieken te maken is door STATISTIEKEN MAKEN-opdrachten uit te geven na het maken van de tabel:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Een opgeslagen procedure gebruiken om statistieken te maken over alle kolommen in een database

SQL-groep heeft geen systeemopgeslagen procedure die gelijkwaardig is aan sp_create_stats in SQL Server. Met deze opgeslagen procedure wordt één object kolomstatistieken gemaakt in elke kolom van de database die nog geen statistieken heeft.

In het volgende voorbeeld u aan de slag met uw databaseontwerp. Voel je vrij om het aan te passen aan uw behoeften.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Als u statistieken wilt maken over alle kolommen in de tabel met de standaardwaarden, voert u de opgeslagen procedure uit.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Als u statistieken wilt maken over alle kolommen in de tabel met behulp van een volledige scan, roept u deze procedure aan.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Als u steekproefstatistieken wilt maken over alle kolommen in de tabel, voert u 3 en het steekproefpercentage in. Deze procedure maakt gebruik van een steekproefpercentage van 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>Voorbeelden: Statistieken bijwerken

Als u statistieken wilt bijwerken, u het nieuws:

- Een statistisch object bijwerken. Geef de naam op van het statistiekobject dat u wilt bijwerken.
- Werk alle statistiekenobjecten op een tabel bij. Geef de naam van de tabel op in plaats van één specifiek statistisch object.

### <a name="update-one-specific-statistics-object"></a>Eén specifiek statistisch object bijwerken

Gebruik de volgende syntaxis om een specifiek object over statistieken bij te werken:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Bijvoorbeeld:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Door specifieke statistiekenobjecten bij te werken, u de tijd en middelen die nodig zijn om statistieken te beheren minimaliseren. Om dit te doen vereist enige gedachte om de beste statistieken objecten te kiezen om bij te werken.

### <a name="update-all-statistics-on-a-table"></a>Alle statistieken op een tabel bijwerken

Een eenvoudige methode voor het bijwerken van alle statistiekenobjecten in een tabel is:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Bijvoorbeeld:

```sql
UPDATE STATISTICS dbo.table1;
```

De update statistieken verklaring is gemakkelijk te gebruiken. Vergeet niet dat het *alle* statistieken op de tafel bijwerkt, en daarom meer werk zou kunnen uitvoeren dan nodig is. Als de prestaties geen probleem zijn, is dit de eenvoudigste en meest complete manier om te garanderen dat statistieken up-to-date zijn.

> [!NOTE]
> Wanneer u alle statistieken op een tabel bijwerkt, doet SQL-pool een scan om de tabel voor elk statistisch object te samplen. Als de tabel groot is en veel kolommen en veel statistieken bevat, is het mogelijk efficiënter om individuele statistieken bij te werken op basis van de behoefte.

Zie [Tijdelijke](sql-data-warehouse-tables-temporary.md)tabellen `UPDATE STATISTICS` voor de uitvoering van een procedure. De implementatiemethode verschilt enigszins van `CREATE STATISTICS` de vorige procedure, maar het resultaat is hetzelfde.

Zie [Statistieken bijwerken](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)voor de volledige syntaxis .

## <a name="statistics-metadata"></a>Metagegevens statistieken

Er zijn verschillende systeemweergaven en functies die u gebruiken om informatie over statistieken te vinden. U bijvoorbeeld zien of een statistisch object mogelijk verouderd is door de functie statistiekendatum te gebruiken om te zien wanneer statistieken voor het laatst zijn gemaakt of bijgewerkt.

### <a name="catalog-views-for-statistics"></a>Catalogusweergaven voor statistieken

Deze systeemweergaven geven informatie over statistieken:

| Catalogusweergave | Beschrijving |
|:--- |:--- |
| [sys.kolommen](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Eén rij voor elke kolom. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Eén rij voor elk object in de database. |
| [sys.schema's](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Eén rij voor elk schema in de database. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Eén rij voor elk object statistiek. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Eén rij voor elke kolom in het object Statistieken. Links terug naar sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Eén rij voor elke tabel (inclusief externe tabellen). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Eén rij voor elk gegevenstype. |

### <a name="system-functions-for-statistics"></a>Systeemfuncties voor statistieken

Deze systeemfuncties zijn handig voor het werken met statistieken:

| Systeemfunctie | Beschrijving |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Datum waarop het statistiekobject voor het laatst is bijgewerkt. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Overzichtsniveau en gedetailleerde informatie over de verdeling van waarden zoals begrepen door het statistische object. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Statistiekenkolommen en -functies combineren in één weergave

Deze weergave brengt kolommen die betrekking hebben op statistieken en resultaten van de functie STATS_DATE() samen.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-show_statistics-examples"></a>Voorbeelden van DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() toont de gegevens die in een statistisch object worden bewaard. Deze gegevens zijn er in drie delen:

- Header
- Dichtheidsvector
- Histogram

De header metadata over de statistieken. Het histogram geeft de verdeling van waarden weer in de eerste sleutelkolom van het object statistieken. De dichtheidsvector meet de correlatie tussen kolommen.

> [!NOTE]
> SQL-groep berekent kardinaliteitsschattingen met een van de gegevens in het object statistieken.

### <a name="show-header-density-and-histogram"></a>Koptekst, dichtheid en histogram weergeven

In dit eenvoudige voorbeeld worden alle drie de delen van een statistisch object weergegeven:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Bijvoorbeeld:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Een of meer delen van DBCC-SHOW_STATISTICS weergeven()

Als u alleen geïnteresseerd bent in het `WITH` bekijken van specifieke onderdelen, gebruikt u de clausule en geeft u op welke onderdelen u wilt zien:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Bijvoorbeeld:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>Verschillen dbcc SHOW_STATISTICS()

DBCC SHOW_STATISTICS() is strikter geïmplementeerd in SQL-groep in vergelijking met SQL Server:

- Functies zonder papieren worden niet ondersteund.
- Kan Stats_stream niet gebruiken.
- Kan geen deelnemen aan resultaten voor specifieke subsets van statistische gegevens. Bijvoorbeeld, STAT_HEADER JOIN DENSITY_VECTOR.
- NO_INFOMSGS kan niet worden ingesteld voor berichtonderdrukking.
- Vierkante haakjes rond statistiekennamen kunnen niet worden gebruikt.
- Kan geen kolomnamen gebruiken om statistiekenobjecten te identificeren.
- Aangepaste fout 2767 wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

Zie Uw werkbelasting controleren om de prestaties van [query's](sql-data-warehouse-manage-monitor.md) verder te verbeteren
