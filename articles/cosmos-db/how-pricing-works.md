---
title: Prijs model van Azure Cosmos DB
description: In dit artikel wordt het prijs model van Azure Cosmos DB uitgelegd en hoe het kosten beheer en de kosten planning worden vereenvoudigd.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7efae8fb3c00868e2740eac2d4d5bcb3c82f663a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977543"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Prijs model in Azure Cosmos DB 

Het prijs model van Azure Cosmos DB vereenvoudigt het kosten beheer en de planning. Met Azure Cosmos DB betaalt u voor de ingerichte door Voer en de opslag die u gebruikt.

* **Ingerichte door Voer**: ingerichte door Voer (ook wel gereserveerde door Voer genoemd) garandeert hoge prestaties op elke schaal. U geeft de door Voer (RU/s) op die u nodig hebt, en Azure Cosmos DB exclusief de benodigde resources om de geconfigureerde door voer te garanderen. U wordt per uur gefactureerd voor de maximale ingerichte door Voer voor een bepaald uur.

   > [!NOTE]
   > Omdat het ingerichte doorvoer model resources toekent aan uw container of Data Base, worden er kosten in rekening gebracht voor de ingerichte door Voer, zelfs als u geen workloads uitvoert.

* **Verbruikte opslag**: er wordt een vast bedrag in rekening gebracht voor de totale hoeveelheid opslag ruimte (GB) die wordt verbruikt voor gegevens en de indexen voor een bepaald uur.

Door de ingerichte door Voer, opgegeven als [aanvraag eenheden](request-units.md) per seconde (ru/s), kunt u gegevens lezen van of schrijven naar containers of data bases. U kunt de [door Voer voor een Data Base of container inrichten](set-throughput.md). Op basis van de behoeften van uw werk belasting kunt u de door Voer op elk gewenst moment omhoog/omlaag schalen. Azure Cosmos DB prijs is elastisch en is evenredig met de door Voer die u configureert in een Data Base of container. De minimale doorvoer-en opslag waarden en de schaal verhogingen bieden een breed scala aan prijzen versus elasticiteits spectrum voor alle segmenten van klanten, van kleinschalige schaal tot grootschalige containers. Elke Data Base of container wordt per uur gefactureerd voor de door Voer die is ingericht in de eenheden van 100 RU/s, met een minimum van 400 RU/s en opslag verbruikt in GB. In tegens telling tot ingerichte door Voer wordt opslag gefactureerd op basis van verbruik. Dat wil zeggen dat u geen enkele opslag vooraf hoeft te reserveren. U wordt alleen gefactureerd voor de opslag die u gebruikt.

Zie voor meer informatie de [pagina met Azure Cosmos DB prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) en [informatie over uw Azure Cosmos DB factuur](understand-your-bill.md).

Het prijs model in Azure Cosmos DB is consistent voor alle Api's. Zie [hoe Azure Cosmos DB prijs model rendabel is voor klanten voor](total-cost-ownership.md)meer informatie. Er is een minimale door Voer vereist voor een Data Base of container om de service overeenkomsten te controleren en u kunt de ingerichte door $6 Voer voor elke 100 RU/s verg Roten of verkleinen.

Op dit moment is de minimum prijs voor zowel de Data Base als de door Voer op basis van een container $24/maand (Zie de [pagina met Azure Cosmos DB prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor de meest recente informatie. Als uw werk belasting meerdere containers gebruikt, kan deze worden geoptimaliseerd voor kosten met behulp van de door Voer van het data base-niveau, omdat door Voer via database niveau een wille keurig aantal containers in een Data Base met de door Voer tussen de containers kan worden gedeeld. De volgende tabel bevat een overzicht van de ingerichte door Voer en de kosten voor verschillende entiteiten:

|**Entiteit**  | **Minimale door Voer & kosten** |**Schaal verhogingen & kosten** |**Inrichtings bereik** |
|---------|---------|---------|-------|
|Database    | 400 RU/s ($ 24/maand)    | 100 RU/s ($ 6/maand)   |De door Voer is gereserveerd voor de data base en wordt gedeeld door containers in de data base |
|Container     | 400 RU/s ($ 24/maand)    | 100 RU/s ($ 6/maand)  |De door Voer is gereserveerd voor een specifieke container |

Zoals u in de voor gaande tabel ziet, begint de minimale door Voer in Azure Cosmos DB met een prijs van $24/maand. Als u begint met de minimale door Voer en er meer tijd wordt geschaald om uw productie workloads te ondersteunen, stijgen uw kosten soepel, in de stappen van $6/maand. Het prijs model in Azure Cosmos DB is elastisch en er is een soepele of afname van de prijs bij het omhoog of omlaag schalen.

## <a name="try-azure-cosmos-db-for-free"></a>Probeer Azure Cosmos DB gratis uit 

Azure Cosmos DB biedt gratis verschillende opties voor ontwikkel aars. Het gaat om deze opties:

* **Gratis Azure-account**: Azure biedt een [gratis laag](https://azure.microsoft.com/free/) met $200 in azure-tegoed voor de eerste 30 dagen en een beperkt aantal gratis services gedurende 12 maanden. Zie [Gratis Azure-account](../cost-management-billing/manage/avoid-charges-free-account.md) voor meer informatie. Azure Cosmos DB maakt deel uit van een gratis Azure-account. Met name voor Azure Cosmos DB biedt deze gratis account 5 GB opslag ruimte en 400 RUs van ingerichte door Voer voor het hele jaar. 

* **Probeer Azure Cosmos DB gratis**: Azure Cosmos DB biedt een tijdrovende ervaring met behulp van de Azure Cosmos DB voor gratis accounts. U kunt een Azure Cosmos DB account maken, data bases en verzamelingen maken en een voorbeeld toepassing uitvoeren met behulp van de Quick starts en zelf studies. U kunt de voorbeeld toepassing uitvoeren zonder u te abonneren op een Azure-account of uw credit card te gebruiken. [Probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) aanbiedingen Azure Cosmos DB gedurende één maand, waarbij u uw account een wille keurig aantal keren kunt verlengen.

* **Azure Cosmos DB emulator**: Azure Cosmos DB-emulator biedt een lokale omgeving die de Azure Cosmos DB-Service voor ontwikkelings doeleinden emuleert. Emulator wordt gratis aangeboden en biedt een hoge mate van nauw keurigheid voor de Cloud service. Met behulp van Azure Cosmos DB-emulator kunt u uw toepassingen lokaal ontwikkelen en testen zonder dat u een Azure-abonnement hoeft te maken of kosten te besparen. U kunt uw toepassingen met behulp van de emulator lokaal ontwikkelen voordat u deze gaat produceren. Nadat u tevreden bent met de functionaliteit van de toepassing met de emulator, kunt u overschakelen naar het Azure Cosmos DB-account in de Cloud en besparen op kosten. Zie [Azure Cosmos DB gebruiken voor ontwikkelen en testen](local-emulator.md) voor meer informatie over emulator.

## <a name="pricing-with-reserved-capacity"></a>Prijzen met gereserveerde capaciteit

Azure Cosmos DB [gereserveerde capaciteit](cosmos-db-reserved-capacity.md) helpt u geld te besparen door voor Azure Cosmos DB resources voor een jaar of drie jaar vooraf te betalen. U kunt uw kosten aanzienlijk verlagen met één jaar of drie jaar vooraf-toezeg gingen en besparen tussen 20-65% kortingen in vergelijking met de reguliere prijzen. Azure Cosmos DB gereserveerde capaciteit helpt u kosten te verlagen door vooraf te betalen voor de ingerichte door Voer (RU/s) gedurende een periode van één of drie jaar en u krijgt een korting op de ingerichte door voer. 

Gereserveerde capaciteit biedt een facturerings korting en heeft geen invloed op de runtime status van uw Azure Cosmos DB-resources. Gereserveerde capaciteit is consistent beschikbaar voor alle Api's, waaronder MongoDB-, Cassandra-, SQL-, Gremlin-en Azure-tabellen en alle regio's over de hele wereld. Meer informatie over gereserveerde capaciteit in vooruitbetalen vindt u [voor Azure Cosmos DB resources met een gereserveerde-capaciteits](cosmos-db-reserved-capacity.md) artikel en het kopen van gereserveerde capaciteit van de [Azure Portal](https://portal.azure.com/).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het optimaliseren van de kosten voor uw Azure Cosmos DB-resources vindt u in de volgende artikelen:

* Meer informatie over het [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [uw Azure Cosmos DB factuur](understand-your-bill.md)
* Meer informatie over het [optimaliseren van doorvoer kosten](optimize-cost-throughput.md)
* Meer informatie over het [optimaliseren van opslag kosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lees-en schrijf bewerkingen](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](optimize-cost-queries.md)
* Meer informatie over [het optimaliseren van de kosten van Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)
* Meer informatie over [Azure Cosmos DB gereserveerde capaciteit](cosmos-db-reserved-capacity.md)
* Meer informatie over [Azure Cosmos DB-emulator](local-emulator.md)
