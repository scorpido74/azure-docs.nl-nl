---
title: Optimaliseren voor ontwikkeling en testen in Azure Cosmos DB
description: In dit artikel wordt uitgelegd hoe Azure Cosmos DB meerdere opties biedt voor het gratis ontwikkelen en testen van de service.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 992d165d323aab79bb7b5475aa396d4432691530
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754903"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optimaliseer de ontwikkelings-en test kosten in Azure Cosmos DB

In dit artikel worden de verschillende opties beschreven voor het gebruik van Azure Cosmos DB voor het ontwikkelen en testen van kosten.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB-emulator (lokaal te downloaden versie)

[Azure Cosmos DB emulator](local-emulator.md) is een lokale Download bare versie die de Azure Cosmos DB-Cloud service imiteert. U kunt code schrijven en testen die gebruikmaakt van de Azure Cosmos DB-Api's, zelfs als u geen netwerk verbinding hebt en zonder kosten te betalen. Azure Cosmos DB-emulator biedt een lokale omgeving voor ontwikkelings doeleinden met hoge betrouw baarheid voor de Cloud service. U kunt uw toepassing lokaal ontwikkelen en testen zonder dat u een Azure-abonnement hoeft te maken. Wanneer u klaar bent om uw toepassing in de cloud te implementeren, moet u de connection string bijwerken om verbinding te maken met het Azure Cosmos DB-eind punt in de Cloud. er zijn geen andere wijzigingen nodig. U kunt ook [een CI/cd-pijp lijn instellen met de taak Azure Cosmos DB emulator](tutorial-setup-ci-cd.md) bouwen in azure DevOps om tests uit te voeren. Ga naar het artikel [Azure Cosmos DB-emulator](local-emulator.md) om aan de slag te gaan.

## <a name="try-azure-cosmos-db-for-free"></a>Probeer Azure Cosmos DB gratis uit

[Probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) uit. Dit is een gratis ervaring waarmee u data bases en verzamelingen kunt maken en experimenteren met Azure Cosmos db in de Cloud. U hoeft zich niet aan te melden voor Azure of kosten te betalen. De accounts voor try-Azure Cosmos DB zijn momenteel 30 dagen beschikbaar voor een beperkte periode. U kunt ze op elk gewenst moment vernieuwen. Met Azure Cosmos DB accounts kunt u gemakkelijk Azure Cosmos DB evalueren, een toepassing bouwen en testen met behulp van de Quick starts of zelf studies. U kunt een demo maken of een eenheids test uitvoeren zonder kosten te betalen. Als u Azure Cosmos DB probeert te gebruiken voor gratis accounts, kunt u de Premium-mogelijkheden van Azure Cosmos DB gratis evalueren, inclusief kant-en-klare wereld wijde distributie, Sla's en consistentie modellen. U kunt een Data Base maken met een maximum van 25 Azure Cosmos-containers en 10.000 RU/s van door voer. U kunt de voorbeeld toepassing uitvoeren zonder u te abonneren op een Azure-account of uw credit card te gebruiken. Als u Azure Cosmos DB gratis wilt gebruiken, kunt u een Azure Cosmos-account met meerdere regio's maken en in slechts een paar minuten een app uitvoeren. Zie [Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) pagina om aan de slag te gaan.

## <a name="azure-free-account"></a>Gratis Azure-account

Azure Cosmos DB is opgenomen in het [gratis Azure-account](https://azure.microsoft.com/free), waarmee Azure-tegoeden en-bronnen gratis voor een bepaalde periode worden aangeboden. Met name voor Azure Cosmos DB biedt deze gratis account 5 GB opslag ruimte en 400 RUs van ingerichte door Voer voor het hele jaar. Met deze ervaring kunnen ontwikkel aars eenvoudig de functies van Azure Cosmos DB testen of deze integreren met andere Azure-Services tegen nul kosten. Met een gratis Azure-account krijgt u een tegoed van $200 van de eerste 30 dagen. Er worden geen kosten in rekening gebracht, zelfs als u begint met het gebruik van de services totdat u ervoor kiest om te upgraden. Ga naar de pagina met [gratis Azure-account](https://azure.microsoft.com/free) om aan de slag te gaan.

## <a name="next-steps"></a>Volgende stappen

U kunt aan de slag met het gebruik van de emulator of de gratis Azure Cosmos DB accounts met de volgende artikelen:

* Meer informatie over het [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [uw Azure Cosmos DB factuur](understand-your-bill.md)
* Meer informatie over het [optimaliseren van doorvoer kosten](optimize-cost-throughput.md)
* Meer informatie over het [optimaliseren van opslag kosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lees-en schrijf bewerkingen](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](optimize-cost-queries.md)
* Meer informatie over [het optimaliseren van de kosten voor Azure Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)

