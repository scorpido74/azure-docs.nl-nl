---
title: Eenheden en doorvoer aanvragen in Azure Cosmos DB
description: Meer informatie over het opgeven en schatten van vereisten voor aanvraageenheden in Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246589"
---
# <a name="request-units-in-azure-cosmos-db"></a>Aanvraageenheden in Azure Cosmos DB

Met Azure Cosmos DB betaalt u de door u ingerichte doorvoer en de opslag die u per uur verbruikt. De doorvoer moet worden ingericht om ervoor te zorgen dat er te allen tijde voldoende systeembronnen beschikbaar zijn voor uw Azure Cosmos-database. U hebt voldoende resources nodig om de [Azure Cosmos DB SLA's](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)te voldoen of te overtreffen.

Azure Cosmos DB ondersteunt veel API's, zoals SQL, MongoDB, Cassandra, Gremlin en Table. Elke API heeft zijn eigen set databasebewerkingen. Deze bewerkingen variëren van eenvoudige puntreads en -schrijfbewerkingen tot complexe query's. Elke databasebewerking verbruikt systeembronnen op basis van de complexiteit van de bewerking. 

De kosten van alle databasebewerkingen worden genormaliseerd door Azure Cosmos DB en worden uitgedrukt door *Request Units* (of RU's, kortom). U kunt RU's per seconde beschouwen als de valuta voor doorvoer. RU's per seconde is een op tarieven gebaseerde valuta. De systeembronnen, zoals CPU, IOPS en geheugen, die nodig zijn om de databasebewerkingen uit te voeren die door Azure Cosmos DB worden ondersteund, worden hierdoor geabstraheerd. 

De kosten voor het lezen van een item van 1 kB bedragen 1 aanvraageenheid (of 1 RU). Er is minimaal 10 RU/s nodig om elke 1 GB aan gegevens op te slaan. Aan alle overige databasebewerkingen worden op dezelfde manier kosten toegewezen in de vorm van RU's. Ongeacht welke API u gebruikt om met uw Azure Cosmos-container te communiceren - de kosten worden altijd gemeten in RU's. Of de databasebewerking een schrijf-, lees-of querybewerking is, de kosten worden altijd gemeten in RU's.

In de volgende afbeelding ziet u het idee op hoog niveau met betrekking tot RU's:

![Databasebewerkingen verbruiken aanvraageenheden](./media/request-units/request-units.png)

Azure Cosmos DB zorgt ervoor dat het aantal RU's voor een bepaalde databasebewerking voor een bepaalde gegevensset deterministisch is om de capaciteit te kunnen beheren en plannen. U kunt de antwoordheader controleren om het aantal RU's bij te houden dat door een databasebewerking wordt gebruikt. Wanneer u de factoren begrijpt [die van invloed zijn op de RU-kosten](request-units.md#request-unit-considerations) en de doorvoervereisten van uw toepassing, u uw toepassing kosteneffectief uitvoeren.

Uw toepassing wordt ingericht met het aantal RU's op basis van seconden in stappen van 100 RU's per seconde. Als u de ingerichte doorvoer voor uw toepassing wilt schalen, kunt u het aantal RU's op elk moment vergroten of verkleinen. U kunt omhoog of omlaag schalen in stappen van 100 RU's. U kunt uw wijzigingen programmatisch aanbrengen of via Azure Portal. U wordt per uur gefactureerd.

U doorvoer inrichten op twee verschillende granulariteiten: 

* **Containers:** Zie Doorvoer [voorziening op een Azure Cosmos-container](how-to-provision-container-throughput.md)voor meer informatie.
* **Databases:** Zie [Doorvoervoorziening op een Azure Cosmos-database](how-to-provision-database-throughput.md)voor meer informatie.

## <a name="request-unit-considerations"></a>Aandachtspunten voor aanvraageenheden

Houd rekening met het volgende als u een schatting maakt van het aantal in te richten RU's per seconde:

* **Objectgrootte:** naarmate de grootte van een item toeneemt, neemt ook het aantal GEBRUIKTE R's toe om het item te lezen of te schrijven.

* **Itemindexering**: Standaard wordt elk item automatisch geïndexeerd. Er worden minder RU's verbruikt als u ervoor kiest bepaalde items in een container niet te indexeren.

* **Aantal eigenschapskenmerken:** Ervan uitgaande dat de standaardindexering op alle eigenschappen is, neemt het aantal R's dat wordt verbruikt om een item te schrijven toe naarmate het aantal itemeigenschappen toeneemt.

* **Geïndexeerde eigenschappen**: Een indexbeleid voor elke container bepaalt standaard welke eigenschappen worden geïndexeerd. Beperk het aantal geïndexeerde eigenschappen om het RU-verbruik voor schrijfbewerkingen te verlagen.

* **Gegevensconsistentie**: De sterke en begrensde consistentieniveaus verbruiken ongeveer twee keer meer R's tijdens het uitvoeren van leesbewerkingen in vergelijking met die van andere ontspannen consistentieniveaus.

* **Querypatronen:** de complexiteit van een query is van invloed op het aantal RU's dat voor een bewerking wordt verbruikt. Factoren die invloed hebben op de kosten van querybewerkingen: 
    
    - Het aantal queryresultaten
    - Het aantal predicaten
    - De aard van de predicaten
    - Het aantal door de gebruiker gedefinieerde functies
    - De grootte van de brongegevens
    - De grootte van de resultatenset
    - Projecties

  In Azure Cosmos DB wordt gegarandeerd dat dezelfde query op dezelfde gegevens altijd hetzelfde aantal RU's kost bij elke herhaalde uitvoering.

* **Scriptgebruik**: Net als bij query's verbruiken opgeslagen procedures en triggers RALLY's op basis van de complexiteit van de bewerkingen die worden uitgevoerd. Controleer tijdens het ontwikkelen van uw toepassing de [aanvraagkostenheader](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) om meer inzicht te krijgen in hoeveel RU-capaciteit elke bewerking verbruikt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [inrichten van doorvoer op Azure Cosmos-containers en -databases](set-throughput.md).
* Meer informatie over [logische partities](partition-data.md).
* Meer informatie over hoe [u de ingerichte doorvoer wereldwijd schaalt.](scaling-throughput.md)
* Meer informatie over het [inrichten van doorvoer op een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van doorvoer op een Azure Cosmos-database](how-to-provision-database-throughput.md).
* Meer informatie over het [vinden van de kosten voor de aanvraageenheid voor een bewerking](find-request-unit-charge.md).
* Meer informatie over het [optimaliseren van de ingerichte doorvoerkosten in Azure Cosmos DB](optimize-cost-throughput.md).
* Meer informatie over het [optimaliseren van lees- en schrijfkosten in Azure Cosmos DB](optimize-cost-reads-writes.md).
* Meer informatie over het [optimaliseren van querykosten in Azure Cosmos DB](optimize-cost-queries.md).
* Meer informatie over het [gebruik van statistieken om de doorvoer te controleren.](use-metrics.md)
