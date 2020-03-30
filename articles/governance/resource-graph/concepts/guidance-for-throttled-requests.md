---
title: Richtlijnen voor vertraagde aanvragen
description: Leer parallel groeperen, spreiden, pagina's en query's om te voorkomen dat aanvragen worden beperkt door Azure Resource Graph.
ms.date: 12/02/2019
ms.topic: conceptual
ms.openlocfilehash: fbd4bec715b187bcc643fe32b8452b0e062e7713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259849"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Richtlijnen voor geweigerde aanvragen in Azure Resource Graph

Bij het maken van programmatische en veelvuldig gebruik van Azure Resource Graph-gegevens moet rekening worden gehouden met de invloed van beperking op de resultaten van de query's. Als u de manier waarop gegevens wordt opgevraagd, u en uw organisatie voorkomen dat u wordt beperkt en de stroom van tijdige gegevens over uw Azure-bronnen behouden.

In dit artikel worden vier gebieden en patronen bewerkt die betrekking hebben op het maken van query's in Azure Resource Graph:

- Informatie over headers voor bandbreedtebeperking
- Query's groeperen
- Query's spreiden
- De impact van pagination

## <a name="understand-throttling-headers"></a>Informatie over headers voor bandbreedtebeperking

Azure Resource Graph kent quotanummer toe voor elke gebruiker op basis van een tijdvenster. Een gebruiker kan bijvoorbeeld maximaal 15 query's binnen elk venster van 5 seconden verzenden zonder te worden beperkt. De quotumwaarde wordt bepaald door vele factoren en kan worden gewijzigd.

In elk queryantwoord voegt Azure Resource Graph twee beperkingskoppen toe:

- `x-ms-user-quota-remaining`(int): het resterende resourcequotum voor de gebruiker. Deze waarde wordt toegewezen aan het aantal query's.
- `x-ms-user-quota-resets-after`(hh:mm:ss): de tijdsduur totdat het quotumverbruik van een gebruiker wordt gereset.

Als u wilt illustreren hoe de kopteksten werken, bekijken we `x-ms-user-quota-remaining: 10` een `x-ms-user-quota-resets-after: 00:00:03`queryantwoord met de koptekst en waarden van en .

- Binnen de volgende 3 seconden kunnen ten hoogste 10 query's worden ingediend zonder te worden beperkt.
- In 3 seconden, `x-ms-user-quota-remaining` de `x-ms-user-quota-resets-after` waarden van `15` `00:00:05` en zal worden gereset naar en respectievelijk.

Zie het voorbeeld in [Query in Parallel](#query-in-parallel)als u een voorbeeld wilt zien van het gebruik van de kopteksten om queryaanvragen te _back-offen._

## <a name="grouping-queries"></a>Query's groeperen

Het groeperen van query's op de abonnementsgroep, resourcegroep of afzonderlijke resource is efficiënter dan het parallelmetten van query's. De quotakosten van een grotere query zijn vaak lager dan de quotakosten van veel kleine en gerichte query's. De groepsgrootte wordt aanbevolen om minder dan _300_te zijn.

- Voorbeeld van een slecht geoptimaliseerde aanpak

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

- Voorbeeld #1 van een geoptimaliseerde groeperingsaanpak

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Voorbeeld #2 van een geoptimaliseerde groeperingsbenadering voor het verkrijgen van meerdere resources in één query

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Query's spreiden

Vanwege de manier waarop beperking wordt afgedwongen, raden we query's aan om te worden gespreid. Dat wil zeggen, in plaats van het verzenden van 60 query's op hetzelfde moment, wankelen de query's in vier 5-seconden vensters:

- Niet-gespreide queryplanning

  | Aantal query's         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Tijdsinterval (sec) | 0-5 | 5-10 | 10-15 | 15-20 |

- Gespreidqueryschema

  | Aantal query's         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Tijdsinterval (sec) | 0-5 | 5-10 | 10-15 | 15-20 |

Hieronder vindt u een voorbeeld van het respecteren van throttling-headers bij het opvragen van Azure Resource Graph:

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

### <a name="query-in-parallel"></a>Query in Parallel

Hoewel groepering wordt aanbevolen via parallelisatie, zijn er momenten waarop query's niet gemakkelijk kunnen worden gegroepeerd. In deze gevallen u Azure Resource Graph opvragen door meerdere query's op een parallelle manier te verzenden. Hieronder vindt u een voorbeeld van hoe _u back-off_ op basis van throttling headers in dergelijke scenario's:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

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

Aangezien Azure Resource Graph maximaal 1000 vermeldingen retourneert in één queryantwoord, moet u mogelijk uw query's [pagina's](./work-with-data.md#paging-results) geven om de volledige gegevensset te krijgen die u zoekt. Sommige Azure Resource Graph-clients gaan echter anders om met pagina's dan andere.

- C# SDK

  Wanneer u ResourceGraph SDK gebruikt, moet u paginatie afhandelen door het overslaan-token dat wordt geretourneerd van het vorige queryantwoord door te geven aan de volgende pagina-query. Dit ontwerp betekent dat u resultaten van alle gebelde oproepen moet verzamelen en deze aan het einde samen moet combineren. In dit geval heeft elke paginated query die u verzendt één queryquotum nodig:

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

- Azure CLI / Azure PowerShell

  Wanneer u Azure CLI of Azure PowerShell gebruikt, worden query's naar Azure Resource Graph automatisch weergegeven om maximaal 5000 vermeldingen op te halen. De queryresultaten retourneren een gecombineerde lijst met items van alle pagina's. In dit geval kan, afhankelijk van het aantal vermeldingen in het queryresultaat, één query met pagina's meer dan één queryquotum verbruiken. In het onderstaande voorbeeld kan bijvoorbeeld één enkele run van de query maximaal vijf queryquota verbruiken:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Nog steeds gewurgd?

Als je na het uitoefenen van de bovenstaande aanbevelingen [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)wordt gewurgd, neem dan contact op met het team op .

Geef deze gegevens op:

- Uw specifieke use-case en business driver heeft behoefte aan een hogere beperkingslimiet.
- Tot hoeveel bronnen heb je toegang? Hoeveel van de worden geretourneerd van een enkele query?
- In welke soorten bronnen bent u geïnteresseerd?
- Wat is je patroon? X query's per Y seconden etc.

## <a name="next-steps"></a>Volgende stappen

- Zie de taal die wordt gebruikt in [Starter-query's](../samples/starter.md).
- Zie geavanceerde toepassingen in [Geavanceerde query's](../samples/advanced.md).
- Meer informatie over het [verkennen van bronnen.](explore-resources.md)