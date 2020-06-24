---
title: Architectuur van Azure Synapse Analytics (voorheen SQL DW)
description: Meer informatie over hoe Azure Synapse Analytics (voorheen SQL DW) enorm parallelle verwerking (MPP) combineert met Azure Storage om hoge prestaties en schaal baarheid te garanderen.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: cde6cb514b6f87315400b3c40d8b86bcb7ff0adb
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85210963"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Architectuur van Azure Synapse Analytics (voorheen SQL DW)

Azure Synapse is een oneindige analyseservice die bedrijfsgegevensopslag en big data-analyses combineert. Deze geeft u de vrijheid om op schaal gegevens op te vragen over uw voorwaarden, met behulp van serverloze on-demand of ingerichte resources. Azure Synapse brengt deze twee werelden samen met een uniforme ervaring om gegevens op te nemen, voor te bereiden, te beheren en te leveren voor directe BI- en machine learning-behoeften.

 Azure Synapse heeft vier onderdelen:

- Synapse SQL: op T-SQL gebaseerde analyses volt ooien

  - SQL-groep (betalen per DWU ingericht): algemeen beschikbaar
  - SQL on-demand (betalen per TB verwerkte) – (preview-versie)
- Spark: diep geïntegreerde Apache Spark (preview-versie)
- Gegevens integratie: hybride gegevens integratie (preview-versie)
- Studio: uniforme gebruikers ervaring.  (Preview)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-mpp-architecture-components"></a>Onderdelen van de SQL MPP-architectuur Synapse

[Synapse SQL](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) maakt gebruik van een scale-out architectuur voor het distribueren van reken kundige verwerking van gegevens over meerdere knoop punten. De schaal eenheid is een abstractie van reken kracht die wordt aangeduid als [Data Warehouse-eenheid](what-is-a-data-warehouse-unit-dwu-cdwu.md). Compute staat los van de opslag, waarmee u de reken kracht onafhankelijk van de gegevens in uw systeem kunt schalen.

![Synapse SQL-architectuur](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Synapse SQL maakt gebruik van een architectuur op basis van een knoop punt. Toepassingen maken verbinding met T-SQL-opdrachten en geven ze aan een besturings element knoop punt. Dit is het enige invoer punt voor Synapse SQL. Het controle knooppunt voert de MPP-engine uit, waarmee query's voor parallelle verwerking worden geoptimaliseerd. vervolgens worden bewerkingen aan reken knooppunten door gegeven om hun werk parallel uit te voeren.

De rekenknooppunten slaan alle gebruikersgegevens op in Azure Storage en voeren de parallelle query's uit. De DMS (Data Movement Service) is een interne service op systeemniveau die de gegevens naar de knooppunten verplaatst om tegelijkertijd query's te kunnen uitvoeren en nauwkeurige resultaten te retourneren.

Bij gebruik van een ontkoppelde opslag en reken kracht kunt u met Synapse SQL pool One het volgende doen:

- Reken kracht onafhankelijk van de grootte van uw opslag behoeften.
- De reken kracht binnen een SQL-groep (Data Warehouse) verg Roten of verkleinen zonder gegevens te verplaatsen.
- De rekencapaciteit onderbreekt terwijl gegevens intact blijven, zodat u alleen betaalt voor opslag.
- De rekencapaciteit hervatten tijdens werktijden.

### <a name="azure-storage"></a>Azure Storage

Synapse SQL maakt gebruik van Azure Storage om uw gebruikers gegevens veilig te maken.  Omdat uw gegevens worden opgeslagen en beheerd door Azure Storage, worden er afzonderlijke kosten in rekening gebracht voor uw opslag verbruik. De gegevens worden in **distributies** Shard om de prestaties van het systeem te optimaliseren. U kunt kiezen welk sharding-patroon u wilt gebruiken om de gegevens te distribueren wanneer u de tabel definieert. Deze sharding-patronen worden ondersteund:

- Hash
- Round robin
- Repliceren

### <a name="control-node"></a>Beheerknooppunt

Het knoop punt beheer is het brein van de architectuur. Het is de front-end met interactie met alle toepassingen en verbindingen. De MPP-engine wordt uitgevoerd op het beheerknooppunt om de parallelle query's te optimaliseren en te coördineren. Wanneer u een T-SQL-query verzendt, transformeert het knoop punt van de controle in query's die parallel worden uitgevoerd op elke distributie.

### <a name="compute-nodes"></a>Rekenknooppunten

De rekenknooppunten leveren de rekenkracht. Distributies worden toegewezen aan reken knooppunten voor verwerking. Wanneer u betaalt voor meer reken bronnen, worden distributies opnieuw toegewezen aan beschik bare reken knooppunten. Het aantal Compute-knoop punten ligt tussen 1 en 60, en wordt bepaald door het service niveau voor Synapse SQL.

Elk Compute-knoop punt heeft een knoop punt-ID die zichtbaar is in systeem weergaven. U kunt de ID van het reken knooppunt zien door te zoeken naar de node_id kolom in systeem weergaven waarvan de naam begint met sys. pdw_nodes. Zie [MPP System views](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)(Engelstalig) voor een lijst met deze systeem weergaven.

### <a name="data-movement-service"></a>Data Movement Service

Gegevens verplaatsings service (DMS) is de gegevens transport technologie die de gegevens verplaatsing tussen de reken knooppunten coördineert. Voor sommige query's is gegevens verplaatsing vereist om ervoor te zorgen dat de parallelle query's accurate resultaten retour neren. Wanneer gegevens verplaatsing vereist is, zorgt DMS ervoor dat de juiste gegevens naar de juiste locatie worden opgehaald.

## <a name="distributions"></a>Distributies

Een distributie is de basiseenheid voor opslag en verwerking van parallelle query's die op gedistribueerde gegevens worden uitgevoerd. Wanneer Synapse SQL een query uitvoert, wordt het werk onderverdeeld in 60 kleinere query's die parallel worden uitgevoerd.

Elk van de 60 kleinere query's worden uitgevoerd op een van de gegevens distributies. Elk Compute-knoop punt beheert een of meer van de 60-distributies. Een SQL-groep met maximale Compute-resources heeft één distributie per reken knooppunt. Een SQL-groep met minimale Compute-resources heeft alle distributies op één reken knooppunt.  

## <a name="hash-distributed-tables"></a>Met hash gedistribueerde tabellen

Een met hash gedistribueerde tabel kan de hoogste queryprestaties leveren voor samenvoegingen en aggregaties in grotere tabellen.

Als u gegevens wilt Shard in een hash-gedistribueerde tabel, wordt een hash-functie gebruikt om per onwaar elke rij aan één verdeling toe te wijzen. In de tabeldefinitie wordt een van de kolommen ingesteld als de distributiekolom. De hashfunctie maakt gebruik van de waarden in de distributiekolom om elke rij toe te wijzen aan een distributie.

In het volgende diagram ziet u hoe een volledige (niet-gedistribueerde tabel) wordt opgeslagen als een hash-Distributed Table.

![Gedistribueerde tabel](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Gedistribueerde tabel")  

- Elke rij behoort tot één distributie.  
- Een deterministische hash-algoritme wijst elke rij toe aan één verdeling.  
- Het aantal tabel rijen per distributie varieert, zoals wordt weer gegeven in de verschillende grootten van tabellen.

Er zijn prestatie overwegingen voor de selectie van een distributie kolom, zoals de distinctiteit, de schei ding van gegevens en de typen query's die op het systeem worden uitgevoerd.

## <a name="round-robin-distributed-tables"></a>Met round robin gedistribueerde tabellen

Een Round Robin-tabel is de eenvoudigste tabel om snelle prestaties te maken en op te halen wanneer deze wordt gebruikt als faserings tabel voor belasting.

Een met round robin gedistribueerde tabel distribueert de gegevens gelijkmatig in de tabel, maar zonder verdere optimalisatie. Een distributie wordt in wille keurige volg orde gekozen en vervolgens worden buffers van rijen opeenvolgend toegewezen aan distributies. In een round robin-tabel kunnen gegevens snel worden geladen. De prestaties van query's van met hash gedistribueerde tabellen zijn echter vaak beter. Voor het samen voegen van Round-Robin tabellen zijn gegevens met een andere volg orde nodig. dit duurt meer tijd.

## <a name="replicated-tables"></a>Gerepliceerde tabellen

Een gerepliceerde tabel biedt de snelste prestaties van query's voor kleine tabellen.

Een tabel die wordt gerepliceerd, plaatst een volledige kopie van de tabel op elk reken knooppunt. Hierdoor hoeven de gegevens niet te worden overgedragen tussen rekenknooppunten voor een samenvoeging of aggregatie. Gerepliceerde tabellen zijn het meest geschikt als het gaat om kleine tabellen. Extra opslag ruimte is vereist en er worden extra overhead kosten in rekening gebracht voor het schrijven van gegevens, waardoor grote tabellen onbruikbaar worden.  

In het onderstaande diagram ziet u een gerepliceerde tabel die in de cache wordt opgeslagen op de eerste distributie op elk reken knooppunt.  

![Gerepliceerde tabel](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Gerepliceerde tabel")

## <a name="next-steps"></a>Volgende stappen

Nu u een beetje weet over Azure Synapse, leert u hoe u snel [een SQL-groep maakt](create-data-warehouse-portal.md) en [voorbeeld gegevens laadt](load-data-from-azure-blob-storage-using-polybase.md). Als u niet bekend bent met Azure, kan de [Azure-woordenlijst](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) handig zijn bij het opzoeken van nieuwe terminologie. Of Bekijk enkele van deze andere Azure Synapse-resources.  

- [Succesverhalen van klanten](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Functieverzoeken](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Video's](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Ondersteuningsticket maken](sql-data-warehouse-get-started-create-support-ticket.md)
- [Microsoft Q&A-vragenpagina](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
- [Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
