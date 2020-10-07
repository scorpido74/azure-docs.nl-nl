---
title: Aan de slag met SQL-query's in Azure Cosmos DB
description: Meer informatie over het gebruik van SQL-query's voor het opvragen van gegevens uit Azure Cosmos DB. U kunt voorbeeld gegevens uploaden naar een container in Azure Cosmos DB en er query's op uitvoeren.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: tisande
ms.openlocfilehash: f4ee0c0af6939e71f696fc900ec2ab1343ca91df
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802511"
---
# <a name="getting-started-with-sql-queries"></a>Aan de slag met SQL-query's

In Azure Cosmos DB SQL-API-accounts zijn er twee manieren om gegevens te lezen:

**Punt Lees bewerkingen** : u kunt een sleutel/waarde zoeken op één *item-id* en partitie sleutel. De combi natie van *item-id* en partitie sleutel is de sleutel en het item zelf is de waarde. Voor een 1 KB-document wordt bij het lezen van punten doorgaans de [aanvraag eenheid](request-units.md) kosten 1 berekend met een latentie van 10 MS. Punt lezen retourneert één item.

**SQL-query's** : u kunt gegevens opvragen door query's te schrijven met behulp van de STRUCTURED query language (SQL) als een JSON-query taal. Query's kosten altijd ten minste 2,3 aanvraag eenheden en, in het algemeen, heeft een hogere en meer variabele latentie dan lees punten. Query's kunnen veel items retour neren.

De meeste Lees-en zware werk belastingen op Azure Cosmos DB gebruiken een combi natie van zowel lees-als SQL-query's. Als u slechts één item hoeft te lezen, zijn punt lezen goed koper en sneller dan query's. Punt Lees bewerkingen hoeven de query-engine niet te gebruiken voor toegang tot gegevens en kunnen de gegevens rechtstreeks lezen. Het is natuurlijk niet mogelijk om alle werk belastingen exclusief gegevens te lezen met behulp van punt Lees bewerkingen, zodat SQL als query taal en [indexering](index-overview.md) van het neutraal een flexibele manier is om toegang te krijgen tot uw gegevens.

Hier volgen enkele voor beelden van hoe u met elke SDK punt Lees bewerkingen kunt uitvoeren:

- [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet&preserve-view=true)
- [Java SDK](https://docs.microsoft.com/java/api/com.azure.cosmos.cosmoscontainer.readitem?view=azure-java-stable&preserve-view=true#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](https://docs.microsoft.com/javascript/api/@azure/cosmos/item?view=azure-node-latest&preserve-view=true#read-requestoptions-)
- [Python SDK](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.containerproxy?view=azure-python&preserve-view=true#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

In de rest van dit document ziet u hoe u SQL-query's in Azure Cosmos DB kunt schrijven. SQL-query's kunnen worden uitgevoerd via de SDK of de Azure Portal.

## <a name="upload-sample-data"></a>Voorbeeld gegevens uploaden

Maak in uw SQL API-Cosmos DB account een container met de naam `Families` . Maak twee eenvoudige JSON-items in de container. U kunt de meeste voorbeeld query's uitvoeren in de Azure Cosmos DB query-documentatie met behulp van deze gegevensset.

### <a name="create-json-items"></a>JSON-items maken

Met de volgende code worden twee eenvoudige JSON-onderdelen gemaakt over families. De eenvoudige JSON-items voor de families Splinter en Wakefield bevatten ouders, kinderen en hun huis dieren, adres en registratie gegevens. Het eerste item heeft teken reeksen, getallen, Booleaanse waarden, matrices en geneste eigenschappen.

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Het tweede item maakt `givenName` Gebruik `familyName` van en in plaats van `firstName` en `lastName` .

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>De JSON-items opvragen

Probeer enkele query's uit op de JSON-gegevens om inzicht te krijgen in de belangrijkste aspecten van de SQL-query taal van Azure Cosmos DB.

De volgende query retourneert de items waarbij het `id` veld overeenkomt `AndersenFamily` . Omdat het een `SELECT *` query is, is de uitvoer van de query het volledige JSON-item. Zie [instructie SELECT](sql-query-select.md)voor meer informatie over de syntaxis SELECT.

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

De query resultaten zijn:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

Met de volgende query wordt de JSON-uitvoer in een andere vorm opgemaakt. De query projecteert een nieuw JSON `Family` -object met twee geselecteerde velden `Name` en `City` , wanneer de adres plaats hetzelfde is als de status. "NY, NY" komt overeen met dit geval.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

De query resultaten zijn:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Met de volgende query worden alle opgegeven namen van onderliggende items geretourneerd in de familie waarvan de `id` treffers `WakefieldFamily` zijn geordend op basis van de plaats.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

U ziet deze uitvoer:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Opmerkingen

In de voor gaande voor beelden worden verschillende aspecten van de Cosmos DB query taal weer gegeven:  

* Aangezien de SQL-API werkt op JSON-waarden, worden er in plaats van rijen en kolommen getreede entiteiten behandeld. U kunt naar de structuur knooppunten op elke wille keurige diepte verwijzen, net als `Node1.Node2.Node3…..Nodem` bij de tweedelige verwijzing van `<table>.<column>` in ANSI SQL.

* Omdat de query taal werkt met schemaloze gegevens, moet het type systeem dynamisch worden gebonden. Een expressie kan verschillende typen voor verschillende elementen opleveren. Het resultaat van een query is een geldige JSON-waarde, maar is niet gegarandeerd een vast schema.  

* Azure Cosmos DB biedt alleen ondersteuning voor JSON-items. Dit betekent dat het typesysteem en expressies alleen geschikt zijn voor JSON-typen. Zie voor meer informatie de [JSON-specificatie](https://www.json.org/).  

* Een Cosmos-container is een schema-gratis verzameling van JSON-items. De relaties binnen en in container items worden impliciet vastgelegd door containment, niet op basis van primaire sleutel en refererende-sleutel relaties. Deze functie is belang rijk voor de objecten die verderop in dit artikel worden besproken.

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT-component](sql-query-select.md)
