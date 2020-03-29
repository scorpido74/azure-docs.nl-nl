---
title: SQL-subquery's voor Azure Cosmos DB
description: Meer informatie over SQL-subquery's en hun algemene use cases en verschillende typen subquery's in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 42d9e8b190747a3ffaf0e46ea1eddda33d09bb24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870561"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>SQL-subqueryvoorbeelden voor Azure Cosmos DB

Een subquery is een query die is genest in een andere query. Een subquery wordt ook wel een innerlijke query of inner select genoemd. De instructie die een subquery bevat, wordt meestal een externe query genoemd.

In dit artikel worden SQL-subquery's en hun algemene use cases in Azure Cosmos DB beschreven. Alle voorbeeldquery's in dit document kunnen worden uitgevoerd tegen een voedingsgegevensset die vooraf is geladen op de [Azure Cosmos DB Query Playground.](https://www.documentdb.com/sql/demo)

## <a name="types-of-subqueries"></a>Typen subquery's

Er zijn twee hoofdtypen subquery's:

* **Gecorreleerd:** een subquery die verwijst naar waarden uit de buitenste query. De subquery wordt eenmaal geëvalueerd voor elke rij die de buitenste query verwerkt.
* **Niet-gecorreleerde**: een subquery die onafhankelijk is van de buitenste query. Het kan worden uitgevoerd op zijn eigen zonder te vertrouwen op de buitenste query.

> [!NOTE]
> Azure Cosmos DB ondersteunt alleen gecorreleerde subquery's.

Subquery's kunnen verder worden geclassificeerd op basis van het aantal rijen en kolommen dat ze retourneren. Er zijn drie soorten:
* **Tabel:** geeft als resultaat meerdere rijen en meerdere kolommen.
* **Multi-value:** geeft als resultaat meerdere rijen en één kolom.
* **Scalar:** geeft als resultaat een enkele rij en één kolom.

SQL-query's in Azure Cosmos DB retourneren altijd één kolom (een eenvoudige waarde of een complex document). Daarom zijn alleen subquery's met meerdere waarde en scalaire query's van toepassing in Azure Cosmos DB. U een subquery met meerdere waardeen alleen gebruiken in de FROM-component als relationele expressie. U een scalaire subquery gebruiken als een scalaire expressie in de SELECT- of WHERE-component, of als relationele expressie in de FROM-component.

## <a name="multi-value-subqueries"></a>Subquery's met meerdere waarde

Subquery's met meerdere waarde's retourneren een set documenten en worden altijd gebruikt binnen de FROM-component. Ze worden gebruikt voor:

* JOIN-expressies optimaliseren. 
* Dure expressies één keer evalueren en meerdere keren verwijzen.

## <a name="optimize-join-expressions"></a>JOIN-expressies optimaliseren

Subquery's met meerdere waarden kunnen JOIN-expressies optimaliseren door na elke select-many expressie op basis van selectie te drukken in plaats van dat alle cross-joins in de WHERE-component.

Laten we nu eens naar deze query kijken:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Voor deze query komt de index overeen met elk document met een tag met de naam 'zuigelingenvoeding'. Het is een voedingsartikel met een waarde tussen 0 en 10, en een serveeritem met een bedrag groter dan 1. De JOIN-expressie hier voert het cross-product uit van alle items van tags, voedingsstoffen en serveerarrays voor elk overeenkomend document voordat een filter wordt toegepast. 

De WHERE-clausule past vervolgens het filterpredicaat toe op elke <c, t, n, s> tuple. Als een overeenkomend document bijvoorbeeld 10 items in elk van de drie arrays had, worden de tuples uitgebreid tot 1 x 10 x 10 x 10 (dat wil zeggen 1.000). Als u subquery's gebruikt, u bij het filteren van samengevoegde arrayitems helpen voordat u aansluit bij de volgende expressie.

Deze query is gelijk aan de vorige query's, maar gebruikt subquery's:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Stel dat slechts één item in de array tags overeenkomt met het filter en dat er vijf items zijn voor zowel voedingsstoffen als arrays voor porties. De JOIN-expressies worden vervolgens uitgebreid tot 1 x 1 x 5 x 5 = 25 items, in tegenstelling tot 1.000 items in de eerste query.

## <a name="evaluate-once-and-reference-many-times"></a>Evalueer één keer en verwijs vele malen

Subquery's kunnen query's optimaliseren met dure expressies zoals door de gebruiker gedefinieerde functies (UDF's), complexe tekenreeksen of rekenkundige expressies. U een subquery samen met een JOIN-expressie gebruiken om de expressie één keer te evalueren, maar er vaak naar te verwijzen.

In de volgende query `GetMaxNutritionValue` wordt de UDF twee keer uitgevoerd:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Hier is een gelijkwaardige query die de UDF slechts één keer uitvoert:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Houd rekening met het cross-product gedrag van JOIN-expressies. Als de UDF-expressie kan worden geëvalueerd naar niet-gedefinieerd, moet u ervoor zorgen dat de JOIN-expressie altijd één rij produceert door een object uit de subquery terug te sturen in plaats van rechtstreeks de waarde.
>

Hier is een vergelijkbaar voorbeeld dat een object retourneert in plaats van een waarde:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

De aanpak is niet beperkt tot UDF's. Het is van toepassing op elke potentieel dure uitdrukking. U bijvoorbeeld dezelfde benadering volgen met `avg`de wiskundige functie:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Join nabootsen met externe referentiegegevens

Het kan vaak nodig zijn om te verwijzen naar statische gegevens die zelden veranderen, zoals meeteenheden of landcodes. Het is beter om dergelijke gegevens niet te dupliceren voor elk document. Het vermijden van deze duplicatie bespaart op opslag en verbetert de schrijfprestaties door de documentgrootte kleiner te houden. U een subquery gebruiken om semantiek voor verzameling en selectie na te bootsen met een verzameling referentiegegevens.

Denk bijvoorbeeld aan deze set referentiegegevens:

| **Eenheid** | **Naam**            | **Multiplier** | **Basiseenheid** |
| -------- | ------------------- | -------------- | ------------- |
| Ng       | Nanogram            | 1.00E-09       | Gram          |
| Μg       | Microgram microgram           | 1.00E-06       | Gram          |
| Mg       | Milligram           | 1.00E-03       | Gram          |
| g        | Gram                | 1.00E+00       | Gram          |
| Kg       | Kilogram            | 1.00E+03       | Gram          |
| Mg       | Megagram Megagram            | 1.00E+06       | Gram          |
| Gg       | Gigagram Gigagram            | 1.00E+09       | Gram          |
| Nj       | Nanojoule Nanojoule           | 1.00E-09       | Joule         |
| μJ       | Microjoule Microjoule          | 1.00E-06       | Joule         |
| Mj       | Millijoule Millijoule          | 1.00E-03       | Joule         |
| J        | Joule               | 1.00E+00       | Joule         |
| Kj       | Kilojoule Kilojoule           | 1.00E+03       | Joule         |
| Mj       | Megajoule Megajoule           | 1.00E+06       | Joule         |
| Gj       | Gigajoule Gigajoule           | 1.00E+09       | Joule         |
| Cal      | Calorie             | 1.00E+00       | Calorie       |
| Kcal     | Calorie             | 1.00E+03       | Calorie       |
| Iu       | Internationale eenheden |                |               |


De volgende query bootst het samenvoegen met deze gegevens na, zodat u de naam van het apparaat aan de uitvoer toevoegt:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Scalaire subquery's

Een scalaire subqueryexpressie is een subquery die wordt geëvalueerd tot één waarde. De waarde van de scalaire subqueryexpressie is de waarde van de projectie (SELECT-component) van de subquery.  U een scalaire subqueryexpressie gebruiken op veel plaatsen waar een scalaire expressie geldig is. U bijvoorbeeld een scalaire subquery gebruiken in elke expressie in zowel de SELECT- als DE WAAR-clausules.

Het gebruik van een scalaire subquery helpt echter niet altijd bij het optimaliseren. Als u bijvoorbeeld een scalaire subquery als argument doorgeeft aan een systeem of door de gebruiker gedefinieerde functies, heeft dit geen voordeel in het ru-verbruik of de latentie van resource-eenheden (RU).

Scalar subqueries kunnen verder worden geclassificeerd als:
* Scalaire subquery's met eenvoudige expressie
* Geaggregeerde scalaire subquery's

## <a name="simple-expression-scalar-subqueries"></a>Scalaire subquery's met eenvoudige expressie

Een scalaire subquery met eenvoudige expressieis een gecorreleerde subquery met een SELECT-component die geen geaggregeerde expressies bevat. Deze subquery's bieden geen optimalisatievoordelen omdat de compiler ze omzet in één grotere eenvoudige expressie. Er is geen gecorreleerde context tussen de binnenste en buitenste query's.

Hier zijn enkele voorbeelden:

**Voorbeeld 1**

```sql
SELECT 1 AS a, 2 AS b
```

U deze query herschrijven door een subquery met eenvoudige expressiescala te gebruiken om:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Beide query's produceren deze uitvoer:

```json
[
  { "a": 1, "b": 2 }
]
```

**Voorbeeld 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

U deze query herschrijven door een subquery met eenvoudige expressiescala te gebruiken om:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Query-uitvoer:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Voorbeeld 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

U deze query herschrijven door een subquery met eenvoudige expressiescala te gebruiken om:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Query-uitvoer:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Geaggregeerde scalaire subquery's

Een geaggregeerde scalaire subquery is een subquery met een geaggregeerde functie in de projectie of het filter die wordt geëvalueerd tot één waarde.

**Voorbeeld 1:**

Hier is een subquery met één samengevoegde functie-expressie in de projectie:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Query-uitvoer:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Voorbeeld 2**

Hier is een subquery met meerdere samengevoegde functieexpressies:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Query-uitvoer:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Voorbeeld 3**

Hier is een query met een geaggregeerde subquery in zowel de projectie als het filter:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Query-uitvoer:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Een meer optimale manier om deze query te schrijven is door deel te nemen aan de subquery en de subqueryalias te verwijzen in zowel de SELECT- als DE WAAR-clausules. Deze query is efficiënter omdat u de subquery alleen binnen de join-instructie moet uitvoeren en niet in zowel de projectie als het filter.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>BESTAANDE-expressie

Azure Cosmos DB ondersteunt BESTAANDE-expressies. Dit is een geaggregeerde scalaire subquery die is ingebouwd in de Azure Cosmos DB SQL API. BESTAAT is een Booleaanse expressie die een subquery-expressie neemt en waar retourneert als de subquery rijen retourneert. Anders wordt het onwaar geretourneerd.

Omdat de Azure Cosmos DB SQL API geen onderscheid maakt tussen Booleaanse expressies en andere scalaire expressies, u BESTAANDE gebruiken in zowel SELECT- als WHERE-clausules. Dit is in tegenstelling tot T-SQL, waarbij een Booleaanse expressie (bijvoorbeeld BESTAAT, TUSSEN en IN) beperkt is tot het filter.

Als de subquery BESTAAT één waarde retourneert die niet is gedefinieerd, wordt BESTAAT geëvalueerd naar false. Houd bijvoorbeeld rekening met de volgende query die wordt geëvalueerd als onwaar:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Als het trefwoord WAARDE in de voorgaande subquery wordt weggelaten, wordt de query geëvalueerd naar true:
```sql
SELECT EXISTS (SELECT undefined) 
```

De subquery wordt de lijst met waarden in de geselecteerde lijst in een object bijgesloten. Als de geselecteerde lijst geen waarden heeft, retourneert de subquery de waarde '{}''. Deze waarde wordt gedefinieerd, dus BESTAAT evalueert naar waar.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Voorbeeld: ARRAY_CONTAINS herschrijven en JOIN als ER BESTAAT

Een veelgebruikte use case van ARRAY_CONTAINS is het filteren van een document op het bestaan van een item in een array. In dit geval controleren we of de array tags een item bevat met de naam 'oranje'.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

U dezelfde query herschrijven om BESTAAT te gebruiken:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Bovendien ARRAY_CONTAINS alleen controleren of een waarde gelijk is aan een element binnen een array. Als u complexere filters voor arrayeigenschappen nodig hebt, gebruikt u JOIN.

Houd rekening met de volgende query `nutritionValue` die wordt gefilterd op basis van de eenheden en eigenschappen in de array: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Voor elk van de documenten in de collectie wordt een cross-product uitgevoerd met de array-elementen. Deze JOIN-bewerking maakt het mogelijk om te filteren op eigenschappen binnen de array. Het RU-verbruik van deze query zal echter aanzienlijk zijn. Als er bijvoorbeeld 1.000 documenten in elke array 100 items bevatten, worden de tuples uitgebreid tot 1.000 x 100 (dat wil zeggen 100.000).

Het gebruik van EXISTS kan helpen om dit dure cross-product te voorkomen:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

In dit geval filtert u op matrixelementen binnen de subquery BESTAAT. Als een arrayelement overeenkomt met het filter, projecteert u het en evalueert U het naar true.

U ook alias EXISTS en verwijzen naar het in de projectie:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Query-uitvoer:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>ARRAY-expressie

U de arrayexpressie gebruiken om de resultaten van een query als een array te projecteren. U deze expressie alleen gebruiken binnen de SELECT-component van de query.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Query-uitvoer:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Net als bij andere subquery's zijn filters met de ARRAY-expressie mogelijk.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Query-uitvoer:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Arrayexpressies kunnen ook na de FROM-component in subquery's komen.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Query-uitvoer:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Volgende stappen

- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Documentgegevens modelleren](modeling-data.md)
