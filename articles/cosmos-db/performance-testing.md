---
title: Prestaties en schaal testen met Azure Cosmos DB
description: Informatie over schalen en prestaties testen met Azure Cosmos DB. U kunt vervolgens de functionaliteit van Azure Cosmos DB voor scenario's met hoogwaardige toepassingen evalueren.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: fb510c5628913fb3fa37b572c4409aee5d1028ab
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76313742"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Prestaties en schaal testen met Azure Cosmos DB

Prestaties en schaal testen is een belangrijke stap in de ontwikkeling van toepassingen. Voor veel toepassingen heeft de databaselaag een aanzienlijke invloed op de algehele prestaties en schaalbaarheid. Daarom is het een essentieel onderdeel van de Prestatietesten. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) is gebouwd om flexibele schaalmogelijkheden en voorspelbare prestaties. Deze mogelijkheden kunt uitstek geschikt voor toepassingen waarvoor een krachtige database-laag. 

In dit artikel is een verwijzing voor ontwikkelaars prestaties testsuites voor hun Azure Cosmos DB-workloads implementeren. Deze kan ook worden gebruikt om te evalueren van Azure Cosmos DB voor scenario's met hoogwaardige toepassingen. Richt zich voornamelijk op prestatietests geïsoleerd van de database, maar bevat ook aanbevolen procedures voor productie-Apps.

Na het lezen van dit artikel, zal het mogelijk om de volgende vragen te beantwoorden: 

* Waar vind ik een voorbeeld van .NET-clienttoepassing voor het Prestatietesten van de van Azure Cosmos DB? 
* Hoe kan ik een hoge doorvoerniveaus met Azure Cosmos DB van mijn clienttoepassing?

Als u wilt aan de slag met code, downloadt u het project uit [Azure Cosmos DB-voorbeeld voor Prestatietesten](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Het doel van deze toepassing is om te laten zien hoe u de beste prestaties opgehaald uit Azure Cosmos DB met een klein aantal clientcomputers. Het doel van het voorbeeld is niet aan het bereiken van de hoogste doorvoercapaciteit van Azure Cosmos DB (die kunnen worden geschaald zonder eventuele beperkingen).
> 
> 

Als u naar client-side-configuratie-opties om Azure Cosmos DB-prestaties te verbeteren zoekt, Zie [tips voor betere prestaties van Azure Cosmos DB](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>De toepassing van de prestatietests uitvoeren
De snelste manier om te beginnen is compileren en uitvoeren van de .NET-voorbeeld, zoals beschreven in de volgende stappen uit. U kunt ook de broncode analyseren en vergelijkbare configuraties implementeren op uw eigen clienttoepassingen.

**Stap 1:** downloaden van het project uit [Azure Cosmos DB-voorbeeld voor Prestatietesten](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark), of de GitHub-opslagplaats splitsen.

**Stap 2:** de instellingen wijzigen voor EndpointUrl, AuthorizationKey, CollectionThroughput en DocumentTemplate (optioneel) in App.config.

> [!NOTE]
> Voordat u verzamelingen met hoge doorvoer inricht, verwijzen naar de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) om in te schatten van de kosten per verzameling. Azure Cosmos DB kosten opslag en doorvoer onafhankelijk van elkaar op uurbasis in rekening gebracht. U kunt kosten besparen door de door Voer van uw Azure Cosmos-containers na het testen te verwijderen of te verlagen.
> 
> 

**Stap 3:** compileren en uitvoeren van de console-app vanaf de opdrachtregel. Hier ziet u de volgende uitvoer:

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


**Stap 4 (indien nodig):** de gerapporteerde doorvoer (RU/s) van het hulpprogramma moet gelijk zijn of hoger is dan de ingerichte doorvoer van de verzameling of een set van verzamelingen. Als dat niet het geval is, de DegreeOfParallelism in kleine stapjes verhogen u kan helpen bij de limiet is bereikt. Als de doorvoer van uw client-app plateaus, start u meerdere exemplaren van de app op aanvullende virtuele machines. Als u hulp nodig hebt bij dit stap bestand, moet u een ondersteunings ticket van de [Azure Portal](https://portal.azure.com).

Nadat u de app die wordt uitgevoerd hebt, kunt u proberen verschillende [indexeringsbeleid](index-policy.md) en [consistentieniveaus](consistency-levels.md) om te begrijpen van de invloed ervan op de doorvoer en latentie. U kunt ook de broncode analyseren en vergelijkbare configuraties die u kunt uw eigen testsuites of productie-Apps implementeren.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebben bekeken hoe u de prestaties en schaal testen met Azure Cosmos DB met behulp van een .NET-console-app kunt uitvoeren. Raadpleeg voor meer informatie de volgende artikelen:

* [Azure Cosmos DB-prestaties testen voorbeeld](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Client-configuratie-opties om Azure Cosmos DB-prestaties te verbeteren](performance-tips.md)
* [Serverzijde partitionering in Azure Cosmos DB](partition-data.md)


