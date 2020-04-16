---
title: Prestaties van de index van het columnstore verbeteren
description: Verminder de geheugenvereisten of verhoog het beschikbare geheugen om het aantal rijen te maximaliseren dat een kolomarchiefindex in elke rijgroep comprimeert.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: f1f3667c088c5f7300317ea02ca19a72e4e62905
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431031"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>De kwaliteit van rijgroepen maximaliseren voor kolomarchief

De kwaliteit van rijgroepen wordt bepaald door het aantal rijen in een rijgroep. Als u het beschikbare geheugen verhoogt, kan het aantal rijen dat een kolomarchiefindex samencomtomeert in elke rijgroep maximaliseren.  Gebruik deze methoden om compressiesnelheden en queryprestaties voor kolomarchiefindexen te verbeteren.

## <a name="why-the-rowgroup-size-matters"></a>Waarom de grootte van de rijgroep belangrijk is

Aangezien een kolomarchiefindex een tabel scant door kolomsegmenten van afzonderlijke rijgroepen te scannen, verbetert het maximaliseren van het aantal rijen in elke rijgroep de queryprestaties. Wanneer rijgroepen een groot aantal rijen hebben, verbetert de gegevenscompressie, wat betekent dat er minder gegevens van de schijf kunnen worden gelezen.

Zie [KolomarchiefIndexengids](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)voor meer informatie over rijgroepen.

## <a name="target-size-for-rowgroups"></a>Doelgrootte voor rijgroepen

Voor de beste queryprestaties is het doel om het aantal rijen per rijgroep in een kolomarchiefindex te maximaliseren. Een rijgroep kan maximaal 1.048.576 rijen hebben. Het is goed om niet het maximum aantal rijen per rijgroep te hebben. Columnstore-indexen leveren goede prestaties wanneer rijgroepen ten minste 100.000 rijen hebben.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Rijgroepen kunnen worden bijgesneden tijdens compressie

Tijdens een bulkbelasting of kolomarchiefindex wordt opnieuw opgebouwd, is er soms niet genoeg geheugen beschikbaar om alle rijen die voor elke rijgroep zijn aangewezen, te comprimeren. Wanneer er geheugendruk is, worden de indexen van kolomarchief de grootte van de rijgroep bijsnijden, zodat compressie in het kolomarchief kan slagen.

Wanneer er onvoldoende geheugen is om ten minste 10.000 rijen in elke rijgroep te comprimeren, wordt er een fout gegenereerd.

Zie [Bulkbelasting in een geclusterde kolomarchiefindex](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#Bulk )voor meer informatie over bulkladen.

## <a name="how-to-monitor-rowgroup-quality"></a>De kwaliteit van rijgroepen controleren

De DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats[(sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) bevat de weergavedefinitie die overeenkomt met SQL DB) die nuttige informatie blootlegt, zoals het aantal rijen in rijgroepen en de reden voor het bijsnijden als er werd bijgeknipt. U de volgende weergave maken als een handige manier om deze DMV op te vragen om informatie te krijgen over het bijsnijden van rijgroepen.

```sql
create view dbo.vCS_rg_physical_stats
as
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]
)
select *
from cte;
```

De trim_reason_desc geeft aan of de rijgroep is bijgesneden (trim_reason_desc = NO_TRIM impliceert dat er geen bijsnijd- en rijgroep van optimale kwaliteit is). De volgende bijsnijdredenen geven aan dat de rijgroep voortijdig moet worden bijgeknipt:

- BULKLOAD: Deze trimreden wordt gebruikt wanneer de binnenkomende batch rijen voor de lading minder dan 1 miljoen rijen had. De engine maakt gecomprimeerde rijgroepen als er meer dan 100.000 rijen worden ingevoegd (in tegenstelling tot het invoegen in het deltaarchief), maar stelt de trimreden in op BULKLOAD. In dit scenario u overwegen de batchbelasting te verhogen om meer rijen op te nemen. Evalueer ook uw partitieschema opnieuw om ervoor te zorgen dat het niet te gedetailleerd is, omdat rijgroepen geen partitiegrenzen kunnen overschrijden.
- MEMORY_LIMITATION: Om rijgroepen met 1 miljoen rijen te maken, is een bepaalde hoeveelheid werkgeheugen vereist door de engine. Wanneer het beschikbare geheugen van de laadsessie lager is dan het vereiste werkgeheugen, worden rijgroepen voortijdig bijgesneden. In de volgende secties wordt uitgelegd hoe u het vereiste geheugen schatten en meer geheugen toewijzen.
- DICTIONARY_SIZE: Deze aanrijreden geeft aan dat het bijsnijden van rijgroepen is opgetreden omdat er ten minste één tekenreekskolom met brede en/of hoge kardinaliteitstekenreeksen was. De grootte van het woordenboek is beperkt tot 16 MB in het geheugen en zodra deze limiet is bereikt, wordt de rijgroep gecomprimeerd. Als u deze situatie tegenkomt, u overwegen de problematische kolom in een aparte tabel te isoleren.

## <a name="how-to-estimate-memory-requirements"></a>Geheugenvereisten schatten

Het maximaal vereiste geheugen om één rijgroep te comprimeren is ongeveer

- 72 MB +
- \#\* \#rijenkolommen \* 8 bytes +
- \#rijen \* \#korte tekenreekskolommen \* 32 bytes +
- \#lange-string-kolommen \* 16 MB voor compressie woordenboek

wanneer korte-string-kolommen tekenreeksgegevenstypen van <= 32 bytes en lange-string-kolommen gebruiken stringgegevenstypen van > 32 bytes.

Lange tekenreeksen worden gecomprimeerd met een compressiemethode die is ontworpen voor het comprimeren van tekst. Deze compressiemethode gebruikt een *woordenboek* om tekstpatronen op te slaan. De maximale grootte van een woordenboek is 16 MB. Er is slechts één woordenboek voor elke lange tekenreekskolom in de rijgroep.

Zie de video [Synapse SQL scaling: configuration and guidance](https://channel9.msdn.com/Events/Ignite/2016/BRK3291)voor een diepgaande bespreking van de geheugenvereisten van columnstore.

## <a name="ways-to-reduce-memory-requirements"></a>Manieren om geheugenvereisten te verminderen

Gebruik de volgende technieken om de geheugenvereisten voor het comprimeren van rijgroepen in kolomarchiefindexen te verminderen.

### <a name="use-fewer-columns"></a>Minder kolommen gebruiken

Ontwerp indien mogelijk de tabel met minder kolommen. Wanneer een rijgroep wordt gecomprimeerd in het kolomarchief, comprimeert de kolomarchiefindex elk kolomsegment afzonderlijk. Daarom nemen de geheugenvereisten voor het comprimeren van een rijgroep toe naarmate het aantal kolommen toeneemt.

### <a name="use-fewer-string-columns"></a>Minder tekenreekskolommen gebruiken

Kolommen met tekenreeksgegevenstypen vereisen meer geheugen dan numerieke en datumgegevenstypen. Als u de geheugenvereisten wilt verminderen, u overwegen tekenreekskolommen uit feitentabellen te verwijderen en ze in tabellen met kleinere afmetingen te plaatsen.

Aanvullende geheugenvereisten voor tekenreekscompressie:

- Voor gegevenstypen tot 32 tekens kunnen 32 extra bytes per waarde worden vereist.
- Tekenreeksgegevenstypen met meer dan 32 tekens worden gecomprimeerd met woordenboekmethoden.  Voor elke kolom in de rijgroep kan maximaal 16 MB extra nodig zijn om het woordenboek te bouwen.

### <a name="avoid-over-partitioning"></a>Over-partitionering voorkomen

Indexen van het columnstore maken een of meer rijgroepen per partitie. Voor gegevensopslag in Azure Synapse Analytics neemt het aantal partities snel toe omdat de gegevens worden gedistribueerd en elke distributie wordt verdeeld. Als de tabel te veel partities heeft, zijn er mogelijk niet genoeg rijen om de rijgroepen te vullen. Het ontbreken van rijen leidt niet tot geheugendruk tijdens compressie, maar leidt tot rijgroepen die niet de beste queryprestaties van de kolomarchief bereiken.

Een andere reden om over-partitionering te vermijden is er een geheugenoverhead voor het laden van rijen in een kolomarchiefindex op een verdeelde lijst. Tijdens een belasting kunnen veel partities de binnenkomende rijen ontvangen, die in het geheugen worden bewaard totdat elke partitie voldoende rijen heeft om te worden gecomprimeerd. Het hebben van te veel partities zorgt voor extra geheugendruk.

### <a name="simplify-the-load-query"></a>De belastingsquery vereenvoudigen

De database deelt de geheugensubsidie voor een query tussen alle operatoren in de query. Wanneer een belastingsquery complexe soorten en joins heeft, wordt het geheugen dat beschikbaar is voor compressie verminderd.

Ontwerp de laadquery om zich alleen te richten op het laden van de query. Als u transformaties op de gegevens moet uitvoeren, voert u deze apart uit van de belastingsquery. Zet de gegevens bijvoorbeeld in een heaptabel, voer de transformaties uit en laad de faseringstabel vervolgens in de kolomarchiefindex. 

### <a name="adjust-maxdop"></a>MAXDOP aanpassen

Elke distributie comprimeert rijgroepen parallel in het kolomarchief wanneer er meer dan één CPU-kern beschikbaar is per distributie. De parallellisme vereist extra geheugenbronnen, wat kan leiden tot geheugendruk en het bijsnijden van rijgroepen.

Om de geheugendruk te verminderen, u de hint VAN MAXDOP-query gebruiken om de belastingsbewerking binnen elke distributie in de seriële modus uit te voeren.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Manieren om meer geheugen toe te wijzen

DWU-grootte en de gebruikersbronklasse bepalen samen hoeveel geheugen beschikbaar is voor een gebruikersquery. Als u de geheugensubsidie voor een belastingquery wilt verhogen, u het aantal DTO's verhogen of de resourceklasse verhogen.

- Zie [Hoe schaal ik de prestaties?](../sql-data-warehouse/quickstart-scale-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- Zie [Voorbeeld van een gebruikersbronklasse wijzigen](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#change-a-users-resource-class)als u de resourceklasse voor een query wilt wijzigen.

## <a name="next-steps"></a>Volgende stappen

Zie het [prestatieoverzicht](../overview-cheat-sheet.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer manieren om de prestaties in Synapse SQL te verbeteren.

 
