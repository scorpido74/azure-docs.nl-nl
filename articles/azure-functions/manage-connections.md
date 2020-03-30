---
title: Verbindingen beheren in Azure-functies
description: Meer informatie over het voorkomen van prestatieproblemen in Azure-functies met behulp van statische verbindingsclients.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276450"
---
# <a name="manage-connections-in-azure-functions"></a>Verbindingen beheren in Azure-functies

Functies in een functie-app delen resources. Onder deze gedeelde bronnen zijn verbindingen: HTTP-verbindingen, databaseverbindingen en verbindingen met services zoals Azure Storage. Wanneer veel functies gelijktijdig worden uitgevoerd, is het mogelijk om geen beschikbare verbindingen meer op te lopen. In dit artikel wordt uitgelegd hoe u uw functies coderen om te voorkomen dat u meer verbindingen gebruikt dan ze nodig hebben.

## <a name="connection-limit"></a>Verbindingslimiet

Het aantal beschikbare verbindingen is beperkt, mede omdat een functie-app wordt uitgevoerd in een [sandbox-omgeving.](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) Een van de beperkingen die de sandbox oplegt aan uw code is een limiet voor het aantal uitgaande verbindingen, dat momenteel 600 actieve (1.200 in totaal) verbindingen per exemplaar is. Wanneer u deze limiet bereikt, worden in de functieruntime het volgende bericht naar de logboeken geschreven: `Host thresholds exceeded: Connections`. Zie de [servicelimieten voor functies](functions-scale.md#service-limits)voor meer informatie .

Deze limiet is per exemplaar. Wanneer de [schaalcontroller functie-app-exemplaren toevoegt](functions-scale.md#how-the-consumption-and-premium-plans-work) om meer aanvragen af te handelen, heeft elke instantie een onafhankelijke verbindingslimiet. Dat betekent dat er geen wereldwijde verbindingslimiet is en dat u veel meer dan 600 actieve verbindingen hebben voor alle actieve instanties.

Controleer bij het oplossen van problemen of u Application Insights voor uw functie-app hebt ingeschakeld. Met Application Insights u statistieken weergeven voor uw functie-apps zoals uitvoeringen. Zie [Telemetrie weergeven in Application Insights voor](functions-monitoring.md#view-telemetry-in-application-insights)meer informatie.  

## <a name="static-clients"></a>Statische clients

Om te voorkomen dat er meer verbindingen dan nodig is, hergebruiken clientinstanties in plaats van het maken van nieuwe exemplaren met elke functie aanroep. We raden u aan clientverbindingen opnieuw te gebruiken voor elke taal waarin u uw functie zou kunnen schrijven. .NET-clients zoals de [httpclient-,](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) [documentclient-](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)en Azure Storage-clients kunnen bijvoorbeeld verbindingen beheren als u één statische client gebruikt.

Hier volgen enkele richtlijnen wanneer u een servicespecifieke client gebruikt in een Azure Functions-toepassing:

- *Maak geen* nieuwe client met elke functie aanroep.
- *Maak* wel één statische client die elke functieaanroep kan gebruiken.
- *Overweeg* om één statische client te maken in een gedeelde helperklasse als verschillende functies dezelfde service gebruiken.

## <a name="client-code-examples"></a>Voorbeelden van clientcode

In deze sectie worden aanbevolen procedures voor het maken en gebruiken van clients vanuit uw functiecode.

### <a name="httpclient-example-c"></a>HttpClient-voorbeeld (C#)

Hier is een voorbeeld van C#-functiecode waarmee een statisch [httpclient-exemplaar](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) wordt gemaakt:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Een veelgestelde vraag over [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) in .NET is "Moet ik mijn klant verwijderen?" In het algemeen beschikt u `IDisposable` over objecten die implementeren wanneer u klaar bent met het gebruik ervan. Maar u beschikt niet over een statische client omdat u nog niet klaar bent met het gebruik ervan wanneer de functie eindigt. U wilt dat de statische client leeft voor de duur van uw aanvraag.

### <a name="http-agent-examples-javascript"></a>HTTP-agentvoorbeelden (JavaScript)

Omdat het betere opties voor verbindingsbeheer [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) biedt, moet u de native klasse `node-fetch` gebruiken in plaats van niet-native methoden, zoals de module. Verbindingsparameters worden geconfigureerd via `http.agent` opties in de klasse. Zie [nieuwe agent(opties)\[\]](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)voor gedetailleerde opties die beschikbaar zijn met de HTTP-agent.

De `http.globalAgent` globale klasse `http.request()` die wordt gebruikt door al deze waarden is ingesteld op hun respectievelijke standaardwaarden. De aanbevolen manier om verbindingslimieten in Functies te configureren, is door wereldwijd een maximumaantal in te stellen. In het volgende voorbeeld wordt het maximum aantal sockets voor de functie-app ingesteld:

```js
http.globalAgent.maxSockets = 200;
```

 In het volgende voorbeeld wordt een nieuwe HTTP-aanvraag gemaakt met alleen een aangepaste HTTP-agent voor dat verzoek:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Voorbeeld van documentclientcode (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) maakt verbinding met een Azure Cosmos DB-exemplaar. De Azure Cosmos DB-documentatie raadt u aan [een singleton Azure Cosmos DB-client te gebruiken voor de levensduur van uw toepassing.](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage) In het volgende voorbeeld ziet u één patroon om dat in een functie te doen:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>Voorbeeld van CosmosClient-code (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) maakt verbinding met een Azure Cosmos DB-exemplaar. De Azure Cosmos DB-documentatie raadt u aan [een singleton Azure Cosmos DB-client te gebruiken voor de levensduur van uw toepassing.](../cosmos-db/performance-tips.md#sdk-usage) In het volgende voorbeeld ziet u één patroon om dat in een functie te doen:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient-verbindingen

Uw functiecode kan de .NET Framework Data Provider voor SQL Server[(SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)gebruiken om verbindingen te maken met een SQL relationele database. Dit is ook de onderliggende leverancier voor gegevensframeworks die afhankelijk zijn van ADO.NET, zoals [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). In tegenstelling tot [httpClient-](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) en [DocumentClient-verbindingen](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) implementeert ADO.NET standaard verbindingspooling. Maar omdat je nog steeds geen verbindingen meer hebt, moet je de verbindingen met de database optimaliseren. Zie [SQL Server Connection Pooling (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)voor meer informatie.

> [!TIP]
> Sommige gegevensframeworks, zoals Entity Framework, krijgen doorgaans verbindingstekenreeksen uit de sectie **ConnectionStrings** van een configuratiebestand. In dit geval moet u sql-databaseverbindingstekenreeksen expliciet toevoegen aan de verzameling **Verbindingstekenreeksen** van uw functie-app-instellingen en in het [bestand local.settings.json](functions-run-local.md#local-settings-file) in uw lokale project. Als u een exemplaar van [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) maakt in uw functiecode, moet u de verbindingstekenreekswaarde opslaan in **toepassingsinstellingen** met uw andere verbindingen.

## <a name="next-steps"></a>Volgende stappen

Zie [Onjuiste instantiatieantipatroon](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)voor meer informatie over waarom we statische clients aanbevelen.

Zie De prestaties en [betrouwbaarheid van Azure-functies optimaliseren voor](functions-best-practices.md)meer prestatietips voor Azure-functies.
