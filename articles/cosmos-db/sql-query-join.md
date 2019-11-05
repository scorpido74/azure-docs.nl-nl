---
title: SQL-SAMENVOEG query's voor Azure Cosmos DB
description: Meer informatie over deelname aan SQL-syntaxis voor Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: d78904fde53da0e800a69d2148a9c4e3acf57307
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494410"
---
# <a name="joins-in-azure-cosmos-db"></a>Samen voegen in Azure Cosmos DB

In een relationele data base is het samen voegen van meerdere tabellen de logische corolling voor het ontwerpen van genormaliseerde schema's. De SQL-API maakt daarentegen gebruik van het Gedenormaliseerde gegevens model van items die zonder schema worden gebruikt. Dit is het logische equivalent van een *Self-deelname*.

Inner joins resulteren in een volledig ander product van de sets die deel nemen aan de join. Het resultaat van een N-way-koppeling is een set N-element-Tuples, waarbij elke waarde in de tuple is gekoppeld aan de aliasset die deelneemt aan de samen voeging en toegankelijk is door naar die alias in andere componenten te verwijzen.

## <a name="syntax"></a>Syntaxis

De taal ondersteunt de syntaxis `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Deze query retourneert een set Tuples met `N` waarden. Elke tuple heeft waarden die worden geproduceerd door alle containeraliassen te herhalen voor hun respectieve sets. 

Laten we eens kijken naar de volgende FROM-component: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Laat elke bron `input_alias1, input_alias2, …, input_aliasN`definiëren. Deze component FROM retourneert een set N-Tuples (tuple met N waarden). Elke tuple heeft waarden die worden geproduceerd door alle containeraliassen te herhalen voor hun respectieve sets.  
  
**Voor beeld 1** -2 bronnen  
  
- Laat `<from_source1>` een container bereik maken en de set {A, B, C} vertegenwoordigen.  
  
- Stel in dat `<from_source2>` input_alias1 zijn die verwijzen naar de scope en die sets vertegenwoordigen:  
  
    {1, 2} voor `input_alias1 = A,`  
  
    {3} voor `input_alias1 = B,`  
  
    {4, 5} voor `input_alias1 = C,`  
  
- De component FROM `<from_source1> JOIN <from_source2>` levert de volgende Tuples op:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Voor beeld 2** -3 bronnen  
  
- Laat `<from_source1>` een container bereik maken en de set {A, B, C} vertegenwoordigen.  
  
- Laat `<from_source2>` verwijzen naar `input_alias1` en vertegenwoordigen sets:  
  
    {1, 2} voor `input_alias1 = A,`  
  
    {3} voor `input_alias1 = B,`  
  
    {4, 5} voor `input_alias1 = C,`  
  
- Laat `<from_source3>` verwijzen naar `input_alias2` en vertegenwoordigen sets:  
  
    {100, 200} voor `input_alias2 = 1,`  
  
    {300} voor `input_alias2 = 3,`  
  
- De component FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` levert de volgende Tuples op:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Geen Tuples voor andere waarden van `input_alias1`, `input_alias2`, waarvoor de `<from_source3>` geen waarden heeft geretourneerd.  
  
**Voor beeld 3** -3 bronnen  
  
- Laat < from_source1 > een container bereik zijn en stel {A, B, C} in.  
  
- Laat `<from_source1>` een container bereik maken en de set {A, B, C} vertegenwoordigen.  
  
- Laat < from_source2 > verwijzen naar input_alias1 en vertegenwoordigen sets:  
  
    {1, 2} voor `input_alias1 = A,`  
  
    {3} voor `input_alias1 = B,`  
  
    {4, 5} voor `input_alias1 = C,`  
  
- Laat `<from_source3>` op `input_alias1` staan en vertegenwoordigen sets:  
  
    {100, 200} voor `input_alias2 = A,`  
  
    {300} voor `input_alias2 = C,`  
  
- De component FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` levert de volgende Tuples op:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > Dit resulteerde in een kruis product tussen `<from_source2>` en `<from_source3>` omdat beide hetzelfde `<from_source1>`zijn.  Dit heeft geleid tot 4 (2x2) Tuples met waarde A, 0 Tuples met waarde B (1x0) en 2 (2x1) Tuples met waarde C.  
  
## <a name="examples"></a>Voorbeelden

De volgende voorbeelden laten zien hoe de JOIN-component werkt. Voordat u deze voor beelden uitvoert, moet u de gegevens van de voorbeeld [familie](sql-query-getting-started.md#upload-sample-data)uploaden. In het volgende voor beeld is het resultaat leeg, omdat het cross-product van elk item van de bron en een lege set leeg is:

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

In het volgende voor beeld is de samen voeging een kruis product tussen twee JSON-objecten, de hoofdmap van het item `id` en de subhoofdmap van `children`. Het feit dat `children` een matrix is, is niet effectief in de samen voeging, omdat deze wordt behandeld met één hoofdmap die de `children`-matrix is. Het resultaat bevat slechts twee resultaten, omdat het cross-product van elk item met de matrix resulteert in slechts één item.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

De resultaten zijn:

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

De resultaten zijn:

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

De FROM-bron van de component JOINEXPRESSIE is een iterator. De stroom in het vorige voor beeld is dus:  

1. Vouw elk onderliggend element uit `c` in de matrix.
2. Een kruis product Toep assen met de hoofdmap van het item `f` met elk onderliggend element `c` dat de eerste stap is afgevlakt.
3. Ten slotte Projecteer het root-object alleen `f` eigenschap `id`.

Het eerste item `AndersenFamily`bevat slechts één `children` element, dus de resultatenset bevat slechts één object. Het tweede item `WakefieldFamily`, bevat twee `children`, zodat het cross-product twee objecten produceert, één voor elk `children`-element. De hoofdvelden in beide items zijn hetzelfde, zoals u zou verwachten bij een vectorproduct.

Het echte hulp programma van de component samen voegen is om Tuples te vormen van het cross product in een vorm die op een andere manier moeilijk te project is. In het onderstaande voor beeld wordt gefilterd op de combi natie van een tuple waarmee de gebruiker een voor waarde kan kiezen waaraan de Tuples zijn voldaan.

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

De resultaten zijn:

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

Met de volgende uitbrei ding van het voor gaande voor beeld wordt een dubbele koppeling uitgevoerd. U kunt het cross-product weer geven als de volgende pseudo-code:

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

`AndersenFamily` heeft één onderliggend item met één huis dier, waardoor het cross product één rij (1\*1\*1) van deze familie overdraagt. `WakefieldFamily` heeft twee onderliggende items, maar slechts één van huis dieren, maar dat kind heeft twee huis dieren. Het cross product voor deze familie levert 1\*1\*2 = 2 rijen.

In het volgende voor beeld is er een aanvullend filter op `pet`, waarbij alle Tuples worden uitgesloten waarvan de naam van het huis dier niet `Shadow`. U kunt Tuples maken op basis van matrices, filteren op een van de elementen van de tuple en een combi natie van de elementen projecteren.

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

De resultaten zijn:

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

- [Aan de slag](sql-query-getting-started.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Subquery's](sql-query-subquery.md)