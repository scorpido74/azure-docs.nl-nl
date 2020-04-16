---
title: Aanbevolen procedures voor SQL-pools
description: Aanbevelingen en aanbevolen procedures die u moet kennen terwijl u met SQL-pools werkt.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b3dc111fe62cbae857f3369165ba29cf40e90342
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427794"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Aanbevolen procedures voor SQL-pools in Azure Synapse Analytics

In dit artikel vindt u een verzameling aanbevolen procedures waarmee u optimale prestaties bereiken voor SQL-pools in Azure Synapse Analytics. Hieronder vindt u basisrichtlijnen en belangrijke gebieden waarop u zich concentreren terwijl u uw oplossing bouwt. Elke sectie laat u kennismaken met een concept en wijst u vervolgens naar meer gedetailleerde artikelen die het concept in meer diepte behandelen.

## <a name="sql-pools-loading"></a>SQL-pools laden

Zie [Richtlijnen voor het laden van SQL-pools voor](data-loading-best-practices.md)het laden van gegevens .

## <a name="reduce-cost-with-pause-and-scale"></a>Kosten verlagen met onderbreken en schalen

Zie [Compute](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)beheren voor meer informatie over het verlagen van de kosten door te pauzeren en te schalen.

## <a name="maintain-statistics"></a>Statistieken bijhouden

Terwijl SQL Server automatisch statistieken over kolommen detecteert en maakt of bijwerkt, vereisen SQL-pools handmatig onderhoud van statistieken. U wilt uw statistieken bijhouden om ervoor te zorgen dat de SQL-poolplannen worden geoptimaliseerd.  De plannen die door de optimalisatie worden gemaakt, zijn maar zo goed als de beschikbare statistieken.

> [!TIP]
> Voorbeeldstatistieken maken voor elke kolom is een gemakkelijke manier om met statistieken aan de slag te gaan.  

Het is net zo belangrijk om uw statistieken bij te werken omdat er significante wijzigingen optreden in uw gegevens.  Een voorzichtige werkwijze is om uw statistieken dagelijks of na elke load bij te werken.  Het maken en bijwerken van statistieken kan ten koste gaan van prestaties en kosten.

Als u de onderhoudstijd van statistieken wilt verkorten, moet u selectief zijn over welke kolommen statistieken hebben of de meest voorkomende bijwerking nodig hebben. U bijvoorbeeld datumkolommen bijwerken waarbij dagelijks nieuwe waarden kunnen worden toegevoegd. Focus op het hebben van statistieken voor kolommen die betrokken zijn bij joins, kolommen die worden gebruikt in de WHERE-component en kolommen die worden gevonden in GROEP BY.

Aanvullende informatie over statistieken is te vinden in de [artikelen Statistieken beheren](develop-tables-statistics.md), STATISTIEKEN [MAKEN](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en [STATISTIEKEN BIJWERKEN.](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="group-insert-statements-into-batches"></a>INSERT-instructie in batches groeperen

Een eenmalige belasting naar een kleine tabel `INSERT INTO MyLookup VALUES (1, 'Type 1')`met een INSERT-instructie, zoals de beste aanpak kan zijn, afhankelijk van uw behoeften. Als u echter duizenden of miljoenen rijen gedurende de dag moet laden, is het waarschijnlijk dat singleton INSERTS niet optimaal zijn.

Een manier om dit probleem op te lossen is door een proces te ontwikkelen dat naar een bestand wordt geschreven, en vervolgens een ander proces om dit bestand periodiek te laden. Raadpleeg het [artikel INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor meer informatie.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>PolyBase gebruiken om snel gegevens te laden en te exporteren

SQL-groep ondersteunt het laden en exporteren van gegevens via verschillende hulpprogramma's, waaronder Azure Data Factory, PolyBase en BCP.  Voor kleine hoeveelheden gegevens waarbij prestaties niet belangrijk zijn, kunnen al deze toepassingen aan uw vereisten voldoen.  

> [!NOTE]
> Polybase is de beste keuze wanneer u grote hoeveelheden gegevens laadt of exporteert, of u snellere prestaties nodig hebt.

PolyBase-loads kunnen worden uitgevoerd met behulp van CTAS of INSERT INTO. CTAS minimaliseert transactielogboekregistratie en is de snelste manier om uw gegevens te laden. Azure Data Factory ondersteunt ook PolyBase-belastingen en kan prestaties bereiken die vergelijkbaar zijn met CTAS. PolyBase ondersteunt verschillende bestandsindelingen, waaronder Gzip-bestanden.

Als u de doorvoer wilt maximaliseren bij het gebruik van Gzip-tekstbestanden, u bestanden opsplitsen in 60 of meer bestanden om de parallellisme van uw belasting te maximaliseren. Voor een snellere totale doorvoer, kunt u overwegen gegevens gelijktijdig te laden. Aanvullende informatie over de onderwerpen die relevant zijn voor deze sectie is opgenomen in de volgende artikelen:

- [Gegevens laden](data-loading-overview.md)
- [Gids voor gebruik van PolyBase](data-loading-best-practices.md)
- [Laadpatronen en -strategieën voor Azure SQL-groep](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Gegevens laden met Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Gegevens verplaatsen met Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Externe tabellen laden en vervolgens query’s uitvoeren

Polybase is niet optimaal voor query's. Polybase-tabellen voor SQL-groepen ondersteunen momenteel alleen Azure blob-bestanden en Azure Data Lake-opslag. Deze bestanden hebben geen rekenbronnen die hen ondersteunen. Als gevolg hiervan kunnen SQL-pools dit werk niet ontladen en moet het hele bestand worden gelezen door het te laden naar tempdb, zodat het de gegevens kan lezen.

Als u meerdere query's hebt voor het opvragen van deze gegevens, is het beter om deze gegevens één keer te laden en query's de lokale tabel te laten gebruiken. Verdere Polybase begeleiding is opgenomen in de [gids voor het gebruik van PolyBase](data-loading-best-practices.md) artikel.

## <a name="hash-distribute-large-tables"></a>Grote tabellen distribueren met hash

Tabellen worden standaard gedistribueerd middels Round Robin.   Deze standaard maakt het gemakkelijk voor gebruikers om tabellen te maken zonder te hoeven beslissen hoe hun tabellen moeten worden gedistribueerd. Ronde Robin-tabellen kunnen voldoende presteren voor sommige workloads. Maar in de meeste gevallen biedt een distributiekolom betere prestaties.  

Het meest voorkomende voorbeeld van een tabel die wordt gedistribueerd door een kolom die beter presteert dan een Ronde Robin-tabel, is wanneer twee grote feitentabellen worden samengevoegd.  

Als u bijvoorbeeld een ordertabel hebt die wordt verdeeld over order_id en een transactietabel ook wordt verdeeld over order_id, wanneer u deelneemt aan uw ordertabel voor uw transactietabel op order_id, wordt deze query een doorgeefquery. Gegevensverplaatsingsbewerkingen worden dan geëlimineerd. Minder stappen betekent een snellere query. Minder gegevensverplaatsing maakt query’s ook sneller.

> [!NOTE]
> Bij het laden van een gedistribueerde tabel mogen uw binnenkomende gegevens niet worden gesorteerd op de distributiesleutel. Hierdoor zal vertragen uw lasten.

Het artikel links hieronder geeft u extra details over het verbeteren van de prestaties via het selecteren van een distributie kolom. U vindt ook informatie over het definiëren van een gedistribueerde tabel in de MET-component van de instructie MAAK TABLE:

- [Tabeloverzicht](develop-tables-overview.md)
- [Tabeldistributie](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Tabeldistributie selecteren](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>Niet te veel partities maken

Hoewel het partitioneren van gegevens effectief kan zijn voor het bijhouden van uw gegevens door het schakelen van partities of het optimaliseren van scans door met partitieverwijdering, kan het hebben van te veel partities uw query's vertragen.  Vaak werkt een hoge granulariteitspartitioneringsstrategie die goed kan werken op SQL Server mogelijk niet goed op SQL-pool.  

Als u te veel partities heeft, kan de effectiviteit van geclusterde kolomarchiefindexen worden verminderd als elke partitie minder dan 1 miljoen rijen heeft. SQL-groepen partitioneren uw gegevens automatisch in 60 databases. Dus als u een tabel met 100 partities maakt, zijn het resultaat 6000 partities. Elke werkbelasting is anders, dus het beste advies is om te experimenteren met partitioneren om te zien wat het beste werkt voor uw werkbelasting.  

Een optie om te overwegen is het gebruik van een granulariteit die lager is dan wat u hebt geïmplementeerd met SQL Server. Overweeg bijvoorbeeld wekelijkse of maandelijkse partities te gebruiken in plaats van dagelijkse partities.

Meer informatie over partitionering wordt beschreven in het artikel [Tabelpartitionering.](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="minimize-transaction-sizes"></a>Transactiegrootten minimaliseren

Overzichten INVOEGEN, BIJWERKEN en VERWIJDEREN worden uitgevoerd in een transactie. Als ze falen, moeten ze worden teruggedraaid. Om de kans op een lange terugdraaiing te verkleinen, minimaliseert u de transactiegrootte waar mogelijk.  Het minimaliseren van transactiegroottes kan worden gedaan door insert-, update- en verwijderinstructies in onderdelen te verdelen. Als u bijvoorbeeld een INSERT hebt waarvan u verwacht dat deze 1 uur in haar opneemt, u de INSERT opsplitsen in vier delen. Elke run wordt dan ingekort tot 15 minuten.  

> [!TIP]
> Maak gebruik van speciale minimale logboekregistratiegevallen, zoals CTAS, TRUNCATE, DROP TABLE of INSERT om tabellen te legen om het terugdraairisico te verminderen.  

Een andere manier om terugdraaiacties te voorkomen, is door alleen-metagegevensbewerkingen, zoals schakelen tussen partities, te gebruiken voor gegevensbeheer.  In plaats van bijvoorbeeld een delete-instructie uit te voeren om alle rijen in een tabel te verwijderen waar de order_date in oktober 2001 was, u uw gegevens maandelijks verdelen. Vervolgens u de partitie met gegevens voor een lege partitie uit een andere tabel uitschakelen (zie VOORBEELDEN VAN TABEL WIJZIGEN).  

Voor niet-gepartitioneerde tabellen u overwegen een CTAS te gebruiken om de gegevens te schrijven die u in een tabel wilt bewaren in plaats van Delete te gebruiken.  Als een CTAS even veel tijd in beslag neemt, is het veel veiliger om uit te voeren, omdat deze minimale transactielogboekregistratie heeft en indien nodig snel kan worden geannuleerd.

Meer informatie over de inhoud met betrekking tot deze sectie is opgenomen in de onderstaande artikelen:

- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Inzicht krijgen in transacties](develop-transactions.md)
- [Transacties optimaliseren](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Tabelpartitionering](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Tabel Afkappen](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>Queryresultaatgroottes verkleinen

Als u de grootte van queryresultaten vermindert, u problemen aan de clientzijde voorkomen die worden veroorzaakt door grote queryresultaten.  U uw query bewerken om het aantal geretourneerde rijen te verminderen. Met sommige hulpmiddelen voor het genereren van query's u 'top N'-syntaxis toevoegen aan elke query.  U het queryresultaat ook CETAS naar een tijdelijke tabel en vervolgens PolyBase-export gebruiken voor de downlevel-verwerking.

## <a name="use-the-smallest-possible-column-size"></a>De kleinst mogelijke kolomgrootte gebruiken

Wanneer u uw DDL definieert, gebruikt u het kleinste gegevenstype dat uw gegevens ondersteunt, waardoor de queryprestaties worden verbeterd.  Deze aanbeveling is met name belangrijk voor char- en VARCHAR-kolommen.  Als de langste waarde in een kolom 25 tekens is, definieert u uw kolom als VARCHAR(25).  U kunt alle tekenkolommen beter niet volgens een grote standaardlengte definiëren.  Definieer bovendien kolommen als VARCHAR wanneer dat alles is wat nodig is in plaats van NVARCHAR te gebruiken.

Zie het [tabeloverzicht](develop-tables-overview.md), [tabelgegevenstypen](develop-tables-data-types.md)en [TABEL-artikelen MAKEN](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor een meer gedetailleerde beoordeling van essentiële concepten die relevant zijn voor de bovenstaande informatie.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Tijdelijke heap-tabellen gebruiken voor tijdelijke gegevens

Wanneer u tijdelijk gegevens op SQL-groepen landt, worden heaptabellen over het algemeen sneller gemaakt.  Als u gegevens alleen laadt om deze te faseren voordat u meer transformaties uitvoert, is het laden van de tabel naar een heaptabel sneller dan het laden van de gegevens naar een geclusterde kolomarchieftabel.  

Het laden van gegevens naar een tijdelijke tabel zal ook veel sneller laden dan het laden van een tabel naar permanente opslag.  Tijdelijke tabellen beginnen met een '#' en zijn alleen toegankelijk voor de sessie die de tabellen heeft gemaakt. Zij mogen dus slechts in beperkte scenario's werken. Heap-tabellen worden gedefinieerd in het WITH-component van een CREATE TABLE-instructie.  Als u wel een tijdelijke tabel gebruikt, vergeet dan niet ook statistieken voor de tijdelijke tabel te maken.

Raadpleeg voor aanvullende richtlijnen de [tijdelijke tabellen](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [TABEL MAKEN](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en TABEL MAKEN [ALS SELECT-artikelen.](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="optimize-clustered-columnstore-tables"></a>Geclusterde columnstore-tabellen optimaliseren

Geclusterde columnstore-indexen zijn een van de meest efficiënte manieren waarop u uw gegevens opslaan in SQL-groep.  Tabellen in SQL-groep worden standaard gemaakt als Clustered ColumnStore.  Een goede segmentkwaliteit is belangrijk om de beste resultaten te behalen voor query’s voor columnstore-tabellen.  Wanneer rijen naar columnstore-tabellen worden geschreven onder geheugendruk, kan dit ten koste gaan van de kwaliteit van columnstore-segmenten.  

De segmentkwaliteit kan worden gemeten aan de slag met het aantal rijen in een gecomprimeerde rijgroep. Zie de [oorzaken van slechte indexkwaliteit van kolomarchiefin](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) het artikel [Tabelindexen](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor stapsgewijze instructies voor het detecteren en verbeteren van de segmentkwaliteit voor geclusterde kolomarchieftabellen.  

Omdat kolomarchiefsegmenten van hoge kwaliteit belangrijk zijn, is het een goed idee om gebruikers-id's te gebruiken die zich in de middelgrote of grote resourceklasse bevinden voor het laden van gegevens. Als u lagere [gegevensmagazijnen gebruikt,](resource-consumption-models.md) wilt u een grotere resourceklasse toewijzen aan uw beladingsgebruiker.

Kolomarchieftabellen pushen gegevens over het algemeen niet in een segment van de gecomprimeerde kolomarchief totdat er meer dan 1 miljoen rijen per tabel zijn. Elke SQL-pooltabel is verdeeld in 60 tabellen. Als zodanig komen kolomarchieftabellen niet ten goede aan een query, tenzij de tabel meer dan 60 miljoen rijen heeft.  

> [!TIP]
> Voor tabellen met minder dan 60 miljoen rijen is het hebben van een kolomarchiefindex mogelijk niet de optimale oplossing.  

Als u uw gegevens partitioneert, moet elke partitie 1 miljoen rijen hebben om te profiteren van een geclusterde kolomarchiefindex.  Voor een tabel met 100 partities moet deze ten minste 6 miljard rijen hebben om te profiteren van een geclusterde kolommenopslag (60 distributies *100 partities* 1 miljoen rijen).  

Als uw tabel geen 6 miljard rijen heeft, hebt u twee hoofdopties. Verminder het aantal partities of overweeg in plaats daarvan een heaptabel te gebruiken.  Het kan ook de moeite waard zijn om te experimenteren om te zien of betere prestaties kunnen worden behaald met behulp van een heap tabel met secundaire indexen in plaats van een columnstore tabel.

Query’s worden sneller uitgevoerd voor een columnstore-tabel als u alleen de kolommen selecteert die u nodig hebt.  Meer informatie over tabel- en kolomarchiefindexen en is te vinden in de [kolomindexen,](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [kolomindexengids](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en [Indexen van kolomarchief-indexen.](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Een grotere resourceklasse gebruiken om queryprestaties te verbeteren

SQL-groepen gebruiken resourcegroepen als een manier om geheugen toe te wijzen aan query's. In eerste instantie worden alle gebruikers toegewezen aan de kleine resourceklasse, die 100 MB geheugen per distributie toekent.  Er zijn altijd 60 distributies. Elke distributie krijgt minimaal 100 MB. De totale systeembrede geheugentoewijzing is 6.000 MB of iets minder dan 6 GB.  

Bepaalde query’s, zoals grote samenvoegingen of loads naar geclusterde columnstore-tabellen, profiteren van grotere geheugentoewijzingen.  Sommige query's, zoals pure scans, zien geen voordeel. Het gebruik van grotere resourceklassen heeft invloed op gelijktijdigheid. U wilt deze feiten dus in gedachten houden voordat u al uw gebruikers naar een grote resourceklasse verplaatst.

Raadpleeg het artikel [Resourceklassen voor werkbelastingbeheer](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor meer informatie over resourceklassen.

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Kleinere resourceklasse gebruiken om de gelijktijdigheid te verhogen

Als u een lange vertraging in gebruikersquery's opmerkt, worden uw gebruikers mogelijk uitgevoerd in grotere resourceklassen. Dit scenario bevordert het verbruik van gelijktijdige valutaslots, waardoor andere query's in de wachtrij kunnen staan.  Als u wilt bepalen of gebruikersquery's in de wachtrij staan, voert u uit `SELECT * FROM sys.dm_pdw_waits` om te zien of er rijen worden geretourneerd.

De [resourceklassen voor workloadmanagement](../sql-data-warehouse/resource-classes-for-workload-management.md) en [sys.dm_pdw_waits-artikelen](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) geven u meer informatie.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Gebruik DMV’s om uw query’s te controleren en te optimaliseren

SQL-pools hebben verschillende DMVs die kunnen worden gebruikt om query-uitvoering te controleren.  In het onderstaande controleartikel u stapsgewijze instructies uitvoeren over het weergeven van details van een uitvoerende query.  Als u snel query’s wilt vinden in de DMV's, kunt u proberen de optie LABEL te gebruiken bij uw query’s. Zie voor meer gedetailleerde informatie de artikelen in de onderstaande lijst:

- [Monitor your workload using DMVs](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [Label](develop-label.md)
- [Optie](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>Volgende stappen

Zie ook het artikel [Probleemoplossing](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor veelvoorkomende problemen en oplossingen.

Als u informatie nodig hebt die niet in dit artikel wordt verstrekt, gebruikt u de 'Zoeken naar documenten' aan de linkerkant van deze pagina om alle SQL-pooldocumenten te doorzoeken.  Het [SQL-poolforum](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) is een plek voor u om vragen te stellen aan andere gebruikers en aan de SQL-pool Product Group.  

We controleren het forum regelmatig om er zeker van te zijn dat uw vragen worden beantwoord door een andere gebruiker of een van ons.  Als u uw vragen liever stelt over Stack Overflow, hebben we ook een [Azure SQL-pool Stack Overflow Forum.](https://stackoverflow.com/questions/tagged/azure-sqldw)

Gebruik voor functieaanvragen de pagina [Feedback van Azure SQL-groep.](https://feedback.azure.com/forums/307516-sql-data-warehouse)  Het toevoegen van uw verzoeken of het up-voting andere verzoeken helpt ons om ons te concentreren op de meest gevraagde functies.
