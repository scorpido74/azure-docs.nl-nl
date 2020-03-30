---
title: Prijsmodel van Azure Cosmos DB
description: In dit artikel wordt het prijsmodel van Azure Cosmos DB uitgelegd en hoe het uw kostenbeheer en kostenplanning vereenvoudigt.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7efae8fb3c00868e2740eac2d4d5bcb3c82f663a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75977543"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Prijsmodel in Azure Cosmos DB 

Met het prijsmodel van Azure Cosmos DB wordt beheren en plannen van de kosten vereenvoudigd. Met Azure Cosmos DB betaalt u voor de ingerichte doorvoer en voor de opslag die u verbruikt.

* **Ingerichte doorvoer**: Ingerichte doorvoer (ook wel gereserveerde doorvoer genoemd) garandeert hoge prestaties op elke schaal. U geeft de doorvoer (RU/s) op die u nodig hebt en Azure Cosmos DB besteedt de resources die nodig zijn om de geconfigureerde doorvoer te garanderen. U wordt per uur gefactureerd voor de maximaal ingerichte doorvoer voor een bepaald uur.

   > [!NOTE]
   > Omdat het ingerichte doorvoermodel resources aan uw container of database wijdt, worden er kosten in rekening gebracht voor de ingerichte doorvoer, zelfs als u geen workloads uitvoert.

* **Verbruikte opslag:** er wordt een vast tarief in rekening gebracht voor de totale hoeveelheid opslagruimte (GB's) die gedurende een bepaald uur voor gegevens en de indexen worden verbruikt.

Met de ingerichte doorvoer, die is opgegeven als [Aanvraageenheden](request-units.md) per seconde (RU/s), u gegevens lezen of schrijven in containers of databases. U [doorvoer inrichten op een database of een container.](set-throughput.md) Op basis van uw werkbelastingbehoeften u de doorvoer op elk gewenst moment omhoog/omlaag schalen. De prijzen van Azure Cosmos DB zijn elastisch en zijn evenredig aan de doorvoer die u configureert in een database of een container. De minimale doorvoer- en opslagwaarden en de schaalverhogingen bieden een volledig bereik van prijs versus elasticiteitsspectrum voor alle segmenten van klanten, van kleinschalige tot grootschalige containers. Elke database of container wordt per uur gefactureerd voor de doorvoer die is ingericht in de eenheden van 100 RU/s, met een minimum van 400 RU/s, en opslag verbruikt in GB's. In tegenstelling tot de ingerichte doorvoer wordt opslag gefactureerd op basis van verbruik. Dat wil zeggen, u hoeft geen opslag te reserveren op voorhand. U wordt alleen gefactureerd voor de opslagruimte die u verbruikt.

Zie de [prijspagina azure cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) en [Inzicht in uw Azure Cosmos DB-factuur](understand-your-bill.md)voor meer informatie.

Het prijsmodel in Azure Cosmos DB is consistent voor alle API's. Zie hoe het [Azure Cosmos DB-prijsmodel kosteneffectief is voor klanten](total-cost-ownership.md)voor meer informatie. Er is een minimale doorvoer vereist op een database of een container om ervoor te zorgen dat de SLA's en u verhogen of verlagen van de ingerichte doorvoer met $ 6 voor elke 100 RU / s.

Momenteel is de minimumprijs voor zowel database als de containerdoorvoer $ 24 per maand (zie de [prijspagina van Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) voor de laatste informatie. Als uw werkbelasting meerdere containers gebruikt, kan deze worden geoptimaliseerd voor kosten door de doorvoer op databaseniveau te gebruiken, omdat u met de doorvoer op databaseniveau een willekeurig aantal containers in een database hebben die de doorvoer tussen de containers delen. In de volgende tabel worden de ingerichte doorvoer en de kosten voor verschillende entiteiten samengevat:

|**Entiteit**  | **Minimale doorvoer & kosten** |**Schaalverhogingen & kosten** |**Inrichtingskader** |
|---------|---------|---------|-------|
|Database    | 400 RU/s ($24/month)    | 100 RU/s ($6/month)   |Doorvoer is gereserveerd voor de database en wordt gedeeld door containers in de database |
|Container     | 400 RU/s ($24/month)    | 100 RU/s ($6/month)  |Doorvoer is gereserveerd voor een specifieke container |

Zoals in de vorige tabel wordt weergegeven, begint de minimale doorvoer in Azure Cosmos DB bij een prijs van $ 24 per maand. Als u begint met de minimale doorvoer en na verloop van tijd wordt opgeschaald om uw productieworkloads te ondersteunen, stijgen uw kosten soepel, in de stappen van $ 6 per maand. Het prijsmodel in Azure Cosmos DB is elastisch en er is een soepele stijging of daling van de prijs als u omhoog of omlaag schaalt.

## <a name="try-azure-cosmos-db-for-free"></a>Probeer Azure Cosmos DB gratis uit 

Azure Cosmos DB biedt verschillende opties voor ontwikkelaars om het gratis. Het gaat om deze opties:

* **Gratis Azure-account:** Azure biedt een [gratis laag](https://azure.microsoft.com/free/) die u $ 200 in Azure-credits biedt voor de eerste 30 dagen en een beperkte hoeveelheid gratis services gedurende 12 maanden. Zie [Gratis Azure-account](../cost-management-billing/manage/avoid-charges-free-account.md) voor meer informatie. Azure Cosmos DB is een onderdeel van azure gratis account. Specifiek voor Azure Cosmos DB biedt dit gratis account 5 GB opslag en 400 RU's met ingerichte doorvoer voor het hele jaar. 

* **Probeer Azure Cosmos DB gratis:** Azure Cosmos DB biedt een beperkte tijd door Azure Cosmos DB voor gratis accounts te proberen. U een Azure Cosmos DB-account maken, database en verzamelingen maken en een voorbeeldtoepassing uitvoeren met behulp van de Quickstarts en tutorials. U de voorbeeldtoepassing uitvoeren zonder u te abonneren op een Azure-account of met uw creditcard. [Probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) biedt Azure Cosmos DB voor een maand, met de mogelijkheid om uw account te vernieuwen een aantal keren.

* **Azure Cosmos DB emulator**: Azure Cosmos DB emulator biedt een lokale omgeving die de Azure Cosmos DB-service emuleert voor ontwikkelingsdoeleinden. Emulator wordt aangeboden zonder kosten en met een hoge trouw aan de cloud service. Met behulp van Azure Cosmos DB emulator u uw toepassingen lokaal ontwikkelen en testen, zonder een Azure-abonnement te maken of kosten te maken. U uw toepassingen ontwikkelen door de emulator lokaal te gebruiken voordat u in productie gaat. Nadat u tevreden bent met de functionaliteit van de toepassing ten opzichte van de emulator, u overschakelen naar het Azure Cosmos DB-account in de cloud gebruiken en aanzienlijk besparen op kosten. Zie [Azure Cosmos DB gebruiken voor ontwikkeling en testen voor](local-emulator.md) meer informatie over emulators voor meer informatie.

## <a name="pricing-with-reserved-capacity"></a>Prijzen met gereserveerde capaciteit

Met [de gereserveerde capaciteit van](cosmos-db-reserved-capacity.md) Azure Cosmos DB u geld besparen door een jaar of drie vooraf te betalen voor Azure Cosmos DB-resources. U uw kosten aanzienlijk verlagen met een jaar of drie jaar vooraf toezeggingen en bespaar tussen de 20-65% kortingen in vergelijking met de reguliere prijzen. Azure Cosmos DB gereserveerde capaciteit helpt u de kosten te verlagen door vooraf te betalen voor de ingerichte doorvoer (RU/s) voor een periode van een jaar of drie jaar en u krijgt een korting op de bedienbare doorvoer. 

RU biedt een factureringskorting en heeft geen invloed op de runtimestatus van Azure Cosmos DB-resources. Gereserveerde capaciteit is consistent beschikbaar voor alle API's, waaronder MongoDB, Cassandra, SQL, Gremlin en Azure Tables en alle regio's wereldwijd. U meer informatie vinden over gereserveerde capaciteit in [Prepay voor Azure Cosmos DB-resources met een](cosmos-db-reserved-capacity.md) artikel over gereserveerde capaciteit en gereserveerde capaciteit kopen via de [Azure-portal.](https://portal.azure.com/)

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen vindt u meer informatie over het optimaliseren van de kosten voor uw Azure Cosmos DB-resources:

* Meer informatie over [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [het begrijpen van uw Azure Cosmos DB-factuur](understand-your-bill.md)
* Meer informatie over [het optimaliseren van doorvoerkosten](optimize-cost-throughput.md)
* Meer informatie over [het optimaliseren van opslagkosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lezen en schrijven](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](optimize-cost-queries.md)
* Meer informatie over [het optimaliseren van de kosten van multi-region Cosmos-accounts](optimize-cost-regions.md)
* Meer informatie over [de gereserveerde capaciteit van Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Meer informatie over [Azure Cosmos DB Emulator](local-emulator.md)
