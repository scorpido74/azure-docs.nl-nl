---
title: Aanbevolen procedures voor Azure SQL Data Warehouse | Microsoft Docs
description: Aanbevelingen en aanbevolen procedures voor het ontwikkelen van oplossingen voor Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/26/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a89988fd369a382ac86f0f4b1ef0f61c0b7b9cad
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178420"
---
# <a name="best-practices-for-azure-sql-data-warehouse"></a>Aanbevolen procedures voor Azure SQL Data Warehouse
Dit artikel bevat een verzameling aanbevolen procedures om u te helpen de prestaties van uw Azure SQL Data Warehouse optimaal te benutten.  Sommige onderwerpen in dit artikel zijn eenvoudig uit te leggen, andere zijn geavanceerder, waardoor we alleen de basis hiervan kunnen behandelen.  Het doel van dit artikel is om u een aantal richtlijnen te geven en u te wijzen op belangrijke onderdelen bij het maken van een datawarehouse.  In elk gedeelte maakt u kennis met een onderwerp en wordt u gewezen op gedetailleerdere artikelen die dieper op het onderwerp ingaan.

Laat u niet afschrikken door dit artikel als u net met Azure SQL Data Warehouse begint.  De onderwerpen zijn grotendeels gerangschikt op belangrijkheid.  Als u met de eerste onderwerpen begint, bent u al goed op weg.  Als u vertrouwd en vertrouwd bent met het gebruik van SQL Data Warehouse, kunt u teruggaan en een paar concepten bekijken.  U hebt het in een mum van tijd onder de knie.

Zie voor hulp bij het laden [Richtlijnen voor het laden van gegevens](guidance-for-loading-data.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Kosten verlagen met onderbreken en schalen
Zie [Compute beheren](sql-data-warehouse-manage-compute-overview.md) voor meer informatie over kostenverlaging via onderbreking en schaling. 


## <a name="maintain-statistics"></a>Statistieken bijhouden
Anders dan SQL Server, die statistieken automatisch detecteert en creëert of bijwerkt in kolommen, vereist SQL Data Warehouse het handmatig bijhouden van statistieken.  Hoewel het plan is dit in de toekomst te veranderen, is het nu nog noodzakelijk dat u zelf uw statistieken bijhoudt om uw SQL Data Warehouse-plannen te optimaliseren.  De plannen die door de optimalisatie worden gemaakt, zijn maar zo goed als de beschikbare statistieken.  **Voorbeeldstatistieken maken voor elke kolom is een gemakkelijke manier om met statistieken aan de slag te gaan.**  Het is net zo belangrijk om uw statistieken bij te werken omdat er significante wijzigingen optreden in uw gegevens.  Een voorzichtige werkwijze is om uw statistieken dagelijks of na elke load bij te werken.  Het maken en bijwerken van statistieken kan ten koste gaan van prestaties en kosten. Als het te lang duurt om al uw statistieken bij te houden, kunt u overwegen selectiever te zijn in welke kolommen statistieken hebben of voor welke kolommen de statistieken regelmatig moeten worden bijgewerkt.  Zo wilt u datumkolommen, waar nieuwe gegevens kunnen zijn toegevoegd, misschien dagelijks bijwerken. **U haalt het meeste voordeel uit statistieken bij kolommen die onderdeel uitmaken van samenvoegingen, kolommen met het WHERE-component en kolommen in GROUP BY.**

Zie ook [Tabelstatistieken beheren][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="group-insert-statements-into-batches"></a>INSERT-instructie in batches groeperen
Een eenmalige load naar een kleine tabel met een INSERT-instructie of zelfs een periodieke reload van een resultaat kan prima aan uw vereisten voldoen met een instructie zoals `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Als u echter gedurende de dag duizenden of zelfs miljoenen rijen moet laden, voldoen afzonderlijke INSERT-instructies mogelijk niet meer.  U kunt uw processen ook zo instellen dat ze naar een bestand schrijven, en een ander proces instellen dat het bestand periodiek laadt.

Zie ook [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>PolyBase gebruiken om snel gegevens te laden en te exporteren
SQL Data Warehouse ondersteunt het laden en exporteren van gegevens via verschillende toepassingen, waaronder Azure Data Factory, PolyBase en BCP.  Voor kleine hoeveelheden gegevens waarbij prestaties niet belangrijk zijn, kunnen al deze toepassingen aan uw vereisten voldoen.  Wanneer u echter grote volumes aan gegevens laadt of exporteert of snelle prestaties vereist zijn, is PolyBase de beste keuze.  PolyBase is ontworpen om gebruik te maken van de MPP-architectuur (Massively Parallel Processing) van SQL Data Warehouse en kan daardoor grote hoeveelheden gegevens sneller laden en exporteren dan elke andere toepassing.  PolyBase-loads kunnen worden uitgevoerd met behulp van CTAS of INSERT INTO.  **Door CTAS te gebruiken, wordt de transactieregistratie geminimaliseerd, waardoor het de snelste manier is om uw gegevens te laden.**  Azure Data Factory biedt ook ondersteuning voor poly base-belastingen en kan soort gelijke prestaties opleveren als CTAS.  PolyBase ondersteunt verschillende bestandsindelingen, waaronder GZip.  **Om de doorvoer te maximaliseren bij het gebruik van GZip-tekstbestanden, splitst u bestanden in 60 of meer bestanden om de parallelle uitvoering van uw load te optimaliseren.**  Voor een snellere totale doorvoer, kunt u overwegen gegevens gelijktijdig te laden.

Zie ook [Gegevens laden][Load data], [Gids voor gebruik van PolyBase][Guide for using PolyBase], [Laadpatronen en -strategieën voor Azure SQL Data Warehouse][Azure SQL Data Warehouse loading patterns and strategies], [Gegevens laden met Azure Data Factory][Load Data with Azure Data Factory], [Gegevens verplaatsen met Azure Data Factory][Move data with Azure Data Factory], [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT], [Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Externe tabellen laden en vervolgens query’s uitvoeren
PolyBase, ook wel bekend als externe tabellen, kan de snelste manier zijn om gegevens te laden, maar het is niet optimaal voor query’s. SQL Data Warehouse PolyBase-tabellen ondersteunen momenteel alleen Azure-blob-bestanden en Azure Data Lake-opslag. Deze bestanden worden niet door rekenresources ondersteunt.  Als gevolg daarvan kan SQL Data Warehouse deze taak niet offloaden en moet het het hele bestand lezen door het naar tempdb te laden om de gegevens te kunnen lezen.  Indien u meerdere query’s uitvoert voor deze gegevens, kunt u beter de gegevens eenmalig laden en query’s op de lokale tabel toepassen.

Zie ook [Gids voor gebruik van PolyBase][Guide for using PolyBase]

## <a name="hash-distribute-large-tables"></a>Grote tabellen distribueren met hash
Tabellen worden standaard gedistribueerd middels Round Robin.  Dit maakt het gemakkelijk voor gebruikers om tabellen te maken zonder te hoeven bepalen hoe de tabellen moeten worden gedistribueerd.  Round Robin-tabellen leveren voldoende prestaties voor sommige workloads, maar in de meeste gevallen leidt het selecteren van een distributiekolom tot veel betere prestaties.  Het meestvoorkomende geval waarin een tabel die per kolom is gedistribueerd veel beter presteert dan een Round Robin-tabel, is wanneer twee grote feitentabellen worden samengevoegd.  Als u bijvoorbeeld een ordertabel hebt die is gedistribueerd op order_id, en een transactietabel die ook is gedistribueerd op order_id, en u de ordertabel met de transactietabel samenvoegt op order_id, wordt deze query een Pass-Through-query. Dit betekent dat bewerkingen voor gegevensverplaatsing worden voorkomen.  Minder stappen betekent een snellere query.  Minder gegevensverplaatsing maakt query’s ook sneller.  Deze uitleg maakt alleen een kras van het Opper vlak. Zorg er bij het laden van een distributietabel voor dat uw inkomende gegevens niet zijn gedistribueerd volgens de verdeelsleutel. Dit vertraagt het laden.  Zie de onderstaande koppelingen voor meer informatie over hoe het selecteren van een distributie kolom de prestaties kan verbeteren en hoe u een gedistribueerde tabel kunt definiëren in de WITH-component van uw CREATE TABLE-instructie.

Zie ook [Tabeloverzicht][Table overview], [Tabeldistributie][Table distribution], [Tabeldistributie selecteren][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Niet te veel partities maken
Partities voor uw gegevens maken kan zeer effectief zijn voor het bijhouden van gegevens door te schakelen tussen partities of het optimaliseren van scans door het schrappen van partities. Te veel partities kunnen echter uw query’s vertragen.  Een partitiestrategie met een hoge granulatie die goed werkt voor SQL Server werkt mogelijk niet goed voor SQL Data Warehouse.  Te veel partities kunnen ook geclusterde columnstore-indexen minder effectief maken als elke partitie minder dan 1 miljoen rijen bevat.  Achter de schermen verdeelt SQL Data Warehouse uw gegevens in partities op meer dan 60 databases. Als u een dus een tabel met 100 partities maakt, resulteert dit in werkelijkheid in 6000 partities.  Elke workload is verschillend, en daarom kunt u het beste experimenteren met partities om te zien wat het beste werkt voor uw workload.  Overweeg een lagere granulatie dan die in SQL Server misschien wel effectief was.  U zou bijvoorbeeld wekelijkse of maandelijkse partities kunnen gebruiken in plaats van dagelijkse partities.

Zie ook [Tabellen partitioneren][Table partitioning]

## <a name="minimize-transaction-sizes"></a>Transactiegrootten minimaliseren
De instructies INSERT, UPDATE en DELETE worden in een transactie uitgevoerd en wanneer ze mislukken, moeten ze worden teruggedraaid.  Om de kans op een lange terugdraaiactie te verkleinen, kunt u transactiegrootten minimaliseren waar mogelijk.  Dit kan door de instructies INSERT, UPDATE en DELETE op te delen.  Als u bijvoorbeeld een INSERT hebt waarvan u verwacht dat deze 1 uur duurt, verdeelt u de INSERT indien mogelijk in 4 delen, die elk in 15 minuten worden uitgevoerd.  Maak gebruik van speciale instructies voor minimale registratie, zoals CTAS, TRUNCATE, DROP TABLE en INSERT, om tabellen leeg te maken, zodat het risico op terugdraaien wordt verkleind.  Een andere manier om terugdraaiacties te voorkomen, is door alleen-metagegevensbewerkingen, zoals schakelen tussen partities, te gebruiken voor gegevensbeheer.  In plaats van een DELETE-instructie uit te voeren om alle rijen in een tabel met een order_date in oktober 2001 te verwijderen, kunt u bijvoorbeeld uw gegevens maandelijks partitioneren en vervolgens de partitie met gegevens omwisselen voor een lege partitie uit een andere tabel (zie voorbeelden ALTER TABLE).  Overweeg voor ongepartitioneerde tabellen een CTAS toe te passen om de gegevens die u wilt behouden naar een tabel te schrijven, in plaats van DELETE te gebruiken.  Als een CTAS net zo lang duurt, heeft dit de voorkeur, aangezien het een veel veiligere bewerking is dankzij de minimale transactieregistratie en snel kan worden geannuleerd, indien nodig.

Zie ook [Inzicht in transacties][Understanding transactions], [Transacties optimaliseren][Optimizing transactions], [Tabellen partitioneren][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="reduce-query-result-sizes"></a>De grootte van het query resultaat verminderen  
Dit helpt u problemen aan de client zijde te voor komen die worden veroorzaakt door grote query resultaten.  U kunt uw query bewerken om het aantal geretourneerde rijen te verminderen. Met de hulpprogram ma's voor het genereren van query's kunt u de syntaxis ' top N ' toevoegen aan elke query.  U kunt het query resultaat ook CETAS naar een tijdelijke tabel en vervolgens poly base exporteren gebruiken voor de Down Level verwerking.

## <a name="use-the-smallest-possible-column-size"></a>De kleinst mogelijke kolomgrootte gebruiken
Als u bij het definiëren van uw DDL het kleinste gegevenstype gebruikt dat uw gegevens ondersteunt, worden de prestaties van uw query's verbeterd.  Dit is met name belangrijk voor CHAR- en VARCHAR-kolommen.  Als de langste waarde in een kolom 25 tekens is, definieert u uw kolom als VARCHAR(25).  U kunt alle tekenkolommen beter niet volgens een grote standaardlengte definiëren.  Definieer daarnaast kolommen als VARCHAR als dat alles is wat nodig is in plaats van NVARCHAR.

Zie ook [Tabeloverzicht][Table overview], [Gegevenstypes tabel][Table data types], [CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>Tijdelijke heap-tabellen gebruiken voor tijdelijke gegevens
Wanneer u tijdelijk gegevens naar SQL Data Warehouse laadt, kan een heap-tabel het proces versnellen.  Als u alleen gegevens laadt om ze klaar te zetten voordat u meer transformaties uitvoert, kan het laden van de tabel naar een heap-tabel veel sneller zijn dan het downloaden van de gegevens naar een geclusterde columnstore-tabel.  Daarnaast is het laden van gegevens naar een tijdelijke tabel ook veel sneller dan het laden van een tabel naar permanente opslag.  Tijdelijke tabellen beginnen met een # en zijn alleen toegankelijk voor de sessie waarin ze zijn gemaakt, dus ze werken alleen maar in beperkte scenario’s.   Heap-tabellen worden gedefinieerd in het WITH-component van een CREATE TABLE-instructie.  Als u wel een tijdelijke tabel gebruikt, vergeet dan niet ook statistieken voor de tijdelijke tabel te maken.

Zie ook [Tijdelijke tabellen][Temporary tables], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>Geclusterde columnstore-tabellen optimaliseren
Geclusterde columnstore-indices vormen een van de meest efficiënte manieren om uw gegevens in SQL Data Warehouse te bewaren.  Tabellen worden in SQL Data Warehouse standaard als geclusterde columnstore gemaakt.  Een goede segmentkwaliteit is belangrijk om de beste resultaten te behalen voor query’s voor columnstore-tabellen.  Wanneer rijen naar columnstore-tabellen worden geschreven onder geheugendruk, kan dit ten koste gaan van de kwaliteit van columnstore-segmenten.  Segmentkwaliteit kan worden gemeten aan de hand van het aantal rijen in een gecomprimeerde rijengroep.  Zie [Oorzaken van slechte kwaliteit voor columnstore-indexen][Causes of poor columnstore index quality] in het artikel [Tabelindexen][Table indexes] voor stapsgewijze instructies voor het detecteren en verbeteren van segmentkwaliteit van geclusterde columnstore-tabellen.  Omdat column Store-segmenten van hoge kwaliteit belang rijk zijn, is het een goed idee om gebruikers-Id's te gebruiken die zich in de middel lange of grote resource klasse bevinden voor het laden van gegevens. Als u lagere [Data Warehouse-eenheden](what-is-a-data-warehouse-unit-dwu-cdwu.md) gebruikt, wilt u een grotere resource klasse toewijzen aan de gebruiker die u wilt laden.

Omdat columnstore-tabellen normaalgesproken gegevens niet naar een gecomprimeerd columnstore-segment doorzetten voordat er meer dan 1 miljoen rijen per tabel zijn en elke SQL Data Warehouse-tabel in 60 tabellen is gepartitioneerd, dragen columnstore-tabellen doorgaans niet bij aan een query tenzij de tabel meer dan 60 miljoen rijen heeft.  Voor tabellen met minder dan 60 miljoen rijen is het meestal niet nodig om een columnstore-index te hebben.  Het is misschien ook niet verkeerd.  Als u uw gegevens partitioneert, houd er dan ook rekening mee dat elke partitie 1 miljoen rijen nodig heeft om voordeel te halen uit een geclusterde columnstore-index.  Als een tabel 100 partities heeft, heeft deze ten minste 6 miljard rijen nodig om voordeel te halen uit een geclusterde columnstore (60 distributies * 100 partities * 1 miljoen rijen).  Als uw tabel in dit voorbeeld geen 6 miljoen rijen heeft, kunt u het aantal partities verminderen of overwegen een heap-tabel te gebruiken.  U kunt ook experimenteren om te zien of u de prestaties kunt verbeteren met een heap-tabel met secondaire sleutels in plaats van een columnstore-tabel.

Query’s worden sneller uitgevoerd voor een columnstore-tabel als u alleen de kolommen selecteert die u nodig hebt.  

Zie ook [Tabel-indexen][Table indexes], [Gids columnstore-indexen][Columnstore indexes guide], [Columnstore-indexen herbouwen][Rebuilding columnstore indexes]

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Een grotere resourceklasse gebruiken om queryprestaties te verbeteren
SQL Data Warehouse gebruikt resourcegroepen om geheugen toe te wijzen aan query’s.  Bij het eerste gebruik is aan alle gebruikers de kleine resourceklasse toegewezen die 100 MB geheugen per distributie biedt.  Omdat er altijd 60 distributies zijn en aan elke distributie een minimum van 100 MB is toegekend, is de totale geheugentoewijzing systeembreed 6000 MB, of net onder 6 GB.  Bepaalde query’s, zoals grote samenvoegingen of loads naar geclusterde columnstore-tabellen, profiteren van grotere geheugentoewijzingen.  Bij andere query’s, zoals pure scans, is er geen merkbaar verschil.  Aan de andere kant hebben grotere resourceklassen invloed op gelijktijdigheid. Kijk hier dus zorgvuldig naar voordat u alle gebruikers naar grotere resourceklassen verplaatst.

Zie ook [Resourceklassen voor het beheer van de werkbelasting](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Een kleinere resourceklasse gebruiken om de gelijktijdigheid te verbeteren
Als query’s van gebruikers erg lang lijken te duren, kan het zijn dat uw gebruikers in grotere resourceklassen worden uitgevoerd en veel gelijktijdige plekken gebruiken, waardoor andere query’s in de wachtrij komen.  Om te zien of er gebruikersquery’s in de wachtrij staan, voert u `SELECT * FROM sys.dm_pdw_waits` uit en kijkt u of er rijen worden geretourneerd.

Zie ook [resource klassen voor workload Management](resource-classes-for-workload-management.md), [sys. DM _pdw_waits][sys.dm_pdw_waits]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Gebruik DMV’s om uw query’s te controleren en te optimaliseren
SQL Data Warehouse heeft verschillende DMV's die kunnen worden gebruikt om het uitvoeren van query’s te controleren.  In het onderstaande artikel over controleren vindt u stapsgewijze instructies voor hoe u de details bekijkt van een query die wordt uitgevoerd.  Als u snel query’s wilt vinden in de DMV's, kunt u proberen de optie LABEL te gebruiken bij uw query’s.

Zie ook [Uw workload controleren met DMV's][Monitor your workload using DMVs], [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Meer informatie
Zie ook ons artikel [Problemen oplossen][Troubleshooting] voor veelvoorkomende problemen en oplossingen.

Als u in dit artikel niet hebt gevonden waarnaar u zocht, kunt u "Zoeken naar documenten" aan de linkerzijde van deze pagina gebruiken om alle Azure SQL Data Warehouse-documenten te doorzoeken.  Het [Azure SQL Data Warehouse forum][Azure SQL Data Warehouse MSDN Forum] is een plek waar u vragen kunt stellen aan andere gebruikers en aan de Product groep SQL Data Warehouse.  We controleren het forum regelmatig om er zeker van te zijn dat uw vragen worden beantwoord door een andere gebruiker of een van ons.  We hebben ook een [Stack Overflow-forum Azure SQL Data Warehouse][Azure SQL Data Warehouse Stack Overflow Forum] voor als u uw vragen liever in Stack Overflow stelt.

Tenslotte willen we u vragen de [Azure SQL Data Warehouse Feedback][Azure SQL Data Warehouse Feedback]-pagina te gebruiken om functieverbeteringen aan te vragen.  Het toevoegen van aanvragen of het stemmen op andere aanvragen helpt ons prioriteit te geven aan bepaalde functies.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

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
