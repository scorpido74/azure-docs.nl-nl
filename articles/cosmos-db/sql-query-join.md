---
title: SQL JOIN-query's voor Azure Cosmos DB
description: Meer informatie over het aansluiten van meerdere tabellen in Azure Cosmos DB om de gegevens op te vragen
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871139"
---
# <a name="joins-in-azure-cosmos-db"></a>Sluit zich aan bij Azure Cosmos DB

In een relationele database zijn joins across tables het logische gevolg van het ontwerpen van genormaliseerde schema's. De SQL API gebruikt daarentegen het gedenormaliseerde gegevensmodel van schemavrije items, wat het logische equivalent is van een *selfjoin.*

Inner joins resulteren in een compleet cross product van de sets die deelnemen aan de join. Het resultaat van een N-way join is een set n-element tuples, waarbij elke waarde in de tuple is gekoppeld aan de aliasset die deelneemt aan de join en toegankelijk is door te verwijzen naar die alias in andere clausules.

## <a name="syntax"></a>Syntaxis

De taal ondersteunt `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`de syntaxis . Met deze query wordt een `N` set tuples met waarden geretourneerd. Elke tuple heeft waarden die worden geproduceerd door alle containeraliassen te herhalen voor hun respectieve sets. 

Laten we eens kijken naar de volgende FROM-clausule:`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Laat elke `input_alias1, input_alias2, …, input_aliasN`bron definiëren . Deze VAN-component retourneert een set N-tuples (tuple with N-waarden). Elke tuple heeft waarden die worden geproduceerd door alle containeraliassen te herhalen voor hun respectieve sets.  
  
**Voorbeeld 1** - 2 bronnen  
  
- Laat `<from_source1>` containerscoped zijn en stel {A, B, C} weergeven.  
  
- Laat `<from_source2>` verwijzennaar input_alias1 en vertegenwoordigsets met documentscoped zijn:  
  
    {1, 2} voor`input_alias1 = A,`  
  
    {3}Voor`input_alias1 = B,`  
  
    {4, 5} voor`input_alias1 = C,`  
  
- De FROM-clausule `<from_source1> JOIN <from_source2>` zal resulteren in de volgende tuples:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Voorbeeld 2** - 3 bronnen  
  
- Laat `<from_source1>` containerscoped zijn en stel {A, B, C} weergeven.  
  
- Laat `<from_source2>` verwijzende `input_alias1` en vertegenwoordigensets met een documentworden aangebracht:  
  
    {1, 2} voor`input_alias1 = A,`  
  
    {3}Voor`input_alias1 = B,`  
  
    {4, 5} voor`input_alias1 = C,`  
  
- Laat `<from_source3>` verwijzende `input_alias2` en vertegenwoordigensets met een documentworden aangebracht:  
  
    {100, 200} voor`input_alias2 = 1,`  
  
    {300}Voor`input_alias2 = 3,`  
  
- De FROM-clausule `<from_source1> JOIN <from_source2> JOIN <from_source3>` zal resulteren in de volgende tuples:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Gebrek aan tuples voor `input_alias1` `input_alias2`andere waarden `<from_source3>` van , waarvoor de niet terug geen waarden.  
  
**Voorbeeld 3** - 3 bronnen  
  
- Laat <from_source1> containerscoped zijn en set {A, B, C} vertegenwoordigen.  
  
- Laat `<from_source1>` containerscoped zijn en stel {A, B, C} weergeven.  
  
- Laat <from_source2> input_alias1- en vertegenwoordigsets met documentscoped zijn:  
  
    {1, 2} voor`input_alias1 = A,`  
  
    {3}Voor`input_alias1 = B,`  
  
    {4, 5} voor`input_alias1 = C,`  
  
- Laat `<from_source3>` worden scoped om `input_alias1` en vertegenwoordigen sets:  
  
    {100, 200} voor`input_alias2 = A,`  
  
    {300}Voor`input_alias2 = C,`  
  
- De FROM-clausule `<from_source1> JOIN <from_source2> JOIN <from_source3>` zal resulteren in de volgende tuples:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300) , (C, 5, 300)  
  
  > [!NOTE]
  > Dit resulteerde in `<from_source2>` cross `<from_source3>` product tussen en omdat `<from_source1>`beide zijn scoped aan dezelfde .  Dit resulteerde in 4 (2x2) tuples met waarde A, 0 tuples met waarde B (1x0) en 2 (2x1) tuples met waarde C.  
  
## <a name="examples"></a>Voorbeelden

De volgende voorbeelden laten zien hoe de JOIN-component werkt. Upload de [voorbeeldfamiliegegevens](sql-query-getting-started.md#upload-sample-data)voordat u deze voorbeelden uitvoert. In het volgende voorbeeld is het resultaat leeg, omdat het kruisproduct van elk artikel van bron en een lege set leeg is:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Het resultaat is:

```json
    [{
    }]
```

In het volgende voorbeeld is de join een kruisproduct tussen `id` twee `children` JSON-objecten, de itemroot en de subroot. Het feit `children` dat een array is, is niet effectief in de join, `children` omdat het gaat om een enkele root die de array is. Het resultaat bevat slechts twee resultaten, omdat het kruisproduct van elk item met de array precies één item oplevert.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

U ziet deze uitvoer:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

In het volgende voorbeeld ziet u een conventionelere join:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

U ziet deze uitvoer:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

De FROM bron van de JOIN-clausule is een iterator. De stroom in het voorgaande voorbeeld is dus:  

1. Vouw elk `c` onderliggend element in de array uit.
2. Breng een cross-product met `f` de wortel `c` van het item met elk onderliggend element dat de eerste stap afgevlakt.
3. Projecteer ten `f` `id` slotte alleen de eigenschap rootobject.

Het eerste `AndersenFamily`item bevat `children` slechts één element, dus de resultaatset bevat slechts één object. Het tweede `WakefieldFamily`item, `children`, bevat twee, zodat het kruisproduct `children` produceert twee objecten, een voor elk element. De hoofdvelden in beide items zijn hetzelfde, zoals u zou verwachten bij een vectorproduct.

Het echte nut van de JOIN-clausule is om tuples van het crossproduct te vormen in een vorm die anders moeilijk te projecteren is. Het voorbeeld hieronder filters op de combinatie van een tuple waarmee de gebruiker een voorwaarde die is voldaan door de tuples in het algemeen te kiezen.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

U ziet deze uitvoer:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Met de volgende uitbreiding van het voorgaande voorbeeld wordt een dubbele join uitgevoerd. U het crossproduct als de volgende pseudo-code bekijken:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily`heeft een kind dat een huisdier heeft, dus\*het\*kruisproduct levert een rij (1 11) van deze familie. `WakefieldFamily`heeft twee kinderen, van wie er slechts één huisdieren heeft, maar dat kind heeft twee huisdieren. Het kruisproduct voor deze\*\*familie levert 1 1 2 = 2 rijen op.

In het volgende voorbeeld is er `pet`een extra filter op , die alle `Shadow`tuples waar de naam van het huisdier is niet uitsluit . U tuples bouwen van arrays, filteren op een van de elementen van de tuple en elke combinatie van de elementen projecteren.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

U ziet deze uitvoer:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>Volgende stappen

- [Slag](sql-query-getting-started.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Subquery's](sql-query-subquery.md)