---
title: Aanbevolen procedures voor het ontwikkelen van Azure SQL Data Warehouse | Microsoft Docs
description: Aanbevelingen en aanbevolen procedures voor het ontwikkelen van oplossingen voor Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 7e0ae5e6159ae0ab4d098d717f433d2ab63770d4
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "68479721"
---
# <a name="development-best-practices-for-azure-sql-data-warehouse"></a>Aanbevolen procedures voor het ontwikkelen van Azure SQL Data Warehouse
In dit artikel worden richt lijnen en aanbevolen procedures beschreven voor het ontwikkelen van uw data warehouse-oplossing. 

## <a name="reduce-cost-with-pause-and-scale"></a>Kosten verlagen met onderbreken en schalen
Zie [Compute beheren](sql-data-warehouse-manage-compute-overview.md) voor meer informatie over kostenverlaging via onderbreking en schaling. 


## <a name="maintain-statistics"></a>Statistieken bijhouden
Zorg ervoor dat u uw statistieken dagelijks of na elke belasting bijwerkt.  Het maken en bijwerken van statistieken kan ten koste gaan van prestaties en kosten. Als het te lang duurt om al uw statistieken bij te houden, kunt u overwegen selectiever te zijn in welke kolommen statistieken hebben of voor welke kolommen de statistieken regelmatig moeten worden bijgewerkt.  Zo wilt u datumkolommen, waar nieuwe gegevens kunnen zijn toegevoegd, misschien dagelijks bijwerken. **U haalt het meeste voordeel uit statistieken bij kolommen die onderdeel uitmaken van samenvoegingen, kolommen met het WHERE-component en kolommen in GROUP BY.**

Zie ook [Tabelstatistieken beheren][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="hash-distribute-large-tables"></a>Grote tabellen distribueren met hash
Tabellen worden standaard gedistribueerd middels Round Robin.  Dit maakt het gemakkelijk voor gebruikers om tabellen te maken zonder te hoeven bepalen hoe de tabellen moeten worden gedistribueerd.  Round Robin-tabellen leveren voldoende prestaties voor sommige workloads, maar in de meeste gevallen leidt het selecteren van een distributiekolom tot veel betere prestaties.  Het meestvoorkomende geval waarin een tabel die per kolom is gedistribueerd veel beter presteert dan een Round Robin-tabel, is wanneer twee grote feitentabellen worden samengevoegd.  Als u bijvoorbeeld een ordertabel hebt die is gedistribueerd op order_id, en een transactietabel die ook is gedistribueerd op order_id, en u de ordertabel met de transactietabel samenvoegt op order_id, wordt deze query een Pass-Through-query. Dit betekent dat bewerkingen voor gegevensverplaatsing worden voorkomen.  Minder stappen betekent een snellere query.  Minder gegevensverplaatsing maakt query’s ook sneller.  Deze uitleg maakt alleen een kras van het Opper vlak. Zorg er bij het laden van een distributietabel voor dat uw inkomende gegevens niet zijn gedistribueerd volgens de verdeelsleutel. Dit vertraagt het laden.  Raadpleeg de onderstaande koppelingen voor meer informatie over hoe het selecteren van een distributiekolom prestaties kan verbeteren en hoe u een distributietabel definieert in het WITH-component van uw CREATE TABLES-instructie.

Zie ook [Tabeloverzicht][Table overview], [Tabeldistributie][Table distribution], [Tabeldistributie selecteren][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Niet te veel partities maken
Partities voor uw gegevens maken kan zeer effectief zijn voor het bijhouden van gegevens door te schakelen tussen partities of het optimaliseren van scans door het schrappen van partities. Te veel partities kunnen echter uw query’s vertragen.  Een partitiestrategie met een hoge granulatie die goed werkt voor SQL Server werkt mogelijk niet goed voor SQL Data Warehouse.  Te veel partities kunnen ook geclusterde columnstore-indexen minder effectief maken als elke partitie minder dan 1 miljoen rijen bevat.  Achter de schermen verdeelt SQL Data Warehouse uw gegevens in partities op meer dan 60 databases. Als u een dus een tabel met 100 partities maakt, resulteert dit in werkelijkheid in 6000 partities.  Elke workload is verschillend, en daarom kunt u het beste experimenteren met partities om te zien wat het beste werkt voor uw workload.  Overweeg een lagere granulatie dan die in SQL Server misschien wel effectief was.  U zou bijvoorbeeld wekelijkse of maandelijkse partities kunnen gebruiken in plaats van dagelijkse partities.

Zie ook [Tabellen partitioneren][Table partitioning]

## <a name="minimize-transaction-sizes"></a>Transactiegrootten minimaliseren
De instructies INSERT, UPDATE en DELETE worden in een transactie uitgevoerd en wanneer ze mislukken, moeten ze worden teruggedraaid.  Om de kans op een lange terugdraaiactie te verkleinen, kunt u transactiegrootten minimaliseren waar mogelijk.  Dit kan door de instructies INSERT, UPDATE en DELETE op te delen.  Als u bijvoorbeeld een INSERT hebt waarvan u verwacht dat deze 1 uur duurt, verdeelt u de INSERT indien mogelijk in 4 delen, die elk in 15 minuten worden uitgevoerd.  Maak gebruik van speciale instructies voor minimale registratie, zoals CTAS, TRUNCATE, DROP TABLE en INSERT, om tabellen leeg te maken, zodat het risico op terugdraaien wordt verkleind.  Een andere manier om terugdraaiacties te voorkomen, is door alleen-metagegevensbewerkingen, zoals schakelen tussen partities, te gebruiken voor gegevensbeheer.  In plaats van een DELETE-instructie uit te voeren om alle rijen in een tabel met een order_date in oktober 2001 te verwijderen, kunt u bijvoorbeeld uw gegevens maandelijks partitioneren en vervolgens de partitie met gegevens omwisselen voor een lege partitie uit een andere tabel (zie voorbeelden ALTER TABLE).  Overweeg voor ongepartitioneerde tabellen een CTAS toe te passen om de gegevens die u wilt behouden naar een tabel te schrijven, in plaats van DELETE te gebruiken.  Als een CTAS net zo lang duurt, heeft dit de voorkeur, aangezien het een veel veiligere bewerking is dankzij de minimale transactieregistratie en snel kan worden geannuleerd, indien nodig.

Zie ook [Inzicht in transacties][Understanding transactions], [Transacties optimaliseren][Optimizing transactions], [Tabellen partitioneren][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="use-the-smallest-possible-column-size"></a>De kleinst mogelijke kolomgrootte gebruiken
Als u bij het definiëren van uw DDL het kleinste gegevenstype gebruikt dat uw gegevens ondersteunt, worden de prestaties van uw query's verbeterd.  Dit is met name belangrijk voor CHAR- en VARCHAR-kolommen.  Als de langste waarde in een kolom 25 tekens is, definieert u uw kolom als VARCHAR(25).  U kunt alle tekenkolommen beter niet volgens een grote standaardlengte definiëren.  Definieer daarnaast kolommen als VARCHAR als dat alles is wat nodig is in plaats van NVARCHAR.

Zie ook [Tabeloverzicht][Table overview], [Gegevenstypes tabel][Table data types], [CREATE TABLE][CREATE TABLE]

## <a name="optimize-clustered-columnstore-tables"></a>Geclusterde columnstore-tabellen optimaliseren
Geclusterde columnstore-indices vormen een van de meest efficiënte manieren om uw gegevens in SQL Data Warehouse te bewaren.  Tabellen worden in SQL Data Warehouse standaard als geclusterde columnstore gemaakt.  Een goede segmentkwaliteit is belangrijk om de beste resultaten te behalen voor query’s voor columnstore-tabellen.  Wanneer rijen naar columnstore-tabellen worden geschreven onder geheugendruk, kan dit ten koste gaan van de kwaliteit van columnstore-segmenten.  Segmentkwaliteit kan worden gemeten aan de hand van het aantal rijen in een gecomprimeerde rijengroep.  Zie [Oorzaken van slechte kwaliteit voor columnstore-indexen][Causes of poor columnstore index quality] in het artikel [Tabelindexen][Table indexes] voor stapsgewijze instructies voor het detecteren en verbeteren van segmentkwaliteit van geclusterde columnstore-tabellen.  Omdat column Store-segmenten van hoge kwaliteit belang rijk zijn, is het een goed idee om gebruikers-Id's te gebruiken die zich in de middel lange of grote resource klasse bevinden voor het laden van gegevens. Als u lagere [Data Warehouse-eenheden](what-is-a-data-warehouse-unit-dwu-cdwu.md) gebruikt, wilt u een grotere resource klasse toewijzen aan de gebruiker die u wilt laden.

Omdat columnstore-tabellen normaalgesproken gegevens niet naar een gecomprimeerd columnstore-segment doorzetten voordat er meer dan 1 miljoen rijen per tabel zijn en elke SQL Data Warehouse-tabel in 60 tabellen is gepartitioneerd, dragen columnstore-tabellen doorgaans niet bij aan een query tenzij de tabel meer dan 60 miljoen rijen heeft.  Voor tabellen met minder dan 60 miljoen rijen is het meestal niet nodig om een columnstore-index te hebben.  Het is misschien ook niet verkeerd.  Als u uw gegevens partitioneert, houd er dan ook rekening mee dat elke partitie 1 miljoen rijen nodig heeft om voordeel te halen uit een geclusterde columnstore-index.  Als een tabel 100 partities heeft, heeft deze ten minste 6 miljard rijen nodig om voordeel te halen uit een geclusterde columnstore (60 distributies * 100 partities * 1 miljoen rijen).  Als uw tabel in dit voorbeeld geen 6 miljoen rijen heeft, kunt u het aantal partities verminderen of overwegen een heap-tabel te gebruiken.  U kunt ook experimenteren om te zien of u de prestaties kunt verbeteren met een heap-tabel met secondaire sleutels in plaats van een columnstore-tabel.

Query’s worden sneller uitgevoerd voor een columnstore-tabel als u alleen de kolommen selecteert die u nodig hebt.  

Zie ook [Tabel-indexen][Table indexes], [Gids columnstore-indexen][Columnstore indexes guide], [Columnstore-indexen herbouwen][Rebuilding columnstore indexes]

## <a name="next-steps"></a>Volgende stappen
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
