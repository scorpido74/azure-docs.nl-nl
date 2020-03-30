---
title: Door de gebruiker gedefinieerde functies (UDF's) in Azure Cosmos DB
description: Meer informatie over door de gebruiker gedefinieerde functies in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614333"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Door de gebruiker gedefinieerde functies (UDF's) in Azure Cosmos DB

De SQL API biedt ondersteuning voor door gebruikers gedefinieerde functies (UDF's). Met scalaire UDF's u nul of veel argumenten doorgeven en een enkel argumentresultaat retourneren. De API controleert elk argument op het zijn wettelijke JSON-waarden.  

De API breidt de SQL-syntaxis uit om aangepaste toepassingslogica te ondersteunen met behulp van UDF's. U UDF's registreren met de SQL-API en ernaar verwijzen in SQL-query's. De UDFs zijn speciaal ontworpen voor aanroepen vanuit query's. Als gevolg hiervan hebben UDF's geen toegang tot het contextobject zoals andere JavaScript-typen, zoals opgeslagen procedures en triggers. Query's zijn alleen-lezen en kunnen worden uitgevoerd op primaire of secundaire replica's. UDF's zijn, in tegenstelling tot andere JavaScript-typen, ontworpen om op secundaire replica's te worden uitgevoerd.

In het volgende voorbeeld wordt een UDF geregistreerd onder een artikelcontainer in de Cosmos-database. In het voorbeeld wordt een `REGEX_MATCH`UDF met de naam . Het accepteert twee JSON-tekenreekswaarden `input` en `pattern`controleert of het eerste overeenkomt met het `string.match()` patroon dat in de tweede is opgegeven met de functie javascript.

## <a name="examples"></a>Voorbeelden

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Gebruik deze UDF in een queryprojectie. U moet UDF's kwalificeren met `udf.` het hoofdlettergevoelige voorvoegsel wanneer u ze vanuit query's aanroept.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

U ziet deze uitvoer:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

U het UDF `udf.` gebruiken dat is gekwalificeerd met het voorvoegsel in een filter, zoals in het volgende voorbeeld:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

U ziet deze uitvoer:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

In wezen zijn UDF's geldige scalaire expressies die u gebruiken in zowel projecties als filters.

Als u de kracht van UDF's wilt uitbreiden, bekijkt u een ander voorbeeld met voorwaardelijke logica:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

In het volgende voorbeeld wordt de UDF uitgevoerd:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

U ziet deze uitvoer:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Als de eigenschappen waarnaar wordt verwezen door de UDF-parameters niet beschikbaar zijn in de JSON-waarde, wordt de parameter als niet gedefinieerd beschouwd en wordt de aanroep van de UDF overgeslagen. Als het resultaat van de UDF niet is gedefinieerd, wordt het niet in het resultaat opgenomen.

Zoals de voorgaande voorbeelden laten zien, integreren UDF's de kracht van JavaScript-taal met de SQL API. UDF's bieden een uitgebreide programmeerbare interface om complexe procedurele, voorwaardelijke logica te doen met behulp van ingebouwde JavaScript-runtime-mogelijkheden. De SQL API biedt de argumenten aan de UDF's voor elk bronitem in de huidige where- of SELECT-bedingfase van verwerking. Het resultaat is naadloos opgenomen in de totale uitvoeringspijplijn. Samengevat zijn UDF's geweldige tools om complexe bedrijfslogica te doen als onderdeel van query's.

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Systeemfuncties](sql-query-system-functions.md)
- [Aggregaties](sql-query-aggregates.md)