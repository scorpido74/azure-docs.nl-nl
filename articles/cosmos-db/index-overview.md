---
title: Indexeren in Azure Cosmos DB
description: Begrijpen hoe indexering werkt in Azure Cosmos DB, verschillende soorten indexen zoals Bereik, Ruimtelijke, samengestelde indexen ondersteund.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: thweiss
ms.openlocfilehash: 65186262095560d7ae54d32b218d1c01f1fb921d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873621"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexeren in Azure Cosmos DB: een overzicht

Azure Cosmos DB is een schema-agnostische database waarmee u uw toepassing herhalen zonder dat u te maken hoeft te krijgen met schema- of indexbeheer. Azure Cosmos DB indexeert standaard automatisch elke eigenschap voor alle items in uw [container](databases-containers-items.md#azure-cosmos-containers) zonder dat u een schema hoeft te definiëren of secundaire indexen hoeft te configureren.

Het doel van dit artikel is om uit te leggen hoe gegevens worden geïndexeerd met Azure Cosmos DB en hoe indexen worden gebruikt om de queryprestaties te verbeteren. Het wordt aanbevolen om door deze sectie te gaan voordat u gaat onderzoeken hoe [indexeringsbeleid](index-policy.md)kan worden aangepast.

## <a name="from-items-to-trees"></a>Van voorwerpen tot bomen

Elke keer dat een item in een container wordt opgeslagen, wordt de inhoud ervan geprojecteerd als een JSON-document en vervolgens omgezet in een structuurweergave. Wat dat betekent is dat elke eigenschap van dat item wordt weergegeven als een knooppunt in een boom. Een pseudo-hoofdknooppunt wordt gemaakt als bovenliggendvoor alle eigenschappen op het eerste niveau van het item. De bladknooppunten bevatten de werkelijke scalaire waarden die door een item worden gedragen.

Overweeg bijvoorbeeld dit object:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

Het zou worden vertegenwoordigd door de volgende boom:

![Het vorige item dat als een boom wordt weergegeven](./media/index-overview/item-as-tree.png)

Merk op hoe arrays worden gecodeerd in de structuur: elke vermelding in een array krijgt een tussenliggend knooppunt gelabeld met de index van die vermelding binnen de array (0, 1 enz.).

## <a name="from-trees-to-property-paths"></a>Van bomen tot eigendomspaden

De reden waarom Azure Cosmos DB items omzet in bomen is omdat hiermee eigenschappen kunnen worden verwezen door hun paden binnen die bomen. Om het pad voor een eigenschap te krijgen, kunnen we de boom doorkruisen vanaf het wortelknooppunt naar die eigenschap en de labels van elk doorreisknooppunt herhalen.

Hier volgen de paden voor elke eigenschap uit het hierboven beschreven voorbeelditem:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Wanneer een item is geschreven, indexeert Azure Cosmos DB effectief het pad van elke eigenschap en de bijbehorende waarde.

## <a name="index-kinds"></a>De soorten van de index

Azure Cosmos DB ondersteunt momenteel drie soorten indexen.

### <a name="range-index"></a>Bereikindex

**De bereikindex** is gebaseerd op een geordende boomachtige structuur. De soort range index wordt gebruikt voor:

- Gelijkheidsquery's:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Gelijkheidsovereenkomst op een arrayelement
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1”)
    ```

- Bereikquery's:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (werken `>`voor `<` `>=`, `<=` `!=`, , , )

- Controleren op de aanwezigheid van een woning:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Tekenreeksvoorvoegingen (trefwoord CONTAINS maakt geen gebruik van de bereikindex):

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY`Query 's:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`Query 's:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Bereikindexen kunnen worden gebruikt op scalaire waarden (tekenreeks of getal).

### <a name="spatial-index"></a>Ruimtelijke index

**Ruimtelijke** indexen maken efficiënte query's mogelijk op georuimtelijke objecten zoals - punten, lijnen, veelhoeken en multipolygoon. Deze query's gebruiken ST_DISTANCE, ST_WITHIN, ST_INTERSECTS trefwoorden. Hieronder volgen enkele voorbeelden die gebruik maken van ruimtelijke indexsoort:

- Query's voor georuimtelijke afstand:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Georuimtelijk binnen query's:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Georuimtelijke kruisquery's:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Ruimtelijke indexen kunnen worden gebruikt op correct opgemaakte [GeoJSON-objecten.](geospatial.md) Punten, LineStrings, Polygonen en MultiPolygons worden momenteel ondersteund.

### <a name="composite-indexes"></a>Samengestelde indexen

**Samengestelde** indexcijfers verhogen de efficiëntie wanneer u bewerkingen uitvoert op meerdere velden. De samengestelde indexsoort wordt gebruikt voor:

- `ORDER BY`query's op meerdere eigenschappen:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Query's met `ORDER BY`een filter en . Deze query's kunnen een samengestelde index gebruiken `ORDER BY` als de eigenschap filter aan de component wordt toegevoegd.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Query's met een filter op twee of meer eigenschappen waarbij ten minste één eigenschap een gelijkheidsfilter is

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Zolang één filterpredicaat op van de indexsoort wordt gebruikt, zal de querymotor dat eerst evalueren alvorens de rest te scannen. Als u bijvoorbeeld een SQL-query hebt, zoals`SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* De bovenstaande query filtert eerst op inzendingen waarbij firstName = "Andrew" wordt gebruikt met behulp van de index. Vervolgens passeren alle firstName = "Andrew" items via een volgende pijplijn om de CONTAINS filter predicaat te evalueren.

* U query's versnellen en volledige containerscans vermijden bij het gebruik van functies die de index niet gebruiken (bijvoorbeeld BEVAT) door extra filterpredicaten toe te voegen die de index wel gebruiken. De volgorde van filterclausules is niet belangrijk. De query engine is zal uitzoeken welke predicaten zijn selectiever en voer de query dienovereenkomstig.


## <a name="querying-with-indexes"></a>Query's met indexen

De paden die worden geëxtraheerd bij het indexeren van gegevens maken het gemakkelijk om de index op te zoeken bij het verwerken van een query. Door de `WHERE` component van een query te koppelen aan de lijst met geïndexeerde paden, is het mogelijk om de items te identificeren die het querypredicaat zeer snel overeenkomen.

Denk bijvoorbeeld aan de `SELECT location FROM location IN company.locations WHERE location.country = 'France'`volgende query: . Het querypredicaat (filteren op items, waarbij elke locatie 'Frankrijk' als land heeft) komt overeen met het rode pad:

![Een specifiek pad binnen een boom afstemmen](./media/index-overview/matching-path.png)

> [!NOTE]
> Een `ORDER BY` clausule die orders door een enkele eigenschap *altijd* een bereikindex nodig heeft en mislukt als het pad waarnaar wordt verwezen er geen heeft. Op dezelfde `ORDER BY` manier heeft een query die op meerdere eigenschappen bestelt *altijd* een samengestelde index nodig.

## <a name="next-steps"></a>Volgende stappen

Lees meer over indexering in de volgende artikelen:

- [Indexeringsbeleid](index-policy.md)
- [Indexeringsbeleid beheren](how-to-manage-indexing-policy.md)
