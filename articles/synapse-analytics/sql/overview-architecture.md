---
title: Azure Synapse SQL-architectuur
description: Ontdek hoe Azure Synapse SQL massaal parallelle verwerking (MPP) combineert met Azure Storage om hoge prestaties en schaalbaarheid te bereiken.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 570e84b3a545736aad6983c7f0d8c0f0296ca589
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431811"
---
# <a name="azure-synapse-sql-architecture"></a>Azure Synapse SQL-architectuur 

In dit artikel worden de architectuurcomponenten van Synapse SQL beschreven.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Synapse SQL-architectuurcomponenten

Synapse SQL maakt gebruik van een scale-out architectuur om computationele verwerking van gegevens over meerdere knooppunten te distribueren. Compute staat los van opslag, waardoor u de compute onafhankelijk van de gegevens in uw systeem schalen. 

Voor SQL-pool is de schaaleenheid een abstractie van rekenkracht die bekend staat als een [gegevensmagazijneenheid.](resource-consumption-models.md) 

Voor SQL on-demand, omdat serverloos is, wordt schalen automatisch uitgevoerd om aan de vereisten van querybronnen te voldoen. Als topologie in de loop van de tijd verandert door knooppunten of failovers toe te voegen, te verwijderen, past deze zich aan wijzigingen aan en zorgt ervoor dat uw query voldoende resources en voltooide resultaten heeft. In de onderstaande afbeelding wordt bijvoorbeeld SQL on-demand weergegeven met behulp van 4 compute nodes om een query uit te voeren.

![Synapsische SQL-architectuur](./media//overview-architecture/sql-architecture.png)

Synapse SQL maakt gebruik van een node-gebaseerde architectuur. Toepassingen verbinden en geven T-SQL-opdrachten uit met een Control-knooppunt, het enige toegangspunt voor Synapse SQL. 

Het SQL-pool Control-knooppunt maakt gebruik van de MPP-engine om query's te optimaliseren voor parallelle verwerking en geeft vervolgens bewerkingen door aan Compute-knooppunten om hun werk parallel te doen. 

Het SQL on-demand Control-knooppunt maakt gebruik van distributed queryprocessing (DQP)-engine om gedistribueerde uitvoering van gebruikersquery's te optimaliseren en te orkestreren door deze op te splitsen in kleinere query's die worden uitgevoerd op Compute-knooppunten. Elke kleine query wordt taak genoemd en vertegenwoordigt gedistribueerde uitvoeringseenheid. Het leest bestand(en) uit opslag, voegt resultaten samen van andere taken, groepen of ordersgegevens die zijn opgehaald uit andere taken. 

De rekenknooppunten slaan alle gebruikersgegevens op in Azure Storage en voeren de parallelle query's uit. De DMS (Data Movement Service) is een interne service op systeemniveau die de gegevens naar de knooppunten verplaatst om tegelijkertijd query's te kunnen uitvoeren en nauwkeurige resultaten te retourneren. 

Met ontkoppelde opslag en rekenkracht kan men bij het gebruik van Synapse SQL profiteren van onafhankelijke grootte van rekenkracht, ongeacht uw opslagbehoeften. Voor SQL on-demand schaling gebeurt automatisch, terwijl voor SQL-pool men:

* Groei of krimp rekenkracht binnen een SQL-pool (datawarehouse), zonder gegevens te verplaatsen.
* De rekencapaciteit onderbreekt terwijl gegevens intact blijven, zodat u alleen betaalt voor opslag.
* De rekencapaciteit hervatten tijdens werktijden.

## <a name="azure-storage"></a>Azure Storage

Synapse SQL maakt gebruik van Azure Storage om uw gebruikersgegevens veilig te houden. Aangezien uw gegevens worden opgeslagen en beheerd door Azure Storage, zijn er een aparte kosten voor uw opslagverbruik. 

SQL on-demand u bestanden query in uw data lake in read-only manier, terwijl SQL pool u inname van gegevens ook. Wanneer gegevens in SQL-pool worden opgenomen, worden de gegevens verhard in **distributies** om de prestaties van het systeem te optimaliseren. U kiezen welk shardingspatroon u wilt gebruiken om de gegevens te distribueren wanneer u de tabel definieert. Deze shardingpatronen worden ondersteund:

* Hash
* Round robin
* Repliceren

## <a name="control-node"></a>Beheerknooppunt

Het Control-knooppunt is het brein van de architectuur. Het is de front-end met interactie met alle toepassingen en verbindingen. 

In SQL-pool wordt de MPP-engine uitgevoerd op het controlknooppunt om parallelle query's te optimaliseren en te coördineren. Wanneer u een T-SQL-query indient bij SQL-groep, wordt deze in het controleknooppunt omgezet in query's die parallel tegen elke distributie worden uitgevoerd.

In SQL on-demand draait de DQP-engine op Control-knooppunt om gedistribueerde uitvoering van gebruikersquery's te optimaliseren en te coördineren door deze op te splitsen in kleinere query's die worden uitgevoerd op Compute-knooppunten. Het wijst ook sets van bestanden worden verwerkt door elk knooppunt.

## <a name="compute-nodes"></a>Rekenknooppunten

De rekenknooppunten leveren de rekenkracht. 

In SQL-groep worden distributies toegewezen aan Compute-knooppunten voor verwerking. Als u betaalt voor meer rekenresources, u de distributies opnieuw in kaart brengen naar de beschikbare Compute-knooppunten. Het aantal compute nodes varieert van 1 tot 60 en wordt bepaald door het serviceniveau voor SQL-pool. Elk Compute-knooppunt heeft een knooppunt-id die zichtbaar is in systeemweergaven. U de Compute-knooppunt-id zien door te zoeken naar de node_id kolom in systeemweergaven waarvan de namen beginnen met sys.pdw_nodes. Zie [MPP-systeemweergaven](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest)voor een lijst met deze systeemweergaven.

In SQL on-demand krijgt elk Compute-knooppunt taak en set bestanden toegewezen om taken uit te voeren. Taak is gedistribueerdquery uitvoeringseenheid, die eigenlijk deel uitmaakt van query gebruiker ingediend. Automatische schaling is van kracht om ervoor te zorgen dat er voldoende Compute-knooppunten worden gebruikt om gebruikersquery's uit te voeren.

## <a name="data-movement-service"></a>Data Movement Service

Data Movement Service (DMS) is de datatransporttechnologie in SQL-groep die gegevensverplaatsing tussen de Compute-knooppunten coördineert. Voor sommige query's is gegevensverplaatsing vereist om ervoor te zorgen dat de parallelle query's nauwkeurige resultaten opleveren. Wanneer gegevensverplaatsing nodig is, zorgt DMS ervoor dat de juiste gegevens op de juiste locatie komen.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distributies

Een distributie is de basiseenheid van opslag en verwerking voor parallelle query's die worden uitgevoerd op gedistribueerde gegevens in SQL-groep. Wanneer SQL-groep een query uitvoert, wordt het werk verdeeld in 60 kleinere query's die parallel worden uitgevoerd. 

Elk van de 60 kleinere query's wordt uitgevoerd op een van de gegevensdistributies. Elk Compute-knooppunt beheert een of meer van de 60 distributies. Een SQL-groep met maximale compute resources heeft één verdeling per Compute-knooppunt. Een SQL-groep met minimale compute resources heeft alle distributies op één compute node. 

## <a name="hash-distributed-tables"></a>Met hash gedistribueerde tabellen
Een met hash gedistribueerde tabel kan de hoogste queryprestaties leveren voor samenvoegingen en aggregaties in grotere tabellen. 

Als u gegevens wilt sharden in een tabel met hash-gedistribueerde bestanden, gebruikt SQL-groep een hash-functie om elke rij deterministisch aan één distributie toe te wijzen. In de tabeldefinitie wordt een van de kolommen ingesteld als de distributiekolom. De hashfunctie maakt gebruik van de waarden in de distributiekolom om elke rij toe te wijzen aan een distributie.

In het volgende diagram ziet u hoe een volledige (niet-gedistribueerde tabel) wordt opgeslagen als een tabel met hash-gedistribueerde bestanden. 

![Gedistribueerde tabel](media//overview-architecture/hash-distributed-table.png "Gedistribueerde tabel") 

* Elke rij behoort tot één verdeling. 
* Een deterministisch hash-algoritme wijst elke rij toe aan één distributie. 
* Het aantal tabelrijen per verdeling varieert, afhankelijk van de verschillende groottes van tabellen.

Er zijn prestatieoverwegingen voor de selectie van een distributiekolom, zoals onderscheid, gegevensscheefheid en de typen query's die op het systeem worden uitgevoerd.

## <a name="round-robin-distributed-tables"></a>Met round robin gedistribueerde tabellen

Een round-robin tafel is de eenvoudigste tafel om te maken en levert snelle prestaties bij gebruik als een staging tafel voor ladingen.

Een met round robin gedistribueerde tabel distribueert de gegevens gelijkmatig in de tabel, maar zonder verdere optimalisatie. Een verdeling wordt eerst willekeurig gekozen en vervolgens worden buffers van rijen achtereenvolgens toegewezen aan distributies. In een round robin-tabel kunnen gegevens snel worden geladen. De prestaties van query's van met hash gedistribueerde tabellen zijn echter vaak beter. Joins op round-robin tabellen vereisen reshuffling gegevens, die extra tijd kost.

## <a name="replicated-tables"></a>Gerepliceerde tabellen
Een gerepliceerde tabel biedt de snelste prestaties van query's voor kleine tabellen.

Een tabel met gerepliceerde caches een volledige kopie van de tabel op elk compute-knooppunt. Hierdoor hoeven de gegevens niet te worden overgedragen tussen rekenknooppunten voor een samenvoeging of aggregatie. Gerepliceerde tabellen zijn het meest geschikt als het gaat om kleine tabellen. Extra opslag is vereist en er is extra overhead die wordt gemaakt bij het schrijven van gegevens, waardoor grote tabellen onpraktisch zijn. 

Het onderstaande diagram toont een gerepliceerde tabel die in de cache wordt opgeslagen bij de eerste distributie op elk compute-knooppunt. 

![Gerepliceerde tabel](media/overview-architecture/replicated-table.png "Gerepliceerde tabel") 

## <a name="next-steps"></a>Volgende stappen

Nu u een beetje weet over Synapse SQL, leert u hoe u snel [een SQL-groep maakt](../quickstart-create-sql-pool.md) en [voorbeeldgegevens laadt](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./sql-data-warehouse-load-sample-databases.md). Of u begint [SQL on-demand te gebruiken.](../quickstart-sql-on-demand.md) Als u niet bekend bent met Azure, kan de [Azure-woordenlijst](../../azure-glossary-cloud-terminology.md) handig zijn bij het opzoeken van nieuwe terminologie. 
