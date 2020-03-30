---
title: Wereldwijd gedistribueerde transactionele en analytische(in private preview)-opslag voor Azure Cosmos-containers
description: Meer informatie over transactionele en analytische opslag en hun configuratieopties voor Azure Cosmos-containers.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623384"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Wereldwijd gedistribueerde transactionele en analytische opslag voor Azure Cosmos-containers

Azure Cosmos-container wordt intern ondersteund door twee opslagengines - transactionele opslagengine en een updatable analytische opslagengine (in privépreview). Beide opslagengines zijn log-gestructureerd en zijn geoptimaliseerd voor snellere updates. Echter, elk van hen is anders gecodeerd:

* **Transactionele opslagengine** – Het is gecodeerd in rij-georiënteerde indeling voor snelle transactionele leest en query's.

* **Analytische opslag engine** - Het is gecodeerd in columnar formaat voor snelle analytische query's en scans.

![Opslagengines en Azure Cosmos DB API-toewijzing](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

De transactionele opslagengine wordt ondersteund door lokale SSD's, terwijl de analytische opslag wordt opgeslagen op een goedkope ssd-opslag buiten het cluster. In de volgende tabel worden de opmerkelijke verschillen tussen de transactionele en de analytische opslag vastgelegd.


|Functie  |Transactionele opslag  |Analytische opslag |
|---------|---------|---------|
|Maximale opslag per Azure Cosmos-container |   Onbeperkt      |    Onbeperkt     |
|Maximale opslag per logische partitiesleutel   |   20 GB      |   Onbeperkt      |
|Opslagcodering  |   Rijgericht, met behulp van een interne indeling.   |   Kolomgericht, met behulp van Apache Parket formaat. |
|Opslagplaats |   Gerepliceerde opslag ondersteund door lokale/intra-cluster-SSD's. |  Gerepliceerde opslag ondersteund door goedkope externe/off-cluster-ssd's.       |
|Duurzaamheid  |    99.99999 (7-9 s)     |  99.99999 (7-9 s)       |
|API's die toegang hebben tot de gegevens  |   SQL, MongoDB, Cassandra, Gremlin, Tabellen en etcd.       | Apache Spark         |
|Retentie (Time-to-live of TTL)   |  Beleidsgestuurd, geconfigureerd op de Azure Cosmos-container met behulp van de `DefaultTimeToLive` eigenschap.       |   Beleidsgestuurd, geconfigureerd op de Azure Cosmos-container met behulp van de `ColumnStoreTimeToLive` eigenschap.      |
|Prijs per GB    |   Bekijk de [prijspagina](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   Bekijk de [prijspagina](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|Prijs voor opslagtransacties    |  Bekijk de [prijspagina](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   Bekijk de [prijspagina](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Voordelen van transactionele en analytische opslag

### <a name="no-etl-operations"></a>Geen ETL-bewerkingen

Traditionele analytische pijplijnen zijn complex met meerdere fasen die elk Extract-Transform-Load (ETL) bewerkingen van en naar de compute en storage lagen vereisen. Het resulteert in complexe dataprocessing architecturen. Dat betekent hoge kosten voor meerdere fasen van opslag en rekenkracht, en hoge latentie als gevolg van enorme hoeveelheden gegevens die worden overgedragen tussen verschillende stadia van opslag en rekenkracht.  

Er is geen overhead van het uitvoeren van ETL-bewerkingen met Azure Cosmos DB. Elke Azure Cosmos-container wordt ondersteund door zowel transactionele als analytische opslagengines en de gegevensoverdracht tussen de transactionele en analytische opslagengine gebeurt binnen de database-engine en zonder netwerkhop. De resulterende latentie en kosten zijn aanzienlijk lager in vergelijking met traditionele analytische oplossingen. En u krijgt één wereldwijd gedistribueerd opslagsysteem voor zowel transactionele als analytische workloads.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Meerdere versies opslaan, bijwerken en query's maken van de analytische opslag

De analytische opslag is volledig updatable en bevat de volledige versiegeschiedenis van alle transactionele updates die zich hebben voorgedaan op de Azure Cosmos-container.

Elke update van de transactionele opslag is gegarandeerd binnen 30 seconden zichtbaar voor de analytische opslag. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Wereldwijd gedistribueerde, multi-master analytische opslag

Als uw Azure Cosmos-account is ingesteld op één regio, worden de gegevens die zijn opgeslagen (in transactionele en analytische opslag) in de containers ook tot één regio beperkt. Aan de andere kant, als het Azure Cosmos-account wereldwijd wordt gedistribueerd, worden de gegevens die in de containers zijn opgeslagen ook wereldwijd gedistribueerd.

Voor Azure Cosmos-accounts die zijn geconfigureerd met meerdere schrijfregio's, worden schrijfbewerkingen naar de container (naar zowel de transactionele als de analytische opslag) altijd uitgevoerd in de lokale regio en dus zijn ze snel.

Voor azure cosmos-accounts met één of meerdere regio's, ongeacht of één schrijfgebied (één master) of meerdere schrijfregio's (ook wel multimaster genoemd) worden zowel transactionele als analytische lees-/query's lokaal uitgevoerd in de opgegeven regio.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Prestatieisolatie tussen transactionele en analytische workloads

In een bepaald gebied werken de transactionele workloads ten opzichte van de transactionele/rijopslag van uw container. Anderzijds werken de analytische workloads tegen de analytische/kolomopslag van uw container. De twee opslagmotoren werken onafhankelijk en bieden een strikte prestatieisolatie tussen de workloads.

De transactionele workloads verbruiken de ingerichte doorvoer (RU's). In tegenstelling tot de transactionele workloads is de doorvoer van analytische workloads gebaseerd op het werkelijke verbruik. De analytische workloads verbruiken resources on-demand.

## <a name="next-steps"></a>Volgende stappen

* [Tijd om te leven in Azure Cosmos DB](time-to-live.md)
