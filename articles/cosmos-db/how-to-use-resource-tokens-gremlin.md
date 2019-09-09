---
title: Azure Cosmos DB bron tokens met Gremlin
description: Meer informatie over het maken van resource tokens en het gebruik ervan om toegang te krijgen tot Graph data base
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/06/2019
ms.author: olignat
ms.openlocfilehash: fcb18fb14cf787713735da07ca2048d0853fa46c
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806909"
---
# <a name="azure-cosmos-db-resource-tokens-with-gremlin"></a>Azure Cosmos DB bron tokens met Gremlin
In dit artikel wordt uitgelegd hoe u [Cosmos DB-bron tokens](secure-access-to-data.md) gebruikt om toegang te krijgen tot Graph data base via Gremlin SDK.

## <a name="create-a-resource-token"></a>Een bron token maken

TinkerPop Gremlin SDK heeft geen API voor het maken van bron tokens. Het bron token is een Cosmos DB concept. Down load [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)om bron tokens te maken. Als uw toepassing resource tokens moet maken en gebruiken om toegang te krijgen tot Graph data base, heeft deze twee afzonderlijke Sdk's nodig.

Hiërarchie van object model boven bron tokens:
- **Cosmos DB account** : entiteit op het hoogste niveau waaraan DNS is gekoppeld, bijvoorbeeld`contoso.gremlin.cosmos.azure.com`
  - **Cosmos DB-Data Base**
    - **Gebruiker**
      - **Hebt**
        - *Token* -een eigenschap van het **machtigings** object waarmee wordt aangegeven welke acties worden toegestaan of geweigerd.

Het bron token heeft een `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"`indeling. Deze teken reeks is ondoorzichtig voor de clients en moet worden gebruikt als-is zonder aanpassing of interpretatie.

```csharp
// Notice that document client is created against .NET SDK end-point rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // Token will not be returned during ReadPermissionReedAsync() call.
  // This call will succeed only if database id, user id and permission id already exist. 
  // Note that <database id> is not a database name, it is a base64 string that represents database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>Een resource token gebruiken
Bron tokens kunnen rechtstreeks worden gebruikt als de eigenschap ' password ' bij het `GremlinServer` maken van een klasse.

```csharp
// Gremlin application needs to be given a resource token. It can't discover the token on its own.
// Token can be obtained for a given permission using Cosmos DB SDK or passed into the application as command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure gremlin servier to use resource token rather than master key
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

Dezelfde benadering werkt in alle TinkerPop Gremlin Sdk's.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// Format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;"
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>Limiet

Eén Gremlin-account kan een onbeperkt aantal tokens uitgeven, maar Maxi maal **100** -tokens kunnen binnen **één uur**gelijktijdig worden gebruikt. Als de toepassing de token limiet per uur overschrijdt, wordt de verificatie aanvraag geweigerd met `"Exceeded allowed resource token limit of 100 that can be used concurrently"`het fout bericht. Het sluiten van actieve verbindingen met specifieke tokens om sleuven voor nieuwe tokens vrij te maken, worden niet fruitful. Cosmos DB Gremlin data base-engine houdt in het afgelopen uur afzonderlijke tokens bij voordat de verificatie aanvraag wordt gevolgd.

## <a name="permission"></a>Machtiging

Bij het gebruik van resource tokens wordt `"Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header."`gebruikgemaakt van veelvoorkomende fout toepassingen. Deze fout wordt geretourneerd wanneer Gremlin traversal probeert een rand of een hoek punt te schrijven, maar het `Read` bron token alleen machtigingen verleent. Inspecteer uw passage of het een van de volgende stappen bevat: `.addV()`, `.addE()`, `.drop()`, of `.property()`.

## <a name="next-steps"></a>Volgende stappen
* [Toegangs beheer op basis van rollen](role-based-access-control.md) in azure Cosmos db
* [Meer informatie over het beveiligen van toegang tot gegevens](secure-access-to-data.md) in azure Cosmos db
