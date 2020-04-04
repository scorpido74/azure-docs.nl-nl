---
title: Transacties optimaliseren
description: Leer hoe u de prestaties van uw transactionele code in Synapse SQL optimaliseren en tegelijkertijd het risico voor lange terugdraaiingen minimaliseren.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 0139c581e6660622f1ab6db9f407725816377a6d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633566"
---
# <a name="optimizing-transactions-in-synapse-sql"></a>Transacties optimaliseren in Synapse SQL

Leer hoe u de prestaties van uw transactionele code in Synapse SQL optimaliseren en tegelijkertijd het risico voor lange terugdraaiingen minimaliseren.

## <a name="transactions-and-logging"></a>Transacties en logboekregistratie

Transacties zijn een belangrijk onderdeel van een relationele database engine. Transacties worden gebruikt tijdens het wijzigen van gegevens. Deze transacties kunnen expliciet of impliciet zijn. Afzonderlijke overzichten voor insert, update en delete zijn allemaal voorbeelden van impliciete transacties. Expliciete transacties gebruiken BEGIN TRAN, COMMIT TRAN of ROLLBACK TRAN. Expliciete transacties worden meestal gebruikt wanneer meerdere wijzigingsoverzichten in één atoomeenheid moeten worden gekoppeld.

Wijzigingen in de database worden bijgehouden met behulp van transactielogboeken. Elke distributie heeft zijn eigen transactielogboek. Transactielogboekschrijft zijn automatisch. Er is geen configuratie vereist. Echter, terwijl dit proces garandeert het schrijven het introduceert een overhead in het systeem. U deze impact minimaliseren door transactioneel efficiënte code te schrijven. Transactioneel efficiënte code valt grofweg in twee categorieën.

* Gebruik waar mogelijk minimale logging-constructies
* Gegevens verwerken met scoped batches om enkelvoudige langlopende transacties te voorkomen
* Een verdelingsschakelpatroon aannemen voor grote wijzigingen in een bepaalde partitie

## <a name="minimal-vs-full-logging"></a>Minimale versus volledige logging

In tegenstelling tot volledig geregistreerde bewerkingen, die het transactielogboek gebruiken om elke rijwijziging bij te houden, houden minimaal geregistreerde bewerkingen alleen de omvangtoewijzingen en metagegevenswijzigingen bij. Daarom omvat minimale logboekregistratie alleen de informatie die nodig is om de transactie terug te draaien na een fout, of voor een expliciet verzoek (ROLLBACK TRAN). Omdat er veel minder informatie wordt bijgehouden in het transactielogboek, presteert een minimaal geregistreerde bewerking beter dan een volledig geregistreerde bewerking van vergelijkbare grootte. Bovendien, omdat minder schrijft gaan de transactie log, een veel kleinere hoeveelheid log gegevens wordt gegenereerd en dus is meer I / O efficiënt.

De veiligheidslimieten voor transacties zijn alleen van toepassing op volledig geregistreerde bewerkingen.

> [!NOTE]
> Minimaal geregistreerde bewerkingen kunnen deelnemen aan expliciete transacties. Aangezien alle wijzigingen in toewijzingsstructuren worden bijgehouden, is het mogelijk om minimaal geregistreerde bewerkingen terug te draaien.

## <a name="minimally-logged-operations"></a>Minimaal geregistreerde bewerkingen

De volgende bewerkingen kunnen minimaal worden geregistreerd:

* TABEL MAKEN ALS SELECTEREN[(CTAS](sql-data-warehouse-develop-ctas.md))
* Invoegen.. Selecteer
* CREATE INDEX
* INDEX WIJZIGEN OPNIEUW OPBOUWEN
* DROP INDEX
* Tabel Afkappen
* KEUZELIJST LATEN VALLEN
* TABELSCHAKELAARPARTITIE WIJZIGEN

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Interne gegevensverplaatsingsbewerkingen (zoals BROADCAST en SHUFFLE) worden niet beïnvloed door de transactieveiligheidslimiet.

## <a name="minimal-logging-with-bulk-load"></a>Minimale logboekregistratie met bulkbelasting

CTAS en INSERT... SELECT zijn beide bulkladingsbewerkingen. Beide worden echter beïnvloed door de definitie van de doeltabel en zijn afhankelijk van het belastingscenario. In de volgende tabel wordt uitgelegd wanneer bulkbewerkingen volledig of minimaal worden geregistreerd:  

| Primaire index | Laadscenario | Logboekmodus |
| --- | --- | --- |
| Heap |Alle |**Minimaal** |
| Geclusterde index |Lege doeltabel |**Minimaal** |
| Geclusterde index |Geladen rijen overlappen elkaar niet met bestaande pagina's in doel |**Minimaal** |
| Geclusterde index |Geladen rijen overlappen met bestaande pagina's in doel |Volledig |
| Geclusterde Kolomarchiefindex |Batchgrootte >= 102.400 per verdeling uitgelijnde distributie |**Minimaal** |
| Geclusterde Kolomarchiefindex |Batchgrootte < 102.400 per partitie uitgelijnde distributie |Volledig |

Het is vermeldenswaard dat alle schrijft om secundaire of niet-geclusterde indexen bij te werken altijd volledig worden geregistreerd bewerkingen.

> [!IMPORTANT]
> Een Synapse SQL-pooldatabase heeft 60 distributies. Ervan uitgaande dat alle rijen gelijkmatig zijn verdeeld en in één partitie worden geland, moet uw batch daarom 6.144.000 rijen of groter bevatten om minimaal te worden geregistreerd bij het schrijven naar een clusteropslagindex. Als de tabel is verdeeld en de rijen worden ingevoegd sewijdverdelingsgrenzen, dan moet u 6.144.000 rijen per partitiegrens ervan uitgaande dat zelfs gegevens distributie. Elke partitie in elke verdeling moet onafhankelijk van elkaar de drempel van 102.400 rij overschrijden om de invoeging minimaal in de distributie te laten inloggen.

Het laden van gegevens in een niet-lege tabel met een geclusterde index kan vaak een mengsel van volledig gelogde en minimaal gelogde rijen bevatten. Een geclusterde index is een gebalanceerde boom (b-tree) van pagina's. Als de pagina die wordt geschreven al rijen van een andere transactie bevat, worden deze schrijfbewerkingen volledig geregistreerd. Als de pagina echter leeg is, wordt het schrijven naar die pagina minimaal geregistreerd.

## <a name="optimizing-deletes"></a>Verwijderen optimaliseren

DELETE is een volledig geregistreerde bewerking.  Als u een grote hoeveelheid gegevens in een tabel of partitie moet `SELECT` verwijderen, is het vaak logischer voor de gegevens die u wilt bewaren, die kunnen worden uitgevoerd als een minimaal geregistreerde bewerking.  Als u de gegevens wilt selecteren, maakt u een nieuwe tabel met [CTAS](sql-data-warehouse-develop-ctas.md).  Gebruik, nadat u bent gemaakt, [RENAME](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om uw oude tabel te ruilen met de nieuw gemaakte tabel.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Updates optimaliseren

UPDATE is een volledig geregistreerde bewerking.  Als u een groot aantal rijen in een tabel of partitie moet bijwerken, kan het vaak veel efficiënter zijn om een minimaal geregistreerde bewerking zoals [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) te gebruiken om dit te doen.

In het onderstaande voorbeeld is een volledige tabelupdate omgezet naar een CTAS, zodat minimale logging mogelijk is.

In dit geval voegen we met terugwerkende kracht een kortingsbedrag toe aan de verkoop in de tabel:

```sql
--Step 01. Create a new table containing the "Update".
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Het opnieuw maken van grote tabellen kan profiteren van het gebruik van Functies voor synapssql-poolworkloadbeheer. Zie [Resourceklassen voor werkbelastingbeheer voor](resource-classes-for-workload-management.md)meer informatie .

## <a name="optimizing-with-partition-switching"></a>Optimaliseren met partitieschakelen

Als geconfronteerd met grootschalige wijzigingen in een [tabel partitie](sql-data-warehouse-tables-partition.md), dan is een partitie schakelen patroon zinvol. Als de gegevenswijziging aanzienlijk is en meerdere partities overspant, bereikt herhalen over de partities hetzelfde resultaat.

De stappen om een partitieswitch uit te voeren zijn als volgt:

1. Een lege partitie maken
2. Voer de 'update' uit als CTAS
3. De bestaande gegevens naar de uittabel overschakelen
4. Schakelen in de nieuwe gegevens
5. De gegevens opschonen

Als u echter wilt helpen bij het identificeren van de partities die u wilt overschakelen, maakt u de volgende helperprocedure.  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id]
                                                AND i.[index_id]        = p.[index_id]
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id]
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Deze procedure maximaliseert het hergebruik van code en houdt het voorbeeld van het schakelen van de partitie compacter.

De volgende code toont de eerder genoemde stappen om een volledige partitieschakelroutine te bereiken.

```sql
--Create a partitioned aligned empty table to switch out the data
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Logboekregistratie met kleine batches minimaliseren

Voor grote gegevenswijzigingsbewerkingen kan het zinvol zijn om de bewerking in brokken of batches te verdelen om de eenheid van het werk te plaatsen.

Een volgende code is een werkend voorbeeld. De batchgrootte is ingesteld op een triviaal getal om de techniek te markeren. In werkelijkheid zou de batchgrootte aanzienlijk groter zijn.

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Richtlijnen onderbreken en schalen

Met Synapse SQL u uw SQL-pool op aanvraag [onderbreken, hervatten en schalen.](sql-data-warehouse-manage-compute-overview.md) Wanneer u uw SQL-pool pauzeert of schaalt, is het belangrijk om te begrijpen dat transacties aan boord onmiddellijk worden beëindigd; waardoor openstaande transacties worden teruggedraaid. Als uw werklast een langdurige en onvolledige gegevenswijziging heeft doorgevoerd voorafgaand aan de pauze- of schaalbewerking, moet dit werk ongedaan worden gemaakt. Deze ongedaan maken kan van invloed zijn op de tijd die nodig is om uw SQL-pool te pauzeren of te schalen.

> [!IMPORTANT]
> Beide `UPDATE` `DELETE` en zijn volledig gelogd operaties en dus deze ongedaan maken / opnieuw operaties kan aanzienlijk langer duren dan gelijkwaardige minimaal gelogde bewerkingen.

Het beste scenario is om in flight data modificatie transacties te laten voltooien voorafgaand aan het pauzeren of schalen sql pool. Dit scenario is echter niet altijd praktisch. Als u het risico van een lange terugdraaiing wilt beperken, moet u een van de volgende opties overwegen:

* Langlopende bewerkingen herschrijven met [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* Breek de bewerking in stukken; werken op een subset van de rijen

## <a name="next-steps"></a>Volgende stappen

Zie [Transacties in Synapse SQL](sql-data-warehouse-develop-transactions.md) voor meer informatie over isolatieniveaus en transactionele limieten.  Zie [Best Practices](sql-data-warehouse-best-practices.md)van SQL Data Warehouse voor een overzicht van andere aanbevolen procedures.
