---
title: Optimaliseren voor ontwikkeling en testen in Azure Cosmos DB
description: In dit artikel wordt uitgelegd hoe Azure Cosmos DB meerdere opties biedt voor het gratis ontwikkelen en testen van de service.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: d2ca7b7e4b637802df6a78c2493e3cc088f09881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246680"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Ontwikkelings- en testkosten optimaliseren in Azure Cosmos DB

In dit artikel worden de verschillende opties beschreven om Azure Cosmos DB gratis te gebruiken voor ontwikkeling en testen, evenals technieken om de kosten voor ontwikkeling of testaccounts te optimaliseren.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB emulator (lokaal downloadbare versie)

[Azure Cosmos DB emulator](local-emulator.md) is een lokale downloadbare versie die de Azure Cosmos DB cloud service nabootst. U code schrijven en testen die gebruik maakt van de Azure Cosmos DB API's, zelfs als u geen netwerkverbinding hebt en zonder kosten te maken. Azure Cosmos DB emulator biedt een lokale omgeving voor ontwikkelingsdoeleinden met hoge trouw aan de cloudservice. U uw toepassing lokaal ontwikkelen en testen, zonder een Azure-abonnement te maken. Wanneer u klaar bent om uw toepassing naar de cloud te implementeren, werkt u de verbindingstekenreeks bij om verbinding te maken met het Azure Cosmos DB-eindpunt in de cloud, zijn er geen andere wijzigingen nodig. U ook [een CI/CD-pijplijn instellen met de Azure Cosmos DB-emulatorbuildtaak](tutorial-setup-ci-cd.md) in Azure DevOps om tests uit te voeren. U aan de slag door het [Azure Cosmos DB-emulatorartikel te](local-emulator.md) bezoeken.

## <a name="azure-cosmos-db-free-tier"></a>Gratis Azure Cosmos DB-laag 
Azure Cosmos DB free tier maakt het gemakkelijk om aan de slag te gaan, uw toepassingen te ontwikkelen en te testen, of zelfs kleine productieworkloads gratis uit te voeren. Wanneer de gratis laag is ingeschakeld voor een account, krijgt u de eerste 400 RU/s en 5 GB opslagruimte gratis in het account. U ook een gedeelde doorvoerdatabase maken met 25 containers die 400 RU/s delen op databaseniveau, allemaal gedekt door een gratis laag (beperk 5 gedeelde doorvoerdatabases in een gratis laagaccount). Gratis laag duurt voor onbepaalde tijd voor de levensduur van het account en wordt geleverd met alle [voordelen en functies](introduction.md#key-benefits) van een gewone Azure Cosmos DB-account, met inbegrip van onbeperkte opslag en doorvoer (RU / s), SLA's, hoge beschikbaarheid, turnkey wereldwijde distributie in alle Azure-regio's, en nog veel meer. U maximaal één gratis laagaccount per Azure-abonnement hebben en u moet zich aanmelden bij het maken van het account. Maak een nieuw account aan om aan de slag te gaan [en maak een nieuw account aan met de ingeschakelde gratis laag.](create-cosmosdb-resources-portal.md) Bekijk de [prijzenpagina](https://azure.microsoft.com/pricing/details/cosmos-db/) voor meer informatie. 

## <a name="try-azure-cosmos-db-for-free"></a>Probeer Azure Cosmos DB gratis uit

[Probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) is een gratis ervaring waarmee u experimenteren met Azure Cosmos DB in de cloud zonder u aan te melden voor een Azure-account of uw creditcard. De Try Azure Cosmos DB-accounts zijn momenteel 30 dagen beschikbaar voor een beperkte tijd. U ze op elk gewenst moment vernieuwen. Probeer Azure Cosmos DB-accounts maakt het eenvoudig om Azure Cosmos DB te evalueren, een toepassing te bouwen en te testen of de Quickstarts of tutorials te gebruiken. U ook een demo maken, eenheidstests uitvoeren of zelfs een multi-regio-account maken en er een app op uitvoeren zonder kosten te maken. In een Try Azure Cosmos DB-account u één gedeelde doorvoerdatabase hebben met maximaal 25 containers en 20.000 RU/s doorvoer, of één container met maximaal 5000 RU/s. Zie [Azure Cosmos DB uitproberen voor de vrije](https://azure.microsoft.com/try/cosmosdb/) pagina om aan de slag te gaan.

## <a name="azure-free-account"></a>Gratis Azure-account

Azure Cosmos DB is opgenomen in het [gratis Azure-account,](https://azure.microsoft.com/free)dat Azure-credits en -bronnen gratis biedt voor een bepaalde periode. Specifiek voor Azure Cosmos DB biedt dit gratis account 5 GB opslag en 400 RU's met ingerichte doorvoer voor het hele jaar. Met deze ervaring kan elke ontwikkelaar de functies van Azure Cosmos DB eenvoudig testen of zonder kosten integreren met andere Azure-services. Met Azure gratis account, krijg je een $ 200 krediet te besteden in de eerste 30 dagen. Er worden geen kosten in rekening gebracht, zelfs niet als u de services gaat gebruiken totdat u ervoor kiest om te upgraden. Ga om te beginnen naar [de gratis accountpagina van Azure.](https://azure.microsoft.com/free)

## <a name="use-shared-throughput-databases"></a>Gedeelde doorvoerdatabases gebruiken

In een [gedeelde doorvoerdatabase](set-throughput.md#set-throughput-on-a-database)delen alle containers in de database de ingerichte doorvoer (RU/s) van de database. Als u bijvoorbeeld een database indient met 400 RU/s en vier containers hebt, delen alle vier de containers de 400 RU/s. In een ontwikkel- of testomgeving, waar elke container minder vaak toegankelijk is en dus lager is dan het minimum van 400 RU/s, kan het plaatsen van containers in een gedeelde doorvoerdatabase helpen bij het optimaliseren van de kosten. 

Stel dat uw ontwikkel- of testaccount vier containers heeft. Als u vier containers maakt met een speciale doorvoer (minimaal 400 RU/s), bedraagt uw totale RU/s 1600 RU/s. Als u daarentegen een gedeelde doorvoerdatabase (minimaal 400 RU/s) maakt en uw containers daar plaatst, zijn uw totale RU/s slechts 400 RU/s. Over het algemeen zijn gedeelde doorvoerdatabases ideaal voor scenario's waarin u geen gegarandeerde doorvoer op een afzonderlijke container nodig hebt.  Meer informatie over [gedeelde doorvoerdatabases.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Volgende stappen

U aan de slag met het gebruik van de emulator of de gratis Azure Cosmos DB-accounts met de volgende artikelen:

* Meer informatie over [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [het begrijpen van uw Azure Cosmos DB-factuur](understand-your-bill.md)
* Meer informatie over [het optimaliseren van doorvoerkosten](optimize-cost-throughput.md)
* Meer informatie over [het optimaliseren van opslagkosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lezen en schrijven](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](optimize-cost-queries.md)
* Meer informatie over [het optimaliseren van de kosten van Azure Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)

