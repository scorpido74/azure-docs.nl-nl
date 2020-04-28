---
title: LINQ to SQL vertaling in Azure Cosmos DB
description: Meer informatie over de LINQ-Opera tors die worden ondersteund en hoe de LINQ-query's worden toegewezen aan SQL-query's in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75441260"
---
# <a name="linq-to-sql-translation"></a>LINQ to SQL-omzetting

De Azure Cosmos DB-query provider voert een beste toewijzing van een LINQ-query in een Cosmos DB SQL-query uit. In de volgende beschrijving wordt uitgegaan van een basis kennis van LINQ.

Het query provider type systeem ondersteunt alleen de JSON-primitieve typen: numeric, Boolean, String en null.

De query provider ondersteunt de volgende scalaire expressies:

- Constante waarden, met inbegrip van constante waarden van de primitieve gegevens typen bij de evaluatie tijd van de query.
  
- Eigenschap/matrix index expressies die verwijzen naar de eigenschap van een object of een matrix element. Bijvoorbeeld:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Reken kundige expressies, waaronder veelvoorkomende reken kundige expressies voor numerieke en Booleaanse waarden. Zie de [Azure Cosmos DB SQL-specificatie](https://go.microsoft.com/fwlink/p/?LinkID=510612)voor de volledige lijst.
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Teken reeks vergelijkings expressies, die een teken reeks waarde vergelijken met een constante teken reeks waarde.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Expressies voor object/matrix maken, die een object van het type samengestelde waarde of anoniem type of een matrix van dergelijke objecten retour neren. U kunt deze waarden nesten.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Ondersteunde LINQ-Opera tors

De LINQ-provider die is opgenomen in de SQL .NET SDK ondersteunt de volgende Opera tors:

- **Select**: projecties vertalen naar SQL SELECT, inclusief object constructie.
- **Waar**: filters vertalen naar SQL waar en ondersteunen de vertaling tussen `&&`, `||`en `!` naar de SQL-Opera tors
- **SelectMany**: maakt het mogelijk om matrices af te wikkelen naar de SQL-JOIN-component. Gebruiken voor het koppelen of nesten van expressies voor het filteren op matrix elementen.
- **OrderBy** en **OrderByDescending**: vertalen naar order by met ASC of DESC.
- Operatoren **Count**, **Sum**, **Min**, **Max** en **Average** voor statistische functies en de bijbehorende asynchrone equivalenten **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** en **AverageAsync**.
- **CompareTo**: wordt omgezet naar bereikvergelijkingen. Wordt meestal gebruikt voor teken reeksen, omdat ze niet vergelijkbaar zijn in .NET.
- **Overs Laan** en **uitvoeren**: VERtaalt de SQL-offset en-limiet voor het beperken van de resultaten van een query en het uitvoeren van paginering.
- **Wiskundige functies**: ondersteunt de vertaling van `Abs`.net `Acos`, `Asin`, `Atan` `Ceiling`,, `Cos`, `Exp`, `Floor` `Log` `Log10` `Pow` `Round` `Sign` `Sin` `Sqrt`,,,,,,,, en `Truncate` naar de equivalente ingebouwde functies van `Tan`SQL.
- **Teken reeks functies**: ondersteunt de omzetting `Concat`van `Contains`.net `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower` `ToUpper` `TrimEnd`,,, en `TrimStart` naar de equivalente ingebouwde functies van SQL.
- **Array functies**: ondersteunt de vertaling van `Concat`.net `Contains`, en `Count` naar de equivalente ingebouwde functies van SQL.
- **Georuimtelijke extensie functies**: ondersteunt de vertaling van `Distance`stub `IsValid`- `IsValidDetailed`methoden, `Within` , en naar de equivalente ingebouwde functies van SQL.
- Door de **gebruiker gedefinieerde functie-extensie functie**: ondersteunt de vertaling van `UserDefinedFunctionProvider.Invoke` de stub-methode naar de bijbehorende door de gebruiker gedefinieerde functie.
- **Diversen**: ondersteunt de omzetting `Coalesce` van en voorwaardelijke Opera tors. Kan vertalen `Contains` naar teken reeks, ARRAY_CONTAINS of SQL in, afhankelijk van de context.

## <a name="examples"></a>Voorbeelden

In de volgende voor beelden ziet u hoe sommige van de standaard LINQ-query operators worden vertaald naar Cosmos DB query's.

### <a name="select-operator"></a>Operator selecteren

De syntaxis is `input.Select(x => f(x))`. Hierbij is `f` een scalaire expressie.

**Operator selecteren, voor beeld 1:**

- **LINQ lambda-expressie**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Operator selecteren, voor beeld 2:** 

- **LINQ lambda-expressie**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Operator selecteren, voor beeld 3:**

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

**De operator where, voor beeld 1:**

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
  
**De operator where, voor beeld 2:**

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

U kunt de voor gaande Opera tors samen stellen om krachtiger query's te maken. Omdat Cosmos DB geneste containers ondersteunt, kunt u de samen stelling samen voegen of nesten.

### <a name="concatenation"></a>Samenvoegen

De syntaxis is `input(.|.SelectMany())(.Select()|.Where())*`. Een samengevoegde query kan beginnen met een optionele `SelectMany` query, gevolgd door meerdere `Select` `Where` Opera tors.

**Samen voegen, voor beeld 1:**

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

**Samen voegen, voor beeld 2:**

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

**Samen voegen, voor beeld 3:**

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

**Samen voegen, voor beeld 4:**

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

De syntaxis is `input.SelectMany(x=>x.Q())` `Q` een `Select`, `SelectMany`, of `Where` -operator.

Een geneste query past de interne query toe op elk element van de buitenste container. Een belang rijke functie is dat de binnenste query kan verwijzen naar de velden van de elementen in de buitenste container, zoals een self-join.

**Nesten, voor beeld 1:**

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

**Nesten, voor beeld 2:**

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

**Nesten, voor beeld 3:**

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
