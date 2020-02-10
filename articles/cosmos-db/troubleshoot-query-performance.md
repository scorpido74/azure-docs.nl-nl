---
title: Problemen met query's oplossen bij het gebruik van Azure Cosmos DB
description: Meer informatie over het identificeren, vaststellen en oplossen van problemen met Azure Cosmos DB SQL-query's.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 01/14/2020
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: f016902f6cf7e0238dadb97d816f4590caec112e
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109340"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Problemen met query's oplossen bij het gebruik van Azure Cosmos DB

In dit artikel wordt een algemene aanbevolen benadering besproken voor het oplossen van problemen met query's in Azure Cosmos DB. Hoewel de stappen die in dit document worden beschreven, niet als een ' catch all ' worden beschouwd voor mogelijke query problemen, hebben we hier de meest voorkomende tips voor de prestaties opgenomen. U moet dit document gebruiken als uitgangs punt voor het oplossen van problemen met trage of dure query's in de core-API (SQL) van Azure Cosmos DB. U kunt ook [Diagnostische logboeken](cosmosdb-monitor-resource-logs.md) gebruiken om query's te identificeren die langzaam zijn of die aanzienlijke hoeveel heden door Voer in beslag nemen.

U kunt query optimalisaties in Azure Cosmos DB breed categoriseren: optimalisaties die de administratie kosten van de aanvraag eenheid (RU) van de query en Optima Lise ring beperken die alleen de latentie verminderen. Door de RU-kosten van een query te verminderen, zult u bijna zeker de latentie verminderen.

In dit document worden voor beelden gebruikt die opnieuw kunnen worden gemaakt met behulp van de [voedings](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) gegevensset.

### <a name="obtaining-query-metrics"></a>Metrische query gegevens verkrijgen:

Wanneer u een query in Azure Cosmos DB optimaliseert, is de eerste stap altijd om [de metrische gegevens van de query](profile-sql-api-query.md) voor uw query te verkrijgen. Deze zijn ook beschikbaar via de Azure Portal, zoals hieronder wordt weer gegeven:

[![de metrische gegevens van de query ophalen](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Nadat u de metrische gegevens van de query hebt verkregen, moet u het opgehaalde document aantal vergelijken met het aantal uitvoer documenten voor uw query. Gebruik deze vergelijking om de relevante secties waarnaar u kunt verwijzen, te identificeren.

Het opgehaalde document telt het aantal documenten dat door de query moet worden geladen. Het uitvoer document telt het aantal documenten dat nodig was voor de resultaten van de query. Als het aantal opgehaalde documenten aanzienlijk hoger is dan het aantal uitvoer documenten, was er ten minste één deel van de query die de index niet kan gebruiken en die nodig is om een scan uit te voeren.

U kunt naar de onderstaande sectie verwijzen om inzicht te krijgen in de relevante query optimalisaties voor uw scenario:

### <a name="querys-ru-charge-is-too-high"></a>De RU-kosten van de query zijn te hoog

#### <a name="retrieved-document-count-is-significantly-greater-than-output-document-count"></a>Het opgehaalde document aantal is aanzienlijk groter dan het aantal uitvoer documenten

- [Benodigde paden in het indexerings beleid toevoegen](#include-necessary-paths-in-the-indexing-policy)

- [Begrijpen welke systeem functies de index gebruiken](#understand-which-system-functions-utilize-the-index)

- [Query's met zowel een filter als een ORDER BY-component](#queries-with-both-a-filter-and-an-order-by-clause)

- [Expressies voor samen voegen optimaliseren met behulp van een subquery](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Het opgehaalde document aantal is ongeveer gelijk aan het aantal uitvoer documenten

- [Kruis partitie query's voor komen](#avoid-cross-partition-queries)

- [Filters op meerdere eigenschappen](#filters-on-multiple-properties)

- [Query's met zowel een filter als een ORDER BY-component](#queries-with-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>De RU-kosten van de query zijn acceptabel, maar de latentie is nog te hoog

- [Proximity verhogen](#improve-proximity)

- [Ingerichte door Voer verhogen](#increase-provisioned-throughput)

- [MaxConcurrency verhogen](#increase-maxconcurrency)

- [MaxBufferedItemCount verhogen](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Query's waarbij het aantal opgehaalde documenten groter is dan het aantal uitvoer documenten

 Het opgehaalde document telt het aantal documenten dat door de query moet worden geladen. Het uitvoer document telt het aantal documenten dat nodig was voor de resultaten van de query. Als het aantal opgehaalde documenten aanzienlijk hoger is dan het aantal uitvoer documenten, was er ten minste één deel van de query die de index niet kan gebruiken en die nodig is om een scan uit te voeren.

 Hieronder ziet u een voor beeld van een scan query die niet volledig is verwerkt door de index.

Query:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Metrische gegevens van query:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Het opgehaalde document aantal (60.951) is aanzienlijk groter dan het aantal uitvoer documenten (7), zodat deze query nodig is om een scan uit te voeren. In dit geval gebruikt de systeem functie [Upper ()](sql-query-upper.md) niet de index.

## <a name="include-necessary-paths-in-the-indexing-policy"></a>Benodigde paden in het indexerings beleid toevoegen

Het indexerings beleid moet betrekking hebben op alle eigenschappen die zijn opgenomen in `WHERE`-componenten, `ORDER BY`-componenten, `JOIN`en de meeste systeem functies. Het pad dat is opgegeven in het index beleid moet overeenkomen (hoofdletter gevoelig) de eigenschap in de JSON-documenten.

Als we een eenvoudige query uitvoeren op de [voedings](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) gegevensset, zien we een veel lagere ru-kosten wanneer de eigenschap in de `WHERE`-component wordt geïndexeerd.

### <a name="original"></a>Origineel

Query:

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

Indexerings beleid:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Ru-kosten:** 409,51 ru

### <a name="optimized"></a>Geoptimaliseerd

Bijgewerkt indexerings beleid:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Ru-kosten:** 2,98 ru

U kunt op elk gewenst moment aanvullende eigenschappen toevoegen aan het indexerings beleid, zonder dat dit van invloed is op de schrijf Beschik baarheid of de prestaties. Als u een nieuwe eigenschap aan de index toevoegt, zullen query's die gebruikmaken van deze eigenschap onmiddellijk de nieuwe beschik bare index gebruiken. De query maakt gebruik van de nieuwe index terwijl deze wordt gebouwd. Als gevolg hiervan zijn de query resultaten mogelijk inconsistent, omdat het opnieuw maken van de index actief is. Als een nieuwe eigenschap is geïndexeerd, worden query's die alleen gebruikmaken van bestaande indexen, niet beïnvloed tijdens het opnieuw opbouwen van de index. U kunt de voortgang van de [index transformatie volgen](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

## <a name="understand-which-system-functions-utilize-the-index"></a>Begrijpen welke systeem functies de index gebruiken

Alleen als de expressie kan worden vertaald in een bereik met tekenreekswaarden, kan de expressie van de index gebruikmaken.

Hier volgt een lijst met tekenreeksfuncties die van de index gebruik kunnen maken:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0

Enkele veelvoorkomende systeem functies die de index niet gebruiken en moeten elk document laden:

| **Systeem functie**                     | **Ideeën voor optimalisatie**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Azure Search gebruiken voor zoeken in volledige tekst                        |
| BOVENSTE/ONDERSTE                             | In plaats van de systeem functie te gebruiken om gegevens elke keer voor vergelijkingen te normaliseren, moet u in plaats daarvan de behuizing normaliseren bij het invoegen. Vervolgens wordt een query zoals ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` gewoon ```SELECT * FROM c WHERE c.name = 'BOB'``` |
| Wiskundige functies (non-aggregaties) | Als u regel matig een waarde in uw query moet berekenen, kunt u overwegen om deze waarde op te slaan als een eigenschap in het JSON-document. |

------

Andere onderdelen van de query kunnen de index nog steeds gebruiken ondanks het systeem werkt niet met de index.

## <a name="queries-with-both-a-filter-and-an-order-by-clause"></a>Query's met zowel een filter als een ORDER BY-component

Hoewel query's met een filter en een `ORDER BY`-component normaal gesp roken gebruikmaken van een bereik index, zijn ze efficiënter als ze kunnen worden bediend vanuit een samengestelde index. Naast het wijzigen van het indexerings beleid, moet u alle eigenschappen in de samengestelde index toevoegen aan de component `ORDER BY`. Met deze gewijzigde query kunt u ervoor zorgen dat de samengestelde index wordt gebruikt.  U kunt het effect observeren door een query uit te voeren op de [voedings](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) gegevensset.

### <a name="original"></a>Origineel

Query:

```sql
SELECT * FROM c WHERE c.foodGroup = “Soups, Sauces, and Gravies” ORDER BY c._ts ASC
```

Indexerings beleid:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Ru-kosten:** 44,28 ru

### <a name="optimized"></a>Geoptimaliseerd

Bijgewerkte query (bevat beide eigenschappen in de `ORDER BY`-component):

```sql
SELECT * FROM c
WHERE c.foodGroup = “Soups, Sauces, and Gravies”
ORDER BY c.foodGroup, c._ts ASC
```

Bijgewerkt indexerings beleid:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Ru-kosten:** 8,86 ru

## <a name="optimize-join-expressions-by-using-a-subquery"></a>Expressies voor samen voegen optimaliseren met behulp van een subquery
Subquery's voor meerdere waarden kunnen `JOIN`-expressies optimaliseren door predikaten te pushen na elke Select-many-expressie in plaats van na alle kruis koppelingen in de `WHERE`-component.

Bekijk de volgende query:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Ru-kosten:** 167,62 ru

Voor deze query komt de index overeen met een document met een tag met de naam ' zuigeling Formula ', nutritionValue groter dan 0 en een bedrag dat groter is dan 1. Met de `JOIN`-expressie kunt u het cross-product van alle items met tags, nutriënten en matrices voor elk overeenstemmende document uitvoeren voordat een filter wordt toegepast. Met de component `WHERE` wordt vervolgens het filter predicaat toegepast op elke `<c, t, n, s>` tuple.

Als bijvoorbeeld een overeenkomend document 10 items in elk van de drie matrices had, wordt het uitgevouwen tot 1 x 10 x 10 x 10 (1.000) Tuples. U kunt subquerys hier gebruiken om gekoppelde matrix items te filteren voordat u met de volgende expressie samenvoegt.

Deze query is gelijk aan de voor gaande, maar gebruikt subquery's:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Ru-kosten:** 22,17 ru

Stel dat er slechts één item in de matrix Tags overeenkomt met het filter en dat er vijf items zijn voor beide nutriënten en dat er matrices worden gereserveerd. De `JOIN`-expressies worden vervolgens uitgebreid naar 1 x 1 x 5 x 5 = 25 items, in tegens telling tot 1.000 items in de eerste query.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Query's waarbij het aantal opgehaalde documenten gelijk is aan het aantal uitvoer documenten

Als het aantal opgehaalde documenten ongeveer gelijk is aan het aantal uitvoer documenten, betekent dit dat de query geen veel onnodige documenten moet scannen. Voor veel query's, zoals die waarin het sleutel woord TOP wordt gebruikt, kan het aantal opgehaalde documenten groter zijn dan het aantal uitvoer documenten door 1. Dit mag niet leiden tot bezorgdheid.

## <a name="avoid-cross-partition-queries"></a>Kruis partitie query's voor komen

Azure Cosmos DB maakt gebruik van [partitionering](partitioning-overview.md) om afzonderlijke containers te schalen als aanvraag eenheid en gegevens opslag moeten toenemen. Elke fysieke partitie heeft een afzonderlijke en onafhankelijke index. Als uw query een gelijkheids filter heeft dat overeenkomt met de partitie sleutel van uw container, hoeft u alleen de index van de relevante partitie te controleren. Deze optimalisatie vermindert het totale aantal RU-aanvragen dat de query vereist.

Als u een groot aantal ingerichte RU (meer dan 30.000) of een grote hoeveelheid opgeslagen gegevens (meer dan ~ 100 GB) hebt, hebt u waarschijnlijk een grote voldoende container om een aanzienlijke verlaging van de query-RU-kosten te zien.

Als we bijvoorbeeld een container met de partitie sleutel foodGroup maken, hoeven de volgende query's slechts één fysieke partitie te controleren:

```sql
SELECT * FROM c
WHERE c.foodGroup = “Soups, Sauces, and Gravies” and c.description = "Mushroom, oyster, raw"
```

Deze query's worden ook geoptimaliseerd door de partitie sleutel in de query op te nemen:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Query's die bereik filters hebben op de partitie sleutel of die geen filters hebben op de partitie sleutel, moeten ' uitwaaieren ' hebben en de index van elke fysieke partitie controleren op resultaten.

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > “Soups, Sauces, and Gravies” and c.description = "Mushroom, oyster, raw"
```

## <a name="filters-on-multiple-properties"></a>Filters op meerdere eigenschappen

Hoewel query's met filters voor meerdere eigenschappen normaal gesp roken gebruikmaken van een bereik index, zijn ze efficiënter als ze kunnen worden geleverd vanuit een samengestelde index. Voor kleine hoeveel heden gegevens heeft deze optimalisatie geen aanzienlijke gevolgen. Het kan echter nuttig zijn voor grote hoeveel heden gegevens. U kunt Maxi maal één niet-gelijkheids filter per samengestelde index optimaliseren. Als uw query meerdere filters heeft die niet gelijk zijn aan de gelijkheid, moet u een ervan kiezen waarmee de samengestelde index wordt gebruikt. Voor het restant blijven de bereik indexen worden gebruikt. Het niet-gelijkheids filter moet als laatste worden gedefinieerd in de samengestelde index. [Meer informatie over samengestelde indexen](index-policy.md#composite-indexes)

Hier volgen enkele voor beelden van query's die kunnen worden geoptimaliseerd met een samengestelde index:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Dit is de relevante samengestelde index:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Optimalisaties die de latentie van query's beperken:

In veel gevallen is het mogelijk dat de RU-kosten acceptabel zijn, maar dat de query latentie nog steeds te hoog is. De onderstaande secties geven een overzicht van tips voor het verminderen van de latentie van query's. Als u dezelfde query meerdere keren op dezelfde gegevensset uitvoert, heeft elke keer dezelfde RU-kosten. De query latentie kan echter verschillen tussen de uitvoering van query's.

## <a name="improve-proximity"></a>Proximity verhogen

Query's die worden uitgevoerd vanuit een andere regio dan het Azure Cosmos DB account, hebben een hogere latentie dan wanneer ze in dezelfde regio worden uitgevoerd. Als u bijvoorbeeld code uitvoert op uw desktop computer, verwacht u dat latentie tien tallen of honderden (of meer) milliseconden groter dan als de query afkomstig is van een virtuele machine in dezelfde Azure-regio als Azure Cosmos DB. Het is eenvoudig om [gegevens wereld wijd te distribueren in azure Cosmos DB](distribute-data-globally.md) om ervoor te zorgen dat uw gegevens dichter bij uw app kunnen worden gebracht.

## <a name="increase-provisioned-throughput"></a>Ingerichte door Voer verhogen

In Azure Cosmos DB wordt uw ingerichte door Voer gemeten in aanvraag eenheden (RU). Stel dat u een query hebt die 5 RU van de door Voer verbruikt. Als u bijvoorbeeld 1.000 RU inricht, kunt u die query 200 keer per seconde uitvoeren. Als u probeert de query uit te voeren terwijl er onvoldoende door Voer beschikbaar is, retourneert Azure Cosmos DB een HTTP 429-fout. Een van de huidige core-api's (SQL) API SDK voert deze query automatisch opnieuw uit nadat een korte periode is gewacht. Vertraagde aanvragen nemen langere tijd in beslag, waardoor het verhogen van de ingerichte door Voer de query latentie kan verbeteren. In de Blade metrische gegevens van de Azure Portal ziet u het [totale aantal](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) aanvragen dat wordt beperkt.

## <a name="increase-maxconcurrency"></a>MaxConcurrency verhogen

Parallelle query's werken door meerdere partities parallel te doorzoeken. Gegevens uit een afzonderlijke gepartitioneerde verzameling worden echter serieel opgehaald ten opzichte van de query. Daarom is het aanpassen van de MaxConcurrency aan het aantal partities de maximale kans om de meest uitvoering van de query te bereiken, op voor waarde dat alle andere systeem omstandigheden hetzelfde blijven. Als u het aantal partities niet weet, kunt u de MaxConcurrency (of MaxDegreesOfParallelism in oudere SDK-versies) instellen op een hoog nummer, en het systeem kiest het minimum (aantal partities, door de gebruiker opgegeven invoer) als de maximale graad van parallelle uitvoering.

## <a name="increase-maxbuffereditemcount"></a>MaxBufferedItemCount verhogen

Query's zijn ontworpen om resultaten vooraf op te halen terwijl de huidige batch met resultaten door de client wordt verwerkt. Het vooraf ophalen helpt bij de algehele latentie verbetering van een query. Het instellen van de MaxBufferedItemCount beperkt het aantal vooraf opgehaalde resultaten. Door deze waarde in te stellen op het verwachte aantal geretourneerde resultaten (of een hoger getal), kan de query het maximale voor deel ontvangen van vooraf ophalen. Als u deze waarde instelt op-1, kan het systeem automatisch het aantal items bepalen dat moet worden gebufferd.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de documenten hieronder over hoe u RUs per query kunt meten, uitvoerings statistieken kunt ophalen om uw query's af te stemmen en meer:

* [Metrische gegevens voor uitvoering van SQL-query's ophalen met .NET SDK](profile-sql-api-query.md)
* [Queryprestaties afstemmen met Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Tips voor betere prestaties voor de .NET-SDK](performance-tips.md)
