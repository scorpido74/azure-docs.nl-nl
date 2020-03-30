---
title: Best practices voor ontwikkeling
description: Aanbevelingen en best practices die u moet kennen bij het ontwikkelen van oplossingen voor Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a4902d2817c98b002b9767ad55d7ac60f5cb0c93
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350713"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Best practices ontwikkelen voor Synapse SQL-pool
In dit artikel worden richtlijnen en best practices beschreven bij het ontwikkelen van uw datawarehouse-oplossing. 

## <a name="tune-query-performance-with-new-product-enhancements"></a>Queryprestaties afstemmen met nieuwe productverbeteringen  
- [Prestaties afstemmen met gerealiseerde weergaven](performance-tuning-materialized-views.md)
- [Prestaties afstemmen met geordende en geclusterde columnstore-index](performance-tuning-ordered-cci.md)
- [Prestaties afstemmen door resultatensets op te slaan in de cache](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Kosten verlagen met onderbreken en schalen
Zie [Compute beheren](sql-data-warehouse-manage-compute-overview.md) voor meer informatie over kostenverlaging via onderbreking en schaling. 

## <a name="maintain-statistics"></a>Statistieken bijhouden
Synapse SQL-pool kan worden geconfigureerd om automatisch statistieken over kolommen te detecteren en te maken.  De queryplannen die door de optimizer zijn gemaakt, zijn slechts zo goed als de beschikbare statistieken.  We raden u aan AUTO_CREATE_STATISTICS voor uw databases in te schakelen en de statistieken dagelijks of na elke belasting up-to-date te houden om ervoor te zorgen dat de statistieken over kolommen die in uw query's worden gebruikt, altijd up-to-date zijn. 

Als u merkt dat het te lang duurt om al uw statistieken bij te werken, wilt u misschien proberen selectiever te zijn over welke kolommen frequente statistische updates nodig hebben. Zo wilt u datumkolommen, waar nieuwe gegevens kunnen zijn toegevoegd, misschien dagelijks bijwerken. **U profiteert het meeste voordeel door bijgewerkte statistieken te hebben over kolommen die betrokken zijn bij joins, kolommen die worden gebruikt in de WHERE-component en kolommen in GROUP BY.**

Zie ook [Tabelstatistieken beheren](sql-data-warehouse-tables-statistics.md), [CREATE STATISTICS](sql-data-warehouse-tables-statistics.md), [UPDATE STATISTICS](sql-data-warehouse-tables-statistics.md#updating-statistics)

## <a name="hash-distribute-large-tables"></a>Grote tabellen distribueren met hash
Tabellen worden standaard gedistribueerd middels Round Robin.  Dit ontwerp maakt het gemakkelijk voor gebruikers om aan de slag te gaan met het maken van tabellen zonder te hoeven beslissen hoe hun tabellen moeten worden gedistribueerd.  Round Robin-tabellen leveren voldoende prestaties voor sommige workloads, maar in de meeste gevallen leidt het selecteren van een distributiekolom tot veel betere prestaties.  Het meestvoorkomende geval waarin een tabel die per kolom is gedistribueerd veel beter presteert dan een Round Robin-tabel, is wanneer twee grote feitentabellen worden samengevoegd.  Als u bijvoorbeeld een ordertabel hebt die is gedistribueerd op order_id, en een transactietabel die ook is gedistribueerd op order_id, en u de ordertabel met de transactietabel samenvoegt op order_id, wordt deze query een Pass-Through-query. Dit betekent dat bewerkingen voor gegevensverplaatsing worden voorkomen.  Minder stappen betekent een snellere query.  Minder gegevensverplaatsing maakt query’s ook sneller.  Deze uitleg alleen krassen op het oppervlak. Zorg er bij het laden van een distributietabel voor dat uw inkomende gegevens niet zijn gedistribueerd volgens de verdeelsleutel. Dit vertraagt het laden.  Zie de onderstaande koppelingen voor veel meer informatie over hoe het selecteren van een distributiekolom de prestaties kan verbeteren en hoe u een gedistribueerde tabel definiëren in de met-component van de instructie TABELLEN MAKEN.

Zie ook [Tabeloverzicht](sql-data-warehouse-tables-overview.md), [Tabeldistributie](sql-data-warehouse-tables-distribute.md), [Tabeldistributie selecteren](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](sql-data-warehouse-tables-overview.md), [CREATE TABLE AS SELECT](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>Niet te veel partities maken
Hoewel het partitioneren van gegevens effectief kan zijn voor het bijhouden van uw gegevens door het schakelen van partities of het optimaliseren van scans door met partitieverwijdering, kan het hebben van te veel partities uw query's vertragen.  Vaak werkt een hoge granulariteitspartitioneringsstrategie die goed kan werken op SQL Server mogelijk niet goed op SQL-pool.  Te veel partities kunnen ook geclusterde columnstore-indexen minder effectief maken als elke partitie minder dan 1 miljoen rijen bevat.  Houd er rekening mee dat SQL-pool achter de schermen uw gegevens voor u in 60 databases verdeelt, dus als u een tabel met 100 partities maakt, resulteert dit in 6000 partities.  Elke workload is verschillend, en daarom kunt u het beste experimenteren met partities om te zien wat het beste werkt voor uw workload.  Overweeg een lagere granulatie dan die in SQL Server misschien wel effectief was.  U zou bijvoorbeeld wekelijkse of maandelijkse partities kunnen gebruiken in plaats van dagelijkse partities.

Zie ook [Tabellen partitioneren](sql-data-warehouse-tables-partition.md)

## <a name="minimize-transaction-sizes"></a>Transactiegrootten minimaliseren
De instructies INSERT, UPDATE en DELETE worden in een transactie uitgevoerd en wanneer ze mislukken, moeten ze worden teruggedraaid.  Om de kans op een lange terugdraaiactie te verkleinen, kunt u transactiegrootten minimaliseren waar mogelijk.  Dit kan door de instructies INSERT, UPDATE en DELETE op te delen.  Als u bijvoorbeeld een INSERT hebt, waarvan u verwacht dat deze 1 uur duurt, wanneer dat mogelijk is, u de INSERT opsplitsen in vier delen, die elk in 15 minuten worden uitgevoerd.  Maak gebruik van speciale instructies voor minimale registratie, zoals CTAS, TRUNCATE, DROP TABLE en INSERT, om tabellen leeg te maken, zodat het risico op terugdraaien wordt verkleind.  Een andere manier om terugdraaiacties te voorkomen, is door alleen-metagegevensbewerkingen, zoals schakelen tussen partities, te gebruiken voor gegevensbeheer.  In plaats van een DELETE-instructie uit te voeren om alle rijen in een tabel met een order_date in oktober 2001 te verwijderen, kunt u bijvoorbeeld uw gegevens maandelijks partitioneren en vervolgens de partitie met gegevens omwisselen voor een lege partitie uit een andere tabel (zie voorbeelden ALTER TABLE).  Overweeg voor ongepartitioneerde tabellen een CTAS toe te passen om de gegevens die u wilt behouden naar een tabel te schrijven, in plaats van DELETE te gebruiken.  Als een CTAS even veel tijd in beslag neemt, is het een veel veiligere bewerking om uit te voeren omdat deze minimale transactielogboekregistratie heeft en indien nodig snel kan worden geannuleerd.

Zie ook [Transacties begrijpen,](sql-data-warehouse-develop-transactions.md) [Transacties optimaliseren](sql-data-warehouse-develop-best-practices-transactions.md), [Tabelpartitionering,](sql-data-warehouse-tables-partition.md) [TRUNCATE-tabel](https://msdn.microsoft.com/library/ms177570.aspx), [TABEL WIJZIGEN](https://msdn.microsoft.com/library/ms190273.aspx), Tabel maken als [selecteren (CTAS).](sql-data-warehouse-develop-ctas.md)

## <a name="use-the-smallest-possible-column-size"></a>De kleinst mogelijke kolomgrootte gebruiken
Wanneer u uw DDL definieert, verbetert het gebruik van het kleinste gegevenstype dat uw gegevens ondersteunt, de queryprestaties.  Dit is met name belangrijk voor CHAR- en VARCHAR-kolommen.  Als de langste waarde in een kolom 25 tekens is, definieert u uw kolom als VARCHAR(25).  U kunt alle tekenkolommen beter niet volgens een grote standaardlengte definiëren.  Definieer daarnaast kolommen als VARCHAR als dat alles is wat nodig is in plaats van NVARCHAR.

Zie ook [Tabeloverzicht](sql-data-warehouse-tables-overview.md), [Gegevenstypes tabel](sql-data-warehouse-tables-data-types.md), [CREATE TABLE](sql-data-warehouse-tables-overview.md)

## <a name="optimize-clustered-columnstore-tables"></a>Geclusterde columnstore-tabellen optimaliseren
Geclusterde columnstore-indexen zijn een van de meest efficiënte manieren waarop u uw gegevens opslaan in SQL-groep.  Tabellen in SQL-groep worden standaard gemaakt als Clustered ColumnStore.  Een goede segmentkwaliteit is belangrijk om de beste resultaten te behalen voor query’s voor columnstore-tabellen.  Wanneer rijen naar columnstore-tabellen worden geschreven onder geheugendruk, kan dit ten koste gaan van de kwaliteit van columnstore-segmenten.  Segmentkwaliteit kan worden gemeten aan de hand van het aantal rijen in een gecomprimeerde rijengroep.  Zie [Oorzaken van slechte kwaliteit voor columnstore-indexen](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) in het artikel [Tabelindexen](sql-data-warehouse-tables-index.md) voor stapsgewijze instructies voor het detecteren en verbeteren van segmentkwaliteit van geclusterde columnstore-tabellen.  Omdat kolomarchiefsegmenten van hoge kwaliteit belangrijk zijn, is het een goed idee om gebruikers-id's te gebruiken die zich in de middelgrote of grote resourceklasse bevinden voor het laden van gegevens. Als u lagere [gegevensmagazijnen gebruikt,](what-is-a-data-warehouse-unit-dwu-cdwu.md) wilt u een grotere resourceklasse toewijzen aan uw beladingsgebruiker.

Aangezien kolomarchieftabellen over het algemeen geen gegevens in een gecomprimeerd kolomarchiefsegment pushen totdat er meer dan 1 miljoen rijen per tabel zijn en elke SQL-pooltabel is verdeeld in 60 tabellen, komen kolomarchieftabellen als vuistregel niet ten goede aan een query, tenzij de tabel heeft meer dan 60 miljoen rijen.  Voor tabellen met minder dan 60 miljoen rijen is het meestal niet nodig om een columnstore-index te hebben.  Het is misschien ook niet verkeerd.  Als u uw gegevens partitioneert, houd er dan ook rekening mee dat elke partitie 1 miljoen rijen nodig heeft om voordeel te halen uit een geclusterde columnstore-index.  Als een tabel 100 partities heeft, heeft deze ten minste 6 miljard rijen nodig om voordeel te halen uit een geclusterde columnstore (60 distributies * 100 partities * 1 miljoen rijen).  Als uw tabel in dit voorbeeld geen 6 miljoen rijen heeft, kunt u het aantal partities verminderen of overwegen een heap-tabel te gebruiken.  U kunt ook experimenteren om te zien of u de prestaties kunt verbeteren met een heap-tabel met secondaire sleutels in plaats van een columnstore-tabel.

Query’s worden sneller uitgevoerd voor een columnstore-tabel als u alleen de kolommen selecteert die u nodig hebt.  

Zie ook [Tabel-indexen](sql-data-warehouse-tables-index.md), [Gids columnstore-indexen](https://msdn.microsoft.com/library/gg492088.aspx), [Columnstore-indexen herbouwen](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="next-steps"></a>Volgende stappen
Als u niet vindt wat u zoekt in dit artikel, probeert u de 'Zoeken naar documenten' aan de linkerkant van deze pagina te gebruiken om alle Azure Synapse Analytics-documenten te doorzoeken.  Het [Azure SQL Data Warehouse Forum](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) is een plek waar u vragen stellen aan andere gebruikers en aan de SQL Data Warehouse Product Group.  We controleren het forum regelmatig om er zeker van te zijn dat uw vragen worden beantwoord door een andere gebruiker of een van ons.  We hebben ook een [Stack Overflow-forum Azure SQL Data Warehouse](https://stackoverflow.com/questions/tagged/azure-sqldw) voor als u uw vragen liever in Stack Overflow stelt.

Tenslotte willen we u vragen de pagina [Azure SQL Data Warehouse Feedback](https://feedback.azure.com/forums/307516-sql-data-warehouse) te gebruiken om functieverbeteringen aan te vragen.  Het toevoegen van aanvragen of het stemmen op andere aanvragen helpt ons prioriteit te geven aan bepaalde functies.


