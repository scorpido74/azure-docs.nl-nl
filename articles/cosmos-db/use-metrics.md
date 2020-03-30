---
title: Controleren en debuggen met statistieken in Azure Cosmos DB
description: Gebruik metrische gegevens in Azure Cosmos DB om veelvoorkomende problemen te opsporen en de database te controleren.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b65bc6097d4841c79a68d4313ac7a3f89f6d1dbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065938"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Controleren en debuggen met statistieken in Azure Cosmos DB

Azure Cosmos DB biedt metrische gegevens voor doorvoer, opslag, consistentie, beschikbaarheid en latentie. De Azure-portal biedt een geaggregeerde weergave van deze metrische gegevens. U kunt ook metrische gegevens uit Azure Cosmos DB bekijken vanuit de Azure Monitor API. Zie het artikel [Statistieken ophalen uit Azure Monitor](cosmos-db-azure-monitor-metrics.md) voor meer informatie over het weergeven van statistieken uit Azure-monitor. 

In dit artikel worden veelvoorkomende use cases besproken en hoe Azure Cosmos DB-statistieken kunnen worden gebruikt om deze problemen te analyseren en te debuggen. Statistieken worden elke vijf minuten verzameld en worden zeven dagen bewaard.

## <a name="view-metrics-from-azure-portal"></a>Statistieken weergeven vanuit Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/)

1. Open het deelvenster **Statistieken.** Standaard toont het deelvenster Metrische gegevens de statistieken, index, aanvraageenheden statistieken voor alle databases in uw Azure Cosmos-account. U deze statistieken filteren per database, container of een regio. U de statistieken ook filteren op een specifieke tijdgranulariteit. Meer informatie over de doorvoer-, opslag-, beschikbaarheids-, latentie- en consistentiestatistieken worden op afzonderlijke tabbladen weergegeven. 

   ![Prestatiestatistieken van Cosmos DB in Azure-portal](./media/use-metrics/performance-metrics.png)

De volgende statistieken zijn beschikbaar in het deelvenster **Statistieken:** 

* **Doorvoerstatistieken** : deze statistiek geeft het aantal aanvragen weer dat is verbruikt of is mislukt (429-responscode) omdat de doorvoer- of opslagcapaciteit die voor de container is ingericht, is overschreden.

* **Opslagstatistieken** : deze statistiek geeft de grootte van gegevens en indexgebruik weer.

* **Beschikbaarheidsstatistieken** : deze statistiek geeft het percentage geslaagde aanvragen weer over het totale aantal aanvragen per uur. Het slagingspercentage wordt bepaald door de Azure Cosmos DB SLA's.

* **Latentiestatistieken** - Deze statistiek toont de lees- en schrijflatentie die azure cosmos DB heeft waargenomen in de regio waar uw account actief is. U latentie in verschillende regio's visualiseren voor een geo-gerepliceerd account. Deze statistiek vertegenwoordigt niet de end-to-end aanvraaglatentie.

* **Consistentiestatistieken** : deze statistiek geeft aan hoe uiteindelijk de consistentie is voor het consistentiemodel dat u kiest. Voor accounts met meerdere regio's wordt in deze statistiek ook de replicatielatentie weergegeven tussen de regio's die u hebt geselecteerd.

* **Systeemstatistieken** : deze statistiek geeft aan hoeveel metagegevensaanvragen worden weergegeven door de hoofdpartitie. Het helpt ook om de gethrottled verzoeken te identificeren.

In de volgende secties worden veelvoorkomende scenario's uitgelegd waarin u Azure Cosmos DB-statistieken gebruiken. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Begrijpen hoeveel aanvragen worden mislukt of fouten veroorzaken

Ga naar de [Azure-portal](https://portal.azure.com) om aan de slag te gaan en navigeer naar het **blade Metrics.** Zoek in het blad het **aantal aanvragen dat de capaciteit per grafiek van 1 minuut heeft overschreden. Deze grafiek toont een minuut voor minuut totaal aantal aanvragen gesegmenteerd door de statuscode. Zie [HTTP-statuscodes voor Azure Cosmos DB voor](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)meer informatie over HTTP-statuscodes.

De meest voorkomende foutstatuscode is 429 (tariefbeperking/beperking). Deze fout betekent dat aanvragen voor Azure Cosmos DB meer zijn dan de ingerichte doorvoer. De meest voorkomende oplossing voor dit probleem is het [opschalen van de R's](./set-throughput.md) voor de gegeven collectie.

![Aantal aanvragen per minuut](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>De doorvoerverdeling over partities bepalen

Het hebben van een goede kardinaliteit van uw partitiesleutels is essentieel voor elke schaalbare toepassing. Als u de doorvoerverdeling wilt bepalen van een partitiecontainer die is opgesplitst in partities, navigeert u naar het **blade metrics** in de [Azure-portal.](https://portal.azure.com) Op het tabblad **Doorvoer** wordt de opslagverdeling weergegeven in de **maximaal verbruikte RU/seconde door elke fysieke partitiegrafiek.** De volgende afbeelding illustreert een voorbeeld van een slechte verdeling van gegevens, zoals blijkt uit de scheve partitie helemaal links.

![Enkele partitie die zwaar gebruik ziet om 15:05](media/use-metrics/metrics-17.png)

Een ongelijke doorvoerverdeling kan leiden tot *hete* partities, wat kan leiden tot gethrottled aanvragen en mogelijk opnieuw moeten worden verdeeld. Zie Partitie en schaal in Azure [Cosmos DB](./partition-data.md)voor meer informatie over partitionering in Azure Cosmos DB.

## <a name="determine-the-storage-distribution-across-partitions"></a>De opslagdistributie over partities bepalen

Het hebben van een goede kardinaliteit van uw partitie is essentieel voor elke schaalbare toepassing. Als u de opslagdistributie wilt bepalen van een partitiecontainer die is opgesplitst in partities, gaat u naar het blade Metrics in de [Azure-portal.](https://portal.azure.com) Op het tabblad Opslag wordt de opslagverdeling weergegeven in de opslag gegevens + index die wordt verbruikt door het bovenste overzicht van partitiesleutels. De volgende afbeelding illustreert een slechte verdeling van de gegevensopslag, zoals blijkt uit de scheve partitie helemaal links.

![Voorbeeld van slechte gegevensdistributie](media/use-metrics/metrics-07.png)

U de hoofdoorzaak gebruiken welke partitiesleutel de verdeling scheeftrekt door op de partitie in de grafiek te klikken.

![Partitiesleutel trekt de verdeling scheef](media/use-metrics/metrics-05.png)

Nadat u hebt geïdentificeerd welke partitiesleutel de spreiding in de distributie veroorzaakt, moet u uw container mogelijk opnieuw verdelen met een meer gedistribueerde partitiesleutel. Zie Partitie en schaal in Azure [Cosmos DB](./partition-data.md)voor meer informatie over partitionering in Azure Cosmos DB.

## <a name="compare-data-size-against-index-size"></a>Gegevensgrootte vergelijken met de indexgrootte

In Azure Cosmos DB is de totale verbruikte opslag de combinatie van zowel de grootte van de gegevens als de grootte van de index. De indexgrootte is doorgaans een fractie van de gegevensgrootte. In het blade Metrics in de [Azure-portal](https://portal.azure.com)toont het tabblad Opslag de uitsplitsing van het opslagverbruik op basis van gegevens en index.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Als u indexruimte wilt behouden, u het [indexeringsbeleid](index-policy.md)aanpassen.

## <a name="debug-why-queries-are-running-slow"></a>Foutopsporing waarom query's traag worden uitgevoerd

In de SQL API SDKs biedt Azure Cosmos DB queryuitvoeringsstatistieken.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* geeft meer informatie over hoe lang het duurde voordat elke component van de query is uitgevoerd. De meest voorkomende oorzaak voor langlopende query's zijn scans, wat betekent dat de query geen gebruik kan maken van de indexen. Dit probleem kan worden opgelost met een betere filterconditie.

## <a name="next-steps"></a>Volgende stappen

U hebt nu geleerd hoe u problemen controleren en opsporen met behulp van de statistieken in de Azure-portal. U wilt misschien meer weten over het verbeteren van de databaseprestaties door de volgende artikelen te lezen:

* Zie het artikel [Statistieken ophalen uit Azure Monitor](cosmos-db-azure-monitor-metrics.md) voor meer informatie over het weergeven van statistieken uit Azure-monitor. 
* [Prestatie- en schaaltests met Azure Cosmos DB](performance-testing.md)
* [Tips voor betere prestaties van Azure Cosmos DB](performance-tips.md)
