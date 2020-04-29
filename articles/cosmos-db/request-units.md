---
title: Aanvraag eenheden en door Voer in Azure Cosmos DB
description: Meer informatie over het opgeven en ramen van vereisten voor aanvraag eenheden in Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79246589"
---
# <a name="request-units-in-azure-cosmos-db"></a>Aanvraageenheden in Azure Cosmos DB

Met Azure Cosmos DB betaalt u de door u ingerichte doorvoer en de opslag die u per uur verbruikt. De doorvoer moet worden ingericht om ervoor te zorgen dat er te allen tijde voldoende systeembronnen beschikbaar zijn voor uw Azure Cosmos-database. U hebt voldoende resources nodig om te voldoen aan de [Azure Cosmos DB sla's](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB ondersteunt veel Api's, zoals SQL, MongoDB, Cassandra, Gremlin en Table. Elke API heeft een eigen set database bewerkingen. Deze bewerkingen variëren van eenvoudig punt Lees-en schrijf bewerkingen naar complexe query's. Bij elke database bewerking worden systeem resources verbruikt op basis van de complexiteit van de bewerking. 

De kosten van alle database bewerkingen worden genormaliseerd door Azure Cosmos DB en worden uitgedrukt in *aanvraag eenheden* (of RUs voor short). U kunt RU's per seconde beschouwen als de valuta voor doorvoer. RU's per seconde is een op tarieven gebaseerde valuta. De systeembronnen, zoals CPU, IOPS en geheugen, die nodig zijn om de databasebewerkingen uit te voeren die door Azure Cosmos DB worden ondersteund, worden hierdoor geabstraheerd. 

De kosten voor het lezen van een item van 1 kB bedragen 1 aanvraageenheid (of 1 RU). Er is mini maal 10 RU/s vereist om elke 1 GB aan gegevens op te slaan. Aan alle overige databasebewerkingen worden op dezelfde manier kosten toegewezen in de vorm van RU's. Ongeacht welke API u gebruikt om met uw Azure Cosmos-container te communiceren - de kosten worden altijd gemeten in RU's. Of de databasebewerking een schrijf-, lees-of querybewerking is, de kosten worden altijd gemeten in RU's.

In de volgende afbeelding ziet u het idee op hoog niveau met betrekking tot RU's:

![Database bewerkingen gebruiken aanvraag eenheden](./media/request-units/request-units.png)

Azure Cosmos DB zorgt ervoor dat het aantal RU's voor een bepaalde databasebewerking voor een bepaalde gegevensset deterministisch is om de capaciteit te kunnen beheren en plannen. U kunt de antwoordheader controleren om het aantal RU's bij te houden dat door een databasebewerking wordt gebruikt. Wanneer u de [factoren begrijpt die van invloed zijn op de ru-kosten](request-units.md#request-unit-considerations) en de doorvoer vereisten van uw toepassing, kunt u uw toepassing effectief uitvoeren.

Uw toepassing wordt ingericht met het aantal RU's op basis van seconden in stappen van 100 RU's per seconde. Als u de ingerichte doorvoer voor uw toepassing wilt schalen, kunt u het aantal RU's op elk moment vergroten of verkleinen. U kunt omhoog of omlaag schalen in stappen van 100 RU's. U kunt uw wijzigingen programmatisch aanbrengen of via Azure Portal. U wordt per uur gefactureerd.

U kunt de door Voer op twee verschillende granulariteit inrichten: 

* **Containers**: Zie [door Voer inrichten op een Azure Cosmos-container](how-to-provision-container-throughput.md)voor meer informatie.
* **Data bases**: Zie [door Voer inrichten voor een Azure Cosmos-data base](how-to-provision-database-throughput.md)voor meer informatie.

## <a name="request-unit-considerations"></a>Aandachtspunten voor aanvraageenheden

Houd rekening met het volgende als u een schatting maakt van het aantal in te richten RU's per seconde:

* **Item grootte**: als de grootte van een item toeneemt, neemt het aantal dat is gebruikt voor het lezen of schrijven van het item ook toe.

* **Item Indexering**: standaard wordt elk item automatisch geïndexeerd. Er worden minder RU's verbruikt als u ervoor kiest bepaalde items in een container niet te indexeren.

* **Aantal item eigenschappen**: aangenomen dat de standaard indexering op alle eigenschappen is ingesteld, wordt het aantal verbruikte RUs voor het schrijven van een item verhoogd naarmate het aantal item eigenschappen toeneemt.

* **Geïndexeerde eigenschappen**: in een index beleid voor elke container wordt bepaald welke eigenschappen standaard worden geïndexeerd. Beperk het aantal geïndexeerde eigenschappen om het RU-verbruik voor schrijfbewerkingen te verlagen.

* **Gegevens consistentie**: de sterke en gebonden consistentie niveaus voor veroudering worden ongeveer twee keer meer gebruikt tijdens het uitvoeren van Lees bewerkingen in vergelijking met die van andere beperkte consistentie niveaus.

* **Query patronen**: de complexiteit van een query is van invloed op het aantal verbruikte RUs voor een bewerking. Factoren die invloed hebben op de kosten van querybewerkingen: 
    
    - Het aantal queryresultaten
    - Het aantal predicaten
    - De aard van de predicaten
    - Het aantal door de gebruiker gedefinieerde functies
    - De grootte van de brongegevens
    - De grootte van de resultatenset
    - Projecties

  In Azure Cosmos DB wordt gegarandeerd dat dezelfde query op dezelfde gegevens altijd hetzelfde aantal RU's kost bij elke herhaalde uitvoering.

* **Script gebruik**: net als bij query's, worden voor opgeslagen procedures en triggers RUs gebruikt op basis van de complexiteit van de bewerkingen die worden uitgevoerd. Controleer tijdens het ontwikkelen van uw toepassing de [aanvraagkostenheader](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) om meer inzicht te krijgen in hoeveel RU-capaciteit elke bewerking verbruikt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [inrichten van de door Voer voor Azure Cosmos-containers en-data bases](set-throughput.md).
* Meer informatie over [logische partities](partition-data.md).
* Meer informatie over het [wereld wijd schalen van ingerichte door Voer](scaling-throughput.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-data base](how-to-provision-database-throughput.md).
* Meer informatie over [het vinden van de kosten voor aanvraag eenheden voor een bewerking](find-request-unit-charge.md).
* Meer informatie over het [optimaliseren van ingerichte doorvoer kosten in azure Cosmos DB](optimize-cost-throughput.md).
* Meer informatie over het [optimaliseren van de kosten voor lezen en schrijven in azure Cosmos DB](optimize-cost-reads-writes.md).
* Meer informatie over het [optimaliseren van query kosten in azure Cosmos DB](optimize-cost-queries.md).
* Meer informatie over het [gebruik van metrische gegevens voor het bewaken van de door Voer](use-metrics.md).
