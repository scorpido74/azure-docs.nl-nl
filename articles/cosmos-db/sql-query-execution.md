---
title: SQL-query-uitvoering in Azure Cosmos DB
description: Meer informatie over het maken van een SQL-query en het uitvoeren ervan in Azure Cosmos DB. In dit artikel wordt beschreven hoe u een SQL-query maakt en uitvoert met REST API, .Net SDK, JavaScript SDK en diverse andere SDK's.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871258"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB SQL-queryuitvoering

Elke taal die HTTP/HTTPS-verzoeken kan indienen, kan de Cosmos DB REST API aanroepen. Cosmos DB biedt ook programmeerbibliotheken voor programmeertalen .NET, Node.js, JavaScript en Python. De REST API en bibliotheken ondersteunen allemaal query's via SQL en de .NET SDK ondersteunt ook [LINQ-query's](sql-query-linq-to-sql.md).

In de volgende voorbeelden ziet u hoe u een query maakt en deze indient tegen een Cosmos-databaseaccount.

## <a name="rest-api"></a><a id="REST-API"></a>REST-API

Cosmos DB biedt een open RESTful-programmeermodel via HTTP. Het resourcemodel bestaat uit een set resources onder een databaseaccount, waarin een Azure-abonnementsvoorziening is opgenomen. Het databaseaccount bestaat uit een reeks *databases,* die elk meerdere *containers*kunnen bevatten, die op hun beurt *items,* UDF's en andere resourcetypen bevatten. Elke Cosmos DB-bron is adresseerbaar met behulp van een logische en stabiele URI. Een set resources wordt een *feed*genoemd. 

Het basisinteractiemodel met deze bronnen is `GET` `PUT`via `POST`de `DELETE`HTTP-werkwoorden , , en , met hun standaard interpretaties. Gebruik `POST` om een nieuwe bron te maken, een opgeslagen procedure uit te voeren of een Cosmos DB-query uit te geven. Query's zijn altijd alleen-lezen bewerkingen zonder neveneffecten.

In de volgende `POST` voorbeelden wordt een VOOR SQL API-query weergegeven tegen de voorbeelditems. De query heeft een eenvoudig `name` filter op de eigenschap JSON. De `x-ms-documentdb-isquery` kopteksten `application/query+json` en inhoudstype: geven aan dat de bewerking een query is. Vervang `mysqlapicosmosdb.documents.azure.com:443` door de URI voor je Cosmos DB-account.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

U ziet deze uitvoer:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

De volgende, complexere query retourneert meerdere resultaten van een join:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

U ziet deze uitvoer: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Als de resultaten van een query niet in één pagina passen, retourneert de REST API een vervolgtoken via de `x-ms-continuation-token` antwoordkop. Clients kunnen resultaten bekijken door de koptekst op te nemen in de volgende resultaten. U ook het aantal resultaten `x-ms-max-item-count` per pagina beheren via de nummerkop.

Als een query een aggregatiefunctie heeft zoals COUNT, kan de querypagina een gedeeltelijk geaggregeerde waarde over slechts één pagina met resultaten retourneren. Clients moeten een aggregatie op het tweede niveau uitvoeren over deze resultaten om de uiteindelijke resultaten te produceren. Som bijvoorbeeld over de tellingen die op de afzonderlijke pagina's zijn geretourneerd om het totale aantal te retourneren.

Als u het beleid voor gegevensconsistentie `x-ms-consistency-level` voor query's wilt beheren, gebruikt u de koptekst zoals in alle REST API-aanvragen. Voor sessieconsistentie moet ook `x-ms-session-token` de nieuwste cookiekop in het queryverzoek worden herhaald. Het indexeringsbeleid van de container waarop de query wordt uitgevoerd kan ook van invloed zijn op de consistentie van queryresultaten. Met de standaardindexeringsbeleidsinstellingen voor containers is de index altijd actueel met de iteminhoud en komen queryresultaten overeen met de consistentie die voor gegevens is gekozen. Zie [Consistentieniveaus azure cosmos DB][consistentieniveaus] voor meer informatie.

Als het geconfigureerde indexeringsbeleid voor de container de opgegeven query niet kan ondersteunen, retourneert de Azure Cosmos DB-server 400 'Bad Request'. Dit foutbericht retourneert voor query's waarbij paden expliciet zijn uitgesloten van indexering. U `x-ms-documentdb-query-enable-scan` de koptekst opgeven zodat de query een scan kan uitvoeren wanneer een index niet beschikbaar is.

U gedetailleerde statistieken over query-uitvoering krijgen door de `x-ms-documentdb-populatequerymetrics` koptekst in te stellen op `true`. Zie [Metrische gegevens van SQL-query's voor Azure Cosmos DB](sql-api-query-metrics.md) voor meer informatie.

## <a name="c-net-sdk"></a>C# (.NET SDK)

De .NET SDK biedt ondersteuning voor het uitvoeren van zowel SQL-query's als LINQ-query's. In het volgende voorbeeld ziet u hoe u de voorgaande filterquery uitvoert met .NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

In het volgende voorbeeld worden twee eigenschappen voor gelijkheid binnen elk item vergeleken en wordt gebruik gemaakt van anonieme projecties.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

In het volgende voorbeeld worden joins `SelectMany`weergegeven, uitgedrukt via LINQ.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

De .NET-client doorloopt automatisch alle pagina's `foreach` met queryresultaten in de blokken, zoals in het voorgaande voorbeeld wordt weergegeven. De queryopties die zijn geïntroduceerd in de sectie [REST API](#REST-API) `FeedOptions` zijn `FeedResponse` ook beschikbaar `CreateDocumentQuery` in de .NET SDK, met behulp van de klassen en klassen in de methode. U het aantal pagina's beheren met behulp van de `MaxItemCount` instelling.

U ook expliciet paging `IDocumentQueryable` controleren `IQueryable` door het object `ResponseContinuationToken` te maken met `RequestContinuationToken` `FeedOptions`behulp van het object, vervolgens door de waarden te lezen en ze terug te geven zoals in. U kunt `EnableScanInQuery` scans instellen wanneer de query niet wordt ondersteund door het geconfigureerde indexeringsbeleid. Voor partitiecontainers kunt `PartitionKey` u de query tegen één partitie uitvoeren, hoewel Azure Cosmos DB deze automatisch uit de querytekst kan extraheren. U kunt `EnableCrossPartitionQuery` query's uitvoeren op meerdere partities.

Zie de [azure cosmos DB .NET-voorbeelden](https://github.com/Azure/azure-cosmos-dotnet-v3) in GitHub voor meer .NET-voorbeelden met query's.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>JavaScript-API op de server

Azure Cosmos DB biedt een programmeermodel voor [het rechtstreeks uitvoeren van JavaScript-gebaseerde toepassingslogica](stored-procedures-triggers-udfs.md) op containers, met behulp van opgeslagen procedures en triggers. De JavaScript-logica die op containerniveau is geregistreerd, kan vervolgens databasebewerkingen uitvoeren op de items van de gegeven container, verpakt in ambient ACID-transacties.

In het volgende voorbeeld `queryDocuments` ziet u hoe u in de JavaScript-server-API query's gebruiken vanuit opgeslagen procedures en triggers:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Azure Cosmos DB-consistentieniveaus](consistency-levels.md)
