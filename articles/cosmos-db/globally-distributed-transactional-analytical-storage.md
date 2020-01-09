---
title: Wereld wijd gedistribueerde transactionele en analytische opslag (in private preview) voor Azure Cosmos-containers
description: Meer informatie over transactionele en analytische opslag en hun configuratie opties voor Azure Cosmos-containers.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 18cf43ba137c92fc00d5f8e82e13501d03b4b6a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445408"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Wereld wijd gedistribueerde transactionele en analytische opslag voor Azure Cosmos-containers

De Azure Cosmos-container wordt intern ondersteund door twee opslag engines: transactionele opslag engine en een bij te werken analytische-opslag engine (in private preview). Beide opslag engines zijn in het logboek gestructureerd en zijn geoptimaliseerd voor snelle updates. Elk van deze wordt echter anders gecodeerd:

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
|Api's die toegang hebben tot de gegevens  |   SQL, MongoDB, Cassandra, Gremlin, Tables en etcd.       | Apache Spark         |
|Bewaren (time-to-Live of TTL)   |  Op beleid gebaseerd, geconfigureerd in de Azure Cosmos-container met behulp van de eigenschap `DefaultTimeToLive`.       |   Op beleid gebaseerd, geconfigureerd in de Azure Cosmos-container met behulp van de eigenschap `ColumnStoreTimeToLive`.      |
|Prijs per GB    |   De [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) bekijken     |   De [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) bekijken        |
|Prijs voor opslag transacties    |  De [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) bekijken         |   De [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) bekijken        |

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

## <a name="next-steps"></a>Volgende stappen

* [Time to Live in Azure Cosmos DB](time-to-live.md)
