---
title: Prestatie- en schaaltests met Azure Cosmos DB
description: Meer informatie over het uitvoeren van schaal- en prestatietests met Azure Cosmos DB. Vervolgens u de functionaliteit van Azure Cosmos DB evalueren voor krachtige toepassingsscenario's.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: fb510c5628913fb3fa37b572c4409aee5d1028ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76313742"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Prestatie- en schaaltests met Azure Cosmos DB

Prestatie- en schaaltesten is een belangrijke stap in de ontwikkeling van toepassingen. Voor veel toepassingen heeft de databaselaag een aanzienlijke impact op de algehele prestaties en schaalbaarheid. Daarom is het een essentieel onderdeel van prestatietests. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) is speciaal gebouwd voor elastische schaal en voorspelbare prestaties. Deze mogelijkheden maken het een geweldige pasvorm voor toepassingen die een high-performance database laag nodig hebben. 

Dit artikel is een referentie voor ontwikkelaars die prestatietestsuites implementeren voor hun Azure Cosmos DB-workloads. Het kan ook worden gebruikt om Azure Cosmos DB te evalueren voor krachtige toepassingsscenario's. Het richt zich voornamelijk op geïsoleerde prestatietests van de database, maar bevat ook best practices voor productietoepassingen.

Na het lezen van dit artikel u de volgende vragen beantwoorden: 

* Waar kan ik een voorbeeld .NET-clienttoepassing vinden voor prestatietests van Azure Cosmos DB? 
* Hoe bereik ik hoge doorvoerniveaus met Azure Cosmos DB vanuit mijn clienttoepassing?

Download het project van azure [cosmos DB-prestatietests om](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)aan de slag te gaan met code. 

> [!NOTE]
> Het doel van deze toepassing is om aan te tonen hoe u de beste prestaties van Azure Cosmos DB krijgen met een klein aantal clientmachines. Het doel van het voorbeeld is niet om de piekdoorvoercapaciteit van Azure Cosmos DB te bereiken (die zonder beperkingen kan worden geschaald).
> 
> 

Zie [Azure Cosmos DB-prestatietips](performance-tips.md)als u op zoek bent naar configuratieopties aan de clientzijde om de prestaties van Azure Cosmos DB te verbeteren.

## <a name="run-the-performance-testing-application"></a>De toepassing voor prestatietests uitvoeren
De snelste manier om aan de slag te gaan, is door het .NET-voorbeeld samen te stellen en uit te voeren, zoals beschreven in de volgende stappen. U ook de broncode bekijken en vergelijkbare configuraties implementeren voor uw eigen clienttoepassingen.

**Stap 1:** Download het project uit het voorbeeld van [Azure Cosmos DB-prestatietests](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)of vork de GitHub-opslagplaats.

**Stap 2:** Wijzig de instellingen voor EndpointUrl, AuthorizationKey, CollectionThroughput en DocumentTemplate (optioneel) in App.config.

> [!NOTE]
> Raadpleeg de [pagina Prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) om de kosten per verzameling te schatten voordat u verzamelingen met een hoge doorvoer indient. Azure Cosmos DB factureert opslag en doorvoer onafhankelijk van uur. U kosten besparen door de doorvoer van uw Azure Cosmos-containers na het testen te verwijderen of te verlagen.
> 
> 

**Stap 3:** Compileer en voer de console-app uit vanaf de opdrachtregel. Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

    C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://arramacquerymetrics.documents.azure.com:443/
    Collection : db.data at 100000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: -1
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Found collection data with 100000 RU/s
    Starting Inserts with 100 tasks
    Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
    Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
    Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
    Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
    Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
    Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
    Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
    Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
    Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
    Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
    Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
    Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
    Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
    Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
    Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
    Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
    Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
    Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
    Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
    Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.
    Press any key to exit...


**Stap 4 (indien nodig):** De gerapporteerde doorvoer (RU/s) van het gereedschap moet dezelfde of hoger zijn dan de ingerichte doorvoer van de verzameling of een reeks verzamelingen. Als dit niet het is, kan het verhogen van de MateOfParallelisme in kleine stappen u helpen de limiet te bereiken. Als de doorvoer van de client-app-plateaus is, start u meerdere exemplaren van de app op extra clientmachines. Als u hulp nodig hebt bij deze stap, u een ondersteuningsticket indienen vanuit de [Azure-portal.](https://portal.azure.com)

Nadat u de app hebt uitgevoerd, u verschillende [indexeringsbeleidsregels](index-policy.md) en [consistentieniveaus](consistency-levels.md) proberen om inzicht te krijgen in hun impact op doorvoer en latentie. U ook de broncode bekijken en vergelijkbare configuraties implementeren als uw eigen testsuites of productietoepassingen.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebben we gekeken hoe u prestaties en schaaltests uitvoeren met Azure Cosmos DB met behulp van een .NET-console-app. Raadpleeg voor meer informatie de volgende artikelen:

* [Voorbeeld van azure Cosmos DB-prestatietests](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Clientconfiguratieopties om de prestaties van Azure Cosmos DB te verbeteren](performance-tips.md)
* [Server-side partitionering in Azure Cosmos DB](partition-data.md)


