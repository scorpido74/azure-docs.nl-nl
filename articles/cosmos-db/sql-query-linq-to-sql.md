---
title: LINQ to SQL vertaling in Azure Cosmos DB
description: LINQ-query's toewijzen aan Azure Cosmos DB SQL-query's.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: ad6dcf7307955300a781a7a649b6ac76b3c69589
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003558"
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

## <a id="SupportedLinqOperators"></a>Ondersteunde LINQ-Opera tors

De LINQ-provider die is opgenomen in de SQL .NET SDK ondersteunt de volgende Opera tors:

- **Select**: Prognoses worden vertaald naar SQL SELECT, inclusief object constructie.
- **Where**: Filters vertalen naar SQL waarbij vertaling wordt ondersteund tussen `&&`, `||`en `!` naar de SQL-Opera tors
- **SelectMany**: maakt het mogelijk om matrices af te wikkelen naar de SQL-JOIN-component. Gebruiken voor het koppelen of nesten van expressies voor het filteren op matrix elementen.
- **OrderBy** en **OrderByDescending**: Vertalen in Best Ellen met ASC of DESC.
- Operatoren **Count**, **Sum**, **Min**, **Max** en **Average** voor statistische functies en de bijbehorende asynchrone equivalenten **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** en **AverageAsync**.
- **CompareTo**: wordt omgezet naar bereikvergelijkingen. Wordt meestal gebruikt voor teken reeksen, omdat ze niet vergelijkbaar zijn in .NET.
- **Take**: Vertaalt de TOP van SQL voor het beperken van de resultaten van een query.
- **Wiskundige functies**: Ondersteunt de vertaling van `Abs`.net `Acos`, `Asin`, `Atan`, `Ceiling`,, `Cos`, ,`Floor`,,, ,`Pow` `Log10` `Log` `Exp` `Round`, `Sign`, ,,`Sin`en naardeequivalenteingebouwde`Truncate` functies van SQL. `Sqrt` `Tan`
- **Teken reeks functies**: Ondersteunt de vertaling van `Concat`.net `Contains`, `Count`, `EndsWith`,`IndexOf`,, `Replace`, ,`StartsWith`,,, ,`ToUpper` `ToLower` `SubString` `Reverse` `TrimEnd` en`TrimStart` voor de equivalente ingebouwde functies van SQL.
- **Matrix functies**: Ondersteunt de vertaling van `Concat`.net `Contains`, en `Count` naar de equivalente ingebouwde functies van SQL.
- **Georuimtelijke extensie functies**: Ondersteunt de vertaling van stub `Distance` `IsValidDetailed`- `IsValid`methoden,, `Within` en naar de equivalente ingebouwde functies van SQL.
- Door de **gebruiker gedefinieerde functie-extensie functie**: Ondersteunt de vertaling van de stub `UserDefinedFunctionProvider.Invoke` -methode naar de bijbehorende door de gebruiker gedefinieerde functie.
- **Miscellaneous**: Ondersteunt de omzetting `Coalesce` van en voorwaardelijke Opera tors. Kan worden `Contains` omgezet in een teken reeks, ARRAY_CONTAINS of SQL in, afhankelijk van de context.

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

De syntaxis is `input(.|.SelectMany())(.Select()|.Where())*`. Een samengevoegde query kan beginnen met een optionele `SelectMany` query, gevolgd door `Where` meerdere `Select` Opera tors.

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

De syntaxis is `input.SelectMany(x=>x.Q())` `Q` een `Select`, ,`SelectMany` of`Where` -operator.

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
- [Document gegevens model leren](modeling-data.md)
