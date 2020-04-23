---
title: Best practices ontwikkelen voor Synapse SQL
description: Aanbevelingen en best practices die u moet kennen terwijl u zich ontwikkelt voor Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: ed2638cfe4ab7e849e428729ccd17ffdeb6314af
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086348"
---
# <a name="development-best-practices-for-synapse-sql"></a>Best practices ontwikkelen voor Synapse SQL
In dit artikel worden richtlijnen en best practices beschreven bij het ontwikkelen van uw datawarehouse-oplossing. 

## <a name="sql-pool-development-best-practices"></a>Best practices voor sql-poolontwikkeling

### <a name="reduce-cost-with-pause-and-scale"></a>Kosten verlagen met onderbreken en schalen

Zie [Compute beheren](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor meer informatie over kostenverlaging via onderbreking en schaling.

### <a name="maintain-statistics"></a>Statistieken bijhouden

Zorg ervoor dat u uw statistieken dagelijks of na elke lading bijwerkt.  Het maken en bijwerken van statistieken kan ten koste gaan van prestaties en kosten. Als u merkt dat het te lang duurt om al uw statistieken bij te houden, wees dan selectiever over welke kolommen statistieken hebben of welke kolommen regelmatig moeten worden bijgewerkt.  

U bijvoorbeeld datumkolommen bijwerken, waarbij dagelijks nieuwe waarden kunnen worden toegevoegd. 

> [!NOTE]
> U profiteert het meeste door statistieken te hebben over kolommen die betrokken zijn bij joins, kolommen die worden gebruikt in de WHERE-component en kolommen in GROEP BY.

Zie ook [Tabelstatistieken beheren](develop-tables-statistics.md), [STATISTIEKEN MAKEN](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [STATISTIEKEN BIJWERKEN](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="hash-distribute-large-tables"></a>Grote tabellen distribueren met hash

Tabellen worden standaard gedistribueerd middels Round Robin.  Dit maakt het gemakkelijk voor gebruikers om tabellen te maken zonder te hoeven beslissen hoe hun tabellen moeten worden gedistribueerd.  Ronde Robin-tabellen kunnen voldoende presteren voor sommige workloads. Maar in de meeste gevallen zal het selecteren van een distributiekolom veel beter presteren.  

Het meestvoorkomende geval waarin een tabel die per kolom is gedistribueerd veel beter presteert dan een Round Robin-tabel, is wanneer twee grote feitentabellen worden samengevoegd.  

Als u bijvoorbeeld een ordertabel hebt, die wordt gedistribueerd per order_id, en een transactietabel, die ook wordt verdeeld over order_id, wanneer u uw ordertabel aan uw transactietabel op order_id, wordt deze query een doorgeefquery. 

Dit betekent dat we gegevensverplaatsingen elimineren.  Minder stappen betekent een snellere query.  Minder gegevensverplaatsing maakt query’s ook sneller.

> [!TIP]
> Zorg er bij het laden van een distributietabel voor dat uw inkomende gegevens niet zijn gedistribueerd volgens de verdeelsleutel. Dit vertraagt het laden.  

Zie de volgende koppelingen voor meer informatie over hoe het selecteren van een distributiekolom de prestaties kan verbeteren en hoe u een gedistribueerde tabel definiëren in de met-component van de instructie TABELLEN MAKEN.

Zie ook [Tabeloverzicht](develop-tables-overview.md), [Tabelverdeling](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Tabeldistributie selecteren,](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/) [TABEL MAKEN](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en [TABEL MAKEN ALS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="do-not-over-partition"></a>Niet te veel partities maken
Hoewel het partitioneren van gegevens effectief kan zijn voor het bijhouden van uw gegevens door het schakelen van partities of het optimaliseren van scans door met partitieverwijdering, kan het hebben van te veel partities uw query's vertragen.  Vaak werkt een hoge granulariteitspartitioneringsstrategie die goed kan werken op SQL Server mogelijk niet goed op SQL-pool.  

> [!NOTE]
> Vaak werkt een hoge granulariteitspartitioneringsstrategie die goed kan werken op SQL Server mogelijk niet goed op SQL-pool.  

Te veel partities kunnen ook geclusterde columnstore-indexen minder effectief maken als elke partitie minder dan 1 miljoen rijen bevat. SQL-groep partities van uw gegevens voor u in 60 databases. 

Dus als u een tabel met 100 partities maakt, zijn het resultaat 6000 partities.  Elke workload is verschillend, en daarom kunt u het beste experimenteren met partities om te zien wat het beste werkt voor uw workload.  

Een optie om te overwegen is het gebruik van een granulariteit die lager is dan wat mogelijk voor u heeft gewerkt in SQL Server.  U zou bijvoorbeeld wekelijkse of maandelijkse partities kunnen gebruiken in plaats van dagelijkse partities.

Zie ook [Tabelpartitionering](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="minimize-transaction-sizes"></a>Transactiegrootten minimaliseren

De instructies INSERT, UPDATE en DELETE worden in een transactie uitgevoerd en wanneer ze mislukken, moeten ze worden teruggedraaid.  Om de kans op een lange terugdraaiactie te verkleinen, kunt u transactiegrootten minimaliseren waar mogelijk.  Dit kan door de instructies INSERT, UPDATE en DELETE op te delen.  

Als u bijvoorbeeld een INSERT hebt waarvan u verwacht dat deze 1 uur duurt, u de INSERT in vier delen opsplitsen, waardoor elke run wordt ingekort tot 15 minuten.

> [!TIP]
> Maak gebruik van speciale instructies voor minimale registratie, zoals CTAS, TRUNCATE, DROP TABLE en INSERT, om tabellen leeg te maken, zodat het risico op terugdraaien wordt verkleind.  

Een andere manier om terugdraaiacties te voorkomen, is door alleen-metagegevensbewerkingen, zoals schakelen tussen partities, te gebruiken voor gegevensbeheer.  

In plaats van een DELETE-instructie uit te voeren om alle rijen in een tabel met een order_date in oktober 2001 te verwijderen, kunt u bijvoorbeeld uw gegevens maandelijks partitioneren en vervolgens de partitie met gegevens omwisselen voor een lege partitie uit een andere tabel (zie voorbeelden ALTER TABLE).  

Overweeg voor ongepartitioneerde tabellen een CTAS toe te passen om de gegevens die u wilt behouden naar een tabel te schrijven, in plaats van DELETE te gebruiken.  Als een CTAS net zo lang duurt, heeft dit de voorkeur, aangezien het een veel veiligere bewerking is dankzij de minimale transactieregistratie en snel kan worden geannuleerd, indien nodig.

Zie ook [Transacties begrijpen,](develop-transactions.md) [Transacties optimaliseren,](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [Tabelpartitionering,](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [TRUNCATE-tabel,](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [WIJZIGINGSTABEL](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en [Tabel maken als selecteren (CTAS).](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="use-the-smallest-possible-column-size"></a>De kleinst mogelijke kolomgrootte gebruiken

Als u bij het definiëren van uw DDL het kleinste gegevenstype gebruikt dat uw gegevens ondersteunt, worden de prestaties van uw query's verbeterd.  Dit is met name belangrijk voor CHAR- en VARCHAR-kolommen.  

Als de langste waarde in een kolom 25 tekens is, definieert u uw kolom als VARCHAR(25).  U kunt alle tekenkolommen beter niet volgens een grote standaardlengte definiëren.  Definieer daarnaast kolommen als VARCHAR als dat alles is wat nodig is in plaats van NVARCHAR.

Zie ook [Tabeloverzicht](develop-tables-overview.md), [Tabelgegevenstypen](develop-tables-data-types.md)en [TABEL MAKEN](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="optimize-clustered-columnstore-tables"></a>Geclusterde columnstore-tabellen optimaliseren

Geclusterde columnstore-indexen zijn een van de meest efficiënte manieren waarop u uw gegevens opslaan in SQL-groep.  Tabellen in SQL-groep worden standaard gemaakt als Clustered ColumnStore.  

Een goede segmentkwaliteit is belangrijk om de beste resultaten te behalen voor query’s voor columnstore-tabellen.  Wanneer rijen naar columnstore-tabellen worden geschreven onder geheugendruk, kan dit ten koste gaan van de kwaliteit van columnstore-segmenten.  

Segmentkwaliteit kan worden gemeten aan de hand van het aantal rijen in een gecomprimeerde rijengroep.  Zie het artikel Over de oorzaken van slechte indexkwaliteit van [kolomarchiefen](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) [en tabelindexen](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor stapsgewijze instructies voor het detecteren en verbeteren van de segmentkwaliteit voor geclusterde kolomarchieftabellen.  

Omdat kolomarchiefsegmenten van hoge kwaliteit belangrijk zijn, is het een goed idee om gebruikers-id's te gebruiken die zich in de middelgrote of grote resourceklasse bevinden voor het laden van gegevens. Als u lagere [gegevensmagazijnen gebruikt,](resource-consumption-models.md) wilt u een grotere resourceklasse toewijzen aan uw beladingsgebruiker.

Aangezien kolomarchieftabellen over het algemeen geen gegevens in een gecomprimeerd kolomarchiefsegment pushen totdat er meer dan 1 miljoen rijen per tabel zijn en elke SQL-pooltabel is verdeeld in 60 tabellen, komen kolomopslagtabellen niet ten goede aan een query, tenzij de tabel meer dan 60 miljoen rijen heeft.  

> [!TIP]
> Voor tabellen met minder dan 60 miljoen rijen is het hebben van een columstore-index mogelijk niet de optimale oplossing.  

Als u uw gegevens partitioneert, houd er dan ook rekening mee dat elke partitie 1 miljoen rijen nodig heeft om voordeel te halen uit een geclusterde columnstore-index.  Als een tabel 100 partities heeft, moet deze ten minste 6 miljard rijen hebben om te profiteren van een geclusterde kolommenopslag (60 distributies *100 partities* 1 miljoen rijen).  

Als uw tabel geen 6 miljard rijen heeft, vermindert u het aantal partities of overweegt u in plaats daarvan een heaptabel te gebruiken.  Het kan ook de moeite waard zijn om te experimenteren om te zien of betere prestaties kunnen worden behaald met behulp van een heap tabel met secundaire indexen in plaats van een columnstore tabel.

Query’s worden sneller uitgevoerd voor een columnstore-tabel als u alleen de kolommen selecteert die u nodig hebt.  

Zie ook [Tabelindexen](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Kolomarchiefindexengids](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Indexen van kolomarchief opnieuw opbouwen](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality).

## <a name="sql-on-demand-development-best-practices"></a>BEST Practices voor SQL on-demand ontwikkeling

### <a name="general-considerations"></a>Algemene overwegingen

Met SQL on-demand u bestanden opvragen in uw Azure-opslagaccounts. Het heeft geen lokale opslag- of opnamemogelijkheden, wat betekent dat alle bestanden die de querydoelen buiten SQL on-demand zijn. Daarom kan alles met betrekking tot het lezen van bestanden uit de opslag een impact hebben op de queryprestaties.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Azure Storage-account en SQL on-demand colocate

Als u de latentie wilt minimaliseren, moet u uw Azure-opslagaccount en uw SQL-eindpunt voor on-demand co-lokaliseren. Opslagaccounts en eindpunten die tijdens het maken van werkruimtes zijn ingericht, bevinden zich in dezelfde regio.

Als u voor optimale prestaties toegang hebt tot andere opslagaccounts met SQL on-demand, controleert u of ze zich in dezelfde regio bevinden. Anders wordt de latentie verhoogd voor de netwerkoverdracht van de gegevens van het externe gebied naar de regio van het eindpunt.

### <a name="azure-storage-throttling"></a>Beperking van Azure-opslag

Meerdere toepassingen en services hebben toegang tot uw opslagaccount. Wanneer de gecombineerde IOPS- of doorvoerdoorvoer gegenereerd door toepassingen, services en SQL on-demand workload de grenzen van het opslagaccount overschrijdt, vindt opslagbeperking plaats. Wanneer opslagbeperking optreedt, is er een aanzienlijk negatief effect op de queryprestaties.

Zodra throttling is gedetecteerd, sql on-demand heeft ingebouwde behandeling van dit scenario. SQL on-demand zal aanvragen voor opslag in een langzamer tempo totdat throttling is opgelost. 

Voor een optimale queryuitvoering wordt u echter aangeraden het opslagaccount niet te belasten met andere workloads tijdens de uitvoering van query's.

### <a name="prepare-files-for-querying"></a>Bestanden voorbereiden voor query's

Indien mogelijk u bestanden voorbereiden op betere prestaties:

- CSV converteren naar parket – Parket is kolomindeling. Omdat het wordt gecomprimeerd, heeft het kleinere bestandsformaten dan CSV-bestanden met dezelfde gegevens en heeft SQL on-demand minder tijd en opslagaanvragen nodig om het te lezen.
- Als een query zich richt op één groot bestand, profiteert u van het splitsen ervan in meerdere kleinere bestanden.
- Probeer de grootte van uw CSV-bestand onder de 10 GB te houden.
- Het heeft de voorkeur om bestanden van gelijke grootte te hebben voor één OPENROWSET-pad of een externe tabelLOCATIE.
- Partitie van uw gegevens door partities op te slaan in verschillende mappen of bestandsnamen - controleer [bestandsnaam en bestandspadfuncties gebruiken om specifieke partities te targeten.](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Bestandsinfo- en bestandspadfuncties gebruiken om specifieke partities te targeten

Gegevens worden vaak georganiseerd in partities. U SQL on-demand instrueren om bepaalde mappen en bestanden op te vragen. Dit vermindert het aantal bestanden en de hoeveelheid gegevens die de query moet lezen en verwerken. 

Hierdoor zult u betere prestaties bereiken. Voor meer informatie, controleer [bestandsnaam](develop-storage-files-overview.md#filename-function) en [bestandspad](develop-storage-files-overview.md#filepath-function) functies en voorbeelden over hoe [u specifieke bestanden opvraagt](query-specific-files.md).

Als uw gegevens in de opslag niet zijn verdeeld, u overwegen deze te partitioneren, zodat u deze functies gebruiken om query's te optimaliseren die zijn gericht op die bestanden.

Wanneer de query partitiesparktabellen op SQL on-demand [opvraagt,](develop-storage-files-spark-tables.md) wordt de query automatisch alleen op bestanden gericht die nodig zijn.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CetAS gebruiken om de queryprestaties en joins te verbeteren

[CETAS](develop-tables-cetas.md) is een van de belangrijkste functies die beschikbaar zijn in SQL on-demand. CETAS is een parallelle bewerking die externe tabelmetagegevens maakt en het resultaat van de SELECT-query exporteert naar een set bestanden in uw opslagaccount.

U CETAS gebruiken om veelgebruikte delen van query's, zoals samengevoegde referentietabellen, op te slaan in een nieuwe set bestanden. Later u deelnemen aan deze ene externe tabel in plaats van algemene joins in meerdere query's te herhalen. 

Aangezien CETAS parketbestanden genereert, worden statistieken automatisch gemaakt wanneer de eerste query zich richt op deze externe tabel en krijgt u betere prestaties.

### <a name="next-steps"></a>Volgende stappen

Als u informatie nodig hebt die niet in dit artikel wordt verstrekt, gebruikt u de 'Zoeken naar documenten' aan de linkerkant van deze pagina om alle SQL-pooldocumenten te doorzoeken.  Het [SQL-poolforum](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) is een plek voor u om vragen te stellen aan andere gebruikers en aan de SQL-pool Product Group.  

We controleren het forum regelmatig om er zeker van te zijn dat uw vragen worden beantwoord door een andere gebruiker of een van ons.  Als u uw vragen liever stelt over Stack Overflow, hebben we ook een [Azure SQL-pool Stack Overflow Forum.](https://stackoverflow.com/questions/tagged/azure-sqldw)
 