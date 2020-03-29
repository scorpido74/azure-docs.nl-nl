---
title: LINQ naar SQL-vertaling in Azure Cosmos DB
description: Lees de ondersteunde LINQ-operatoren en hoe de LINQ-query's zijn toegewezen aan SQL-query's in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441260"
---
# <a name="linq-to-sql-translation"></a>LINQ to SQL-omzetting

De Azure Cosmos DB-queryprovider voert een best effort mapping uit van een LINQ-query naar een Cosmos DB SQL-query. De volgende beschrijving gaat uit van een fundamentele vertrouwdheid met LINQ.

Het typesysteem van de queryprovider ondersteunt alleen de primitieve JSON-typen: numeriek, Booleaans, tekenreeks en null.

De queryprovider ondersteunt de volgende scalaire expressies:

- Constante waarden, inclusief constante waarden van de primitieve gegevenstypen tijdens de evaluatietijd van query's.
  
- Eigenschap-/arrayindexexpressies die verwijzen naar de eigenschap van een object of een arrayelement. Bijvoorbeeld:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Rekenkundige uitdrukkingen, waaronder algemene rekenkundige uitdrukkingen over numerieke en Booleaanse waarden. Zie de [Azure Cosmos DB SQL-specificatie](https://go.microsoft.com/fwlink/p/?LinkID=510612)voor de volledige lijst.
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Tekenreeksvergelijkingsexpressies, waaronder het vergelijken van een tekenreekswaarde met een constante tekenreekswaarde.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Object/arraycreatieexpressies, die een object van samengestelde waardetype of anoniem type of een array van dergelijke objecten retourneren. U deze waarden nesten.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Ondersteunde LINQ-operators

De LINQ-provider die bij de SQL .NET SDK is opgenomen, ondersteunt de volgende operatoren:

- **Selecteer**: Projecties vertalen naar SQL SELECT, inclusief objectconstructie.
- **Waar**: Filters vertalen naar SQL `&&`WHERE `||`en `!` ondersteunen vertaling tussen , en naar de SQL-operatoren
- **SelectMany**: maakt het mogelijk om matrices af te wikkelen naar de SQL-JOIN-component. Met deze optie u expressies vastketenen of nesten om op matrixelementen te filteren.
- **OrderBy** en **OrderByDescending**: Vertalen naar ORDE BY met ASC of DESC.
- Operatoren **Count**, **Sum**, **Min**, **Max** en **Average** voor statistische functies en de bijbehorende asynchrone equivalenten **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** en **AverageAsync**.
- **CompareTo**: wordt omgezet naar bereikvergelijkingen. Vaak gebruikt voor tekenreeksen, omdat ze niet vergelijkbaar zijn in .NET.
- **Overslaan** en **nemen:** vertaalt naar SQL OFFSET en LIMIT voor het beperken van de resultaten van een query en het uitvoeren van pagination.
- **Wiskundige functies**: Ondersteunt `Abs`vertaling `Acos` `Asin`van `Atan` `Ceiling`.NET , `Log` `Log10`, `Pow` `Round`, `Sign` `Sin`, `Sqrt` `Tan` `Cos` `Exp`, `Floor` `Truncate` , , , , , , , , , en naar de gelijkwaardige SQL-ingebouwde functies.
- **Tekenreeksfuncties**: Ondersteunt vertaling `Concat` `Contains`van `Count` `EndsWith`.NET `Replace` `Reverse`, , , `TrimEnd`, `TrimStart` `IndexOf`, , `StartsWith` `SubString`, , `ToLower`, `ToUpper`, en naar de gelijkwaardige SQL-ingebouwde functies.
- **Arrayfuncties**: ondersteunt vertaling `Concat` `Contains`van `Count` .NET en naar de gelijkwaardige SQL-ingebouwde functies.
- **Geospatial Extension-functies**: Ondersteunt `Distance`vertaling `IsValid` `IsValidDetailed`van `Within` stompmethoden , , en naar de gelijkwaardige SQL-ingebouwde functies.
- **Door de gebruiker gedefinieerde functie-extensiefunctie**: `UserDefinedFunctionProvider.Invoke` ondersteunt vertaling van de stompmethode naar de overeenkomstige door de gebruiker gedefinieerde functie.
- **Diversen**: `Coalesce` Ondersteunt vertaling van en voorwaardelijke operatoren. Kan `Contains` vertalen naar String CONTAINS, ARRAY_CONTAINS of SQL IN, afhankelijk van de context.

## <a name="examples"></a>Voorbeelden

De volgende voorbeelden illustreren hoe sommige van de standaard LINQ-queryoperatoren vertalen naar Cosmos DB-query's.

### <a name="select-operator"></a>Operator selecteren

De syntaxis is `input.Select(x => f(x))`. Hierbij is `f` een scalaire expressie.

**Selecteer operator, voorbeeld 1:**

- **LINQ lambda-expressie**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Selecteer operator, voorbeeld 2:** 

- **LINQ lambda-expressie**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Selecteer operator, voorbeeld 3:**

- **LINQ lambda-expressie**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>Operator SelectMany

De syntaxis is `input.SelectMany(x => f(x))`. Hierbij is `f` een scalaire expressie die een containertype retourneert.

- **LINQ lambda-expressie**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Operator Where

De syntaxis is `input.Where(x => f(x))`. Hierbij is `f` een scalaire expressie die een Booleaanse waarde retourneert.

**Wanneer exploitant, voorbeeld 1:**

- **LINQ lambda-expressie**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Wanneer exploitant, voorbeeld 2:**

- **LINQ lambda-expressie**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>Samengestelde SQL-query's

U de voorgaande operatoren samenstellen om krachtigere query's te vormen. Aangezien Cosmos DB geneste containers ondersteunt, u de samenstelling samenbrengen of nesten.

### <a name="concatenation"></a>Samenvoegen

De syntaxis is `input(.|.SelectMany())(.Select()|.Where())*`. Een gelijktijdige query kan beginnen met `SelectMany` een optionele query, gevolgd door meerdere `Select` of `Where` operatoren.

**Samenvoeging, voorbeeld 1:**

- **LINQ lambda-expressie**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Samenvoeging, voorbeeld 2:**

- **LINQ lambda-expressie**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Samenvoeging, voorbeeld 3:**

- **LINQ lambda-expressie**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Samenvoeging, voorbeeld 4:**

- **LINQ lambda-expressie**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>Nesten

De syntaxis `Q` is `Select` `input.SelectMany(x=>x.Q())` `SelectMany`waar `Where` een , , of operator is.

Een geneste query past de innerlijke query toe op elk element van de buitenste container. Een belangrijk kenmerk is dat de binnenste query kan verwijzen naar de velden van de elementen in de buitenste container, zoals een zelf-join.

**Nesten, voorbeeld 1:**

- **LINQ lambda-expressie**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Nesten, voorbeeld 2:**

- **LINQ lambda-expressie**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Nesten, voorbeeld 3:**

- **LINQ lambda-expressie**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```


## <a name="next-steps"></a>Volgende stappen

- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Documentgegevens modelleren](modeling-data.md)
