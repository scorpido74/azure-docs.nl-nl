---
title: Aan de slag met SQL-query's in Azure Cosmos DB
description: Meer informatie over het gebruik van SQL-query's voor het opvragen van gegevens uit Azure Cosmos DB. U kunt voorbeeld gegevens uploaden naar een container in Azure Cosmos DB en er query's op uitvoeren.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873332"
---
# <a name="getting-started-with-sql-queries"></a>Aan de slag met SQL-query's

Azure Cosmos DB SQL-API-accounts ondersteunen het opvragen van items met behulp van Structured Query Language (SQL) als een JSON-query taal. De ontwerp doelen van de Azure Cosmos DB query taal zijn:

* Ondersteuning voor SQL, een van de meest bekende en populaire query talen, in plaats van een nieuwe query taal. SQL biedt een formeel programmeer model voor uitgebreide query's over JSON-items.  

* Gebruik het programmeer model java script als basis voor de query taal. Java script-type systeem, expressie-evaluatie en functie aanroep zijn de hoofd mappen van de SQL-API. Deze roots bieden een natuurlijk programmeer model voor functies zoals relationele projecties, hiërarchische navigatie in JSON-items, Self-join's, ruimtelijke query's en het aanroepen van door de gebruiker gedefinieerde functies (Udf's), die volledig zijn geschreven in Java script.

## <a name="upload-sample-data"></a>Voorbeeld gegevens uploaden

Maak in uw SQL API-Cosmos DB account een container met `Families`de naam. Maak twee eenvoudige JSON-items in de container. U kunt de meeste voorbeeld query's uitvoeren in de Azure Cosmos DB query-documenten met behulp van deze gegevensset.

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

Het tweede item maakt `givenName` gebruik `familyName` van en `firstName` in `lastName`plaats van en.

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

De volgende query retourneert de items waarbij het `id` veld overeenkomt `AndersenFamily`. Omdat het een `SELECT *` query is, is de uitvoer van de query het volledige JSON-item. Zie [instructie SELECT](sql-query-select.md)voor meer informatie over de syntaxis SELECT. 

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

Met de volgende query wordt de JSON-uitvoer in een andere vorm opgemaakt. De query projecteert een nieuw `Family` JSON- `Name` object met twee geselecteerde velden `City`en, wanneer de adres plaats hetzelfde is als de status. "NY, NY" komt overeen met dit geval.

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

Met de volgende query worden alle opgegeven namen van onderliggende items geretourneerd in de `id` familie `WakefieldFamily`waarvan de treffers zijn geordend op basis van de plaats.

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

* Aangezien de SQL-API werkt op JSON-waarden, worden er in plaats van rijen en kolommen getreede entiteiten behandeld. U kunt naar de structuur knooppunten op elke wille keurige diepte `Node1.Node2.Node3…..Nodem`verwijzen, net als bij de tweedelige `<table>.<column>` verwijzing van in ANSI SQL.

* Omdat de query taal werkt met schemaloze gegevens, moet het type systeem dynamisch worden gebonden. Een expressie kan verschillende typen voor verschillende elementen opleveren. Het resultaat van een query is een geldige JSON-waarde, maar is niet gegarandeerd een vast schema.  

* Azure Cosmos DB biedt alleen ondersteuning voor JSON-items. Het type systeem en expressies zijn beperkt tot het verwerken van JSON-typen. Zie voor meer informatie de [JSON-specificatie](https://www.json.org/).  

* Een Cosmos-container is een schema-gratis verzameling van JSON-items. De relaties binnen en in container items worden impliciet vastgelegd door containment, niet op basis van primaire sleutel en refererende-sleutel relaties. Deze functie is belang rijk voor de objecten die verderop in dit artikel worden besproken.

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT-component](sql-query-select.md)
