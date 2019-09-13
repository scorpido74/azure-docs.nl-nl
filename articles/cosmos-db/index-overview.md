---
title: Indexeren in Azure Cosmos DB
description: Begrijpen hoe indexering werkt in Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: 4d961f8635a52a09011543b793ce8a87eaa4ea9e
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914198"
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

Azure Cosmos DB ondersteunt momenteel drie soorten indexen:

Het **bereik** index type wordt gebruikt voor:

- Gelijkheids query's:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

- Bereik query's:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (werkt voor `>`, `<`, `>=` ,,`!=`,) `<=`

- `ORDER BY`aanvragen

   ```sql 
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`aanvragen

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Bereik indexen kunnen worden gebruikt voor scalaire waarden (teken reeks of getal).

Het type **ruimtelijke** index wordt gebruikt voor:

- Georuimtelijke afstand query's: 

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Georuimtelijk binnen query's: 

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

Ruimtelijke indexen kunnen worden gebruikt op correct opgemaakte [GEOjson](geospatial.md) -objecten. Punten, line strings toe, veelhoeken en multiveelhoeken worden momenteel ondersteund.

De **samengestelde** index soort wordt gebruikt voor:

- `ORDER BY`query's op meerdere eigenschappen:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Query's met een filter en `ORDER BY`. Deze query's kunnen gebruikmaken van een samengestelde index als de eigenschap filter wordt toegevoegd aan de `ORDER BY` component.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Query's met een filter voor twee of meer eigenschappen waarbij ten minste één eigenschap een gelijkheids filter is

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

## <a name="querying-with-indexes"></a>Query's uitvoeren met indexen

De paden die worden geëxtraheerd bij het indexeren van gegevens, maken het gemakkelijker om de index bij het verwerken van een query te zoeken. Door de `WHERE` component van een query te vergelijken met de lijst met geïndexeerde paden, is het mogelijk om de items te identificeren die overeenkomen met het query predicaat zeer snel.

Bekijk bijvoorbeeld de volgende query: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Het query predicaat (filteren op items, waarbij een wille keurige locatie ' Frank rijk ' heeft als land) overeenkomt met het pad dat in rood is gemarkeerd:

![Een specifiek pad binnen een structuur zoeken](./media/index-overview/matching-path.png)

> [!NOTE]
> Een `ORDER BY` component waarbij orders met één eigenschap *altijd* een bereik index nodig heeft en mislukt als het pad waarnaar wordt verwezen, niet is opgenomen. Op dezelfde manier `ORDER BY` heeft een query die door meerdere eigenschappen orders *altijd* een samengestelde index nodig.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over indexeren vindt u in de volgende artikelen:

- [Indexeringsbeleid](index-policy.md)
- [Indexerings beleid beheren](how-to-manage-indexing-policy.md)
