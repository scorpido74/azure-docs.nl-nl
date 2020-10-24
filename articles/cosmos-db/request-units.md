---
title: Eenheden aanvragen als een door Voer en een prestatie valuta in Azure Cosmos DB
description: Meer informatie over het opgeven en ramen van vereisten voor aanvraag eenheden in Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 556b3915336d0ca9cec8ac67a34778cfb2523cbe
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92475070"
---
# <a name="request-units-in-azure-cosmos-db"></a>Aanvraageenheden in Azure Cosmos DB

Azure Cosmos DB ondersteunt veel Api's, zoals SQL, MongoDB, Cassandra, Gremlin en Table. Elke API heeft een eigen set database bewerkingen. Deze bewerkingen variëren van eenvoudig punt Lees-en schrijf bewerkingen naar complexe query's. Bij elke database bewerking worden systeem resources verbruikt op basis van de complexiteit van de bewerking.

De kosten van alle database bewerkingen worden genormaliseerd door Azure Cosmos DB en worden uitgedrukt in *aanvraag eenheden* (of RUs voor short). U kunt RUs beschouwen als een prestatie valuta die de systeem bronnen aanabstractt, zoals CPU, IOPS en geheugen die nodig zijn om de database bewerkingen uit te voeren die door Azure Cosmos DB worden ondersteund.

De kosten voor het lezen van een punt (dat wil zeggen het ophalen van één item met de ID en de partitie sleutel waarde) voor een 1 KB-item is 1 aanvraag eenheid (of 1 RU). Aan alle overige databasebewerkingen worden op dezelfde manier kosten toegewezen in de vorm van RU's. Ongeacht welke API u gebruikt om met uw Azure Cosmos-container te communiceren - de kosten worden altijd gemeten in RU's. Of de database bewerking een schrijven, een lees punt of een query is, worden de kosten altijd gemeten in RUs.

In de volgende afbeelding ziet u het idee op hoog niveau met betrekking tot RU's:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Database bewerkingen gebruiken aanvraag eenheden" border="false":::

Azure Cosmos DB zorgt ervoor dat het aantal RU's voor een bepaalde databasebewerking voor een bepaalde gegevensset deterministisch is om de capaciteit te kunnen beheren en plannen. U kunt de antwoordheader controleren om het aantal RU's bij te houden dat door een databasebewerking wordt gebruikt. Wanneer u de [factoren begrijpt die van invloed zijn op de ru-kosten](request-units.md#request-unit-considerations) en de doorvoer vereisten van uw toepassing, kunt u uw toepassing effectief uitvoeren.

Het type Azure Cosmos-account dat u gebruikt, bepaalt de manier waarop het verbruikte RUs in rekening wordt gebracht:

- In de [ingerichte doorvoer](set-throughput.md) modus voorziet u het aantal RUs voor uw toepassing per seconde op basis van een interval van 100 RUs per seconde. Als u de ingerichte door Voer voor uw toepassing wilt schalen, kunt u het aantal RUs op elk gewenst moment verg Roten of verkleinen in stappen of verlaagt u 100 RUs. U kunt uw wijzigingen programmatisch aanbrengen of via Azure Portal. U wordt gefactureerd op basis van de hoeveelheid RUs per seconde die u hebt ingericht. U kunt de door Voer op twee verschillende granulariteit inrichten:
  - **Containers**: Zie [door Voer inrichten op een Azure Cosmos-container](how-to-provision-container-throughput.md)voor meer informatie.
  - **Data bases**: Zie [door Voer inrichten voor een Azure Cosmos-data base](how-to-provision-database-throughput.md)voor meer informatie.
- In de [serverloze](serverless.md) modus hoeft u geen door Voer in te richten bij het maken van resources in uw Azure Cosmos-account. Aan het einde van de facturerings periode wordt u gefactureerd voor de hoeveelheid aanvraag eenheden die is verbruikt door uw database bewerkingen.

## <a name="request-unit-considerations"></a>Aandachtspunten voor aanvraageenheden

Houd rekening met de volgende factoren wanneer u een schatting maakt van het aantal dat door uw workload verbruikt.

- **Itemgrootte**: Als de grootte van een item toeneemt, neemt ook het aantal verbruikte RU's om het item te lezen of schrijven toe.

- **Itemindexing**: Elk item wordt standaard automatisch geïndexeerd. Er worden minder RU's verbruikt als u ervoor kiest bepaalde items in een container niet te indexeren.

- **Het aantal itemeigenschappen**: Ervan uitgaande dat alle eigenschappen de standaardindexering hebben, wordt het aantal verbruikte RU's om een item te schrijven, groter naarmate het aantal itemeigenschappen toeneemt.

- **Geïndexeerde eigenschappen**: Een indexbeleid voor elke container bepaalt welke eigenschappen standaard worden geïndexeerd. Beperk het aantal geïndexeerde eigenschappen om het RU-verbruik voor schrijfbewerkingen te verlagen.

- **Gegevens consistentie**: de sterke en gebonden consistentie niveaus voor veroudering worden ongeveer twee keer meer gebruikt tijdens het uitvoeren van Lees bewerkingen in vergelijking met die van andere beperkte consistentie niveaus.

- **Type Lees bewerkingen**: punt besparingen zijn aanzienlijk minder RUs dan query's.

- **Querypatronen**: De complexiteit van een query beïnvloedt het aantal verbruikte RU's voor een bewerking. Factoren die invloed hebben op de kosten van querybewerkingen: 

  - Het aantal queryresultaten
  - Het aantal predicaten
  - De aard van de predicaten
  - Het aantal door de gebruiker gedefinieerde functies
  - De grootte van de brongegevens
  - De grootte van de resultatenset
  - Projecties

  Dezelfde query op dezelfde gegevens kost altijd hetzelfde aantal RUs bij herhaalde uitvoeringen.

- **Script gebruik**: net als bij query's, worden voor opgeslagen procedures en triggers RUs gebruikt op basis van de complexiteit van de bewerkingen die worden uitgevoerd. Controleer tijdens het ontwikkelen van uw toepassing de [aanvraagkostenheader](./optimize-cost-reads-writes.md#measuring-the-ru-charge-of-a-request) om meer inzicht te krijgen in hoeveel RU-capaciteit elke bewerking verbruikt.

## <a name="request-units-and-multiple-regions"></a>Aanvraag eenheden en meerdere regio's

Als u *' r '* Rus inricht in een Cosmos-container (of-data base), zorgt Cosmos DB ervoor dat *' r '* Rus beschikbaar is in *elke* regio die aan uw Cosmos-account is gekoppeld. U kunt RUs niet selectief toewijzen aan een bepaalde regio. Het RUs-aanbod dat is ingericht voor een Cosmos-container (of-data base) wordt ingericht in alle regio's die zijn gekoppeld aan uw Cosmos-account.

Ervan uitgaande dat een Cosmos-container is geconfigureerd met *' R '* RUs en dat er *N* -regio's zijn gekoppeld aan het Cosmos-account, het totale RUs-totaal beschikbaar op de container = *R* x *N*.

Uw keuze van [consistentie model](consistency-levels.md) is ook van invloed op de door voer. U kunt ongeveer 2x Lees doorvoer bereiken voor de minder consistente consistentie niveaus (zoals een *sessie*, *consistent voor voegsel* en *uiteindelijke* consistentie) in vergelijking met sterkere consistentie niveaus (bijvoorbeeld *gebonden veroudering* of *sterke* consistentie).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [inrichten van de door Voer voor Azure Cosmos-containers en-data bases](set-throughput.md).
- Meer informatie over [serverloze op Azure Cosmos DB](serverless.md).
- Meer informatie over [logische partities](./partitioning-overview.md).
- Meer informatie over het [wereld wijd schalen van ingerichte door Voer]().
- Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-container](how-to-provision-container-throughput.md).
- Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-data base](how-to-provision-database-throughput.md).
- Meer informatie over [het vinden van de kosten voor aanvraag eenheden voor een bewerking](find-request-unit-charge.md).
- Meer informatie over het [optimaliseren van ingerichte doorvoer kosten in azure Cosmos DB](optimize-cost-throughput.md).
- Meer informatie over het [optimaliseren van de kosten voor lezen en schrijven in azure Cosmos DB](optimize-cost-reads-writes.md).
- Meer informatie over het [optimaliseren van query kosten in azure Cosmos DB](./optimize-cost-reads-writes.md).
- Meer informatie over het [gebruik van metrische gegevens voor het bewaken van de door Voer](use-metrics.md).