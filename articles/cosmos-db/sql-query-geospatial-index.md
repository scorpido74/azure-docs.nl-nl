---
title: Georuimtelijke gegevens indexeren met Azure Cosmos DB
description: Ruimtelijke gegevens indexeren met Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 2cf682a404154b9c1bb94680b3adb673892c1c72
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566372"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Georuimtelijke gegevens indexeren met Azure Cosmos DB

De data base-engine van Azure Cosmos DB is ontworpen om echt schema neutraal te zijn en biedt ondersteuning voor de eerste klasse voor JSON. De geoptimaliseerde data base-engine voor schrijven van Azure Cosmos DB heeft in systeem eigen inzicht in de ruimtelijke gegevens die in de geojson-standaard worden weer gegeven.

In een kort gezegd wordt de geometrie geprojecteerd van Geodetic-coördinaten naar een 2D-vlak en vervolgens geleidelijk onderverdeeld in cellen met behulp van een **quadtree**. Deze cellen worden toegewezen aan 1D op basis van de locatie van de cel binnen een **Hilbert ruimte**die de lokale positie van punten behoudt. Wanneer locatie gegevens worden geïndexeerd, wordt er ook een proces met de naam **mozaïek patroon**genoemd, dat wil zeggen dat alle cellen die een locatie overlappen worden geïdentificeerd en opgeslagen als sleutels in de Azure Cosmos DB index. Op het moment dat de query, argumenten, zoals punten en polygonen zijn ook representatie mozaïekpatroon om op te halen van de relevante ID celbereiken, en vervolgens gebruikt voor het ophalen van gegevens uit de index.

Als u een indexerings beleid opgeeft dat ruimtelijke index voor/* (alle paden) bevat, worden alle gegevens die in de container zijn gevonden, geïndexeerd voor efficiënte ruimtelijke query's.

> [!NOTE]
> Azure Cosmos DB ondersteunt het indexeren van punten, line strings toe, veelhoeken en multiveelhoeken
>
>

## <a name="geography-data-indexing-examples"></a>Voor beelden van geografie gegevens indexering

Het volgende JSON-code fragment bevat een indexerings beleid waarvoor ruimtelijke indexering is ingeschakeld voor het **geografie** gegevens type. Het is geldig voor ruimtelijke gegevens met het geografie gegevens type en indexeert een geojson Point, veelhoek, multiveelhoek of lines Tring die zich binnen documenten bevinden voor ruimtelijke query's. Als u het indexerings beleid wijzigt met behulp van de Azure Portal, kunt u de volgende JSON voor het indexerings beleid opgeven om ruimtelijke indexering in te scha kelen in uw container:

**Container indexerings beleid JSON met geografie ruimtelijke indexeringen**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
        "includedPaths": [
        {
            "path": "/*"
        }
        ],
        "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
       "excludedPaths":[]
    }
```

> [!NOTE]
> Als de locatie GeoJSON-waarde in het document onjuist gevormd of ongeldig is, worden klikt u vervolgens het niet geïndexeerd voor ruimtelijke query's. U kunt de locatie waarden met behulp van ST_ISVALID en ST_ISVALIDDETAILED valideren.
>
>
>

U kunt het [indexerings beleid ook wijzigen](how-to-manage-indexing-policy.md) met behulp van de Azure CLI, Power shell of een SDK.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u aan de slag met georuimtelijke ondersteuning in Azure Cosmos DB, naast kunt u:

* Meer informatie over [Azure Cosmos DB query](sql-query-getting-started.md)
* Meer informatie over het [opvragen van ruimtelijke gegevens met Azure Cosmos DB](sql-query-geospatial-query.md)
* Meer informatie over [georuimtelijke en GEOjson-locatie gegevens in azure Cosmos DB](sql-query-geospatial-intro.md)