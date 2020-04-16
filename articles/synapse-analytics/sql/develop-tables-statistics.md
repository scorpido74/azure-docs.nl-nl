---
title: Statistieken maken, bijwerken
description: Aanbevelingen en voorbeelden voor het maken en bijwerken van query-optimalisatiestatistieken in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 798fec4dacb33a9f16de319062baf12adaffdbd0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428743"
---
# <a name="statistics-in-synapse-sql"></a>Statistieken in Synapse SQL

In dit artikel zijn aanbevelingen en voorbeelden voor het maken en bijwerken van queryoptimalisatiestatistieken met behulp van de Synapse SQL-resources: SQL-pool en SQL on-demand (preview).

## <a name="statistics-in-sql-pool"></a>Statistieken in SQL-pool

### <a name="why-use-statistics"></a>Waarom statistieken gebruiken

Hoe meer de SQL-poolbron over uw gegevens weet, hoe sneller query's kunnen worden uitgevoerd. Na het laden van gegevens in SQL-pool is het verzamelen van statistieken over uw gegevens een van de belangrijkste dingen die u doen voor queryoptimalisatie.  

De SQL-groepqueryoptimizer is een op kosten gebaseerde optimizer. Het vergelijkt de kosten van verschillende queryplannen en kiest vervolgens het plan met de laagste kosten. In de meeste gevallen kiest het het plan dat het snelst wordt uitgevoerd.

Als de optimizer bijvoorbeeld schat dat de datum waarop uw query filtert, één rij wordt weergegeven, wordt één plan gekozen. Als de geselecteerde datum 1 miljoen rijen wordt weergegeven, wordt een ander plan weergegeven.

### <a name="automatic-creation-of-statistics"></a>Automatische creatie van statistieken

SQL-groep analyseert binnenkomende gebruikersquery's op ontbrekende `ON`statistieken wanneer de AUTO_CREATE_STATISTICS-optie voor database is ingesteld op .  Als statistieken ontbreken, maakt de queryoptimizer statistieken over afzonderlijke kolommen in het querypredicaat of de aan/uit-staat. Deze functie wordt gebruikt om de schattingen van de kardinaliteit voor het queryplan te verbeteren.

> [!IMPORTANT]
> Het automatisch maken van statistieken is momenteel standaard ingeschakeld.

U controleren of uw gegevensmagazijn is AUTO_CREATE_STATISTICS geconfigureerd door de volgende opdracht uit te voeren:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Als uw gegevensmagazijn niet is ingeschakeld AUTO_CREATE_STATISTICS, raden we u aan deze eigenschap in te schakelen door de volgende opdracht uit te voeren:

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
> De automatische creatie van statistieken wordt niet gegenereerd op tijdelijke of externe tabellen.

Het automatisch maken van statistieken gebeurt synchroon. U dus enigszins gedegradeerde queryprestaties uitvoeren als uw kolommen statistieken missen. De tijd om statistieken voor één kolom te maken, is afhankelijk van de grootte van de tabel.

Om meetbare prestatiedegradatie te voorkomen, moet u ervoor zorgen dat statistieken eerst zijn gemaakt door de benchmarkworkload uit te voeren voordat u het systeem profileert.

> [!NOTE]
> Het maken van statistieken wordt aangemeld [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) onder een andere gebruikerscontext.

Wanneer automatische statistieken worden gemaakt, nemen ze het formulier aan: _WA_Sys_<8-cijferige kolom-id in Hex>_<8-cijferige tabel-id in Hex>. U reeds gemaakte statistieken bekijken door de opdracht [DBCC-SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) uit te voeren:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

De table_name is de naam van de tabel die de statistieken bevat die moeten worden weergegeven, wat geen externe tabel kan zijn. Het doel is de naam van de doelindex, statistieken of kolom waarvoor statistiekeninformatie kunnen worden weergegeven.

### <a name="update-statistics"></a>Statistieken bijwerken

Een van de beste praktijken is om statistieken over datumkolommen elke dag bij te werken als er nieuwe datums worden toegevoegd. Telkens wanneer nieuwe rijen in het gegevensmagazijn worden geladen, worden nieuwe laaddatums of transactiedatums toegevoegd. Deze toevoegingen veranderen de gegevensdistributie en maken de statistieken verouderd.

Statistieken over een land- of regiokolom in een klantentabel hoeven mogelijk nooit te worden bijgewerkt omdat de verdeling van waarden meestal niet verandert. Ervan uitgaande dat de distributie constant is tussen klanten, zal het toevoegen van nieuwe rijen aan de tabelvariatie de gegevensdistributie niet veranderen.

Wanneer uw gegevensmagazijn echter slechts één land of regio bevat en u gegevens uit een nieuw land of een nieuwe regio inbrengt, moet u statistieken over de land- of regiokolom bijwerken.

Hieronder volgen aanbevelingen voor het bijwerken van statistieken:

|||
|-|-|
| **Frequentie van statistieken updates**  | Conservatief: Dagelijks </br> Na het laden of transformeren van uw gegevens |
| **Steekproeven** |  Gebruik minder dan 1 miljard rijen standaardsampling (20 procent). </br> Met meer dan 1 miljard rijen, gebruik bemonstering van twee procent. |

### <a name="determine-last-statistics-update"></a>Laatste statistische update bepalen

Een van de eerste vragen die u moet stellen wanneer u een query probleemoplost, **is: "Zijn de statistieken up-to-date?"**

Deze vraag is niet een die kan worden beantwoord door de leeftijd van de gegevens. Een up-to-date statistiekenobject kan oud zijn als er geen wezenlijke wijziging is aangebracht in de onderliggende gegevens. Wanneer het aantal rijen aanzienlijk is gewijzigd of wanneer er een wezenlijke wijziging in de verdeling van waarden voor een kolom *optreedt,* is het tijd om statistieken bij te werken.

Er is geen dynamische beheerweergave beschikbaar om te bepalen of de gegevens in de tabel zijn gewijzigd sinds de laatste keer dat statistieken zijn bijgewerkt. Het kennen van de leeftijd van uw statistieken kan u voorzien van een deel van het beeld. U de volgende query gebruiken om te bepalen wanneer uw statistieken in elke tabel zijn bijgewerkt.

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

**Datumkolommen** in een gegevensmagazijn hebben bijvoorbeeld meestal frequente statistische updates nodig. Telkens wanneer nieuwe rijen in het gegevensmagazijn worden geladen, worden nieuwe laaddatums of transactiedatums toegevoegd. Deze toevoegingen veranderen de gegevensdistributie en maken de statistieken verouderd.

Statistieken over een geslachtkolom in een klantentabel hoeven mogelijk nooit te worden bijgewerkt. Ervan uitgaande dat de distributie constant is tussen klanten, zal het toevoegen van nieuwe rijen aan de tabelvariatie de gegevensdistributie niet veranderen.

Maar als uw gegevensmagazijn slechts één geslacht bevat en een nieuwe vereiste resulteert in meerdere geslachten, moet u statistieken over de geslachtkolom bijwerken. Voor meer informatie, bekijk het [artikel statistieken.](/sql/relational-databases/statistics/statistics)

### <a name="implementing-statistics-management"></a>Uitvoering van het beheer van de statistiek

Het is vaak een goed idee om uw gegevenslaadproces uit te breiden om ervoor te zorgen dat statistieken aan het einde van de belasting worden bijgewerkt. De gegevensbelasting is wanneer tabellen het vaakst hun grootte, verdeling van waarden of beide wijzigen. Als zodanig is het laadproces een logische plaats om sommige beheerprocessen te implementeren.

De volgende leidende principes zijn voorzien voor het bijwerken van uw statistieken tijdens het laadproces:

- Controleer of in elke geladen tabel ten minste één statistisch object is bijgewerkt. Met dit proces worden de tabelgrootte (aantal rijen en het aantal pagina's) bijgewerkt als onderdeel van de statistische update.
- Focus op kolommen die deelnemen aan JOIN, GROUP BY, ORDER BY en DISTINCT-clausules.
- Overweeg om kolommen met 'opgaande sleutel' zoals transactiedatums vaker bij te werken, omdat deze waarden niet worden opgenomen in het histogram voor statistieken.
- Overweeg om statische distributiekolommen minder vaak bij te werken.
- Vergeet niet dat elk statistisch object achter elkaar wordt bijgewerkt. Gewoon implementeren `UPDATE STATISTICS <TABLE_NAME>` is niet altijd ideaal, vooral voor brede tabellen met veel statistieken objecten.

Zie [Cardinality Estimation](/sql/relational-databases/performance/cardinality-estimation-sql-server)voor meer informatie.

### <a name="examples-create-statistics"></a>Voorbeelden: Statistieken maken

Deze voorbeelden laten zien hoe u verschillende opties gebruiken voor het maken van statistieken. Welke opties u voor elke kolom gebruikt, zijn afhankelijk van de kenmerken van uw gegevens en hoe de kolom in query's wordt gebruikt.

#### <a name="create-single-column-statistics-with-default-options"></a>Statistieken met één kolom maken met standaardopties

Als u statistieken over een kolom wilt maken, geeft u een naam op voor het object statistieken en de naam van de kolom.
Met deze syntaxis worden alle standaardopties gebruikt. Sql-groep bemonstert standaard **20 procent** van de tabel wanneer deze statistieken maakt.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Bijvoorbeeld:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Statistieken met één kolom maken door elke rij te onderzoeken

De standaard sampling rate van 20 procent is voldoende voor de meeste situaties. U echter de bemonsteringsfrequentie aanpassen. Als u de volledige tabel wilt proeven, gebruikt u de volgende syntaxis:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Bijvoorbeeld:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Statistieken met één kolom maken door de steekproefgrootte op te geven

Een andere optie die u hebt, is het opgeven van de steekproefgrootte als een percentage:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Statistieken met één kolom maken voor slechts enkele rijen

U ook statistieken maken over een gedeelte van de rijen in uw tabel, dat een gefilterde statistiek wordt genoemd.

U bijvoorbeeld gefilterde statistieken gebruiken wanneer u van plan bent een specifieke partitie van een grote partitietabel op te vragen. Door alleen statistieken te maken over de partitiewaarden, zal de nauwkeurigheid van de statistieken verbeteren. U zult ook een verbetering in queryprestaties ervaren.

In dit voorbeeld worden statistieken over een bereik van waarden. De waarden kunnen eenvoudig worden gedefinieerd op basis van het waardenbereik in een partitie.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Als u de queryoptimizer moet overwegen om gefilterde statistieken te gebruiken wanneer deze het gedistribueerde queryplan kiest, moet de query binnen de definitie van het statistiekobject passen. In het vorige voorbeeld moet de WHERE-clausule van de query col1-waarden tussen 2000101 en 20001231 opgeven.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Statistieken met één kolom maken met alle opties

U de opties ook combineren. In het volgende voorbeeld wordt een gefilterd statistisch object gemaakt met een aangepaste steekproefgrootte:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Zie STATISTIEKEN MAKEN [voor](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)de volledige referentie .

#### <a name="create-multi-column-statistics"></a>Statistieken met meerdere kolommen maken

Als u een object met statistieken met meerdere kolommen wilt maken, gebruikt u de vorige voorbeelden, maar geeft u meer kolommen op.

> [!NOTE]
> Het histogram, dat wordt gebruikt om het aantal rijen in het queryresultaat te schatten, is alleen beschikbaar voor de eerste kolom die wordt vermeld in de definitie van het statistische object.

In dit voorbeeld staat het histogram op *de productcategorie.\_* Statistieken over verschillende kolommen worden berekend op *basis van productcategorie\_* en *productsub_category:\_*

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Omdat er een correlatie bestaat tussen *productcategorie\_* en *\_productsubcategorie,\_* kan een object met statistieken met meerdere kolommen nuttig zijn als deze kolommen tegelijkertijd worden geopend.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Statistieken maken over alle kolommen in een tabel

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

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Een opgeslagen procedure gebruiken om statistieken te maken over alle kolommen in een database

SQL-groep heeft geen systeemopgeslagen procedure die gelijkwaardig is aan sp_create_stats in SQL Server. Met deze opgeslagen procedure wordt één object kolomstatistieken gemaakt in elke kolom van de database die nog geen statistieken heeft.
In het volgende voorbeeld u aan de slag met uw databaseontwerp. Voel je vrij om het aan te passen aan uw behoeften:

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

Als u statistieken wilt maken over alle kolommen in de tabel met een volledige scan, roept u deze procedure aan:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Als u steekproefstatistieken wilt maken over alle kolommen in de tabel, voert u 3 en het steekproefpercentage in. De onderstaande procedure maakt gebruik van een steekproefpercentage van 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Voorbeelden: Statistieken bijwerken

Als u statistieken wilt bijwerken, u het nieuws:

- Een statistisch object bijwerken. Geef de naam op van het statistiekobject dat u wilt bijwerken.
- Werk alle statistiekenobjecten op een tabel bij. Geef de naam van de tabel op in plaats van één specifiek statistisch object.

#### <a name="update-one-specific-statistics-object"></a>Eén specifiek statistisch object bijwerken

Gebruik de volgende syntaxis om een specifiek object over statistieken bij te werken:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Bijvoorbeeld:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Door specifieke statistiekenobjecten bij te werken, u de tijd en middelen die nodig zijn om statistieken te beheren minimaliseren. Met deze actie moet er goed over worden nagedacht om de beste statistiekenobjecten te selecteren die u wilt bijwerken.

#### <a name="update-all-statistics-on-a-table"></a>Alle statistieken op een tabel bijwerken

Een eenvoudige methode voor het bijwerken van alle statistiekenobjecten in een tabel is:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Bijvoorbeeld:

```sql
UPDATE STATISTICS dbo.table1;
```

De update statistieken verklaring is gemakkelijk te gebruiken. Vergeet niet dat het *bijhoudt alle* statistieken op de tafel, vraagt meer werk dan nodig is. Als de prestaties geen probleem zijn, is deze methode de eenvoudigste en meest complete manier om te garanderen dat statistieken up-to-date zijn.

> [!NOTE]
> Wanneer u alle statistieken op een tabel bijwerkt, doet SQL-pool een scan om de tabel voor elk statistisch object te samplen. Als de tabel groot is en veel kolommen en veel statistieken bevat, is het mogelijk efficiënter om individuele statistieken bij te werken op basis van de behoefte.

Zie [Tijdelijke](develop-tables-temporary.md)tabellen `UPDATE STATISTICS` voor de uitvoering van een procedure. De implementatiemethode verschilt enigszins van `CREATE STATISTICS` de vorige procedure, maar het resultaat is hetzelfde.
Zie [Statistieken bijwerken](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)voor de volledige syntaxis .

### <a name="statistics-metadata"></a>Metagegevens statistieken

Er zijn verschillende systeemweergaven en functies die u gebruiken om informatie over statistieken te vinden. U bijvoorbeeld zien of een statistisch object mogelijk verouderd is met de functie STATS_DATE(). STATS_DATE() u zien wanneer statistieken voor het laatst zijn gemaakt of bijgewerkt.

#### <a name="catalog-views-for-statistics"></a>Catalogusweergaven voor statistieken

Deze systeemweergaven geven informatie over statistieken:

| Catalogusweergave | Beschrijving |
|:--- |:--- |
| [sys.kolommen](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Eén rij voor elke kolom. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Eén rij voor elk object in de database. |
| [sys.schema's](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Eén rij voor elk schema in de database. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Eén rij voor elk object statistiek. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Eén rij voor elke kolom in het object Statistieken. Links terug naar sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Eén rij voor elke tabel (inclusief externe tabellen). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Eén rij voor elk gegevenstype. |

#### <a name="system-functions-for-statistics"></a>Systeemfuncties voor statistieken

Deze systeemfuncties zijn handig voor het werken met statistieken:

| Systeemfunctie | Beschrijving |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Datum waarop het statistiekobject voor het laatst is bijgewerkt. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Overzichtsniveau en gedetailleerde informatie over de verdeling van waarden zoals begrepen door het statistische object. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Statistiekenkolommen en -functies combineren in één weergave

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

### <a name="dbcc-show_statistics-examples"></a>Voorbeelden van DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() toont de gegevens die in een statistisch object worden bewaard. Deze gegevens zijn er in drie delen:

- Header
- Dichtheidsvector
- Histogram

De header is de metadata over de statistieken. Het histogram geeft de verdeling van waarden weer in de eerste sleutelkolom van het object statistieken. De dichtheidsvector meet de correlatie tussen kolommen. SQL-groep berekent kardinaliteitsschattingen met een van de gegevens in het object statistieken.

#### <a name="show-header-density-and-histogram"></a>Koptekst, dichtheid en histogram weergeven

In dit eenvoudige voorbeeld worden alle drie de delen van een statistisch object weergegeven:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Bijvoorbeeld:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Een of meer delen van DBCC-SHOW_STATISTICS weergeven()

Als u alleen geïnteresseerd bent in het `WITH` bekijken van specifieke onderdelen, gebruikt u de clausule en geeft u op welke onderdelen u wilt zien:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Bijvoorbeeld:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>Verschillen dbcc SHOW_STATISTICS()

DBCC SHOW_STATISTICS() is strikter geïmplementeerd in SQL-groep in vergelijking met SQL Server:

- Functies zonder papieren worden niet ondersteund.
- Ik kan Stats_stream niet gebruiken.
- Kan geen deelnemen aan resultaten voor specifieke subsets van statistische gegevens. Bijvoorbeeld, STAT_HEADER JOIN DENSITY_VECTOR.
- NO_INFOMSGS kunnen niet worden ingesteld voor het onderdrukken van berichten.
- Vierkante haakjes rond statistiekennamen kunnen niet worden gebruikt.
- U geen kolomnamen gebruiken om statistiekenobjecten te identificeren.
- Aangepaste fout 2767 wordt niet ondersteund.

### <a name="next-steps"></a>Volgende stappen

Zie Uw werkbelasting controleren om de prestaties van [query's](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) verder te verbeteren

## <a name="statistics-in-sql-on-demand-preview"></a>Statistieken in SQL on-demand (voorbeeld)

Statistieken worden gemaakt per bepaalde kolom voor bepaalde gegevensset (opslagpad).

### <a name="why-use-statistics"></a>Waarom statistieken gebruiken

Hoe meer SQL on-demand (preview) weet over uw gegevens, hoe sneller het query's kan uitvoeren tegen. Het verzamelen van statistieken over uw gegevens is een van de belangrijkste dingen die u doen om uw zoekopdrachten te optimaliseren. De SQL on-demand queryoptimizer is een op kosten gebaseerde optimizer. Het vergelijkt de kosten van verschillende queryplannen en kiest vervolgens het plan met de laagste kosten. In de meeste gevallen kiest het het plan dat het snelst wordt uitgevoerd. Als de optimizer bijvoorbeeld schat dat de datum waarop uw query filtert, één rij wordt weergegeven, wordt één plan gekozen. Als de geselecteerde datum 1 miljoen rijen wordt weergegeven, wordt een ander plan weergegeven.

### <a name="automatic-creation-of-statistics"></a>Automatische creatie van statistieken

SQL on-demand analyseert binnenkomende gebruikersquery's voor ontbrekende statistieken. Als statistieken ontbreken, maakt de queryoptimizer statistieken over afzonderlijke kolommen in het querypredicaat of de join-voorwaarde om de schataldaliteitsschattingen voor het queryplan te verbeteren.

De select-instructie activeert het automatisch maken van statistieken.

> [!NOTE]
> Het automatisch maken van statistieken is ingeschakeld voor parketbestanden. Voor CSV-bestanden moet u handmatig statistieken maken totdat automatisch CSV-bestandenstatistieken worden ondersteund.

Het automatisch maken van statistieken gebeurt synchroon, zodat u mogelijk enigszins gedegradeerde queryprestaties krijgt als uw kolommen statistieken missen. De tijd om statistieken voor één kolom te maken, is afhankelijk van de grootte van de beoogde bestanden.

### <a name="manual-creation-of-statistics"></a>Handmatige creatie van statistieken

Met SQL on-demand u handmatig statistieken maken. Voor CSV-bestanden moet u handmatig statistieken maken omdat het automatisch maken van statistieken niet is ingeschakeld voor CSV-bestanden. Zie de onderstaande voorbeelden voor instructies over het handmatig maken van statistieken.

### <a name="updating-statistics"></a>Statistieken bijwerken

Wijzigingen in gegevens in bestanden, verwijderen en toevoegen van bestanden leiden tot wijzigingen in de gegevensdistributie en maken statistieken verouderd. In dat geval moeten de statistieken worden bijgewerkt.

SQL on-demand maakt automatisch statistieken opnieuw als gegevens aanzienlijk worden gewijzigd. Elke keer dat statistieken automatisch worden gemaakt, wordt ook de huidige status van de gegevensset opgeslagen: bestandspaden, groottes, laatste wijzigingsdatums.

Wanneer statistieken verouderd zijn, zullen er nieuwe worden gemaakt. Het algoritme gaat door de gegevens en vergelijkt deze met de huidige status van de gegevensset. Als de grootte van de wijzigingen groter is dan de specifieke drempelwaarde, worden oude statistieken verwijderd en opnieuw gemaakt via de nieuwe gegevensset.

Handmatige statistieken worden nooit muf verklaard.

> [!NOTE]
> Automatische recreatie van statistieken is ingeschakeld voor parketbestanden. Voor CSV-bestanden moet u handmatig statistieken laten vallen en maken totdat automatisch CSV-bestandenstatistieken worden ondersteund. Bekijk de onderstaande voorbeelden over hoe u vallen en statistieken maken.

Een van de eerste vragen die u moet stellen wanneer u een query probleemoplost, **is: "Zijn de statistieken up-to-date?"**

Wanneer het aantal rijen aanzienlijk is gewijzigd of er een wezenlijke wijziging is in de verdeling van waarden voor een *kolom,* is het tijd om statistieken bij te werken.

> [!NOTE]
> Als er een wezenlijke wijziging is in de verdeling van waarden voor een kolom, moet u statistieken bijwerken, ongeacht de laatste keer dat ze zijn bijgewerkt.

### <a name="implementing-statistics-management"></a>Uitvoering van het beheer van de statistiek

U uw gegevenspijplijn uitbreiden om ervoor te zorgen dat statistieken worden bijgewerkt wanneer gegevens aanzienlijk worden gewijzigd door toevoeging, verwijdering of wijziging van bestanden.

De volgende leidende beginselen zijn voorzien voor het bijwerken van uw statistieken:

- Controleer of de gegevensset ten minste één statistisch object heeft bijgewerkt. Deze updategrootte (aantal rij's en pagina's) als onderdeel van de statistische update.
- Focus op kolommen die deelnemen aan JOIN, GROUP BY, ORDER BY en DISTINCT-clausules.
- Werk kolommen met 'opgaande sleutel' zoals transactiedatums vaker bij omdat deze waarden niet worden opgenomen in het histogram met statistieken.
- Werk statische distributiekolommen minder vaak bij.

Zie [Cardinality Estimation](/sql/relational-databases/performance/cardinality-estimation-sql-server)voor meer informatie.

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Voorbeelden: Statistieken maken voor kolom in OPENROWSET-pad

In de volgende voorbeelden ziet u hoe u verschillende opties gebruiken voor het maken van statistieken. Welke opties u voor elke kolom gebruikt, zijn afhankelijk van de kenmerken van uw gegevens en hoe de kolom in query's wordt gebruikt.

> [!NOTE]
> U alleen op dit moment statistieken met één kolom maken.

De volgende opgeslagen procedure wordt gebruikt om statistieken te maken:

```sql
sys.sp_create_file_statistics [ @stmt = ] N'statement_text'
```

Argumenten: @stmt [ = ] N'statement_text' - Hiermee geeft u een transact-SQL-instructie op waarmee kolomwaarden worden weergegeven die voor statistieken moeten worden gebruikt. U TABLESAMPLE gebruiken om voorbeelden van gegevens op te geven die moeten worden gebruikt. Als TABLESAMPLE niet is opgegeven, wordt FULLSCAN gebruikt.

```sql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> CSV sampling werkt op dit moment niet, alleen FULLSCAN wordt ondersteund voor CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Statistieken met één kolom maken door elke rij te onderzoeken

Als u statistieken over een kolom wilt maken, geeft u een query op die de kolom retourneert waarvoor u statistieken nodig hebt.

Als u niet anders opgeeft, gebruikt SQL on-demand standaard 100% van de gegevens in de gegevensset wanneer er statistieken worden gemaakt.

Bijvoorbeeld statistieken maken met standaardopties (FULLSCAN) voor een kolom van een jaar van de gegevensset op basis van het bestand population.csv:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Statistieken met één kolom maken door de steekproefgrootte op te geven

U de steekproefgrootte als een percentage opgeven:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Voorbeelden: Statistieken bijwerken

Als u statistieken wilt bijwerken, moet u statistieken laten vallen en maken. De volgende opgeslagen procedure wordt gebruikt om statistieken te laten vallen:

```sql
sys.sp_drop_file_statistics [ @stmt = ] N'statement_text'
```

Argumenten: @stmt [ = ] N'statement_text' - Hiermee geeft u dezelfde Transact-SQL-instructie op die wordt gebruikt bij het maken van de statistieken.

Als u de statistieken voor de jaarkolom in de gegevensset wilt bijwerken, die is gebaseerd op het bestand population.csv, moet u de statistieken laten vallen en statistieken maken:

```sql
EXEC sys.sp_drop_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Voorbeelden: Statistieken maken voor externe tabelkolom

In de volgende voorbeelden ziet u hoe u verschillende opties gebruiken voor het maken van statistieken. Welke opties u voor elke kolom gebruikt, zijn afhankelijk van de kenmerken van uw gegevens en hoe de kolom in query's wordt gebruikt.

> [!NOTE]
> U alleen op dit moment statistieken met één kolom maken.

Als u statistieken over een kolom wilt maken, geeft u een naam op voor het object statistieken en de naam van de kolom.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Argumenten: external_table Hiermee geeft u externe tabel op die statistieken moeten maken.

FullSCAN Compute-statistieken door alle rijen te scannen. FULLSCAN en SAMPLE 100 PROCENT hebben dezelfde resultaten. FULLSCAN kan niet worden gebruikt met de optie SAMPLE.

VOORBEELDnummer PERCENTEN Hiermee geeft u het geschatte percentage of het aantal rijen in de tabel of de geïndexeerde weergave op die de queryoptimizer moet gebruiken wanneer deze statistieken maakt. Aantal kan van 0 tot 100 zijn.

SAMPLE kan niet worden gebruikt met de optie FULLSCAN.

> [!NOTE]
> CSV sampling werkt op dit moment niet, alleen FULLSCAN wordt ondersteund voor CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Statistieken met één kolom maken door elke rij te onderzoeken

```sql
CREATE STATISTICS sState on census_external_table (STATENAME) WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Statistieken met één kolom maken door de steekproefgrootte op te geven

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState on census_external_table (STATENAME) WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Voorbeelden: Statistieken bijwerken

Als u statistieken wilt bijwerken, moet u statistieken laten vallen en maken. Drop statistieken eerst:

```sql
DROP STATISTICS census_external_table.sState
```

En maak statistieken:

```sql
CREATE STATISTICS sState on census_external_table (STATENAME) WITH FULLSCAN, NORECOMPUTE
```

## <a name="next-steps"></a>Volgende stappen

Zie Aanbevolen procedures voor [SQL-pool voor](best-practices-sql-pool.md#maintain-statistics)verdere verbeteringen in queryprestaties.
