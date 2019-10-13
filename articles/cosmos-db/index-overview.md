---
title: Indexeren in Azure Cosmos DB
description: Begrijpen hoe indexering werkt in Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: thweiss
ms.openlocfilehash: d679208914eb7d1f74bfaec77fbcff196909a2f4
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299783"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexeren in Azure Cosmos DB-overzicht

Azure Cosmos DB is een schema-neutraal-data base waarmee u de toepassing kunt herhalen zonder schema-of index beheer. Standaard indexeert Azure Cosmos DB automatisch elke eigenschap voor alle items in uw [container](databases-containers-items.md#azure-cosmos-containers) zonder schema te hoeven definiëren of secundaire indexen te configureren.

Het doel van dit artikel is om uit te leggen hoe Azure Cosmos DB gegevens indexeert en hoe het indexen gebruikt om de query prestaties te verbeteren. U wordt aangeraden deze sectie door te lopen voordat u het [indexerings beleid](index-policy.md)aanpast.

## <a name="from-items-to-trees"></a>Van items naar bomen

Telkens wanneer een item wordt opgeslagen in een container, wordt de inhoud ervan geprojecteerd als een JSON-document en vervolgens omgezet in een structuur weergave. Dat betekent dat elke eigenschap van dat item wordt weer gegeven als een knoop punt in een boom structuur. Er wordt een pseudo-hoofd knooppunt gemaakt als bovenliggend knoop punt voor alle eigenschappen van het eerste niveau van het item. De blad knooppunten bevatten de werkelijke scalaire waarden die door een item worden uitgevoerd.

Bekijk bijvoorbeeld dit item:

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

Deze wordt weer gegeven in de volgende structuur:

![Het vorige item wordt weer gegeven als een boom structuur](./media/index-overview/item-as-tree.png)

U ziet hoe matrices worden gecodeerd in de boom structuur: elke vermelding in een matrix haalt een tussenliggend knoop punt op dat is gelabeld met de index van die vermelding in de matrix (0, 1 enz.).

## <a name="from-trees-to-property-paths"></a>Van structuren naar eigenschaps paden

De reden waarom Azure Cosmos DB het transformeren van items naar bomen, is omdat hiermee kan worden verwezen naar de paden binnen deze trees. Om het pad voor een eigenschap op te halen, kunnen we de structuur van het hoofd knooppunt naar die eigenschap door lopen en de labels van elk gepasseerd knoop punt samen voegen.

Dit zijn de paden voor elke eigenschap van het hierboven beschreven voor beeld-item:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Wanneer een item wordt geschreven, indexeert Azure Cosmos DB in feite elke eigenschap van het pad en de bijbehorende waarde.

## <a name="index-kinds"></a>Index typen

Azure Cosmos DB ondersteunt momenteel drie soorten indexen.

### <a name="range-index"></a>Bereik index

De **bereik** index is gebaseerd op een geordende boom structuur. Het bereik index type wordt gebruikt voor:

- Gelijkheids query's:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Gelijkheids overeenkomst voor een matrix element
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1”)
    ```

- Bereik query's:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (werkt voor `>`, `<`, `>=`, `<=`, `!=`)

- Controleren op de aanwezigheid van een eigenschap:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Komt overeen met het voor voegsel van de teken reeks (bevat de bereik index):

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY` query's:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN` query's:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Bereik indexen kunnen worden gebruikt voor scalaire waarden (teken reeks of getal).

### <a name="spatial-index"></a>Ruimtelijke index

Met **ruimtelijke** indexen kunnen efficiënte query's worden uitgevoerd op georuimtelijke objecten zoals-Points, lijnen, veelhoeken en multiveelhoeken. Deze query's gebruiken ST_DISTANCE-, ST_WITHIN-, ST_INTERSECTS-tref woorden. Hieronder vindt u enkele voor beelden van ruimtelijke-index type:

- Georuimtelijke afstand query's:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Georuimtelijk binnen query's:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- Georuimtelijke Intersect-query's:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Ruimtelijke indexen kunnen worden gebruikt op correct opgemaakte [GEOjson](geospatial.md) -objecten. Punten, line strings toe, veelhoeken en multiveelhoeken worden momenteel ondersteund.

### <a name="composite-indexes"></a>Samengestelde indexen

**Samengestelde** indexen verhogen de efficiëntie wanneer u bewerkingen uitvoert op meerdere velden. De samengestelde index soort wordt gebruikt voor:

- `ORDER BY` query's op meerdere eigenschappen:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Query's met een filter en `ORDER BY`. Deze query's kunnen gebruikmaken van een samengestelde index als de filter eigenschap wordt toegevoegd aan de component `ORDER BY`.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Query's met een filter voor twee of meer eigenschappen waarbij ten minste één eigenschap een gelijkheids filter is

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Zolang de ene filter-predikaat gebruikmaakt van het index type, evalueert de query-engine dat eerst voordat de rest wordt gescand. Als u bijvoorbeeld een SQL-query hebt, bijvoorbeeld `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* Met de bovenstaande query wordt eerst gefilterd op vermeldingen waarbij voor naam "Andrew" met behulp van de index. Vervolgens worden alle voor naam = "Andrew" vermeldingen door gegeven aan een volgende pijp lijn om het CONTAINs-filter predicaat te evalueren.

* U kunt query's versnellen en volledige container scans voor komen met behulp van functies die de index niet gebruiken (bijvoorbeeld CONTAINs) door extra filter predikaten toe te voegen die de index gebruiken. De volg orde van de filter componenten is niet belang rijk. De query-engine geeft aan welke predikaten meer selectief zijn en voeren de query dienovereenkomstig uit.


## <a name="querying-with-indexes"></a>Query's uitvoeren met indexen

De paden die worden geëxtraheerd bij het indexeren van gegevens, maken het gemakkelijker om de index bij het verwerken van een query te zoeken. Door de component `WHERE` van een query te vergelijken met de lijst met geïndexeerde paden, is het mogelijk om de items te identificeren die overeenkomen met het query predicaat zeer snel.

Bekijk bijvoorbeeld de volgende query: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Het query predicaat (filteren op items, waarbij een wille keurige locatie ' Frank rijk ' heeft als land) overeenkomt met het pad dat in rood is gemarkeerd:

![Een specifiek pad binnen een structuur zoeken](./media/index-overview/matching-path.png)

> [!NOTE]
> Een `ORDER BY`-component waarmee orders met één eigenschap *altijd* een bereik index nodig hebben en mislukt als het pad waarnaar wordt verwezen, niet in een van beide eigenschappen voor komt. Op dezelfde manier kan een `ORDER BY`-query voor orders met meerdere eigenschappen *altijd* een samengestelde index nodig hebben.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over indexeren vindt u in de volgende artikelen:

- [Indexeringsbeleid](index-policy.md)
- [Indexerings beleid beheren](how-to-manage-indexing-policy.md)
