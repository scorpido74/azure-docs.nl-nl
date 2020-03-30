---
title: Sql-queryprestaties & uitvoeringsstatistieken ophalen
description: Meer informatie over het ophalen van SQL-queryuitvoeringsstatistieken en profielSQL-queryprestaties van Azure Cosmos DB-aanvragen.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70998371"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Sql-queryuitvoeringsstatistieken ophalen en queryprestaties analyseren met .NET SDK

In dit artikel wordt uitgelegd hoe sql-queryprestaties op Azure Cosmos DB worden geprofileerd. Deze profilering kan worden `QueryMetrics` gedaan met behulp van opgehaald uit de .NET SDK en wordt hier beschreven. [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) is een sterk getypt object met informatie over de uitvoering van de backendquery. Deze statistieken worden gedetailleerder gedocumenteerd in het artikel [Queryprestaties afstemmen.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="set-the-feedoptions-parameter"></a>De parameter FeedOptions instellen

Alle overbelasting voor [DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) neemt een optionele [parameter FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) in. Met deze optie kan queryuitvoering worden afgestemd en geparameteriseerd. 

Als u de uitvoeringsstatistieken van sql-query's wilt verzamelen, `true`moet u de parameter [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) in de [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) instellen op . Het `PopulateQueryMetrics` instellen op true zal `FeedResponse` het zo `QueryMetrics`maken dat de zal de relevante bevatten . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Querystatistieken opvragen met AsDocumentQuery()
In het volgende codevoorbeeld ziet u hoe u metrische gegevens ophalen wanneer u de methode [AsDocumentQuery() gebruikt:](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx)

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Querymetrics aggregeren

In de vorige sectie werd opgemerkt dat er meerdere oproepen naar [de methode ExecutNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) zijn uitgevoerd. Elke aanroep `FeedResponse` heeft een object `QueryMetrics`geretourneerd met een woordenboek van ; een voor elke voortzetting van de query. In het volgende voorbeeld `QueryMetrics` ziet u hoe u deze samenvoegen met LINQ:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Querystatistieken groeperen op partitie-id

U `QueryMetrics` de groep groeperen op de partitie-id. Met groeperen op partitie-id u zien of een specifieke partitie prestatieproblemen veroorzaakt in vergelijking met anderen. In het volgende voorbeeld `QueryMetrics` ziet u hoe u groeperen met LINQ:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ op DocumentQuery

U de `FeedResponse` van een LINQ `AsDocumentQuery()` Query ook krijgen met behulp van de methode:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Dure query's

U de aanvraageenheden vastleggen die door elke query worden verbruikt om dure query's of query's te onderzoeken die een hoge doorvoer verbruiken. U de aanvraagkosten in rekening `FeedResponse`brengen via de eigenschap [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) in. Zie het artikel over [het opladen](find-request-unit-charge.md) van aanvragen voor aanvragen voor meer informatie over het aanvragen van de aanvraagkosten via de Azure-portal en verschillende SDK's.

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>De uitvoeringstijd van query's opvragen

Bij het berekenen van de tijd die nodig is om een client-side query uit te voeren, zorg ervoor dat u alleen de tijd om de `ExecuteNextAsync` methode aan te roepen en niet andere delen van uw code basis. Alleen deze oproepen helpen u bij het berekenen hoe lang de query-uitvoering heeft geduurd, zoals in het volgende voorbeeld wordt weergegeven:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Query's scannen (vaak traag en duur)

Een scanquery verwijst naar een query die niet door de index is weergegeven, waardoor veel documenten worden geladen voordat de resultaatset wordt teruggeplaatst.

Hieronder vindt u een voorbeeld van een scanquery:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Het filter van deze query maakt gebruik van de systeemfunctie UPPER, die niet wordt weergegeven vanuit de index. Het uitvoeren van deze query tegen een grote verzameling leverde de volgende querystatistieken op voor de eerste voortzetting:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Let op de volgende waarden uit de uitvoer van querymetrische gegevens:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

Deze query laadde 60.951 documenten, die in totaal 399.998.938 bytes. Het laden van deze vele bytes resulteert in hoge kosten of aanvraag eenheid kosten. Het duurt ook lang om de query uit te voeren, wat duidelijk is met de totale tijddie is besteed:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Dit betekent dat de query 4,5 seconden duurde om uit te voeren (en dit was slechts één vervolg).

Als u deze voorbeeldquery wilt optimaliseren, vermijdt u het gebruik van BOVEN in het filter. Wanneer documenten worden gemaakt of `c.description` bijgewerkt, moeten de waarden in alle hoofdletters worden ingevoegd. De query wordt dan: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Deze query kan nu worden weergegeven vanuit de index.

Zie het artikel Queryprestaties afstemmen voor meer informatie over het afstemmen [van queryprestaties.](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)

## <a name="references"></a><a id="References"></a>Verwijzingen

- [SQL-specificatie voor Azure Cosmos DB](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [Json](https://json.org/)
- [Linq](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Volgende stappen

- [Queryprestaties afstemmen](sql-api-query-metrics.md)
- [Indexoverzicht](index-overview.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
