---
title: Richtlijnen voor vertraagde aanvragen
description: Meer informatie over het maken van betere query's om te voor komen dat aanvragen voor een Azure-resource grafiek worden beperkt.
ms.date: 10/18/2019
ms.topic: conceptual
ms.openlocfilehash: 651a5daa9e7e19a5dc157ba0cfa17da2c8abe3db
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038332"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Richt lijnen voor vertraagde aanvragen in azure resource Graph

Bij het maken van een programma en veelvuldig gebruik van gegevens van Azure-resource grafiek, moet u rekening houden met de manier waarop beperking invloed heeft op de resultaten van de query's. Het wijzigen van de manier waarop gegevens worden aangevraagd, kan u helpen om te voor komen dat uw organisatie wordt beperkt en de stroom van gegevens van uw Azure-resources behouden.

In dit artikel worden vier gebieden en patronen besproken die betrekking hebben op het maken van query's in azure resource Graph:

- Informatie over beperkings headers
- Query's uitvoeren op batch
- Sprong query's
- De impact van de paginering

## <a name="understand-throttling-headers"></a>Informatie over beperkings headers

In azure resource Graph wordt een quotum nummer toegewezen voor elke gebruiker op basis van een tijd venster. Een gebruiker kan bijvoorbeeld Maxi maal 15 query's verzenden binnen elk 5-seconde venster zonder dat dit wordt beperkt. De quotum waarde wordt bepaald door een groot aantal factoren en kan worden gewijzigd.

In elke query-antwoord voegt Azure-resource grafiek twee beperkings koppen toe:

- `x-ms-user-quota-remaining` (int): het resterende resource quotum voor de gebruiker. Deze waarde wordt toegewezen aan het aantal query's.
- `x-ms-user-quota-resets-after` (UU: mm: SS): de tijds duur tot het quotum verbruik van een gebruiker opnieuw wordt ingesteld.

Voor een demonstratie van de manier waarop de headers werken, bekijken we een antwoord op de query met de koptekst en de waarden van `x-ms-user-quota-remaining: 10` en `x-ms-user-quota-resets-after: 00:00:03`.

- Binnen de komende 3 seconden kunnen Maxi maal 10 query's worden verzonden zonder te zijn beperkt.
- In 3 seconden worden de waarden van `x-ms-user-quota-remaining` en `x-ms-user-quota-resets-after` teruggezet naar respectievelijk `15` en `00:00:05`.

Als u een voor beeld wilt van het gebruik van de kopteksten voor _uitstel_ op query aanvragen, raadpleegt u het voor beeld in [query parallel](#query-in-parallel).

## <a name="batching-queries"></a>Query's uitvoeren op batch

Het batcheren van query's op het abonnement, de resource groep of de afzonderlijke resource is efficiënter dan gelijktijdig query's. De quota kosten van een grotere query zijn vaak lager dan de quota kosten van veel kleine en doel query's. De Batch grootte wordt aanbevolen om minder dan _300_te zijn.

- Voor beeld van een slecht geoptimaliseerde benadering

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Voor beeld #1 van een geoptimaliseerde methode voor batch verwerking

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Voor beeld #2 van een geoptimaliseerde methode voor batch verwerking

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Sprong query's

Vanwege de manier waarop het beperken van beperkingen wordt afgedwongen, wordt aangeraden query's te spreiden. Dat wil zeggen, in plaats van 60 query's tegelijkertijd te verzenden naar vier 5-Second Windows-vensters:

- Niet-gespreide query planning

  | Aantal query's         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Tijds interval (sec.) | 0-5 | 5-10 | 10-15 | 15-20 |

- Verspringend query schema

  | Aantal query's         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Tijds interval (sec.) | 0-5 | 5-10 | 10-15 | 15-20 |

Hieronder ziet u een voor beeld van het respecteren van beperkings koppen bij het uitvoeren van query's in azure resource Graph:

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Parallelle query

Hoewel batch verwerking wordt aangeraden voor parallel Lise ring, zijn er situaties waarin query's niet eenvoudig kunnen worden gebatcheerd. In dergelijke gevallen kunt u een query uitvoeren op Azure resource Graph door meerdere query's op parallelle wijze te verzenden. Hieronder ziet u een voor beeld van hoe u _uitstel_ op basis van het beperken van headers in dergelijke scenario's:

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Paginering

Omdat Azure resource Graph Maxi maal 1000 vermeldingen in één query antwoord retourneert, moet u mogelijk uw query's [pagineren](./work-with-data.md#paging-results) om de volledige gegevensset te verkrijgen die u zoekt. Sommige Azure-resource grafiek-clients verwerken de paginering echter anders dan andere.

- C# SDK

  Wanneer u de ResourceGraph-SDK gebruikt, moet u de paginering afhandelen door het Skip-token door te geven dat wordt geretourneerd door de vorige query respons naar de volgende gepagineerde query. Dit ontwerp houdt in dat u de resultaten van alle gepagineerde aanroepen moet verzamelen en samen aan het eind kunt combi neren. In dit geval neemt elke gepagineerde query die u verzendt één query quotum:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI/Azure PowerShell

  Wanneer u Azure CLI of Azure PowerShell gebruikt, worden query's naar Azure resource Graph automatisch gepagineerd om Maxi maal 5000 vermeldingen op te halen. De query resultaten retour neren een gecombineerde lijst met vermeldingen van alle gepagineerde aanroepen. In dit geval, afhankelijk van het aantal items in het query resultaat, kan één gepagineerde query meer dan één query quotum verbruiken. In het onderstaande voor beeld kan één uitvoering van de query bijvoorbeeld tot vijf query quota verbruiken:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Nog steeds beperkt?

Neem contact op met het team op [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)als u een beperking krijgt na de bovenstaande aanbevelingen.

Geef de volgende gegevens op:

- Uw specifieke gebruiks voorbeeld-en Business-stuur programma moeten een hogere beperkings limiet hebben.
- Hoeveel resources hebt u toegang tot? Hoeveel van de retour neren van één query
- Van welke typen resources bent u geïnteresseerd?
- Wat is uw query patroon? X query's per Y seconden etc.

## <a name="next-steps"></a>Volgende stappen

- Zie de taal die wordt gebruikt in [Start query's](../samples/starter.md).
- Zie Geavanceerd gebruik in [Geavanceerde query's](../samples/advanced.md).
- Meer informatie over hoe u [resources kunt verkennen](explore-resources.md).