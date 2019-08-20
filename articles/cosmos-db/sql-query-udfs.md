---
title: Door de gebruiker gedefinieerde functies (Udf's) in Azure Cosmos DB
description: Meer informatie over door de gebruiker gedefinieerde functies in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614333"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Door de gebruiker gedefinieerde functies (Udf's) in Azure Cosmos DB

De SQL-API biedt ondersteuning voor door de gebruiker gedefinieerde functies (Udf's). Met scalaire Udf's kunt u nul of veel argumenten door geven en een enkel argument resultaat retour neren. Met de API wordt elk argument gecontroleerd op geldige JSON-waarden.  

De API breidt de SQL-syntaxis uit ter ondersteuning van aangepaste toepassings logica met behulp van Udf's. U kunt Udf's met de SQL-API registreren en ernaar verwijzen in SQL-query's. De UDFs zijn speciaal ontworpen voor aanroepen vanuit query's. As-corolling heeft Udf's geen toegang tot het context object, zoals andere Java script-typen, zoals opgeslagen procedures en triggers. Query's zijn alleen-lezen en kunnen worden uitgevoerd op de primaire of secundaire replica's. Udf's, in tegens telling tot andere Java script-typen, zijn ontworpen om te worden uitgevoerd op secundaire replica's.

In het volgende voor beeld wordt een UDF onder een item container in de Cosmos-data base geregistreerd. In het voor beeld wordt een UDF gemaakt `REGEX_MATCH`waarvan de naam is. Er worden twee JSON- `input` teken reeks waarden geaccepteerd en `pattern`gecontroleerd of de eerste overeenkomt met het patroon dat is opgegeven in de `string.match()` tweede met behulp van Java script-functie.

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

Gebruik deze UDF nu in een query-projectie. U moet udf's kwalificeren met het hoofdletter gevoelige voor voegsel `udf.` wanneer u ze aanroept vanuit query's.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

De resultaten zijn:

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

U kunt de UDF gekwalificeerd met het `udf.` voor voegsel binnen een filter gebruiken, zoals in het volgende voor beeld:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

De resultaten zijn:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

In essentie zijn Udf's geldige scalaire expressies die u in zowel projecties als filters kunt gebruiken.

Als u de kracht van Udf's wilt uitbreiden, bekijkt u een ander voor beeld met voorwaardelijke logica:

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

In het volgende voor beeld wordt de UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

De resultaten zijn:

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

Als de eigenschappen waarnaar wordt verwezen door de UDF-para meters niet beschikbaar zijn in de JSON-waarde, wordt de para meter beschouwd als niet-gedefinieerd en wordt de UDF-aanroep overgeslagen. Als het resultaat van de UDF niet is gedefinieerd, wordt het niet opgenomen in het resultaat.

Zoals in de voor gaande voor beelden wordt weer gegeven, integreert UDFs de kracht van Java script-taal met de SQL-API. Udf's bieden een uitgebreide programmeer bare interface voor het uitvoeren van complexe procedures, voorwaardelijke logica, met behulp van ingebouwde Java Script runtime-functies. De SQL-API biedt de argumenten voor de Udf's voor elk bron item bij de huidige WHERE-of SELECT-component fase van de verwerking. Het resultaat is naadloos opgenomen in de algehele uitvoerings pijplijn. In samen vatting zijn Udf's fantastische hulp middelen voor het uitvoeren van complexe bedrijfs logica als onderdeel van query's.

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Systeem functies](sql-query-system-functions.md)
- [Aggregates](sql-query-aggregates.md)