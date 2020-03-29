---
title: Azure Cosmos DB-brontokens gebruiken met de Gremlin SDK
description: Meer informatie over het maken van resourcetokens en deze gebruiken om toegang te krijgen tot de Graph-database.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 42f3c7f3351bddab429489dccf28587549d76e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897848"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Azure Cosmos DB-brontokens gebruiken met de Gremlin SDK

In dit artikel wordt uitgelegd hoe [u Azure Cosmos DB-brontokens](secure-access-to-data.md) gebruiken om toegang te krijgen tot de Graph-database via de Gremlin SDK.

## <a name="create-a-resource-token"></a>Een resourcetoken maken

De Apache TinkerPop Gremlin SDK heeft geen API om resourcetokens te maken. De term *resource token* is een Azure Cosmos DB concept. Download de [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)om brontokens te maken. Als uw toepassing brontokens moet maken en deze moet gebruiken om toegang te krijgen tot de Graph-database, zijn twee afzonderlijke SDK's nodig.

De hiërarchie van objectmodellen boven resourcetokens wordt geïllustreerd in het volgende overzicht:

- **Azure Cosmos DB-account** - De entiteit op het hoogste niveau `contoso.gremlin.cosmos.azure.com`met een DNS die eraan is gekoppeld (bijvoorbeeld ).
  - **Azure Cosmos DB-database**
    - **Gebruiker**
      - **Machtiging**
        - **Token** - Een eigenschap van het object Machtiging dat aangeeft welke acties zijn toegestaan of geweigerd.

Een resourcetoken gebruikt de `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`volgende indeling: . Deze tekenreeks is ondoorzichtig voor de clients en moet worden gebruikt zoals het is, zonder wijziging of interpretatie.

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Een resourcetoken gebruiken
U resourcetokens rechtstreeks gebruiken als eigenschap 'wachtwoord' wanneer u de klasse GremlinServer maakt.

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a master key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Dezelfde aanpak werkt in alle TinkerPop Gremlin SDKs.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Limiet

Met één Gremlin-account u een onbeperkt aantal tokens uitgeven. U echter slechts tot 100 tokens tegelijk gebruiken binnen 1 uur. Als een toepassing de tokenlimiet per uur overschrijdt, wordt een verificatieverzoek geweigerd en ontvangt u het volgende foutbericht: 'Overschreden toegestane resourcetokenlimiet van 100 die gelijktijdig kan worden gebruikt.' Het werkt niet om actieve verbindingen te sluiten die specifieke tokens gebruiken om slots vrij te maken voor nieuwe tokens. De Azure Cosmos DB Gremlin-databaseengine houdt unieke tokens bij gedurende het uur voorafgaand aan de verificatieaanvraag.

## <a name="permission"></a>Machtiging

Een veelvoorkomende fout die toepassingen tegenkomen tijdens het gebruik van resourcetokens is: "Onvoldoende machtigingen die zijn opgegeven in de autorisatiekopvoor de bijbehorende aanvraag. Probeer het opnieuw met een andere autorisatiekoptekst." Deze fout wordt geretourneerd wanneer een Gremlin traversal probeert een rand of een hoekpunt te schrijven, maar het resourcetoken geeft alleen *Leesmachtigingen* toe. Controleer uw traversal om te zien of deze een van de volgende stappen bevat: *.addV()*, *.addE()*, *.drop()* of *.property()*.

## <a name="next-steps"></a>Volgende stappen
* [Op rollen gebaseerdtoegangsbeheer](role-based-access-control.md) in Azure Cosmos DB
* [Meer informatie over het beveiligen van toegang tot gegevens](secure-access-to-data.md) in Azure Cosmos DB
