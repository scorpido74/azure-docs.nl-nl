---
title: Kosten en RU/s optimaliseren om query's uit te voeren in Azure Cosmos DB
description: Meer informatie over het evalueren van kosten voor aanvraageenheden voor een query en het optimaliseren van de query in termen van prestaties en kosten.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: dd75ad4ed1024292868f113e474fe8b8b73679b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445132"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Kosten van query's optimaliseren in Azure Cosmos DB

Azure Cosmos DB biedt een uitgebreide set databasebewerkingen, waaronder relationele en hiërarchische query's die worden uitgevoerd op de items in een container. De kosten die gepaard gaan met elke bewerking hangen af van de CPU, de IO en het geheugen, vereist om de bewerking uit te voeren. In plaats van dat u zich bezighoudt met hardware, kunt u zich een aanvraageenheid (RU) voorstellen als één maat voor de bronnen die vereist zijn om verschillende databasebewerkingen uit te voeren om een aanvraag in te dienen. In dit artikel wordt beschreven hoe u de kosten voor aanvraageenheden voor een query kunt evalueren en de query kunt optimaliseren in termen van prestaties en kosten. 

Query's in Azure Cosmos DB worden meestal als volgt besteld van snelste/meest efficiënte tot tragere/minder efficiënte doorvoer:  

* GET-bewerking op één partitiesleutel en itemsleutel.

* Query met een filterclausule in één partitiesleutel.

* Query zonder een gelijkheid of bereik filter clausule op een eigenschap.

* Query zonder filters.

Query's die gegevens van een of meer partities lezen, hebben een hogere latentie en verbruiken een hoger aantal aanvraageenheden. Aangezien elke partitie automatische indexering heeft voor alle eigenschappen, kan de query efficiënt worden weergegeven vanuit de index. U query's maken die meerdere partities sneller gebruiken met behulp van de parallelleopties. Zie [Partitioneren in Azure Cosmos DB](partitioning-overview.md)voor meer informatie over partitionering en partitiesleutels.

## <a name="evaluate-request-unit-charge-for-a-query"></a>Kosten van aanvraageenheid voor een query evalueren

Zodra u bepaalde gegevens in uw Azure Cosmos-containers hebt opgeslagen, u de Gegevensverkenner in de Azure-portal gebruiken om uw query's te construeren en uit te voeren. U ook de kosten van de query's krijgen met behulp van de gegevensverkenner. Deze methode geeft u een idee van de werkelijke kosten die betrokken zijn bij typische query's en bewerkingen die uw systeem ondersteunt.

U ook de kosten van query's programmatisch krijgen met behulp van de SDKs. Als u de overhead van een bewerking wilt meten, zoals maken, bijwerken of verwijderen, inspecteert u de koptekst wanneer u DE `x-ms-request-charge` API REST gebruikt. Als u de .NET of de `RequestCharge` Java SDK gebruikt, is de eigenschap de gelijkwaardige eigenschap om de aanvraagkosten te ontvangen en is deze eigenschap aanwezig in de ResourceResponse of FeedResponse.

```csharp
// Measure the performance (request units) of writes 
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Factoren die van invloed zijn op de kosten van de aanvraageenheid voor een query

Aanvraageenheden voor query's zijn afhankelijk van een aantal factoren. Bijvoorbeeld het aantal geladen/geretourneerde Azure Cosmos-items, het aantal zoekopdrachten ten opzichte van de index, de querycompilatietijd enz. Azure Cosmos DB garandeert dat dezelfde query wanneer deze op dezelfde gegevens wordt uitgevoerd, altijd hetzelfde aantal aanvraageenheden verbruikt, zelfs bij herhaalde uitvoeringen. Het queryprofiel met behulp van queryuitvoeringsstatistieken geeft u een goed idee van hoe de aanvraageenheden worden besteed.  

In sommige gevallen ziet u een reeks van 200 en 429 antwoorden en variabele aanvraageenheden in een pagina-uitvoering van query's, dat komt omdat query's zo snel mogelijk worden uitgevoerd op basis van de beschikbare INFO. Mogelijk ziet u een query-uitvoering inbreken in meerdere pagina's/retourritten tussen server en client. 10.000 items kunnen bijvoorbeeld als meerdere pagina's worden geretourneerd, elk in rekening gebracht op basis van de berekening die voor die pagina wordt uitgevoerd. Wanneer u deze pagina's opsomt, moet u hetzelfde aantal RU's krijgen als voor de hele query.  

## <a name="metrics-for-troubleshooting"></a>Statistieken voor het oplossen van problemen

De prestaties en doorvoer die worden verbruikt door query's, door de gebruiker gedefinieerde functies (UDF's) zijn meestal afhankelijk van de functiebody. De eenvoudigste manier om erachter te komen hoeveel tijd de queryuitvoering in de UDF wordt besteed en het aantal verbruikte R's, is door querystatistieken in te schakelen. Als u de .NET SDK gebruikt, vindt u hier voorbeeldquerystatistieken die door de SDK zijn geretourneerd:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>Aanbevolen procedures voor het optimaliseren van query's voor kosten 

Houd rekening met de volgende aanbevolen procedures bij het optimaliseren van query's voor kosten:

* **Meerdere entiteitstypen co-lokaliseren**

   Probeer meerdere entiteitstypen binnen één of kleiner aantal containers te colokaliseren. Deze methode levert voordelen op, niet alleen vanuit een prijsperspectief, maar ook voor query-uitvoering en transacties. Query's worden in één container geplaatst. en atoomtransacties over meerdere records via opgeslagen procedures/triggers worden ondergebracht bij een partitiesleutel in één container. Colocating-entiteiten binnen dezelfde container kunnen het aantal netwerkretouren verminderen om relaties tussen records op te lossen. Dus het verhoogt de end-to-end prestaties, maakt atomaire transacties over meerdere records voor een grotere dataset, en als gevolg daarvan verlaagt de kosten. Als het colocating van meerdere entiteitstypen binnen een enkel of kleiner aantal containers moeilijk is voor uw scenario, meestal omdat u een bestaande toepassing migreert en u geen codewijzigingen wilt aanbrengen - moet u overwegen om in te richten doorvoer op databaseniveau.  

* **Meet en stem af voor lager aanvraageenheden/secondegebruik**

   De complexiteit van een query is van invloed op het aantal aanvraageenheden (RU's) dat wordt verbruikt voor een bewerking. Het aantal predicaten, de aard van de predicaten, het aantal UDF's en de grootte van de brongegevensset. Al deze factoren beïnvloeden de kosten van querybewerkingen. 

   De kosten van de aanvraag die in de aanvraagkop worden geretourneerd, geven de kosten van een bepaalde query aan. Als een query bijvoorbeeld 1000 1-KB-items retourneert, zijn de kosten van de bewerking 1000. Als zodanig, binnen een seconde, de server honoreert slechts twee van dergelijke verzoeken voordat de snelheid beperken van latere verzoeken. Zie artikel [aanvraageenheden](request-units.md) en de calculator voor de aanvraageenheid voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

Vervolgens u meer te weten komen over kostenoptimalisatie in Azure Cosmos DB met de volgende artikelen:

* Meer informatie over [hoe de prijzen van Azure Cosmos werken](how-pricing-works.md)
* Meer informatie over [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [het begrijpen van uw Azure Cosmos DB-factuur](understand-your-bill.md)
* Meer informatie over [het optimaliseren van doorvoerkosten](optimize-cost-throughput.md)
* Meer informatie over [het optimaliseren van opslagkosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lezen en schrijven](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van Azure Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)
* Meer informatie over [de gereserveerde capaciteit van Azure Cosmos DB](cosmos-db-reserved-capacity.md)

