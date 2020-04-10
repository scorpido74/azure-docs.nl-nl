---
title: Door de gebruiker gedefinieerde functies (UDF's) in Azure Cosmos DB
description: Meer informatie over door de gebruiker gedefinieerde functies in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011120"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Door de gebruiker gedefinieerde functies (UDF's) in Azure Cosmos DB

De SQL API biedt ondersteuning voor door gebruikers gedefinieerde functies (UDF's). Met scalaire UDF's u nul of veel argumenten doorgeven en een enkel argumentresultaat retourneren. De API controleert elk argument op het zijn wettelijke JSON-waarden.  

## <a name="udf-use-cases"></a>UDF-gebruiksaanvragen

De API breidt de SQL-syntaxis uit om aangepaste toepassingslogica te ondersteunen met behulp van UDF's. U UDF's registreren met de SQL-API en ernaar verwijzen in SQL-query's. In tegenstelling tot opgeslagen procedures en triggers zijn UDF's alleen-lezen.

Met UDF's u de querytaal van Azure Cosmos DB uitbreiden. UDF's zijn een geweldige manier om complexe bedrijfslogica uit te drukken in de projectie van een query.

We raden echter aan om UDF's te vermijden wanneer:

- Er bestaat al een gelijkwaardige [systeemfunctie](sql-query-system-functions.md) in Azure Cosmos DB. Systeemfuncties gebruiken altijd minder RU's dan de gelijkwaardige UDF.
- De UDF is het `WHERE` enige filter in de clausule van uw query. UDF's maken geen gebruik van de index, dus de evaluatie van de UDF vereist het laden van documenten. Door extra filterpredicaten te combineren die de index gebruiken, `WHERE` in combinatie met een UDF, in de clausule, wordt het aantal documenten dat door de UDF wordt verwerkt, verminderd.

Als u dezelfde UDF meerdere keren in een query moet gebruiken, moet u in een [subquery](sql-query-subquery.md#evaluate-once-and-reference-many-times)naar de UDF verwijzen, zodat u een JOIN-expressie gebruiken om de UDF één keer te evalueren, maar er vaak naar te verwijzen.

## <a name="examples"></a>Voorbeelden

In het volgende voorbeeld wordt een UDF geregistreerd onder een artikelcontainer in de Cosmos-database. In het voorbeeld wordt een `REGEX_MATCH`UDF met de naam . Het accepteert twee JSON-tekenreekswaarden `input` en `pattern`controleert of het eerste overeenkomt met het `string.match()` patroon dat in de tweede is opgegeven met de functie javascript.

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