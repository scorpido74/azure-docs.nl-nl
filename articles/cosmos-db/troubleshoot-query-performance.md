---
title: Problemen met query's oplossen bij het gebruik van Azure Cosmos DB
description: Meer informatie over het identificeren, vaststellen en oplossen van problemen met Azure Cosmos DB SQL-query's.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 1859fa8f71b5c4c44d6e5da1b6a36ca9d9399516
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444727"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Problemen met de query prestaties voor Azure Cosmos DB oplossen
In dit artikel wordt beschreven hoe u Azure Cosmos DB SQL-query problemen kunt identificeren, vaststellen en oplossen. Volg de onderstaande stappen voor probleem oplossing om de prestaties van Azure Cosmos DB query's optimaal te benutten. 

## <a name="collocate-clients-in-same-azure-region"></a>Termijnen-clients in dezelfde Azure-regio 
De laagst mogelijke latentie wordt bereikt door ervoor te zorgen dat de aanroepende toepassing zich in dezelfde Azure-regio bevindt als het ingerichte Azure Cosmos DB-eind punt. Zie het artikel [Azure Regions](https://azure.microsoft.com/global-infrastructure/regions/#services) voor een lijst met beschik bare regio's.

## <a name="check-consistency-level"></a>Consistentie niveau controleren
Het [consistentie niveau](consistency-levels.md) kan invloed hebben op de prestaties en kosten. Zorg ervoor dat het consistentie niveau geschikt is voor het gegeven scenario. Zie [consistentie niveau kiezen](consistency-levels-choosing.md)voor meer informatie.

## <a name="log-the-executed-sql-query"></a>De uitgevoerde SQL-query registreren 

U kunt de uitgevoerde SQL-query in een opslag account of in de diagnostische logboek tabel registreren. [Met de SQL-query logboeken via Diagnostische logboeken](cosmosdb-monitor-resource-logs.md) kunt u de verborgen query in een opslag account van uw keuze registreren. Zo kunt u de logboeken bekijken en de query zoeken die gebruikmaakt van hoger RUs. Later kunt u de activiteits-ID gebruiken om te voldoen aan de werkelijke query in de QueryRuntimeStatistics. De query is verborgen voor het beveiligings doel en de namen van de query parameters, en de waarden in de WHERE-componenten verschillen van werkelijke namen en waarden. U kunt logboek registratie gebruiken om het account te bewaren voor de lange termijn retentie van de uitgevoerde query's.  

## <a name="log-query-metrics"></a>Metrische gegevens van logboek query

Gebruik `QueryMetrics` om trage of dure query's op te lossen. 

  * Stel `FeedOptions.PopulateQueryMetrics = true` in om `QueryMetrics` in het antwoord te hebben.
  * `QueryMetrics` klasse heeft een overbelaste `.ToString()` functie die kan worden aangeroepen om de teken reeks representatie van `QueryMetrics`te verkrijgen. 
  * De metrische gegevens kunnen worden gebruikt voor het afleiden van de volgende inzichten, onder andere: 
  
      * Of een specifiek onderdeel van de query pijplijn abnormaal lang duurde om te volt ooien (in volg orde van honderden milliseconden of meer). 

          * Kijk naar `TotalExecutionTime`.
          * Als de `TotalExecutionTime` van de query kleiner is dan het einde van de uitvoerings tijd, wordt de tijd aan de client zijde of het netwerk uitgegeven. Controleer of de client en de Azure-regio co zijn.
      
      * Hiermee wordt aangegeven of er sprake is van foutieve positieven in de documenten die worden geanalyseerd (als het aantal uitvoer documenten veel minder is dan het opgehaalde document aantal).  

          * Kijk naar `Index Utilization`.
          * `Index Utilization` = (aantal geretourneerde documenten/aantal geladen documenten)
          * Als het aantal geretourneerde documenten veel minder is dan het aantal dat is geladen, worden fout-positieven geanalyseerd.
          * Beperk het aantal documenten dat wordt opgehaald met beperkende filters.  

      * Hoe afzonderlijke retouren fared (Zie de `Partition Execution Timeline` van de teken reeks representatie van `QueryMetrics`). 
      * Hiermee wordt aangegeven of de query hoge kosten voor aanvragen heeft verbruikt. 

Zie [het artikel metrische gegevens over SQL-query's uitvoeren](profile-sql-api-query.md) voor meer informatie.
      
## <a name="tune-query-feed-options-parameters"></a>Parameters voor feed-opties van query's afstemmen 
De prestaties van query's kunnen worden afgestemd via de parameters voor de [feed-opties](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) van de aanvraag. Stel de onderstaande opties in:

  * Stel `MaxDegreeOfParallelism` eerst in op-1 en vergelijkt de prestaties over verschillende waarden. 
  * Stel `MaxBufferedItemCount` eerst in op-1 en vergelijkt de prestaties over verschillende waarden. 
  * Stel `MaxItemCount` in op-1.

Bij het vergelijken van de prestaties voor verschillende waarden, kunt u waarden proberen als 2, 4, 8, 16 en andere.
 
## <a name="read-all-results-from-continuations"></a>Alle resultaten van vervolgen lezen
Als u denkt dat u niet alle resultaten krijgt, dient u het vervolg volledig leeg te halen. Met andere woorden: blijf de resultaten aflezen terwijl het vervolgtoken meer documenten blijft opleveren.

U kunt het vervolg volledig leeghalen met een van de twee volgende patronen:

  * Door gaan met het verwerken van resultaten terwijl voortzetting niet leeg is.
  * Ga verder met de verwerking terwijl de query meer resultaten heeft. 

    ```csharp
    // using AsDocumentQuery you get access to whether or not the query HasMoreResults
    // If it does, just call ExecuteNextAsync until there are no more results
    // No need to supply a continuation token here as the server keeps track of progress
    var query = client.CreateDocumentQuery<Family>(collectionLink, options).AsDocumentQuery();
    while (query.HasMoreResults)
    {
        foreach (Family family in await query.ExecuteNextAsync())
        {
            families.Add(family);
        }
    }
    ```

## <a name="choose-system-functions-that-utilize-index"></a>Systeemfuncties kiezen die gebruikmaken van de index
Alleen als de expressie kan worden vertaald in een bereik met tekenreekswaarden, kan de expressie van de index gebruikmaken. 

Hier volgt een lijst met tekenreeksfuncties die van de index gebruik kunnen maken: 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0 
    
    Hier volgen enkele voor beelden van query's: 
    
    ```sql

    -- If there is a range index on r.name, STARTSWITH will utilize the index while ENDSWITH won't 
    SELECT * 
    FROM c 
    WHERE STARTSWITH(c.name, 'J') AND ENDSWITH(c.name, 'n')

    ```
    
    ```sql

    -- LEFT will utilize the index while RIGHT won't 
    SELECT * 
    FROM c 
    WHERE LEFT(c.name, 2) = 'Jo' AND RIGHT(c.name, 2) = 'hn'

    ```

  * Vermijd systeem functies in het filter (of de component WHERE) die niet worden geleverd door de index. Enkele voor beelden van dergelijke systeem functies zijn onder andere contains, Upper, Lower.
  * Neem in query's indien mogelijk een filter op de partitiesleutel op.
  * Om query's uit te voeren, kunt u het gebruik van een bovenste/onderste in het filter vermijden. Maak in plaats daarvan hoofdletter gebruik bij het invoegen. Voor elk van de waarden voert u de waarde in met de gewenste behuizing of voegt u de oorspronkelijke waarde en de waarde in met de gewenste behuizing. 

    Bijvoorbeeld:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    In dit geval moet u ' JOE ' in hoofd letters opslaan of zowel ' Joe ' opslaan als de oorspronkelijke waarde en ' JOE '. 
    
    Als de JSON-gegevens behuizing wordt genormaliseerd, wordt de query:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    De tweede query is meer presteert omdat hiervoor geen trans formaties voor elk van de waarden hoeft te worden uitgevoerd om de waarden te vergelijken met ' JOE '.

Zie het artikel over [systeem functies](sql-query-system-functions.md) voor meer informatie over de systeem functie.

## <a name="check-indexing-policy"></a>Indexerings beleid controleren
U kunt als volgt verifiëren of het huidige [Indexeringsbeleid](index-policy.md) optimaal is:

  * Zorg ervoor dat alle JSON-paden die in query's worden gebruikt, zijn opgenomen in het indexerings beleid voor snellere lees bewerkingen.
  * Paden uitsluiten die niet worden gebruikt in query's voor meer uitvoeringen van uitvoerende hand.

Zie [het artikel Indexing-beleid beheren](how-to-manage-indexing-policy.md) voor meer informatie.

## <a name="spatial-data-check-ordering-of-points"></a>Ruimtelijke gegevens: volg orde van punten controleren
Punten in een Polygoon moeten worden opgegeven in volgorde van linksom draaien. Een veelhoek in rechtsom volgorde opgegeven vertegenwoordigt de omgekeerde waarde van de regio binnen het.

## <a name="optimize-join-expressions"></a>KOPPELINGs expressies optimaliseren
`JOIN` expressies kunnen worden uitgebreid naar grote kruis producten. Als dat mogelijk is, kunt u query's uitvoeren op een kleinere Zoek ruimte met een meer beperkt filter.

Subquery's voor meerdere waarden kunnen `JOIN`-expressies optimaliseren door predikaten te pushen na elke Select-many-expressie in plaats van na alle kruis koppelingen in de `WHERE`-component. Zie het artikel over het [optimaliseren van joinexpressie](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) voor een gedetailleerd voor beeld.

## <a name="optimize-order-by-expressions"></a>Volg orde van expressies optimaliseren 
`ORDER BY` de prestaties van de query worden mogelijk vertraagd als de velden verspreid zijn of niet zijn opgenomen in het index beleid.

  * Voor velden met een sparse veld, zoals tijd, verlaagt u de zoek ruimte zoveel mogelijk met filters. 
  * Neem voor de `ORDER BY`van één eigenschap de eigenschap op in het index beleid. 
  * Voor meerdere eigenschappen `ORDER BY` expressies definieert u een [samengestelde index](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) voor velden die worden gesorteerd.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Veel grote documenten die worden geladen en verwerkt
De tijd en RUs die door een query zijn vereist, zijn niet alleen afhankelijk van de grootte van het antwoord, ze zijn ook afhankelijk van het werk dat door de query processing-pijp lijn is uitgevoerd. Tijd en RUs worden proportioneel verhoogd met de hoeveelheid werk die door de hele query verwerking pijp lijn is uitgevoerd. Meer werk wordt uitgevoerd voor grote documenten, wat tijd en RUs vereist om grote documenten te laden en te verwerken.

## <a name="low-provisioned-throughput"></a>Lage ingerichte door Voer
Zorg ervoor dat de ingerichte door Voer werk belasting kan verwerken. Verhoog het RU-budget voor verzamelingen met impact.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Voer een upgrade uit naar de nieuwste SDK-versie
Raadpleeg het artikel [SDK downloaden en release opmerkingen](sql-api-sdk-dotnet.md) om de nieuwste SDK te bepalen.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de documenten hieronder over hoe u RUs per query kunt meten, uitvoerings statistieken kunt ophalen om uw query's af te stemmen en meer:

* [Metrische gegevens voor uitvoering van SQL-query's ophalen met .NET SDK](profile-sql-api-query.md)
* [Queryprestaties afstemmen met Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Tips voor betere prestaties voor de .NET-SDK](performance-tips.md)
