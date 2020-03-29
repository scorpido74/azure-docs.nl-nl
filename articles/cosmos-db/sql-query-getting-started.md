---
title: Aan de slag met SQL-query's in Azure Cosmos DB
description: Meer informatie over het gebruik van SQL-query's om gegevens van Azure Cosmos DB op te vragen. U voorbeeldgegevens uploaden naar een container in Azure Cosmos DB en deze opvragen.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873332"
---
# <a name="getting-started-with-sql-queries"></a>Aan de slag met SQL-query's

Azure Cosmos DB SQL API-accounts ondersteunen het opvragen van items met SQL (Structured Query Language) als JSON-querytaal. De ontwerpdoelen van de Azure Cosmos DB-querytaal zijn:

* Ondersteuning SQL, een van de meest bekende en populaire querytalen, in plaats van het uitvinden van een nieuwe querytaal. SQL biedt een formeel programmeermodel voor uitgebreide query's over JSON-items.  

* Gebruik het programmeermodel van JavaScript als basis voor de querytaal. Het typesysteem, de expressie-evaluatie en de functieaanroep van JavaScript zijn de wortels van de SQL-API. Deze wortels bieden een natuurlijk programmeermodel voor functies zoals relationele projecties, hiërarchische navigatie over JSON-items, zelfjoins, ruimtelijke query's en aanroep van door de gebruiker gedefinieerde functies (UDF's) die volledig in JavaScript zijn geschreven.

## <a name="upload-sample-data"></a>Voorbeeldgegevens uploaden

Maak in uw SQL API Cosmos `Families`DB-account een container met de naam . Maak twee eenvoudige JSON-items in de container. U de meeste voorbeeldquery's uitvoeren in de Azure Cosmos DB-querydocumenten met behulp van deze gegevensset.

### <a name="create-json-items"></a>JSON-items maken

De volgende code maakt twee eenvoudige JSON-items over gezinnen. De eenvoudige JSON-items voor de Families Andersen en Wakefield omvatten ouders, kinderen en hun huisdieren, adres en registratiegegevens. Het eerste item heeft tekenreeksen, getallen, Booleaanse, arrays en geneste eigenschappen.


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

Het tweede `givenName` item `familyName` wordt `firstName` `lastName`gebruikt en in plaats van en .

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

Probeer een paar query's tegen de JSON-gegevens om enkele van de belangrijkste aspecten van de SQL-querytaal van Azure Cosmos DB te begrijpen.

Met de volgende query `id` worden `AndersenFamily`de items geretourneerd waarin het veld overeenkomt . Aangezien het een `SELECT *` query is, is de uitvoer van de query het volledige JSON-item. Zie [SELECT-instructie voor](sql-query-select.md)meer informatie over de syntaxis SELECT . 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

De queryresultaten zijn: 

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

Met de volgende query wordt de JSON-uitvoer opnieuw gesaald in een andere vorm. De query projecteert `Family` een nieuw JSON-object met twee geselecteerde velden `Name` en `City`wanneer de adresstad hetzelfde is als de status. "NY, NY" komt overeen met deze zaak.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

De queryresultaten zijn:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

De volgende query retourneert alle gegeven `id` namen `WakefieldFamily`van kinderen in het gezin waarvan de overeenkomsten , geordend door de stad.

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

De voorgaande voorbeelden tonen verschillende aspecten van de cosmos DB-querytaal:  

* Aangezien SQL API werkt op JSON-waarden, gaat het om structuurvormige entiteiten in plaats van rijen en kolommen. U verwijzen naar de boomknooppunten op `Node1.Node2.Node3…..Nodem`elke willekeurige diepte, zoals `<table>.<column>` , vergelijkbaar met de tweedelige verwijzing naar in ANSI SQL.

* Omdat de querytaal werkt met schemaloze gegevens, moet het typesysteem dynamisch worden gebonden. Een expressie kan verschillende typen voor verschillende elementen opleveren. Het resultaat van een query is een geldige JSON-waarde, maar is niet gegarandeerd van een vast schema.  

* Azure Cosmos DB biedt alleen ondersteuning voor JSON-items. Het typesysteem en de expressies zijn beperkt tot alleen JSON-typen. Zie voor meer informatie de [JSON-specificatie](https://www.json.org/).  

* Een Cosmos container is een schemavrije verzameling JSON-items. De relaties binnen en tussen containeritems worden impliciet vastgelegd door containment, niet door primaire sleutel- en buitenlandse sleutelrelaties. Deze functie is belangrijk voor de intra-item joins besproken later in dit artikel.

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT-component](sql-query-select.md)
