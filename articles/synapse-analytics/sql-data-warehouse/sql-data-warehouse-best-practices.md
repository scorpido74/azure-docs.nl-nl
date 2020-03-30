---
title: Aanbevolen procedures voor SQL Analytics in Azure Synapse Analytics (voorheen SQL DW)
description: Aanbevelingen en aanbevolen procedures voor het ontwikkelen van oplossingen voor SQL Analytics in Azure Synapse Analytics (voorheen SQL DW).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: d0b32fb2b52d2dbb126053247cff83f05781ba5e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350874"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Aanbevolen procedures voor SQL Analytics in Azure Synapse Analytics (voorheen SQL DW)

Dit artikel is een verzameling van best practices om u te helpen optimale prestaties te bereiken vanuit uw [SQL Analytics-implementatie.](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse)  Het doel van dit artikel is om u een aantal fundamentele begeleiding en markeren belangrijke aandachtsgebieden.  Elke sectie laat u kennismaken met een concept en wijst u vervolgens naar meer gedetailleerde artikelen die het concept in meer diepte behandelen. De volgorde van onderwerpen is in de volgorde van belang. 

## <a name="reduce-cost-with-pause-and-scale"></a>Kosten verlagen met onderbreken en schalen

Zie [Compute beheren](sql-data-warehouse-manage-compute-overview.md) voor meer informatie over kostenverlaging via onderbreking en schaling. 

## <a name="maintain-statistics"></a>Statistieken bijhouden
Azure SQL Data Warehouse kan worden geconfigureerd om automatisch statistieken op kolommen te detecteren en te maken.  De queryplannen die door de optimizer zijn gemaakt, zijn slechts zo goed als de beschikbare statistieken.  We raden u aan AUTO_CREATE_STATISTICS voor uw databases in te schakelen en de statistieken dagelijks of na elke belasting up-to-date te houden om ervoor te zorgen dat de statistieken over kolommen die in uw query's worden gebruikt, altijd up-to-date zijn. 

Als u merkt dat het te lang duurt om al uw statistieken bij te werken, wilt u misschien proberen selectiever te zijn over welke kolommen frequente statistische updates nodig hebben. Zo wilt u datumkolommen, waar nieuwe gegevens kunnen zijn toegevoegd, misschien dagelijks bijwerken. **U profiteert het meeste voordeel door bijgewerkte statistieken te hebben over kolommen die betrokken zijn bij joins, kolommen die worden gebruikt in de WHERE-component en kolommen in GROUP BY.**

Zie ook [Tabelstatistieken beheren][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Gebruik DMV’s om uw query’s te controleren en te optimaliseren
SQL Analytics heeft verschillende DMVs die kunnen worden gebruikt om query-uitvoering te controleren.  In het onderstaande artikel over controleren vindt u stapsgewijze instructies voor hoe u de details bekijkt van een query die wordt uitgevoerd.  Als u snel query’s wilt vinden in de DMV's, kunt u proberen de optie LABEL te gebruiken bij uw query’s.

Zie ook [Uw workload controleren met DMV's][Monitor your workload using DMVs], [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="tune-query-performance-with-new-product-enhancements"></a>Queryprestaties afstemmen met nieuwe productverbeteringen
- [Prestaties afstemmen met gerealiseerde weergaven](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Prestaties afstemmen met geordende en geclusterde columnstore-index](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Prestaties afstemmen door resultatensets op te slaan in de cache](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>INSERT-instructie in batches groeperen
Een eenmalige load naar een kleine tabel met een INSERT-instructie of zelfs een periodieke reload van een resultaat kan prima aan uw vereisten voldoen met een instructie zoals `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Als u echter gedurende de dag duizenden of zelfs miljoenen rijen moet laden, voldoen afzonderlijke INSERT-instructies mogelijk niet meer.  U kunt uw processen ook zo instellen dat ze naar een bestand schrijven, en een ander proces instellen dat het bestand periodiek laadt.

Zie ook [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>PolyBase gebruiken om snel gegevens te laden en te exporteren

 SQL Analytics ondersteunt het laden en exporteren van gegevens via verschillende hulpprogramma's, waaronder Azure Data Factory, PolyBase en BCP.  Voor kleine hoeveelheden gegevens waarbij prestaties niet belangrijk zijn, kunnen al deze toepassingen aan uw vereisten voldoen.  Wanneer u echter grote volumes aan gegevens laadt of exporteert of snelle prestaties vereist zijn, is PolyBase de beste keuze.  
 
 PolyBase is ontworpen om gebruik te maken van de MPP -architectuur (Massively Parallel Processing) en zal gegevensgroottes sneller laden en exporteren dan enig ander gereedschap.  PolyBase-loads kunnen worden uitgevoerd met behulp van CTAS of INSERT INTO.  **Door CTAS te gebruiken, wordt de transactieregistratie geminimaliseerd, waardoor het de snelste manier is om uw gegevens te laden.** 
 
  Azure Data Factory ondersteunt ook PolyBase-belastingen en kan vergelijkbare prestaties leveren als CTAS.  PolyBase ondersteunt verschillende bestandsindelingen, waaronder GZip.  **Als u de doorvoer wilt maximaliseren bij het gebruik van gzip-tekstbestanden, u bestanden opsplitsen in 60 of meer bestanden om de parallellisme van uw belasting te maximaliseren.**  Voor een snellere totale doorvoer, kunt u overwegen gegevens gelijktijdig te laden.

Zie ook [Gegevens laden][Load data], Handleiding voor het gebruik [van PolyBase,][Guide for using PolyBase] [SQL-groeplaadpatronen en -strategieën][Azure SQL Data Warehouse loading patterns and strategies], [Gegevens laden met Azure Data Factory][Load Data with Azure Data Factory], Gegevens verplaatsen met Azure Data [Factory][Move data with Azure Data Factory], [EXTERNE BESTANDSINDELING MAKEN][CREATE EXTERNAL FILE FORMAT], Tabel maken als selecteren [(CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Externe tabellen laden en vervolgens query’s uitvoeren
PolyBase, ook wel bekend als externe tabellen, kan de snelste manier zijn om gegevens te laden, maar het is niet optimaal voor query’s. Polybase-tabellen ondersteunen momenteel alleen Azure blob-bestanden en Azure Data Lake-opslag. Deze bestanden worden niet door rekenresources ondersteunt.  

Als gevolg hiervan kan SQL Analytics dit werk niet ontladen en moet het daarom het hele bestand lezen door het naar tempdb te laden om de gegevens te lezen.  Indien u meerdere query’s uitvoert voor deze gegevens, kunt u beter de gegevens eenmalig laden en query’s op de lokale tabel toepassen.

Zie ook [Gids voor gebruik van PolyBase][Guide for using PolyBase]

## <a name="hash-distribute-large-tables"></a>Grote tabellen distribueren met hash
Tabellen worden standaard gedistribueerd middels Round Robin.  Dit maakt het gemakkelijk voor gebruikers om tabellen te maken zonder te hoeven bepalen hoe de tabellen moeten worden gedistribueerd.  Round Robin-tabellen leveren voldoende prestaties voor sommige workloads, maar in de meeste gevallen leidt het selecteren van een distributiekolom tot veel betere prestaties.  Het meestvoorkomende geval waarin een tabel die per kolom is gedistribueerd veel beter presteert dan een Round Robin-tabel, is wanneer twee grote feitentabellen worden samengevoegd.  

Als u bijvoorbeeld een ordertabel hebt die is gedistribueerd op order_id, en een transactietabel die ook is gedistribueerd op order_id, en u de ordertabel met de transactietabel samenvoegt op order_id, wordt deze query een Pass-Through-query. Dit betekent dat bewerkingen voor gegevensverplaatsing worden voorkomen.  Minder stappen betekent een snellere query.  Minder gegevensverplaatsing maakt query’s ook sneller.  

Zorg er bij het laden van een distributietabel voor dat uw inkomende gegevens niet zijn gedistribueerd volgens de verdeelsleutel. Dit vertraagt het laden.  Zie de onderstaande koppelingen voor meer informatie over hoe het selecteren van een distributiekolom de prestaties kan verbeteren en hoe u een gedistribueerde tabel definiëren in de MET-component van de instructie TABEL MAKEN.

Zie ook [Tabeloverzicht][Table overview], [Tabeldistributie][Table distribution], [Tabeldistributie selecteren][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Niet te veel partities maken
Hoewel het partitioneren van gegevens effectief kan zijn voor het bijhouden van uw gegevens door het schakelen van partities of het optimaliseren van scans door met partitieverwijdering, kan het hebben van te veel partities uw query's vertragen.  Vaak werkt een hoge granulariteitspartitioneringsstrategie, die goed kan werken op SQL Server, mogelijk niet goed in SQL Analytics.  

Te veel partities kunnen ook geclusterde columnstore-indexen minder effectief maken als elke partitie minder dan 1 miljoen rijen bevat.  Houd er rekening mee dat SQL Analytics achter de schermen uw gegevens voor u in 60 databases verdeelt, dus als u een tabel met 100 partities maakt, resulteert dit in 6000 partities.  

Elke workload is verschillend, en daarom kunt u het beste experimenteren met partities om te zien wat het beste werkt voor uw workload.  Overweeg een lagere granulatie dan die in SQL Server misschien wel effectief was.  U zou bijvoorbeeld wekelijkse of maandelijkse partities kunnen gebruiken in plaats van dagelijkse partities.

Zie ook [Tabellen partitioneren][Table partitioning]

## <a name="minimize-transaction-sizes"></a>Transactiegrootten minimaliseren
De instructies INSERT, UPDATE en DELETE worden in een transactie uitgevoerd en wanneer ze mislukken, moeten ze worden teruggedraaid.  Om de kans op een lange terugdraaiactie te verkleinen, kunt u transactiegrootten minimaliseren waar mogelijk.  Dit kan door de instructies INSERT, UPDATE en DELETE op te delen.  

Als u bijvoorbeeld een INSERT hebt waarvan u verwacht dat deze 1 uur duurt, wanneer dat mogelijk is, breekt u de INSERT op in vier delen, die elk in 15 minuten worden uitgevoerd.  Maak gebruik van speciale instructies voor minimale registratie, zoals CTAS, TRUNCATE, DROP TABLE en INSERT, om tabellen leeg te maken, zodat het risico op terugdraaien wordt verkleind.  

Een andere manier om terugdraaiacties te voorkomen, is door alleen-metagegevensbewerkingen, zoals schakelen tussen partities, te gebruiken voor gegevensbeheer.  In plaats van een DELETE-instructie uit te voeren om alle rijen in een tabel met een order_date in oktober 2001 te verwijderen, kunt u bijvoorbeeld uw gegevens maandelijks partitioneren en vervolgens de partitie met gegevens omwisselen voor een lege partitie uit een andere tabel (zie voorbeelden ALTER TABLE).  

Voor niet-gepartitioneerde tabellen u overwegen een CTAS te gebruiken om de gegevens te schrijven die u in een tabel wilt bewaren in plaats van Delete te gebruiken.  Als een CTAS even veel tijd in beslag neemt, is het een veel veiligere bewerking om uit te voeren omdat deze minimale transactielogboekregistratie heeft en indien nodig snel kan worden geannuleerd.

Zie ook [Inzicht in transacties][Understanding transactions], [Transacties optimaliseren][Optimizing transactions], [Tabellen partitioneren][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [CREATE TABLE AS SELECT (CTAS)][Create table as select (CTAS)]

## <a name="reduce-query-result-sizes"></a>Queryresultaatgroottes verkleinen  
Met deze stap u problemen aan de clientzijde voorkomen die worden veroorzaakt door een groot queryresultaat.  U uw query bewerken om het aantal geretourneerde rijen te verminderen. Met sommige hulpmiddelen voor het genereren van query's u 'top N'-syntaxis toevoegen aan elke query.  U het queryresultaat ook CETAS naar een tijdelijke tabel en vervolgens PolyBase-export gebruiken voor de downlevel-verwerking.

## <a name="use-the-smallest-possible-column-size"></a>De kleinst mogelijke kolomgrootte gebruiken
Wanneer u uw DDL definieert, verbetert het gebruik van het kleinste gegevenstype dat uw gegevens ondersteunt, de queryprestaties.  Dit is met name belangrijk voor CHAR- en VARCHAR-kolommen.  Als de langste waarde in een kolom 25 tekens is, definieert u uw kolom als VARCHAR(25).  U kunt alle tekenkolommen beter niet volgens een grote standaardlengte definiëren.  Definieer daarnaast kolommen als VARCHAR als dat alles is wat nodig is in plaats van NVARCHAR.

Zie ook [Tabeloverzicht][Table overview], [Gegevenstypes tabel][Table data types], [CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>Tijdelijke heap-tabellen gebruiken voor tijdelijke gegevens
Wanneer u tijdelijk gegevens landt, u merken dat het gebruik van een heaptabel het algehele proces sneller zal maken.  Als u alleen gegevens laadt om ze klaar te zetten voordat u meer transformaties uitvoert, kan het laden van de tabel naar een heap-tabel veel sneller zijn dan het downloaden van de gegevens naar een geclusterde columnstore-tabel.  

Daarnaast is het laden van gegevens naar een tijdelijke tabel ook veel sneller dan het laden van een tabel naar permanente opslag.  Tijdelijke tabellen beginnen met een '#' en zijn alleen toegankelijk voor de sessie die deze heeft gemaakt, zodat ze alleen in beperkte scenario's kunnen werken.   Heap-tabellen worden gedefinieerd in het WITH-component van een CREATE TABLE-instructie.  Als u wel een tijdelijke tabel gebruikt, vergeet dan niet ook statistieken voor de tijdelijke tabel te maken.

Zie ook [Tijdelijke tabellen][Temporary tables], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>Geclusterde columnstore-tabellen optimaliseren
Geclusterde columnstore-indexen zijn een van de meest efficiënte manieren waarop u uw gegevens opslaan in SQL Analytics.  Tabellen in SQL Analytics worden standaard gemaakt als Clustered ColumnStore.  Een goede segmentkwaliteit is belangrijk om de beste resultaten te behalen voor query’s voor columnstore-tabellen.  

Wanneer rijen naar columnstore-tabellen worden geschreven onder geheugendruk, kan dit ten koste gaan van de kwaliteit van columnstore-segmenten.  Segmentkwaliteit kan worden gemeten aan de hand van het aantal rijen in een gecomprimeerde rijengroep.  Zie [Oorzaken van slechte kwaliteit voor columnstore-indexen][Causes of poor columnstore index quality] in het artikel [Tabelindexen][Table indexes] voor stapsgewijze instructies voor het detecteren en verbeteren van segmentkwaliteit van geclusterde columnstore-tabellen.  

Omdat kolomarchiefsegmenten van hoge kwaliteit belangrijk zijn, is het een goed idee om gebruikers-id's te gebruiken die zich in de middelgrote of grote resourceklasse bevinden voor het laden van gegevens. Als u lagere [gegevensmagazijnen gebruikt,](what-is-a-data-warehouse-unit-dwu-cdwu.md) wilt u een grotere resourceklasse toewijzen aan uw beladingsgebruiker.

Aangezien kolomarchieftabellen over het algemeen geen gegevens in een gecomprimeerd kolomarchiefsegment pushen totdat er meer dan 1 miljoen rijen per tabel zijn en elke SQL Analytics-tabel is verdeeld in 60 tabellen, komen kolomarchieftabellen als vuistregel niet ten goede aan een query, tenzij de tabel heeft meer dan 60 miljoen rijen.  Voor tabellen met minder dan 60 miljoen rijen is het meestal niet nodig om een columnstore-index te hebben.  Het is misschien ook niet verkeerd.  

Als u uw gegevens partitioneert, houd er dan ook rekening mee dat elke partitie 1 miljoen rijen nodig heeft om voordeel te halen uit een geclusterde columnstore-index.  Als een tabel 100 partities heeft, heeft deze ten minste 6 miljard rijen nodig om voordeel te halen uit een geclusterde columnstore (60 distributies * 100 partities * 1 miljoen rijen).  

Als uw tabel in dit voorbeeld geen 6 miljoen rijen heeft, kunt u het aantal partities verminderen of overwegen een heap-tabel te gebruiken.  U kunt ook experimenteren om te zien of u de prestaties kunt verbeteren met een heap-tabel met secondaire sleutels in plaats van een columnstore-tabel.

Query’s worden sneller uitgevoerd voor een columnstore-tabel als u alleen de kolommen selecteert die u nodig hebt.  

Zie ook [Tabel-indexen][Table indexes], [Gids columnstore-indexen][Columnstore indexes guide], [Columnstore-indexen herbouwen][Rebuilding columnstore indexes]

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Een grotere resourceklasse gebruiken om queryprestaties te verbeteren
SQL Analytics gebruikt brongroepen als een manier om geheugen toe te wijzen aan query's.  Uit het vak worden alle gebruikers toegewezen aan de kleine resourceklasse, die 100 MB geheugen per distributie toekent.  Omdat er altijd 60 distributies zijn en aan elke distributie een minimum van 100 MB is toegekend, is de totale geheugentoewijzing systeembreed 6000 MB, of net onder 6 GB.  

Bepaalde query’s, zoals grote samenvoegingen of loads naar geclusterde columnstore-tabellen, profiteren van grotere geheugentoewijzingen.  Bij andere query’s, zoals pure scans, is er geen merkbaar verschil.  Aan de andere kant, gebruik te maken van grotere resource klassen vermindert gelijktijdigheid, dus je wilt deze impact in aanmerking te nemen voordat u al uw gebruikers naar een grote resource klasse.

Zie ook [Resourceklassen voor het beheer van de werkbelasting](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Een kleinere resourceklasse gebruiken om de gelijktijdigheid te verbeteren
Als u merkt dat gebruikersquery's een lange vertraging lijken te hebben, kan het zijn dat uw gebruikers in grotere resourceklassen worden uitgevoerd en veel gelijktijdige sleuven verbruiken waardoor andere query's in de wachtrij staan.  Om te zien of er gebruikersquery’s in de wachtrij staan, voert u `SELECT * FROM sys.dm_pdw_waits` uit en kijkt u of er rijen worden geretourneerd.

Zie ook [Resourceklassen voor het beheer van de werkbelasting](resource-classes-for-workload-management.md), [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Meer informatie
Zie ook ons artikel [Problemen oplossen][Troubleshooting] voor veelvoorkomende problemen en oplossingen.

Als u niet hebt gevonden wat u zoekt in dit artikel, probeert u de 'Zoeken naar documenten' aan de linkerkant van deze pagina te gebruiken om alle Azure Synapse-documenten te doorzoeken.  Het [Azure Synapse Forum][Azure SQL Data Warehouse MSDN Forum] is een plek waar u vragen stellen aan andere gebruikers en aan de Azure Synapse Product Group. 

We controleren het forum regelmatig om er zeker van te zijn dat uw vragen worden beantwoord door een andere gebruiker of een van ons.  Als u uw vragen liever stelt over Stack Overflow, hebben we ook een [Azure Synapse Stack Overflow Forum.][Azure SQL Data Warehouse Stack Overflow Forum]

Tot slot u de pagina [Feedback van Azure Synapse][Azure SQL Data Warehouse Feedback] gebruiken om functieaanvragen in te dienen.  Het toevoegen van aanvragen of het stemmen op andere aanvragen helpt ons prioriteit te geven aan bepaalde functies.

<!--Image references-->

<!--Article references-->
[Create a support ticket]:sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]:sql-data-warehouse-develop-ctas.md
[Table overview]:sql-data-warehouse-tables-overview.md
[Table data types]:sql-data-warehouse-tables-data-types.md
[Table distribution]:sql-data-warehouse-tables-distribute.md
[Table indexes]:sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]:sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]:sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]:sql-data-warehouse-tables-partition.md
[Manage table statistics]:sql-data-warehouse-tables-statistics.md
[Temporary tables]:sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]:guidance-for-loading-data.md
[Load data]:design-elt-data-loading.md
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]:load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]:sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]:sql-data-warehouse-develop-transactions.md
[Optimizing transactions]:sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]:sql-data-warehouse-troubleshoot.md
[LABEL]:sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
