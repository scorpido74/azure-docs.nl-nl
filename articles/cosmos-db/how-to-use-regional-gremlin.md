---
title: Regionale eindpunten voor Azure Cosmos DB Graph-database
description: Meer informatie over hoe u verbinding maken met het dichtstbijzijnde graph-databaseeindpunt voor uw toepassing
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 7aa1e0aa6bbbee9d40eb0d48318a8e2908a75f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897867"
---
# <a name="regional-endpoints-for-azure-cosmos-db-graph-account"></a>Regionale eindpunten voor Azure Cosmos DB Graph-account
Azure Cosmos DB Graph-database is [wereldwijd gedistribueerd,](distribute-data-globally.md) zodat toepassingen meerdere leeseindpunten kunnen gebruiken. Toepassingen die schrijftoegang op meerdere locaties nodig hebben, moeten [multi-mastermogelijkheden](how-to-multi-master.md) inschakelen.

Redenen om voor meer dan één regio te kiezen:
1. **Horizontale leesschaalbaarheid** - naarmate de belasting van toepassingen toeneemt, kan het verstandig zijn om leesverkeer naar verschillende Azure-regio's te routeren.
2. **Lagere latentie** - u de overhead van de netwerklatentie van elke traversal verminderen door lees- en schrijfverkeer naar de dichtstbijzijnde Azure-regio te routeren.

**Vereisten voor gegevensresidentie** wordt bereikt door azure resource manager-beleid in te stellen op het Cosmos DB-account. Klant kan regio's beperken waarin Cosmos DB gegevens repliceert.

## <a name="traffic-routing"></a>Verkeersroutering

Cosmos DB Graph database engine draait in meerdere regio's, die elk meerdere clusters bevatten. Elk cluster heeft honderden machines. Cosmos DB Graph-account DNS CNAME *accountname.gremlin.cosmos.azure.com* wordt opgelost in DNS Een record van een cluster. Eén IP-adres van een load-balancer verbergt interne clustertopologie.

Er wordt een regionale DNS CNAME-record gemaakt voor elke regio van het Cosmos DB Graph-account. De indeling van het regionale eindpunt is *accountname-region.gremlin.cosmos.azure.com*. Regiosegment van regionaal eindpunt wordt verkregen door alle spaties uit de naam van [azure-regio's te](https://azure.microsoft.com/global-infrastructure/regions) verwijderen. Regio `"East US 2"` voor `"contoso"` een globaal databaseaccount heeft bijvoorbeeld een DNS CNAME-contoso-eastus2.gremlin.cosmos.azure.com *contoso-eastus2.gremlin.cosmos.azure.com*

TinkerPop Gremlin client is ontworpen om te werken met een enkele server. Toepassing kan gebruik maken van global beschrijfbare DNS CNAME voor lees- en schrijfverkeer. Regiobewuste toepassingen moeten regionaal eindpunt gebruiken voor leesverkeer. Gebruik regionaal eindpunt alleen voor schrijfverkeer als een specifiek gebied is geconfigureerd om schrijfbewerkingen te accepteren. 

> [!NOTE]
> Cosmos DB Graph-engine kan schrijfbewerking in leesgebied accepteren door verkeer te proxy's om regio te schrijven. Het wordt afgeraden om schrijfbewerkingen te verzenden naar alleen-lezen regio als het verhoogt traversal latentie en is onderworpen aan beperkingen in de toekomst.

Global database account CNAME verwijst altijd naar een geldig schrijfgebied. Tijdens server-side failover van het schrijfgebied werkt Cosmos DB het wereldwijde databaseaccount CNAME bij om naar een nieuwe regio te wijzen. Als de toepassing de omleiding van het verkeer na een failover niet kan verwerken, moet deze gebruikmaken van dns-cname van het wereldwijde databaseaccount.

> [!NOTE]
> Cosmos DB leidt geen verkeer op basis van geografische nabijheid van de beller. Het is aan elke toepassing om de juiste regio te selecteren op basis van unieke toepassingsbehoeften.

## <a name="portal-endpoint-discovery"></a>Portal-eindpuntdetectie

De eenvoudigste manier om de lijst met regio's voor Azure Cosmos DB Graph-account te krijgen, is het overzichtsblad in Azure-portal. Het werkt voor toepassingen die niet vaak van regio veranderen of een manier hebben om de lijst bij te werken via toepassingsconfiguratie.

![Regio's van Cosmos DB Graph-account ophalen van de portal](./media/how-to-use-regional-gremlin/get-end-point-portal.png )

Onderstaand voorbeeld toont algemene beginselen voor de toegang tot het regionale Gremlin-eindpunt. Toepassing moet rekening houden met het aantal regio's om het verkeer te sturen naar en het aantal overeenkomstige Gremlin clients te instantiate.

```csharp
// Example value: Central US, West US and UK West. This can be found in the overview blade of you Azure Cosmos DB Gremlin Account. 
// Look for Write Locations in the overview blade. You can click to copy and paste.
string[] gremlinAccountRegions = new string[] {"Central US", "West US" ,"UK West"};
string gremlinAccountName = "PUT-COSMOSDB-ACCOUNT-NAME-HERE";
string gremlinAccountKey = "PUT-ACCOUNT-KEY-HERE";
string databaseName = "PUT-DATABASE-NAME-HERE";
string graphName = "PUT-GRAPH-NAME-HERE";

foreach (string gremlinAccountRegion in gremlinAccountRegions)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = $"{gremlinAccountName}-{gremlinAccountRegion.ToLowerInvariant().Replace(" ", string.Empty)}.gremlin.cosmos.azure.com";

  GremlinServer regionalGremlinServer = new GremlinServer(
    hostname: regionalGremlinEndPoint, 
    port: 443,
    enableSsl: true,
    username: "/dbs/" + databaseName + "/colls/" + graphName,
    password: gremlinAccountKey);

  GremlinClient regionalGremlinClient = new GremlinClient(
    gremlinServer: regionalGremlinServer,
    graphSONReader: new GraphSON2Reader(),
    graphSONWriter: new GraphSON2Writer(),
    mimeType: GremlinClient.GraphSON2MimeType);
}
```

## <a name="sdk-endpoint-discovery"></a>SDK-eindpuntdetectie

Toepassing kan [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) gebruiken om lees- en schrijflocaties voor Graph-account te ontdekken. Deze locaties kunnen op elk gewenst moment veranderen door handmatige herconfiguratie aan de serverzijde of automatische failover.

TinkerPop Gremlin SDK heeft geen API om Cosmos DB Graph database account regio's te ontdekken. Toepassingen die runtime-eindpuntdetectie nodig hebben, moeten twee afzonderlijke SDK's in de procesruimte hosten.

```csharp
// Depending on the version and the language of the SDK (.NET vs Java vs Python)
// the API to get readLocations and writeLocations may vary.
IDocumentClient documentClient = new DocumentClient(
    new Uri(cosmosUrl),
    cosmosPrimaryKey,
    connectionPolicy,
    consistencyLevel);

DatabaseAccount databaseAccount = await cosmosClient.GetDatabaseAccountAsync();

IEnumerable<DatabaseAccountLocation> writeLocations = databaseAccount.WritableLocations;
IEnumerable<DatabaseAccountLocation> readLocations = databaseAccount.ReadableLocations;

// Pick write or read locations to construct regional endpoints for.
foreach (string location in readLocations)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = location
    .Replace("http:\/\/", string.Empty)
    .Replace("documents.azure.com:443/", "gremlin.cosmos.azure.com");
  
  // Use code from the previous sample to instantiate Gremlin client.
}
```

## <a name="next-steps"></a>Volgende stappen
* [Databaseaccountsbeheren](how-to-manage-database-account.md) in Azure Cosmos DB
* [Hoge beschikbaarheid](high-availability.md) in Azure Cosmos DB
* [Wereldwijde distributie met Azure Cosmos DB - onder de motorkap](global-dist-under-the-hood.md)
* [Azure CLI-voorbeelden](cli-samples.md) voor Azure Cosmos DB
