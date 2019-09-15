---
title: Uitvoering van SQL-query's in Azure Cosmos DB
description: Meer informatie over het uitvoeren van SQL-query's in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: c42732df1bcfa8649c89899febc364bb1f5f9b5a
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999918"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Uitvoering van SQL-query Azure Cosmos DB

Elke taal waarmee HTTP/HTTPS-aanvragen kunnen worden gemaakt, kan de Cosmos DB REST API aanroepen. Cosmos DB biedt ook programmeer bibliotheken voor .NET-, node. js-, java script-en python-programmeer talen. De REST API en bibliotheken bieden ondersteuning voor het uitvoeren van query's via SQL en de .NET SDK biedt ook ondersteuning voor [LINQ-query's](sql-query-linq-to-sql.md).

In de volgende voor beelden ziet u hoe u een query kunt maken en deze kunt verzenden voor een Cosmos-database account.

## <a id="REST-API"></a>REST API

Cosmos DB biedt een open RESTful-programmeermodel via HTTP. Het resource model bestaat uit een set resources onder een database account, die een Azure-abonnements voorzieningen heeft. Het database account bestaat uit een set *data bases*die elk meerdere *containers*kunnen bevatten, die op zijn beurt *items*, udf's en andere bron typen bevatten. Elke Cosmos DB resource is adresseerbaar met behulp van een logische en stabiele URI. Een set resources wordt een *feed*genoemd. 

Het basis interactie model met deze resources is via de HTTP-woorden `GET`, `PUT`, `POST`en `DELETE`, met de standaard interpretaties. Gebruik `POST` om een nieuwe resource te maken, een opgeslagen procedure uit te voeren of een Cosmos DB query uit te geven. Query's zijn altijd alleen-lezen bewerkingen zonder neveneffecten.

In de volgende voor beelden `POST` ziet u een voor een SQL-API-query op basis van de voorbeeld items. De query heeft een eenvoudig filter voor de JSON `name` -eigenschap. De `x-ms-documentdb-isquery` gegevens en het type inhoud `application/query+json` : headers duiden aan dat de bewerking een query is. Vervang `mysqlapicosmosdb.documents.azure.com:443` door de URI voor uw Cosmos DB-account.

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

De resultaten zijn:

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

De volgende, complexere query retourneert meerdere resultaten van een samen voeging:

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

De resultaten zijn: 

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

Als de resultaten van een query niet op één pagina passen, retourneert de rest API een vervolg token via de `x-ms-continuation-token` reactie header. Clients kunnen resultaten pagineren door de koptekst op te nemen in de volgende resultaten. U kunt ook het aantal resultaten per pagina bepalen door de `x-ms-max-item-count` nummer-header.

Als een query een samenvoegings functie heeft, zoals COUNT, kan de query pagina een gedeeltelijk geaggregeerde waarde op slechts één pagina met resultaten retour neren. Clients moeten een aggregatie op tweede niveau uitvoeren voor deze resultaten om de uiteindelijke resultaten te produceren. Bijvoorbeeld, som over de aantallen die worden geretourneerd op de afzonderlijke pagina's om het totaal aantal te retour neren.

Als u het beleid voor gegevens consistentie voor query's wilt `x-ms-consistency-level` beheren, gebruikt u de header als in alle rest API-aanvragen. Sessie consistentie vereist ook de laatste `x-ms-session-token` cookie-header in de query aanvraag. Het indexeringsbeleid van de container waarop de query wordt uitgevoerd kan ook van invloed zijn op de consistentie van queryresultaten. Met de standaard instellingen voor het indexerings beleid voor containers is de index altijd actueel met de item inhoud en de query resultaten overeenkomen met de gekozen consistentie voor de gegevens. Zie [consistentie niveaus van Azure Cosmos DB] [consistentie niveaus] voor meer informatie.

Als het geconfigureerde indexerings beleid voor de container de opgegeven query niet kan ondersteunen, retourneert de Azure Cosmos DB-Server 400 ' ongeldige aanvraag '. Dit fout bericht wordt geretourneerd voor query's met paden die expliciet zijn uitgesloten van indexeren. U kunt de `x-ms-documentdb-query-enable-scan` header opgeven om de query een scan uit te voeren wanneer een index niet beschikbaar is.

U kunt gedetailleerde gegevens over het uitvoeren van query's verkrijgen door de `x-ms-documentdb-populatequerymetrics` header in `true`te stellen op. Zie [Metrische gegevens van SQL-query's voor Azure Cosmos DB](sql-api-query-metrics.md) voor meer informatie.

## <a name="c-net-sdk"></a>C# (.NET SDK)

De .NET SDK biedt ondersteuning voor het uitvoeren van zowel SQL-query's als LINQ-query's. In het volgende voor beeld ziet u hoe u de voor gaande filter query met .NET uitvoert:

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

In het volgende voor beeld worden twee eigenschappen vergeleken voor gelijkheid binnen elk item en worden anonieme projecties gebruikt.

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

In het volgende voor beeld ziet u samen voegingen `SelectMany`, uitgedrukt in LINQ.

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

De .net-client doorloopt automatisch alle pagina's met query resultaten in de `foreach` blokken, zoals wordt weer gegeven in het voor gaande voor beeld. De query opties die in de [sectie rest API](#REST-API) zijn geïntroduceerd, zijn ook beschikbaar in de .NET SDK `FeedOptions` , `FeedResponse` met behulp `CreateDocumentQuery` van de klassen en in de-methode. U kunt het aantal pagina's bepalen door gebruik te maken `MaxItemCount` van de instelling.

U kunt paginering ook expliciet beheren door het `IDocumentQueryable` `IQueryable` object te maken, en vervolgens de `ResponseContinuationToken` waarden te lezen en ze terug te `RequestContinuationToken` geven `FeedOptions`als in. U kunt instellen `EnableScanInQuery` om scans in te scha kelen wanneer de query niet wordt ondersteund door het geconfigureerde indexerings beleid. Voor gepartitioneerde containers kunt u gebruiken `PartitionKey` om de query uit te voeren op één partitie, hoewel Azure Cosmos DB deze automatisch kan ophalen uit de query tekst. U kunt gebruiken `EnableCrossPartitionQuery` om query's uit te voeren op meerdere partities.

Zie voor meer .NET-voor beelden met query's de [Azure Cosmos db .net](https://github.com/Azure/azure-cosmos-dotnet-v3) -voor beelden in github.

## <a id="JavaScript-server-side-API"></a>JavaScript-API op de server

Azure Cosmos DB biedt een programmeer model voor het rechtstreeks [uitvoeren van Java script-toepassings](stored-procedures-triggers-udfs.md) logica op containers, met behulp van opgeslagen procedures en triggers. De Java script-logica die is geregistreerd op het niveau van de container kan vervolgens database bewerkingen uitvoeren op de items van de opgegeven container, verpakt in ambient trans acties.

In het volgende voor beeld ziet u `queryDocuments` hoe u in de Java script-Server-API kunt gebruiken om query's in opgeslagen procedures en triggers te maken:

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
- [Consistentie niveaus Azure Cosmos DB](consistency-levels.md)
