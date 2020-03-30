---
title: Indexeren van tabellen
description: Aanbevelingen en voorbeelden voor het indexeren van tabellen in SQL Analytics.
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
ms.openlocfilehash: ced965f94808bdc672f694bede5c239178891f97
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351289"
---
# <a name="indexing-tables-in-sql-analytics"></a>Indexeren van tabellen in SQL Analytics

Aanbevelingen en voorbeelden voor het indexeren van tabellen in SQL Analytics.

## <a name="index-types"></a>Indextypen

SQL Analytics biedt verschillende indexeringsopties, waaronder [geclusterde columnstore-indexen](/sql/relational-databases/indexes/columnstore-indexes-overview), [geclusterde indexen en niet-geclusterde indexen](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described), en een niet-indexoptie die ook wel [heap](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes)wordt genoemd .  

Zie de SQL [Analytics-documentatie (CREATE TABLE)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) om een tabel met een index te maken.

## <a name="clustered-columnstore-indexes"></a>Geclusterde kolomarchiefindexen

SQL Analytics maakt standaard een geclusterde columnstore-index wanneer er geen indexopties zijn opgegeven in een tabel. Geclusterde kolomarchieftabellen bieden zowel het hoogste niveau van gegevenscompressie als de beste algemene queryprestaties.  Geclusterde kolomarchieftabellen presteren over het algemeen beter dan geclusterde index- of heaptabellen en zijn meestal de beste keuze voor grote tabellen.  Om deze redenen is het geclusterde kolomarchief de beste plek om te beginnen wanneer u niet zeker weet hoe u uw tabel indexeren.  

Als u een geclusterde kolomarchieftabel wilt maken, geeft u gewoon CLUSTERED COLUMNSTORE INDEX op in de MET-component of laat u de WITH-component uit:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Er zijn een paar scenario's waarin geclusterde kolomarchief mogelijk geen goede optie is:

- Columnstore tabellen ondersteunen geen varchar(max), nvarchar(max) en varbinary(max). Overweeg heap of geclusterde index in plaats daarvan.
- Kolomarchieftabellen kunnen minder efficiënt zijn voor tijdelijke gegevens. Overweeg heap en misschien zelfs tijdelijke tafels.
- Kleine tafels met minder dan 60 miljoen rijen. Overweeg heap tabellen.

## <a name="heap-tables"></a>Heaptabellen

Wanneer u tijdelijk gegevens in SQL Analytics landt, u merken dat het gebruik van een heaptabel het algehele proces sneller maakt. Dit komt omdat ladingen op heaps sneller zijn dan het indexeren van tabellen en in sommige gevallen kan het volgende lezen vanuit de cache worden gedaan.  Als u gegevens alleen laadt om deze te faseren voordat u meer transformaties uitvoert, is het laden van de tabel naar heaptabel veel sneller dan het laden van de gegevens naar een geclusterde kolomarchieftabel. Bovendien laadt het laden van gegevens naar een [tijdelijke tabel](sql-data-warehouse-tables-temporary.md) sneller dan het laden van een tabel naar permanente opslag.  

Voor kleine opzoektabellen, minder dan 60 miljoen rijen, zijn heaptabellen vaak zinvol.  Clusterkolomarchieftabellen beginnen optimale compressie te bereiken zodra er meer dan 60 miljoen rijen zijn.

Als u een heaptabel wilt maken, geeft u HEAP op in de MET-component:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Geclusterde en niet-geclusterde indexen

Geclusterde indexen kunnen beter presteren dan geclusterde kolomarchieftabellen wanneer een enkele rij snel moet worden opgehaald. Voor query's waarbij een enkele of zeer weinig rij-opzoek met extreme snelheid moet worden uitgevoerd, u rekening houden met een clusterindex of niet-geclusterde secundaire index. Het nadeel van het gebruik van een geclusterde index is dat alleen query's die profiteren, alleen query's zijn die een zeer selectief filter op de geclusterde indexkolom gebruiken. Als u het filter op andere kolommen wilt verbeteren, kan een niet-geclusterde index aan andere kolommen worden toegevoegd. Elke index die aan een tabel wordt toegevoegd, voegt echter zowel ruimte als verwerkingstijd toe aan ladingen.

Als u een geclusterde indextabel wilt maken, geeft u de clusterindex op in de met-component:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Als u een niet-geclusterde index aan een tabel wilt toevoegen, gebruikt u de volgende syntaxis:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Geclusterde kolomarchiefindexen optimaliseren

Geclusterde kolomarchieftabellen zijn ingedeeld in gegevens in segmenten.  Het hebben van hoge segmentkwaliteit is essentieel voor het bereiken van optimale queryprestaties in een kolomarchieftabel.  De segmentkwaliteit kan worden gemeten aan de slag met het aantal rijen in een gecomprimeerde rijgroep.  De segmentkwaliteit is het meest optimaal wanneer er ten minste 100.000 rijen per gecomprimeerde rijgroep zijn en de prestaties winnen omdat het aantal rijen per rijgroep 1.048.576 rijen nadert, de meeste rijen die een rijgroep kan bevatten.

De onderstaande weergave kan worden gemaakt en gebruikt op uw systeem om de gemiddelde rijen per rijgroep te berekenen en suboptimale clusterkolomstoreindexen te identificeren.  De laatste kolom in deze weergave genereert een SQL-instructie die kan worden gebruikt om uw indexen opnieuw op te bouwen.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Nu u de weergave hebt gemaakt, voert u deze query uit om tabellen te identificeren met rijgroepen met minder dan 100.000 rijen. Natuurlijk u de drempel van 100K verhogen als u op zoek bent naar een meer optimale segmentkwaliteit.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Zodra u de query hebt uitgevoerd, u beginnen met het bekijken van de gegevens en het analyseren van uw resultaten. In deze tabel wordt uitgelegd waar u naar moet zoeken in de analyse van uw rijgroepgroep.

| Kolom | Hoe deze gegevens te gebruiken |
| --- | --- |
| [table_partition_count] |Als de tabel is verdeeld, u verwachten dat hogere groepen van de rij openen worden geteld. Elke partitie in de verdeling kan in theorie een open rijgroep hebben die eraan is gekoppeld. Factor dit in uw analyse. Een kleine tabel die is verdeeld kan worden geoptimaliseerd door het verwijderen van de partitionering helemaal als dit zou verbeteren compressie. |
| [row_count_total] |Totaal aantal rij's voor de tabel. U deze waarde bijvoorbeeld gebruiken om het percentage rijen in de gecomprimeerde status te berekenen. |
| [row_count_per_distribution_MAX] |Als alle rijen gelijkmatig zijn verdeeld, is deze waarde het doelaantal rijen per distributie. Vergelijk deze waarde met de compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Totaal aantal rijen in kolomarchiefindeling voor de tabel. |
| [COMPRESSED_rowgroup_rows_AVG] |Als het gemiddelde aantal rijen aanzienlijk lager is dan het maximumaantal rijen voor een rijgroep, u overwegen CTAS of ALTER INDEX REBUILD te gebruiken om de gegevens opnieuw te comprimeren |
| [COMPRESSED_rowgroup_count] |Aantal rijgroepen in kolomarchiefindeling. Als dit getal zeer hoog is ten opzichte van de tabel, is dit een indicator dat de dichtheid van de kolomarchief laag is. |
| [COMPRESSED_rowgroup_rows_DELETED] |Rijen worden logischerwijs verwijderd in de indeling columnstore. Als het getal hoog is ten opzichte van de tabelgrootte, u overwegen de partitie opnieuw te maken of de index opnieuw op te bouwen, omdat deze deze fysiek worden verwijderd. |
| [COMPRESSED_rowgroup_rows_MIN] |Gebruik dit in combinatie met de kolommen AVG en MAX om inzicht te krijgen in het waardenbereik voor de rijgroepen in uw kolomarchief. Een laag getal over de belastingsdrempel (102.400 per verdelingsverdeling) suggereert dat optimalisaties beschikbaar zijn in de gegevensbelasting |
| [COMPRESSED_rowgroup_rows_MAX] |Zoals hierboven |
| [OPEN_rowgroup_count] |Open rijgroepen zijn normaal. Men zou redelijkerwijs verwachten dat een OPEN rij groep per tabel distributie (60). Overmatige aantallen suggereren dat gegevens over partities worden geladen. Controleer de partitioneringsstrategie om er zeker van te zijn dat deze goed is |
| [OPEN_rowgroup_rows] |Elke rijgroep kan maximaal 1.048.576 rijen hebben. Deze waarde gebruiken om te zien hoe volledig de open rijgroepen momenteel zijn |
| [OPEN_rowgroup_rows_MIN] |Open groepen geven aan dat gegevens ofwel worden doorgesijpeld geladen in de tabel of dat de vorige belasting gemorst over de resterende rijen in deze rij groep. Gebruik de kolommen MIN, MAX en AVG om te zien hoeveel gegevens er in open rijgroepen zitten. Voor kleine tabellen kan het 100% van alle gegevens! In dat geval alter index herbouwen om de gegevens te dwingen tot columnstore. |
| [OPEN_rowgroup_rows_MAX] |Zoals hierboven |
| [OPEN_rowgroup_rows_AVG] |Zoals hierboven |
| [CLOSED_rowgroup_rows] |Kijk naar de rijen met gesloten rijgroepen als een geestelijke gezondheid. |
| [CLOSED_rowgroup_count] |Het aantal groepen met gesloten rijen moet laag zijn als er überhaupt wordt gezien. Gesloten rijgroepen kunnen worden geconverteerd naar gecomprimeerde rijgroepen met behulp van de ALTER INDEX ... OPDRACHT REORGANISEREN. Dit is echter normaal gesproken niet nodig. Gesloten groepen worden automatisch geconverteerd naar rijgroepen in kolomopslag door het achtergrondproces "tuple mover". |
| [CLOSED_rowgroup_rows_MIN] |Gesloten rijgroepen moeten een zeer hoog vulpercentage hebben. Als het vulpercentage voor een groep met gesloten rijen laag is, is verdere analyse van het kolomarchief vereist. |
| [CLOSED_rowgroup_rows_MAX] |Zoals hierboven |
| [CLOSED_rowgroup_rows_AVG] |Zoals hierboven |
| [Rebuild_Index_SQL] |SQL om de kolomarchiefindex voor een tabel opnieuw op te bouwen |

## <a name="causes-of-poor-columnstore-index-quality"></a>Oorzaken van slechte kwaliteit voor columnstore-indexen

Als u tabellen met een slechte segmentkwaliteit hebt geïdentificeerd, wilt u de hoofdoorzaak identificeren.  Hieronder zijn enkele andere veel voorkomende oorzaken van slechte segment kwaliteit:

1. Geheugendruk bij de opbouw van de index
2. Hoog volume DML-activiteiten
3. Kleine of druppellaadbewerkingen
4. Te veel partities

Deze factoren kunnen ertoe leiden dat een index voor kolomopslag aanzienlijk minder heeft dan de optimale 1 miljoen rijen per rijgroep. Ze kunnen er ook voor zorgen dat rijen naar de deltarijgroep gaan in plaats van naar een gecomprimeerde rijgroep.

### <a name="memory-pressure-when-index-was-built"></a>Geheugendruk bij de opbouw van de index

Het aantal rijen per gecomprimeerde rijgroep is rechtstreeks gerelateerd aan de breedte van de rij en de hoeveelheid geheugen die beschikbaar is om de rijgroep te verwerken.  Wanneer rijen naar columnstore-tabellen worden geschreven onder geheugendruk, kan dit ten koste gaan van de kwaliteit van columnstore-segmenten.  Daarom is de beste praktijk is om de sessie die is het schrijven naar uw columnstore index tabellen toegang tot zo veel mogelijk geheugen.  Aangezien er een trade-off is tussen geheugen en gelijktijdigheid, is de richtlijnen voor de juiste geheugentoewijzing afhankelijk van de gegevens in elke rij van uw tabel, de SQL Analytics-eenheden die aan uw systeem zijn toegewezen en het aantal gelijktijdige sleuven dat u geven aan de sessie die gegevens naar uw tabel te schrijven.

### <a name="high-volume-of-dml-operations"></a>Hoog volume DML-activiteiten

Een groot aantal DML-bewerkingen die rijen bijwerken en verwijderen, kan inefficiëntie in het kolomarchief introduceren. Dit geldt vooral wanneer de meeste rijen in een rijgroep worden gewijzigd.

- Als u een rij uit een gecomprimeerde rijgroep verwijderde, wordt de rij alleen logisch als verwijderd markeert. De rij blijft in de gecomprimeerde rijgroep totdat de partitie of tabel opnieuw is opgebouwd.
- Als u een rij invoegt, wordt de rij toegevoegd aan een interne rowstore-tabel, een deltarijgroep. De ingevoegde rij wordt niet geconverteerd naar kolomarchief totdat de deltarijgroep vol is en is gemarkeerd als gesloten. Rijgroepen worden gesloten zodra ze de maximale capaciteit van 1.048.576 rijen hebben bereikt.
- Het bijwerken van een rij in de kolomarchiefindeling wordt verwerkt als een logische verwijdering en vervolgens als een invoeging. De ingevoegde rij kan worden opgeslagen in de deltawinkel.

Batched update- en invoegbewerkingen die de bulkdrempel van 102.400 rijen per verdelingsverdeling overschrijden, gaan rechtstreeks naar de kolomarchiefindeling. Echter, uitgaande van een gelijkmatige verdeling, zou je nodig hebt om meer dan 6,144 miljoen rijen te wijzigen in een enkele bewerking voor dit optreden. Als het aantal rijen voor een bepaalde partitie-uitgelijnde verdeling minder dan 102.400 is, gaan de rijen naar het deltaarchief en blijven ze daar totdat voldoende rijen zijn ingevoegd of gewijzigd om de rijgroep te sluiten of de index opnieuw is opgebouwd.

### <a name="small-or-trickle-load-operations"></a>Kleine of druppellaadbewerkingen

Kleine lasten die in SQL Analytics-databases stromen, worden ook wel trickle loads genoemd. Ze vertegenwoordigen meestal een bijna constante stroom van gegevens die door het systeem worden ingenomen. Echter, als deze stroom is in de buurt van continu het volume van de rijen is niet bijzonder groot. Vaker wel dan niet de gegevens is aanzienlijk onder de drempel die nodig is voor een directe belasting naar columnstore formaat.

In deze situaties is het vaak beter om de gegevens eerst in Azure blob-opslag te landen en deze te laten accumuleren voordat deze wordt geladen. Deze techniek is vaak bekend als *micro-batching*.

### <a name="too-many-partitions"></a>Te veel partities

Een ander ding om te overwegen is de impact van partitionering op uw geclusterde kolomstore tabellen.  Voordat sql analytics wordt partitie, worden uw gegevens al opgesplitst in 60 databases.  Het verdelen verdeelt uw gegevens verder.  Als u uw gegevens partitioneert, moet u er rekening mee houden dat **elke** partitie ten minste 1 miljoen rijen nodig heeft om te profiteren van een geclusterde kolomarchiefindex.  Als u uw tabel in 100 partities verdeelt, heeft uw tabel ten minste 6 miljard rijen nodig om te profiteren van een geclusterde kolomarchiefindex (60 distributies *100 partities* 1 miljoen rijen). Als uw tabel met 100 partities geen 6 miljard rijen heeft, vermindert u het aantal partities of overweegt u in plaats daarvan een heaptabel te gebruiken.

Zodra uw tabellen zijn geladen met bepaalde gegevens, volgt u de onderstaande stappen om tabellen te identificeren en opnieuw op te bouwen met suboptimale geclusterde kolomarchiefindexen.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Indexen opnieuw opbouwen om de kwaliteit van het segment te verbeteren

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Stap 1: Gebruiker identificeren of maken die de juiste resourceklasse gebruikt

Een snelle manier om de segmentkwaliteit onmiddellijk te verbeteren, is door de index opnieuw op te bouwen.  De SQL die door de bovenstaande weergave wordt geretourneerd, retourneert een ALTER INDEX RESOCIALering-instructie die kan worden gebruikt om uw indexen opnieuw op te bouwen. Wanneer u uw indexen opnieuw herstelt, moet u ervoor zorgen dat u voldoende geheugen toewijst aan de sessie waarin de index wordt hersteld.  Als u dit wilt doen, verhoogt u de resourceklasse van een gebruiker die machtigingen heeft om de index in deze tabel opnieuw op te bouwen tot het aanbevolen minimum.

Hieronder vindt u een voorbeeld van hoe u meer geheugen aan een gebruiker toewijst door hun resourceklasse te verhogen. Zie [Resourceklassen voor werkbelastingbeheer](resource-classes-for-workload-management.md)als u wilt werken met resourceklassen.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Stap 2: Geclusterde kolomarchiefindexen opnieuw opbouwen met een hogere gebruiker van de resourceklasse

Meld u aan als gebruiker vanaf stap 1 (bijvoorbeeld LoadUser), die nu een hogere resourceklasse gebruikt, en voer de ALTER INDEX-instructies uit. Zorg ervoor dat deze gebruiker ALTER-toestemming heeft voor de tabellen waarin de index wordt herbouwd. In deze voorbeelden ziet u hoe u de hele kolomarchiefindex herbouwen of hoe u één partitie herbouwen. Op grote tafels is het praktischer om indexen één partitie tegelijk weer op te bouwen.

Als alternatief u in plaats van de index opnieuw op te bouwen, de tabel kopiëren naar een nieuwe tabel [met CTAS](sql-data-warehouse-develop-ctas.md). Welke kant is het beste? Voor grote hoeveelheden gegevens is CTAS meestal sneller dan [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql). Voor kleinere hoeveelheden gegevens is ALTER INDEX gemakkelijker te gebruiken en hoeft u de tabel niet uit te wisselen.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Het opnieuw opbouwen van een index in SQL Analytics is een offlinebewerking.  Zie de sectie REBOUWEN VAN DE INDEXEN WIJZIGEN in [Columnstore Indexen Defragmentatie](/sql/relational-databases/indexes/columnstore-indexes-defragmentation)en [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql)voor meer informatie over het opnieuw opbouwen van indexen .

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Stap 3: De kwaliteit van het geclusterde kolomarchief is verbeterd

Voer de query opnieuw uit waarin de tabel met een slechte segmentkwaliteit is geïdentificeerd en de kwaliteit van het segment is verbeterd.  Als de segmentkwaliteit niet is verbeterd, kan het zijn dat de rijen in uw tabel extra breed zijn.  Overweeg een hogere resourceklasse of DWU te gebruiken bij het opnieuw opbouwen van uw indexen.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Indexen opnieuw opbouwen met CTAS en partitiewisseling

In dit voorbeeld wordt de [CTAS-instructie (CREATE TABLE AS SELECT)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) en partitieoverschakelen gebruikt om een tabelpartitie opnieuw op te bouwen.

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
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
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

Zie [Partities gebruiken in SQL Analytics](sql-data-warehouse-tables-partition.md)voor meer informatie over het opnieuw maken van partities met CTAS.

## <a name="next-steps"></a>Volgende stappen

Zie Tabellen ontwikkelen voor meer informatie over het ontwikkelen [van tabellen.](sql-data-warehouse-tables-overview.md)
