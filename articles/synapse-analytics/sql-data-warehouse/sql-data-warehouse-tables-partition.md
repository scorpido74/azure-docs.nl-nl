---
title: Partitietabellen
description: Aanbevelingen en voorbeelden voor het gebruik van tabelpartities in de Synapse SQL-groep
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4e19c20036d74752b75a668d6a37c46ef1b008e6
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583193"
---
# <a name="partitioning-tables-in-synapse-sql-pool"></a>Partitietabellen in Synapse SQL-groep

Aanbevelingen en voorbeelden voor het gebruik van tabelpartities in de Synapse SQL-pool.

## <a name="what-are-table-partitions"></a>Wat zijn tabelpartities?

Met tabelpartities u uw gegevens verdelen in kleinere groepen gegevens. In de meeste gevallen worden tabelpartities gemaakt op een datumkolom. Partitionering wordt ondersteund op alle Synapse SQL-pooltabeltypen; inclusief geclusterde kolomarchief, geclusterde index en heap. Partitionering wordt ook ondersteund op alle distributietypen, inclusief hash of round robin distributed.  

Partitionering kan het onderhoud van gegevens en queryprestaties ten goede komen. Of het beide of slechts één ten goede komt, is afhankelijk van hoe gegevens worden geladen en of dezelfde kolom voor beide doeleinden kan worden gebruikt, omdat partitioneren alleen op één kolom kan worden uitgevoerd.

### <a name="benefits-to-loads"></a>Voordelen voor ladingen

Het belangrijkste voordeel van partitioneren in Synapse SQL-pool is het verbeteren van de efficiëntie en prestaties van laadgegevens door het gebruik van partitieverwijdering, switching en samenvoegen. In de meeste gevallen worden gegevens verdeeld over een datumkolom die nauw is gekoppeld aan de volgorde waarin de gegevens in de database worden geladen. Een van de grootste voordelen van het gebruik van partities om gegevens te behouden het vermijden van transactie logging. Hoewel het eenvoudig invoegen, bijwerken of verwijderen van gegevens de meest eenvoudige aanpak kan zijn, met een beetje nadenken en moeite, kan het gebruik van partitionering tijdens uw laadproces de prestaties aanzienlijk verbeteren.

Het schakelen van partities kan worden gebruikt om een gedeelte van een tabel snel te verwijderen of te vervangen.  Een verkoopfeitentabel kan bijvoorbeeld alleen gegevens van de afgelopen 36 maanden bevatten. Aan het einde van elke maand wordt de oudste maand met verkoopgegevens uit de tabel verwijderd.  Deze gegevens kunnen worden verwijderd met behulp van een verwijderinstructie om de gegevens voor de oudste maand te verwijderen. Het verwijderen van een grote hoeveelheid gegevens rij voor rij met een verwijderinstructie kan echter te veel tijd in beslag nemen, evenals het risico van grote transacties die lang duren om terug te draaien als er iets misgaat. Een meer optimale aanpak is het laten vallen van de oudste partitie van gegevens. Waar het verwijderen van de afzonderlijke rijen uren kan duren, kan het verwijderen van een hele partitie seconden duren.

### <a name="benefits-to-queries"></a>Voordelen voor query's

Partitionering kan ook worden gebruikt om de queryprestaties te verbeteren. Een query die een filter toepast op partitiegegevens, kan de scan beperken tot alleen de in aanmerking komende partities. Deze filtermethode kan een volledige tabelscan voorkomen en alleen een kleinere subset van gegevens scannen. Met de introductie van geclusterde columnstore-indexen zijn de prestatievoordelen van predicaateliminatie minder gunstig, maar in sommige gevallen kan er een voordeel zijn voor query's. Als de verkoopfeitentabel bijvoorbeeld is verdeeld in 36 maanden met behulp van het veld verkoopdatum, kunnen query's die filteren op de verkoopdatum het zoeken overslaan in partities die niet overeenkomen met het filter.

## <a name="sizing-partitions"></a>Grootte van partities

Hoewel partitionering kan worden gebruikt om de prestaties van sommige scenario's te verbeteren, kan het maken van een tabel met **te veel** partities onder bepaalde omstandigheden de prestaties schaden.  Deze problemen gelden met name voor geclusterde kolomarchieftabellen. Om partities nuttig te laten zijn, is het belangrijk om te begrijpen wanneer partities moeten worden gebruikt en het aantal partities dat moet worden gemaakt. Er is geen harde snelle regel over hoeveel partities zijn te veel, het hangt af van uw gegevens en hoeveel partities u tegelijkertijd laadt. Een succesvol partitioneringsschema heeft meestal tientallen tot honderden partities, niet duizenden.

Bij het maken van partities in **geclusterde kolomarchieftabellen** is het belangrijk om rekening te houden met het aantal rijen dat tot elke partitie behoort. Voor optimale compressie en prestaties van geclusterde kolomarchieftabellen is minimaal 1 miljoen rijen per distributie en partitie nodig. Voordat partities worden gemaakt, verdeelt Synapse SQL-pool elke tabel al in 60 gedistribueerde databases. Elke partitie toegevoegd aan een tabel is in aanvulling op de distributies gemaakt achter de schermen. Als de verkoopfeitentabel met dit voorbeeld 36 maandelijkse partities bevat en een Synapse SQL-groep 60-distributies heeft, moet de verkoopfeitentabel 60 miljoen rijen per maand bevatten, of 2,1 miljard rijen wanneer alle maanden worden ingevuld. Als een tabel minder bevat dan het aanbevolen minimumaantal rijen per partitie, u overwegen minder partities te gebruiken om het aantal rijen per partitie te verhogen. Zie voor meer informatie het artikel [Indexeren,](sql-data-warehouse-tables-index.md) waarin query's zijn bevat die de kwaliteit van clusterkolomarchiefindexen kunnen beoordelen.

## <a name="syntax-differences-from-sql-server"></a>Syntaxisverschillen ten opzichte van SQL Server

Synapse SQL-pool introduceert een manier om partities te definiëren die eenvoudiger is dan SQL Server. Partitiefuncties en -schema's worden niet gebruikt in de Synapse SQL-groep zoals ze zich in SQL Server bevinden. In plaats daarvan hoeft u alleen de partitiekolom en de grenspunten te identificeren. Hoewel de syntaxis van partitionering enigszins kan verschillen van SQL Server, zijn de basisconcepten hetzelfde. SQL Server en Synapse SQL-pool ondersteunen één partitiekolom per tabel, die kan worden verdeeld. Zie [Partitietabellen en -indexen](/sql/relational-databases/partitions/partitioned-tables-and-indexes)voor meer informatie over partitionering.

In het volgende voorbeeld wordt de instructie [TABEL MAKEN](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) gebruikt om de factinternetsales-tabel te partitioneren in de kolom OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Partitionering migreren vanuit SQL Server

Ga als lid van het emigreren van SQL Server-partitiedefinities eenvoudig naar de Synapse SQL-groep:

- Elimineer het SQL [Server-partitieschema](/sql/t-sql/statements/create-partition-scheme-transact-sql).
- Voeg de definitie van de [partitiefunctie](/sql/t-sql/statements/create-partition-function-transact-sql) toe aan de TABEL MAKEN.

Als u een partitietabel migreert vanuit een SQL Server-instantie, kan de volgende SQL u helpen om het aantal rijen te achterhalen dat in elke partitie zit. Houd er rekening mee dat als dezelfde partitioneringsgranulariteit wordt gebruikt op synapse SQL-pool, het aantal rijen per partitie met een factor 60 afneemt.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="partition-switching"></a>Partitieschakelen

Synapse SQL-pool ondersteunt partitiesplitsing, samenvoegen en schakelen. Elk van deze functies wordt uitgevoerd met de [verklaring ALTER TABLE.](/sql/t-sql/statements/alter-table-transact-sql)

Als u partities tussen twee tabellen wilt schakelen, moet u ervoor zorgen dat de partities worden uitgelijnd op hun respectieve grenzen en dat de tabeldefinities overeenkomen. Omdat controlebeperkingen niet beschikbaar zijn om het waardenbereik in een tabel af te dwingen, moet de brontabel dezelfde partitiegrenzen bevatten als de doeltabel. Als de partitiegrenzen dan niet hetzelfde zijn, mislukt de partitieswitch omdat de partitiemetagegevens niet worden gesynchroniseerd.

### <a name="how-to-split-a-partition-that-contains-data"></a>Een partitie splitsen die gegevens bevat

De meest efficiënte methode om een partitie te `CTAS` splitsen die al gegevens bevat, is het gebruik van een instructie. Als de partitietabel een geclusterd kolomarchief is, moet de tabelpartitie leeg zijn voordat deze kan worden gesplitst.

In het volgende voorbeeld wordt een tabel met een partitionerende kolomarchief maakt. Het voegt één rij in elke partitie in:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);
```

In de volgende query wordt `sys.partitions` het aantal rijs gevonden met behulp van de catalogusweergave:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Met de volgende gesplitste opdracht ontvangt u een foutbericht:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, Level 15, State 1, Line 44 SPLIT-clausule van de WIJZIGINGSVERDELING-instructie is mislukt omdat de partitie niet leeg is. Alleen lege partities kunnen worden opgesplitst wanneer er een kolomarchiefindex op de tabel staat. Overweeg de kolomarchiefindex uit te schakelen voordat u de wijzigingspartitie-instructie uitgeeft en vervolgens de kolomarchiefindex opnieuw optebouwen nadat ALTER PARTITION is voltooid.

U echter `CTAS` wel een nieuwe tabel maken om de gegevens vast te houden.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Als de scheidingsgrenzen zijn uitgelijnd, is een schakelaar toegestaan. Hierdoor blijft de brontabel met een lege partitie die u vervolgens splitsen.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Het enige dat overblijft is om de `CTAS`gegevens uit te lijnen op de nieuwe partitiegrenzen met behulp van , en vervolgens de gegevens terug te schakelen naar de hoofdtabel.

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Zodra u de verplaatsing van de gegevens hebt voltooid, is het een goed idee om de statistieken op de doeltabel te vernieuwen. Het bijwerken van statistieken zorgt ervoor dat de statistieken de nieuwe verdeling van de gegevens in hun respectieve partities nauwkeurig weergeven.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Nieuwe gegevens in partities laden die gegevens in één stap bevatten

Het laden van gegevens in partities met partitieschakelen is een handige manier om nieuwe gegevens in een tabel te plaatsen die niet zichtbaar is voor gebruikers van de switch in de nieuwe gegevens.  Het kan een uitdaging zijn op drukke systemen om te gaan met de vergrendeling stelling in verband met partitie schakelen.  Om de bestaande gegevens in een `ALTER TABLE` partitie te wissen, moest er vroeger een partitie worden uitgeschakeld.  Dan `ALTER TABLE` was een ander nodig om over te schakelen in de nieuwe gegevens.  In synapse SQL-pool wordt de `TRUNCATE_TARGET` `ALTER TABLE` optie ondersteund in de opdracht.  Met `TRUNCATE_TARGET` `ALTER TABLE` de opdracht overschrijft bestaande gegevens in de partitie met nieuwe gegevens.  Hieronder vindt u `CTAS` een voorbeeld dat wordt gebruikt om een nieuwe tabel met de bestaande gegevens te maken, nieuwe gegevens in te voegen en vervolgens alle gegevens terug te schakelen naar de doeltabel en de bestaande gegevens te overschrijven.

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>Bronbeheer voor tabelpartitionering

Om te voorkomen dat uw tabeldefinitie **roestin** uw broncontrolesysteem, u de volgende benadering overwegen:

1. De tabel maken als een partitietabel, maar zonder partitiewaarden

    ```sql
    CREATE TABLE [dbo].[FactInternetSales]
    (
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
    )
    WITH
    (   CLUSTERED COLUMNSTORE INDEX
    ,   DISTRIBUTION = HASH([ProductKey])
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. `SPLIT`de tabel als onderdeel van het implementatieproces:

    ```sql
     -- Create a table containing the partition boundaries

    CREATE TABLE #partitions
    WITH
    (
        LOCATION = USER_DB
    ,   DISTRIBUTION = HASH(ptn_no)
    )
    AS
    SELECT  ptn_no
    ,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
    FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
    ) a
    ;

     -- Iterate over the partition boundaries and split the table

    DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
    ,       @i INT = 1                                 --iterator for while loop
    ,       @q NVARCHAR(4000)                          --query
    ,       @p NVARCHAR(20)     = N''                  --partition_number
    ,       @s NVARCHAR(128)    = N'dbo'               --schema
    ,       @t NVARCHAR(128)    = N'FactInternetSales' --table
    ;

    WHILE @i <= @c
    BEGIN
        SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
        SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

        -- PRINT @q;
        EXECUTE sp_executesql @q;
        SET @i+=1;
    END

     -- Code clean-up

    DROP TABLE #partitions;
    ```

Met deze aanpak blijft de code in bronbesturingselement statisch en mogen de partitionerende grenswaarden dynamisch zijn; in de loop van de tijd met de database evolueren.

## <a name="next-steps"></a>Volgende stappen

Zie de artikelen over [tabeloverzicht](sql-data-warehouse-tables-overview.md)voor meer informatie over het ontwikkelen van tabellen.

