---
title: Wereld wijd gedistribueerde transactionele en analytische opslag voor Azure Cosmos-containers
description: Meer informatie over transactionele en analytische opslag en hun configuratie opties voor Azure Cosmos-containers.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: abf222b7a6d6e8fd053fa83c066d2b7850f575ab
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756911"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Wereld wijd gedistribueerde transactionele en analytische opslag voor Azure Cosmos-containers

Azure Cosmos-container wordt intern ondersteund door twee opslag engines: transactionele opslag engine en een bij te werken engine voor analytische opslag. Beide opslag engines zijn in het logboek gestructureerd en zijn geoptimaliseerd voor snelle updates. Elk van deze wordt echter anders gecodeerd:

* **Transactionele opslag engine** : deze wordt gecodeerd in een rij-georiënteerde indeling voor snelle trans actie-lees bewerkingen en-query's.

* **Engine voor analytische opslag** : deze wordt gecodeerd in de kolom indeling voor snelle analytische query's en scans.

![Toewijzing van opslag engines en Azure Cosmos DB-API](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

De transactionele opslag engine wordt ondersteund door de lokale Ssd's, terwijl de analytische opslag wordt opgeslagen op een goedkope externe SSD-opslag. In de volgende tabel worden de belangrijkste verschillen tussen de transactionele en de analytische opslag vastgelegd.


|Functie  |Transactionele opslag  |Analytische opslag |
|---------|---------|---------|
|Maximale opslag per Azure Cosmos-container |   Onbeperkt      |    Onbeperkt     |
|Maximale opslag per logische partitie sleutel   |   10 GB      |   Onbeperkt      |
|Opslag codering  |   Rij-georiënteerde, met een interne indeling.   |   Kolom-georiënteerd met Apache Parquet-indeling. |
|Opslag locatie |   Gerepliceerde opslag die wordt ondersteund door de lokale/intra cluster-Ssd's. |  Gerepliceerde opslag wordt ondersteund door goedkope externe/off-Ssd's.       |
|Duurzaamheid  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|Api's die toegang hebben tot de gegevens  |   SQL, MongoDB, Cassandra, Gremlin, Tables en Etcd.       | Apache Spark         |
|Bewaren (time-to-Live of TTL)   |  Op beleid gebaseerd, geconfigureerd in de Azure Cosmos-container met behulp van de eigenschap `DefaultTimeToLive`.       |   Op beleid gebaseerd, geconfigureerd in de Azure Cosmos-container met behulp van de eigenschap `ColumnStoreTimeToLive`.      |
|Prijs per GB    |   $0,25/GB      |  $0,02/GB       |
|Prijs voor opslag transacties    | De ingerichte door Voer wordt in rekening gebracht op $0,008 per 100 RU/s met facturering per uur.        |  Door Voer op basis van verbruik wordt in rekening gebracht op $0,05 voor 10.000 schrijf transacties en $0,004 voor 10.000 Lees trans acties.       |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Voor delen van transactionele en analytische opslag

### <a name="no-etl-operations"></a>Geen ETL-bewerkingen

Traditionele analytische pijp lijnen zijn complex met meerdere fasen waarvoor elke ETL-bewerking (taak verdeling) moet worden uitgevoerd naar en van de reken-en opslag lagen. Het resulteert in complexe gegevensverwerkings architecturen. Dit betekent dat hoge kosten voor meerdere stadia van opslag en reken kracht en hoge latentie als gevolg van enorme hoeveel heden gegevens die worden overgebracht tussen de verschillende stadia van opslag en reken kracht.  

Er zijn geen overhead voor het uitvoeren van ETL-bewerkingen met Azure Cosmos DB. Elke Azure Cosmos-container wordt ondersteund door zowel transactionele als analytische opslag engines, en de gegevens overdracht tussen de transactionele en analytische opslag engine wordt uitgevoerd in de data base-engine en zonder netwerk-hops. De resulterende latentie en kosten worden aanzienlijk lager vergeleken met traditionele analytische oplossingen. En u krijgt één wereld wijd gedistribueerd opslag systeem voor transactionele en analytische workloads.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Meerdere versies opslaan, bijwerken en query's uitvoeren op de analytische opslag

De analytische opslag kan volledig worden bijgewerkt en bevat de volledige versie geschiedenis van alle transactionele updates die zijn opgetreden in de Azure Cosmos-container.

Alle updates die zijn aangebracht in de transactionele opslag, worden gegarandeerd binnen 30 seconden zichtbaar voor de analytische opslag. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Wereld wijd gedistribueerde analytische opslag met meerdere masters

Als uw Azure Cosmos-account is toegewezen aan een enkele regio, worden de opgeslagen gegevens (in transactionele en analytische opslag) in de containers ook binnen een enkele regio ingedeeld. Als het Azure Cosmos-account daarentegen wereld wijd wordt gedistribueerd, worden de gegevens die zijn opgeslagen in de containers ook wereld wijd gedistribueerd.

Voor Azure Cosmos-accounts die zijn geconfigureerd met meerdere schrijf regio's, worden de schrijf bewerkingen naar de container (naar zowel de transactionele als de analytische opslag) altijd uitgevoerd in de lokale regio. Daarom zijn ze snel te vinden.

Voor zowel een Azure Cosmos-account voor één of meerdere regio's, ongeacht of de enkelvoudige schrijf regio (één master) of meerdere schrijf regio's (ook wel bekend als meerdere masters), worden zowel transactionele als analytische Lees bewerkingen/query's lokaal uitgevoerd in de betreffende regio.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Isolatie van de prestaties tussen transactionele en analytische werk belastingen

In een bepaalde regio werkt de transactionele workloads voor de transactionele/Row-opslag van uw container. Anderzijds worden de analytische werk belastingen toegepast op de analytische/kolom opslag van uw container. De twee opslag engines functioneren onafhankelijk van elkaar en bieden strikte isolatie van de prestaties tussen de werk belastingen.

De transactionele workloads gebruiken de ingerichte door Voer (RUs). In tegens telling tot de transactionele werk belastingen is de door Voer van de analytische workloads gebaseerd op het werkelijke verbruik. De analytische werk belastingen verbruiken resources op aanvraag.

### <a name="on-demand-snapshots-and-time-travel-analytics"></a>Moment opnamen op aanvraag en Time-Travel Analytics

U kunt moment opnamen maken van uw gegevens die zijn opgeslagen in de analytische opslag van uw Azure Cosmos-containers, op elk gewenst moment door de `CreateSnapshot (name, timestamp)` opdracht in de container aan te roepen. Moment opnamen hebben de naam blad wijzers in de geschiedenis van de updates die ooit in uw container zijn uitgevoerd.

![Moment opnamen op aanvraag en Time-Travel Analytics](./media/globally-distributed-transactional-analytical-storage/ondemand-analytical-data-snapshots.png)

Op het moment dat u de moment opname maakt, kunt u naast de naam de tijds tempel opgeven waarmee de status van uw container wordt gedefinieerd in de geschiedenis van updates. U kunt vervolgens de gegevens van de moment opname laden in Spark en de query's uitvoeren.

Op dit moment kunt u op elk gewenst moment alleen moment opnamen op aanvraag maken op de container. de mogelijkheid om automatisch moment opnamen te maken op basis van een planning of aangepast beleid wordt nog niet ondersteund.

### <a name="configure-and-tier-data-between-transactional-and-analytical-storage-independently"></a>Gegevens tussen transactionele en analytische opslag onafhankelijk van elkaar configureren en trapsgewijs instellen

Afhankelijk van uw scenario kunt u elk van de twee opslag engines onafhankelijk in-of uitschakelen. Hier volgen de configuraties voor elk scenario:

|Scenario |Transactionele opslag instelling  |Instelling voor analytische opslag |
|---------|---------|---------|
|Analytische werk belastingen uitvoeren, exclusief (met oneindige retentie) |  DefaultTimeToLive = 0       |  ColumnStoreTimeToLive =-1       |
|Transactionele workloads alleen uitvoeren (met een oneindige retentie)  |   DefaultTimeToLive =-1      |  ColumnStoreTimeToLive = 0       |
|Transactionele en analytische werk belastingen uitvoeren (met oneindige retentie)   |   DefaultTimeToLive =-1      | ColumnStoreTimeToLive =-1        |
|Transactionele en analytische werk belastingen uitvoeren (met verschillende Bewaar intervallen, ook wel bekend als opslag lagen)  |  DefaultTimeToLive = <Value1>       |     ColumnStoreTimeToLive = <Value2>    |

1. **De container alleen configureren voor analytische werk belastingen (met een oneindige retentie)**

   U kunt uw Azure Cosmos-container alleen configureren voor analytische werk belastingen. Deze configuratie heeft als voor deel dat u niet hoeft te betalen voor de transactionele opslag. Als uw doel is de container alleen voor analytische werk belastingen te gebruiken, kunt u de transactionele opslag uitschakelen door `DefaultTimeToLive` in te stellen op 0 in de Cosmos-container. u kunt ook analytische opslag met oneindige retentie inschakelen door `ColumnStoreTimeToLive` op-1 in te stellen.

   ![Analytische werk belastingen met een oneindige retentie](./media/globally-distributed-transactional-analytical-storage/analytical-workload-configuration.png)

1. **De container alleen configureren voor transactionele workloads (met een oneindige retentie)**

   U kunt uw Azure Cosmos-container alleen configureren voor transactionele werk belastingen. U kunt de analytische opslag uitschakelen door `ColumnStoreTimeToLive` in te stellen op 0 in de container. u kunt ook analytische opslag met oneindige retentie inschakelen door `DefaultTimeToLive` op-1 in te stellen.

   ![Transactionele workloads met een oneindige retentie](./media/globally-distributed-transactional-analytical-storage/transactional-workload-configuration.png)

1. **De container voor transactionele en analytische workloads configureren (met een oneindige retentie)**

   U kunt uw Azure Cosmos-container configureren voor transactionele en analytische workloads met volledige prestatie isolatie ertussen. U kunt de analytische opslag inschakelen door `ColumnStoreTimeToLive` op-1 in te stellen en transactionele opslag met oneindige retentie in te stellen met behulp van `DefaultTimeToLive ` op-1.

   ![Transactionele en analytische workloads met een oneindige retentie](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-infinite-retention.png)

1. **De container configureren voor transactionele en analytische workloads met opslag lagen**

   U kunt uw Azure Cosmos-container configureren voor transactionele en analytische workloads met volledige prestatie isolatie ertussen met verschillende Bewaar intervallen. Azure Cosmos DB afdwingt dat uw analytische opslag altijd gedurende een langere duur wordt bewaard dan de transactionele opslag.

   U kunt transactionele opslag met oneindige retentie inschakelen door `DefaultTimeToLive` in te stellen op < waarde 1 > en analytische opslag in te scha kelen door `ColumnStoreTimeToLive` in te stellen op < waarde 2 >. Azure Cosmos DB wordt afgedwongen dat < waarde 2 > altijd groter is dan < waarde 1 >.

   ![Transactionele en analytische werk belastingen met opslag lagen](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-specified-retention.png)

## <a name="next-steps"></a>Volgende stappen

* [Time to Live in Azure Cosmos DB](time-to-live.md)
