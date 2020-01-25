---
title: Architectuur van Azure Synapse Analytics (voorheen SQL DW)
description: Meer informatie over hoe Azure Synapse Analytics (voorheen SQL DW) enorm parallelle verwerking (MPP) combineert met Azure Storage om hoge prestaties en schaal baarheid te garanderen.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d808210861d971b2915206e7be0fe9b955616c5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720313"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Architectuur van Azure Synapse Analytics (voorheen SQL DW) 

Azure Synapse is een oneindige analyseservice die bedrijfsgegevensopslag en big data-analyses combineert. Dit geeft u de vrijheid om geheel naar eigen wens op schaal gegevens op te vragen, met behulp van serverloze on-demand of ingerichte resources. In Azure Synapse komen deze werelden bij elkaar met een uniforme ervaring om gegevens op te nemen, voor te bereiden, te beheren en te leveren voor directe BI- en machine learning-behoeften.

 Azure Synapse heeft vier onderdelen:
- SQL Analytics: op T-SQL gebaseerde analyses volt ooien 
    - SQL-groep (betalen per DWU ingericht): algemeen beschikbaar
    - SQL on-demand (betalen per TB verwerkte) – (preview-versie)
- Spark: diep geïntegreerde Apache Spark (preview-versie) 
- Gegevens integratie: hybride gegevens integratie (preview-versie)
- Studio: uniforme gebruikers ervaring.  (Preview)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="sql-analytics-mpp-architecture-components"></a>Onderdelen van SQL Analytics MPP-architectuur

[SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) maakt gebruik van een scale-out architectuur om reken kundige verwerking van gegevens over meerdere knoop punten te distribueren. De schaal eenheid is een abstractie van reken kracht die wordt aangeduid als [Data Warehouse-eenheid](what-is-a-data-warehouse-unit-dwu-cdwu.md). Compute staat los van de opslag, waarmee u de reken kracht onafhankelijk van de gegevens in uw systeem kunt schalen.

![Architectuur van SQL Analytics](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Analytics maakt gebruik van een architectuur op basis van een knoop punt. Toepassingen maken verbinding met T-SQL-opdrachten en geven ze aan een besturings element knoop punt. Dit is het enige invoer punt voor SQL Analytics. Het controle knooppunt voert de MPP-engine uit, waarmee query's voor parallelle verwerking worden geoptimaliseerd. vervolgens worden bewerkingen aan reken knooppunten door gegeven om hun werk parallel uit te voeren. 

De reken knooppunten slaan alle gebruikers gegevens op in Azure Storage en voeren parallelle query's uit. De gegevens verplaatsings service (DMS) is een interne service op systeem niveau waarmee gegevens over de knoop punten zo nodig worden verplaatst om query's parallel uit te voeren en nauw keurige resultaten te retour neren. 

Met een ontkoppelde opslag en reken kracht kunt u met behulp van SQL Analytics één het volgende doen:

* Reken kracht onafhankelijk van de grootte van uw opslag behoeften.
* De reken kracht binnen een SQL-groep (Data Warehouse) verg Roten of verkleinen zonder gegevens te verplaatsen.
* De reken capaciteit onderbreken terwijl de gegevens intact blijven, zodat u alleen betaalt voor opslag.
* De rekencapaciteit hervatten tijdens werktijden.

### <a name="azure-storage"></a>Azure Storage

SQL Analytics maakt gebruik van Azure Storage om uw gebruikers gegevens veilig te maken.  Omdat uw gegevens worden opgeslagen en beheerd door Azure Storage, worden er afzonderlijke kosten in rekening gebracht voor uw opslag verbruik. De gegevens worden in **distributies** Shard om de prestaties van het systeem te optimaliseren. U kunt kiezen welk sharding-patroon u wilt gebruiken om de gegevens te distribueren wanneer u de tabel definieert. Deze sharding-patronen worden ondersteund:

* Hash
* Round robin
* Repliceren

### <a name="control-node"></a>Knoop punt beheren

Het knoop punt beheer is het brein van de architectuur. Het is de front-end met interactie met alle toepassingen en verbindingen. De MPP-engine wordt uitgevoerd op het beheer knooppunt om parallelle query's te optimaliseren en te coördineren. Wanneer u een T-SQL-query naar SQL Analytics verzendt, transformeert het knoop punt beheer deze in query's die parallel op elke distributie worden uitgevoerd.

### <a name="compute-nodes"></a>Reken knooppunten

De reken knooppunten bieden de reken kracht. Distributies worden toegewezen aan reken knooppunten voor verwerking. Wanneer u betaalt voor meer reken bronnen, wijst SQL Analytics de distributies opnieuw toe aan de beschik bare reken knooppunten. Het aantal Compute-knoop punten ligt tussen 1 en 60, en wordt bepaald door het service niveau voor SQL Analytics.

Elk Compute-knoop punt heeft een knoop punt-ID die zichtbaar is in systeem weergaven. U kunt de ID van het reken knooppunt zien door te zoeken naar de node_id kolom in systeem weergaven waarvan de naam begint met sys. pdw_nodes. Zie [MPP System views](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7)(Engelstalig) voor een lijst met deze systeem weergaven.

### <a name="data-movement-service"></a>Gegevens verplaatsings service
Gegevens verplaatsings service (DMS) is de gegevens transport technologie die de gegevens verplaatsing tussen de reken knooppunten coördineert. Voor sommige query's is gegevens verplaatsing vereist om ervoor te zorgen dat de parallelle query's accurate resultaten retour neren. Wanneer gegevens verplaatsing vereist is, zorgt DMS ervoor dat de juiste gegevens naar de juiste locatie worden opgehaald. 

## <a name="distributions"></a>Distributies

Een distributie is de basis eenheid voor opslag en verwerking voor parallelle query's die worden uitgevoerd op gedistribueerde gegevens. Wanneer met SQL Analytics een query wordt uitgevoerd, wordt het werk onderverdeeld in 60 kleinere query's die parallel worden uitgevoerd. 

Elk van de 60 kleinere query's worden uitgevoerd op een van de gegevens distributies. Elk Compute-knoop punt beheert een of meer van de 60-distributies. Een SQL-groep met maximale Compute-resources heeft één distributie per reken knooppunt. Een SQL-groep met minimale Compute-resources heeft alle distributies op één reken knooppunt.  

## <a name="hash-distributed-tables"></a>Hash-gedistribueerde tabellen
Een gedistribueerde hash-tabel kan de hoogste query prestaties bieden voor samen voegingen en aggregaties op grote tabellen. 

Als u gegevens wilt Shard in een hash-gedistribueerde tabel, gebruikt SQL Analytics een hash-functie om elke rij per onbewerkte toewijzing toe te wijzen aan één verdeling. In de tabel definitie wordt een van de kolommen aangeduid als de kolom distributie. De hash-functie gebruikt de waarden in de kolom distributie om elke rij toe te wijzen aan een distributie.

In het volgende diagram ziet u hoe een volledige (niet-gedistribueerde tabel) wordt opgeslagen als een hash-Distributed Table. 

![Gedistribueerde tabel](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Gedistribueerde tabel")  

* Elke rij behoort tot één distributie.  
* Een deterministische hash-algoritme wijst elke rij toe aan één verdeling.  
* Het aantal tabel rijen per distributie varieert, zoals wordt weer gegeven in de verschillende grootten van tabellen.

Er zijn prestatie overwegingen voor de selectie van een distributie kolom, zoals de distinctiteit, de schei ding van gegevens en de typen query's die op het systeem worden uitgevoerd.

## <a name="round-robin-distributed-tables"></a>Round-Robin gedistribueerde tabellen
Een Round Robin-tabel is de eenvoudigste tabel om snelle prestaties te maken en op te halen wanneer deze wordt gebruikt als faserings tabel voor belasting.

Een met Round Robin gedistribueerde tabel distribueert gegevens gelijkmatig over de tabel, maar zonder verdere optimalisatie. Een distributie wordt in wille keurige volg orde gekozen en vervolgens worden buffers van rijen opeenvolgend toegewezen aan distributies. Het is snel om gegevens te laden in een Round-Robin tabel, maar de prestaties van query's kunnen vaak beter zijn bij gedistribueerde hash-tabellen. Voor het samen voegen van Round-Robin tabellen zijn gegevens met een andere volg orde nodig. dit duurt meer tijd.


## <a name="replicated-tables"></a>Gerepliceerde tabellen
Een gerepliceerde tabel biedt de snelste query prestaties voor kleine tabellen.

Een tabel die wordt gerepliceerd, plaatst een volledige kopie van de tabel op elk reken knooppunt. Daarom verwijdert het repliceren van een tabel de nood zaak om gegevens over te dragen tussen reken knooppunten vóór een samen voeging of aggregatie. Gerepliceerde tabellen worden het meest gebruikt met kleine tabellen. Extra opslag ruimte is vereist en er worden extra overhead kosten in rekening gebracht voor het schrijven van gegevens, waardoor grote tabellen onbruikbaar worden.  

In het onderstaande diagram ziet u een gerepliceerde tabel die in de cache wordt opgeslagen op de eerste distributie op elk reken knooppunt.  

![Gerepliceerde tabel](media/sql-data-warehouse-distributed-data/replicated-table.png "Gerepliceerde tabel") 

## <a name="next-steps"></a>Volgende stappen
Nu u een beetje weet over Azure Synapse, leert u hoe u snel [een SQL-groep maakt](./sql-data-warehouse-get-started-provision.md) en [voorbeeld gegevens laadt](./sql-data-warehouse-load-sample-databases.md). Als u niet bekend bent met Azure, kan de [Azure-woordenlijst](../azure-glossary-cloud-terminology.md) handig zijn bij het opzoeken van nieuwe terminologie. Of Bekijk enkele van deze andere Azure Synapse-resources.  

* [Succesverhalen van klanten](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
* [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Functieverzoeken](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Video's](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Ondersteuningsticket maken](./sql-data-warehouse-get-started-create-support-ticket.md)
* [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* [Stack Overflow-forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)

