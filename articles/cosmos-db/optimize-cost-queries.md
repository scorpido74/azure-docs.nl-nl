---
title: Kosten en RU/s optimaliseren voor het uitvoeren van query's in Azure Cosmos DB
description: Meer informatie over het evalueren van de kosten voor aanvraag eenheden voor een query en het optimaliseren van de query in termen van prestaties en kosten.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: dd75ad4ed1024292868f113e474fe8b8b73679b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75445132"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Kosten van query's optimaliseren in Azure Cosmos DB

Azure Cosmos DB biedt een uitgebreide set databasebewerkingen, waaronder relationele en hiërarchische query's die worden uitgevoerd op de items in een container. De kosten die gepaard gaan met elke bewerking hangen af van de CPU, de IO en het geheugen, vereist om de bewerking uit te voeren. In plaats van dat u zich bezighoudt met hardware, kunt u zich een aanvraageenheid (RU) voorstellen als één maat voor de bronnen die vereist zijn om verschillende databasebewerkingen uit te voeren om een aanvraag in te dienen. In dit artikel wordt beschreven hoe u de kosten voor aanvraageenheden voor een query kunt evalueren en de query kunt optimaliseren in termen van prestaties en kosten. 

Query's in Azure Cosmos DB meestal worden besteld van snelst/meest efficiënte naar trage/minder efficiënte manier van door Voer als volgt:  

* GET-bewerking op één partitie sleutel en item sleutel.

* Query met een filter component binnen één partitie sleutel.

* Query zonder een gelijkheids-of bereik filter component voor een eigenschap.

* Query zonder filters.

Query's waarmee gegevens van een of meer partities worden gelezen, hebben een hogere latentie en nemen een hoger aantal aanvraag eenheden in beslag. Omdat elke partitie automatische indexering voor alle eigenschappen heeft, kan de query efficiënt vanuit de index worden bediend. U kunt query's die gebruikmaken van meerdere partities sneller maken met behulp van de parallelle opties. Zie [partitioneren in azure Cosmos DB](partitioning-overview.md)voor meer informatie over partitioneren en partitie sleutels.

## <a name="evaluate-request-unit-charge-for-a-query"></a>De kosten voor aanvraag eenheden voor een query evalueren

Zodra u enkele gegevens in uw Azure Cosmos-containers hebt opgeslagen, kunt u de Data Explorer in de Azure Portal gebruiken om uw query's te maken en uit te voeren. U kunt de kosten van de query's ook ophalen met behulp van Data Explorer. Deze methode geeft u een idee van de werkelijke kosten die worden betrokken bij typische query's en bewerkingen die uw systeem ondersteunt.

U kunt de kosten van query's ook programmatisch ophalen met behulp van de Sdk's. Als u de overhead van elke bewerking wilt meten, zoals maken, bijwerken of verwijderen, `x-ms-request-charge` moet u de koptekst controleren bij gebruik van rest API. Als u .NET of de Java SDK gebruikt, is de `RequestCharge` eigenschap de equivalente eigenschap om de aanvraag kosten op te halen en deze eigenschap is aanwezig in de ResourceResponse of FeedResponse.

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

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Factoren die van invloed zijn op de kosten voor aanvraag eenheden voor een query

Aanvraag eenheden voor query's zijn afhankelijk van een aantal factoren. Bijvoorbeeld het aantal Azure Cosmos-items dat is geladen/geretourneerd, het aantal lookups op basis van de index, de compilatie tijd van de query etc. Details. Azure Cosmos DB garandeert dat bij het uitvoeren van dezelfde query altijd hetzelfde aantal aanvraag eenheden wordt gebruikt, zelfs bij herhalingen. Het query profiel met metrische gegevens voor query uitvoering geeft u een goed beeld van de manier waarop de aanvraag eenheden worden uitgegeven.  

In sommige gevallen ziet u mogelijk een reeks van 200-en 429-reacties en variabele-aanvraag eenheden in een wissel bare uitvoering van query's, dat wil zeggen dat query's zo snel mogelijk worden uitgevoerd op basis van het beschik bare RUs. Mogelijk wordt het uitvoeren van een query met meerdere pagina's of afrondingen tussen de server en de client weer geven. 10.000-items kunnen bijvoorbeeld worden geretourneerd als meerdere pagina's, waarbij elke kosten worden berekend op basis van de berekening die voor die pagina is uitgevoerd. Wanneer u de som van deze pagina's opneemt, moet u hetzelfde aantal RUs ophalen als voor de hele query.  

## <a name="metrics-for-troubleshooting"></a>Metrische gegevens voor het oplossen van problemen

De prestaties en de door Voer die worden gebruikt door query's, door de gebruiker gedefinieerde functies (Udf's), zijn grotendeels afhankelijk van de functie hoofd tekst. De eenvoudigste manier om erachter te komen hoe lang de uitvoering van de query wordt uitgevoerd in de UDF en hoeveel RUs wordt verbruikt, is door de metrische gegevens van de query in te scha kelen. Als u de .NET SDK gebruikt, ziet u hier de metrische gegevens van de voorbeeld query die door de SDK worden geretourneerd:

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

## <a name="best-practices-to-cost-optimize-queries"></a>Aanbevolen procedures voor het optimaliseren van query's 

Houd rekening met de volgende aanbevolen procedures bij het optimaliseren van query's voor kosten:

* **Meerdere entiteits typen selecteren**

   Probeer meerdere entiteits typen te verplaatsen binnen één of een kleiner aantal containers. Deze methode levert niet alleen voor delen op uit een prijs perspectief, maar ook voor het uitvoeren van query's en trans acties. Query's zijn binnen het bereik van één container; en atomische trans acties ten opzichte van meerdere records via opgeslagen procedures/triggers bevinden zich binnen één container op een partitie sleutel. Het verplaatsen van entiteiten binnen dezelfde container kan het aantal netwerk round trips voor het oplossen van relaties tussen records verminderen. Hierdoor worden de end-to-end-prestaties verhoogd, kunnen atomische trans acties worden uitgevoerd via meerdere records voor een grotere gegevensset, en als gevolg hiervan worden de kosten verlaagd. Als het verplaatsen van meerdere entiteits typen binnen één of een kleiner aantal containers moeilijk is voor uw scenario, meestal omdat u een bestaande toepassing migreert en u geen code wijzigingen wilt aanbrengen, moet u de door Voer inrichten op database niveau.  

* **Meten en afstemmen voor lagere aanvraag eenheden/tweede gebruik**

   De complexiteit van een query is van invloed op het aantal aanvraag eenheden (RUs) dat voor een bewerking wordt gebruikt. Het aantal predikaten, de aard van de predikaten, het aantal Udf's en de grootte van de bron gegevensverzameling. Al deze factoren beïnvloeden de kosten van de query bewerkingen. 

   In de aanvraag header worden de kosten van een bepaalde query geretourneerd. Als een query bijvoorbeeld 1000 1 KB-items retourneert, zijn de kosten van de bewerking 1000. Binnen één seconde voldoet de server slechts twee aanvragen voordat de volgende aanvragen worden beperkt. Zie het artikel [aanvraag eenheden](request-units.md) en de Calculator van de aanvraag eenheid voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

Daarna kunt u meer te weten komen over cost Optimization in Azure Cosmos DB met de volgende artikelen:

* Meer informatie over [hoe prijzen voor Azure Cosmos werken](how-pricing-works.md)
* Meer informatie over het [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [uw Azure Cosmos DB factuur](understand-your-bill.md)
* Meer informatie over het [optimaliseren van doorvoer kosten](optimize-cost-throughput.md)
* Meer informatie over het [optimaliseren van opslag kosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lees-en schrijf bewerkingen](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten voor Azure Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)
* Meer informatie over [Azure Cosmos DB gereserveerde capaciteit](cosmos-db-reserved-capacity.md)

