---
title: Azure Synapse Analytics (voorheen SQL DW)-architectuur
description: Ontdek hoe Azure Synapse Analytics (voorheen SQL DW) massively parallel processing (MPP) combineert met Azure Storage om hoge prestaties en schaalbaarheid te bereiken.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: d204477818ce2733d9f6d1e3dcc7455018456bcb
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884829"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Azure Synapse Analytics (voorheen SQL DW)-architectuur

Azure Synapse is een oneindige analyseservice die bedrijfsgegevensopslag en big data-analyses combineert. Deze geeft u de vrijheid om op schaal gegevens op te vragen over uw voorwaarden, met behulp van serverloze on-demand of ingerichte resources. Azure Synapse brengt deze twee werelden samen met een uniforme ervaring om gegevens in te nemen, voor te bereiden, te beheren en te leveren voor onmiddellijke BI- en machine learning-behoeften.

 Azure Synapse heeft vier componenten:

- SQL Analytics: Complete T-SQL gebaseerde analytics

  - SQL-pool (pay per DWU-voorziening) – Algemeen beschikbaar
  - SQL on-demand (pay per TB processed) – (Preview)
- Spark: Diep geïntegreerde Apache Spark (Preview)
- Gegevensintegratie: hybride gegevensintegratie (Preview)
- Studio: uniforme gebruikerservaring.  (Preview)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-mpp-architecture-components"></a>Synapse SQL MPP-architectuurcomponenten

[Synapse SQL](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) maakt gebruik van een scale-out architectuur om computationele verwerking van gegevens over meerdere knooppunten te distribueren. De schaaleenheid is een abstractie van rekenkracht die bekend staat als een [datawarehouse-eenheid.](what-is-a-data-warehouse-unit-dwu-cdwu.md) Compute staat los van opslag, waardoor u de compute onafhankelijk van de gegevens in uw systeem schalen.

![Synapsische SQL-architectuur](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Analytics maakt gebruik van een node-gebaseerde architectuur. Toepassingen verbinden en geven T-SQL-opdrachten uit met een Control-knooppunt, het enige toegangspunt voor SQL Analytics. Het control-knooppunt voert de MPP-engine uit, die query's optimaliseert voor parallelle verwerking en vervolgens bewerkingen doorgeeft aan Compute-knooppunten om hun werk parallel te doen.

De rekenknooppunten slaan alle gebruikersgegevens op in Azure Storage en voeren de parallelle query's uit. De DMS (Data Movement Service) is een interne service op systeemniveau die de gegevens naar de knooppunten verplaatst om tegelijkertijd query's te kunnen uitvoeren en nauwkeurige resultaten te retourneren.

Met ontkoppelde opslag en rekenkracht kan men bij het gebruik van Synapse SQL-pool:

- Onafhankelijk grootte rekenkracht, ongeacht uw opslagbehoeften.
- Groei of krimp rekenkracht binnen een SQL-pool (datawarehouse), zonder gegevens te verplaatsen.
- De rekencapaciteit onderbreekt terwijl gegevens intact blijven, zodat u alleen betaalt voor opslag.
- De rekencapaciteit hervatten tijdens werktijden.

### <a name="azure-storage"></a>Azure Storage

Synapse SQL maakt gebruik van Azure Storage om uw gebruikersgegevens veilig te houden.  Aangezien uw gegevens worden opgeslagen en beheerd door Azure Storage, zijn er een aparte kosten voor uw opslagverbruik. De gegevens worden verhard in **distributies** om de prestaties van het systeem te optimaliseren. U kiezen welk shardingspatroon u wilt gebruiken om de gegevens te distribueren wanneer u de tabel definieert. Deze shardingpatronen worden ondersteund:

- Hash
- Round robin
- Repliceren

### <a name="control-node"></a>Beheerknooppunt

Het Control-knooppunt is het brein van de architectuur. Het is de front-end met interactie met alle toepassingen en verbindingen. De MPP-engine wordt uitgevoerd op het beheerknooppunt om de parallelle query's te optimaliseren en te coördineren. Wanneer u een T-SQL-query verzendt, wordt deze in het bedieningsknooppunt omgezet in query's die parallel tegen elke distributie worden uitgevoerd.

### <a name="compute-nodes"></a>Rekenknooppunten

De rekenknooppunten leveren de rekenkracht. Distributies worden toegewezen aan Compute-knooppunten voor verwerking. Naarmate u voor meer rekenresources betaalt, worden distributies toegewezen aan beschikbare Compute-knooppunten. Het aantal compute nodes varieert van 1 tot 60 en wordt bepaald door het serviceniveau voor Synapse SQL.

Elk Compute-knooppunt heeft een knooppunt-id die zichtbaar is in systeemweergaven. U de Compute-knooppunt-id zien door te zoeken naar de node_id kolom in systeemweergaven waarvan de namen beginnen met sys.pdw_nodes. Zie [MPP-systeemweergaven](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)voor een lijst met deze systeemweergaven.

### <a name="data-movement-service"></a>Data Movement Service

Data Movement Service (DMS) is de datatransporttechnologie die de gegevensverplaatsing tussen de Compute-knooppunten coördineert. Voor sommige query's is gegevensverplaatsing vereist om ervoor te zorgen dat de parallelle query's nauwkeurige resultaten opleveren. Wanneer gegevensverplaatsing nodig is, zorgt DMS ervoor dat de juiste gegevens op de juiste locatie komen.

## <a name="distributions"></a>Distributies

Een distributie is de basiseenheid voor opslag en verwerking van parallelle query's die op gedistribueerde gegevens worden uitgevoerd. Wanneer SQL Analytics een query uitvoert, wordt het werk verdeeld in 60 kleinere query's die parallel worden uitgevoerd.

Elk van de 60 kleinere query's wordt uitgevoerd op een van de gegevensdistributies. Elk Compute-knooppunt beheert een of meer van de 60 distributies. Een SQL-groep met maximale compute resources heeft één verdeling per Compute-knooppunt. Een SQL-groep met minimale compute resources heeft alle distributies op één compute node.  

## <a name="hash-distributed-tables"></a>Met hash gedistribueerde tabellen

Een met hash gedistribueerde tabel kan de hoogste queryprestaties leveren voor samenvoegingen en aggregaties in grotere tabellen.

Om gegevens in een hash-gedistribueerde tabel te sharden, wordt een hash-functie gebruikt om elke rij deterministisch aan één distributie toe te wijzen. In de tabeldefinitie wordt een van de kolommen ingesteld als de distributiekolom. De hashfunctie maakt gebruik van de waarden in de distributiekolom om elke rij toe te wijzen aan een distributie.

In het volgende diagram ziet u hoe een volledige (niet-gedistribueerde tabel) wordt opgeslagen als een tabel met hash-gedistribueerde bestanden.

![Gedistribueerde tabel](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Gedistribueerde tabel")  

- Elke rij behoort tot één verdeling.  
- Een deterministisch hash-algoritme wijst elke rij toe aan één distributie.  
- Het aantal tabelrijen per verdeling varieert, afhankelijk van de verschillende groottes van tabellen.

Er zijn prestatieoverwegingen voor de selectie van een distributiekolom, zoals onderscheid, gegevensscheefheid en de typen query's die op het systeem worden uitgevoerd.

## <a name="round-robin-distributed-tables"></a>Met round robin gedistribueerde tabellen

Een round-robin tafel is de eenvoudigste tafel om te maken en levert snelle prestaties bij gebruik als een staging tafel voor ladingen.

Een met round robin gedistribueerde tabel distribueert de gegevens gelijkmatig in de tabel, maar zonder verdere optimalisatie. Een verdeling wordt eerst willekeurig gekozen en vervolgens worden buffers van rijen achtereenvolgens toegewezen aan distributies. In een round robin-tabel kunnen gegevens snel worden geladen. De prestaties van query's van met hash gedistribueerde tabellen zijn echter vaak beter. Joins op round-robin tabellen vereisen reshuffling gegevens, die extra tijd kost.

## <a name="replicated-tables"></a>Gerepliceerde tabellen

Een gerepliceerde tabel biedt de snelste prestaties van query's voor kleine tabellen.

Een tabel met gerepliceerde caches een volledige kopie van de tabel op elk compute-knooppunt. Hierdoor hoeven de gegevens niet te worden overgedragen tussen rekenknooppunten voor een samenvoeging of aggregatie. Gerepliceerde tabellen zijn het meest geschikt als het gaat om kleine tabellen. Extra opslag is vereist en er is extra overhead die wordt gemaakt bij het schrijven van gegevens, waardoor grote tabellen onpraktisch zijn.  

Het onderstaande diagram toont een gerepliceerde tabel die in de cache wordt opgeslagen bij de eerste distributie op elk compute-knooppunt.  

![Gerepliceerde tabel](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Gerepliceerde tabel")

## <a name="next-steps"></a>Volgende stappen

Nu u een beetje weet over Azure Synapse, leest u hoe u snel [een SQL-groep maakt](create-data-warehouse-portal.md) en [voorbeeldgegevens laadt.](load-data-from-azure-blob-storage-using-polybase.md) Als u niet bekend bent met Azure, kan de [Azure-woordenlijst](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) handig zijn bij het opzoeken van nieuwe terminologie. Of bekijk een aantal van deze andere Azure Synapse Resources.  

- [Succesverhalen van klanten](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Functieverzoeken](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Video's](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Ondersteuningsticket maken](sql-data-warehouse-get-started-create-support-ticket.md)
- [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
- [Stack Overflow-forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
