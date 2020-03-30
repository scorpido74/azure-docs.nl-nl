---
title: Problemen met query's oplossen bij het gebruik van Azure Cosmos DB
description: Meer informatie over het identificeren, diagnosticeren en oplossen van Azure Cosmos DB SQL-queryproblemen.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 852ed8c49eda7f13542eb0bad63d84e1cf770e92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131384"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Problemen met query's oplossen bij het gebruik van Azure Cosmos DB

In dit artikel wordt een algemene aanbevolen aanpak gevolgd voor het oplossen van problemen met query's in Azure Cosmos DB. Hoewel u de stappen in dit artikel niet als een volledige verdediging tegen potentiële queryproblemen moet beschouwen, hebben we hier de meest voorkomende prestatietips opgenomen. U moet dit artikel gebruiken als startplaats voor het oplossen van problemen met langzame of dure query's in de SQL-api (Azure Cosmos DB Core). U ook [diagnostische logboeken](cosmosdb-monitor-resource-logs.md) gebruiken om query's te identificeren die traag zijn of die aanzienlijke hoeveelheden doorvoer verbruiken.

U queryoptimalisaties in Azure Cosmos DB in grote lijnen categoriseren: 

- Optimalisaties die de kosten van de query (Request Unit) verlagen
- Optimalisaties die alleen de latentie verminderen

Als u de RU-kosten van een query vermindert, zult u vrijwel zeker ook de latentie verlagen.

In dit artikel vindt u voorbeelden die [nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) u opnieuw maken met behulp van de voedingsgegevensset.

## <a name="important"></a>Belangrijk

- Volg de [prestatietips voor](performance-tips.md)de beste prestaties.
    > [!NOTE]
    > Voor betere prestaties raden we windows 64-bits hostverwerking aan. De SQL SDK bevat een native ServiceInterop.dll om query's lokaal te ontlopen en te optimaliseren. ServiceInterop.dll wordt alleen ondersteund op het Windows x64-platform. Voor Linux en andere niet-ondersteunde platforms waar ServiceInterop.dll niet beschikbaar is, wordt een extra netwerkoproep naar de gateway gemaakt om de geoptimaliseerde query te krijgen.
- Azure Cosmos DB-query's ondersteunen geen minimum aantal items.
    - Code moet elk paginaformaat verwerken, van nul tot het maximale aantal items.
    - Het aantal items op een pagina kan en zal zonder kennisgeving veranderen.
- Lege pagina's worden verwacht voor query's en kunnen op elk gewenst moment worden weergegeven.
    - Lege pagina's worden weergegeven in de SDK's, omdat die belichting meer mogelijkheden biedt om een query te annuleren. Het maakt ook duidelijk dat de SDK meerdere netwerkgesprekken voert.
    - Lege pagina's kunnen worden weergegeven in bestaande workloads omdat een fysieke partitie is gesplitst in Azure Cosmos DB. De eerste partitie heeft nul resultaten, waardoor de lege pagina.
    - Lege pagina's worden veroorzaakt door dat de backend een query voorrang krijgt, omdat de query meer dan een vaste hoeveelheid tijd op de backend in beslag neemt om de documenten op te halen. Als Azure Cosmos DB een query voorloopt, wordt een vervolgtoken weergegeven waarmee de query kan worden voortgezet.
- Zorg ervoor dat u de query volledig afvoer. Kijk naar de SDK monsters, en gebruik een `while` lus op `FeedIterator.HasMoreResults` om de hele query drain.

## <a name="get-query-metrics"></a>Querystatistieken opvragen

Wanneer u een query optimaliseert in Azure Cosmos DB, is de eerste stap altijd om [de querystatistieken](profile-sql-api-query.md) voor uw query te krijgen. Deze statistieken zijn ook beschikbaar via de Azure-portal:

[![Querystatistieken](./media/troubleshoot-query-performance/obtain-query-metrics.png) weergeven](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

Nadat u de querystatistieken hebt opgehaald, vergelijkt u het aantal opgehaalde documenten met het aantal uitvoerdocumenten voor uw query. Gebruik deze vergelijking om de relevante secties te identificeren die in dit artikel moeten worden bekeken.

Het aantal opgehaalde documenten is het aantal documenten dat de query moet laden. Het aantal uitvoerdocumenten is het aantal documenten dat nodig was voor de resultaten van de query. Als het aantal opgehaalde documenten aanzienlijk hoger is dan het aantal uitvoerdocumenten, was er ten minste één deel van uw query dat de index niet kon gebruiken en een scan moest uitvoeren.

Raadpleeg de volgende secties om inzicht te krijgen in de relevante queryoptimalisaties voor uw scenario.

### <a name="querys-ru-charge-is-too-high"></a>De RU-kosten van query's zijn te hoog

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Opgehaald documentaantal is aanzienlijk hoger dan het aantal uitvoerdocumenten

- [Neem de benodigde paden op in het indexeringsbeleid.](#include-necessary-paths-in-the-indexing-policy)

- [Begrijpen welke systeemfuncties de index gebruiken.](#understand-which-system-functions-use-the-index)

- [Query's wijzigen met zowel een filter als een order-door-clausule.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Optimaliseer JOIN-expressies met behulp van een subquery.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Opgehaald documentaantal is ongeveer gelijk aan het aantal uitvoerdocument

- [Vermijd kruispartitiequery's.](#avoid-cross-partition-queries)

- [Query's optimaliseren met filters voor meerdere eigenschappen.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Query's wijzigen met zowel een filter als een order-door-clausule.](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>De RU-kosten van query zijn acceptabel, maar de latentie is nog steeds te hoog

- [Verbeter de nabijheid.](#improve-proximity)

- [Verhoog de ingerichte doorvoer.](#increase-provisioned-throughput)

- [Verhoog MaxConcurrency.](#increase-maxconcurrency)

- [Maximaalgebufferd itemaantal verhogen.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Query's waarbij opgehaald documentaantal het aantal uitvoerdocumenten overschrijdt

 Het aantal opgehaalde documenten is het aantal documenten dat de query moet laden. Het aantal uitvoerdocumenten is het aantal documenten dat nodig was voor de resultaten van de query. Als het aantal opgehaalde documenten aanzienlijk hoger is dan het aantal uitvoerdocumenten, was er ten minste één deel van uw query dat de index niet kon gebruiken en een scan moest uitvoeren.

Hier is een voorbeeld van scanquery's die niet volledig door de index is weergegeven:

Query:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Querystatistieken:

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

Het aantal opgehaalde documenten (60.951) is aanzienlijk hoger dan het aantal uitvoerdocumenten (7), dus deze query moest een scan uitvoeren. In dit geval gebruikt de systeemfunctie [UPPER()](sql-query-upper.md) de index niet.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>De benodigde paden opnemen in het indexeringsbeleid

Uw indexeringsbeleid moet betrekking `WHERE` hebben `ORDER BY` op `JOIN`alle eigenschappen die zijn opgenomen in clausules, clausules en de meeste systeemfuncties. Het pad dat in het indexbeleid is opgegeven, moet overeenkomen met (hoofdlettergevoelig) van de eigenschap in de JSON-documenten.

Als u een eenvoudige query uitvoert op de [voedingsgegevensset,](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) ziet `WHERE` u een veel lagere RU-kosten wanneer de eigenschap in de clausule wordt geïndexeerd:

#### <a name="original"></a>Origineel

Query:

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

Indexeringsbeleid:

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

**RU-kosten:** 409,51 RU's

#### <a name="optimized"></a>Geoptimaliseerd

Bijgewerkt indexeringsbeleid:

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

**RU-kosten:** 2,98 RU's

U op elk gewenst moment eigenschappen toevoegen aan het indexeringsbeleid, zonder dat dit gevolgen heeft voor de beschikbaarheid of prestaties van de schrijftekst. Als u een nieuwe eigenschap aan de index toevoegt, worden met query's die de eigenschap gebruiken onmiddellijk de nieuwe beschikbare index gebruikt. De query gebruikt de nieuwe index tijdens de bouw. Queryresultaten kunnen dus inconsistent zijn terwijl de index wordt hersteld. Als een nieuwe eigenschap wordt geïndexeerd, worden query's die alleen bestaande indexen gebruiken, niet beïnvloed tijdens het opnieuw opbouwen van de index. U [de voortgang van de indextransformatie bijhouden](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3).

### <a name="understand-which-system-functions-use-the-index"></a>Begrijpen welke systeemfuncties de index gebruiken

Als een expressie kan worden vertaald in een reeks tekenreekswaarden, kan deze de index gebruiken. Anders kan het niet.

Hier is de lijst met tekenreeksfuncties die de index kunnen gebruiken:

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr, maar alleen als de eerste num_expr 0 is

Hieronder volgen enkele veelvoorkomende systeemfuncties die de index niet gebruiken en elk document moeten laden:

| **Systeemfunctie**                     | **Ideeën voor optimalisatie**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | Gebruik Azure Search voor zoeken met volledige tekst.                        |
| BOVEN/ONDER                             | In plaats van de systeemfunctie te gebruiken om gegevens te normaliseren voor vergelijkingen, normaliseer tubijt u de behuizing bij het inbrengen. Een query ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` ```SELECT * FROM c WHERE c.name = 'BOB'```als wordt . |
| Wiskundige functies (niet-aggregaten) | Als u een waarde vaak in uw query moet berekenen, u overwegen de waarde als eigenschap op te slaan in uw JSON-document. |

------

Andere delen van de query kunnen de index nog steeds gebruiken, ook al zijn de systeemfuncties dat niet.

### <a name="modify-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Query's wijzigen met zowel een filter als een order-door-clausule

Hoewel query's met een `ORDER BY` filter en een clausule normaal gesproken een bereikindex gebruiken, zijn ze efficiënter als ze kunnen worden bediend vanuit een samengestelde index. Naast het wijzigen van het indexeringsbeleid, moet u alle `ORDER BY` eigenschappen in de samengestelde index aan de component toevoegen. Deze wijziging in de query zorgt ervoor dat de samengestelde index wordt gebruikt.  U de impact waarnemen [nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) door een query uit te voeren op de voedingsgegevensset:

#### <a name="original"></a>Origineel

Query:

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

Indexeringsbeleid:

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

**RU-kosten:** 44,28 RU's

#### <a name="optimized"></a>Geoptimaliseerd

Bijgewerkte query (bevat beide `ORDER BY` eigenschappen in de clausule):

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Bijgewerkt indexeringsbeleid:

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

**RU-kosten:** 8,86 RU's

### <a name="optimize-join-expressions-by-using-a-subquery"></a>JOIN-expressies optimaliseren met behulp van een subquery
Subquery's met meerdere `JOIN` waarden kunnen expressies optimaliseren door na elke select-many `WHERE` expressie op basis van selectie te drukken in plaats van dat alle kruisteksten in de clausule worden opgenomen.

Houd rekening met deze query:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**RU-lading:** 167,62 RU's

Voor deze query komt de index overeen met elk document met een tag met de naam 'zuigelingenvoeding', voedingWaarde groter dan 0 en een weergavebedrag groter dan 1. De `JOIN` expressie hier voert het cross-product uit van alle items van tags, voedingsstoffen en serveerarrays voor elk overeenkomend document voordat een filter wordt toegepast. De `WHERE` clausule past dan het `<c, t, n, s>` filterpredicaat toe op elke tuple.

Als een overeenkomend document bijvoorbeeld 10 items in elk van de drie arrays heeft, worden de tuples uitgebreid tot 1 x 10 x 10 x 10 (dat wil zeggen 1.000). Het gebruik van subquery's hier kan helpen om samengevoegde arrayitems uit te filteren voordat u aansluit bij de volgende expressie.

Deze query is gelijk aan de vorige query's, maar gebruikt subquery's:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**RU-kosten:** 22,17 RU's

Stel dat slechts één item in de array tags overeenkomt met het filter en dat er vijf items zijn voor zowel de arrays met voedingsstoffen als voor porties. De `JOIN` expressies worden uitgebreid tot 1 x 1 x 5 x 5 = 25 items, in tegenstelling tot 1.000 items in de eerste query.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Query's waarbij opgehaald documentaantal gelijk is aan het aantal uitvoerdocumenten

Als het aantal opgehaalde documenten ongeveer gelijk is aan het aantal uitvoerdocumenten, hoeft de query niet veel onnodige documenten te scannen. Voor veel query's, zoals query's die het trefwoord TOP gebruiken, kan het aantal opgehaalde documenten met 1 groter zijn. Je hoeft je hier geen zorgen over te maken.

### <a name="avoid-cross-partition-queries"></a>Kruispartitiequery's vermijden

Azure Cosmos DB gebruikt [partitionering](partitioning-overview.md) om afzonderlijke containers te schalen naarmate de behoeften aan aanvraageenheden en de behoeften aan gegevensopslag toenemen. Elke fysieke partitie heeft een aparte en onafhankelijke index. Als uw query een gelijkheidsfilter heeft dat overeenkomt met de partitiesleutel van uw container, moet u alleen de index van de betreffende partitie controleren. Deze optimalisatie vermindert het totale aantal RU's dat de query nodig heeft.

Als u een groot aantal ingerichte RU's (meer dan 30.000) of een grote hoeveelheid opgeslagen gegevens hebt (meer dan ongeveer 100 GB), hebt u waarschijnlijk een container die groot genoeg is om een aanzienlijke vermindering van de queryRU-kosten te zien.

Als u bijvoorbeeld een container maakt met de partitiesleutel foodGroup, hoeven de volgende query's slechts één fysieke partitie te controleren:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Deze query's worden ook geoptimaliseerd door de toevoeging van de partitiesleutel in de query:

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Query's met bereikfilters op de partitiesleutel of die geen filters op de partitiesleutel hebben, moeten de index van elke fysieke partitie controleren op resultaten:

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Query's optimaliseren met filters op meerdere eigenschappen

Hoewel query's met filters op meerdere eigenschappen normaal gesproken een bereikindex gebruiken, zijn ze efficiënter als ze kunnen worden weergegeven vanuit een samengestelde index. Voor kleine hoeveelheden gegevens heeft deze optimalisatie geen significante impact. Het kan echter nuttig zijn voor grote hoeveelheden gegevens. U hoogstens één filter zonder gelijkheid per samengestelde index optimaliseren. Als uw query meerdere filters voor niet-gelijkheid heeft, kiest u een van deze filters die de samengestelde index gebruiken. De rest blijft bereikindexen gebruiken. Het filter voor niet-gelijkheid moet als laatste in de samengestelde index worden gedefinieerd. [Meer informatie over samengestelde indexen](index-policy.md#composite-indexes).

Hier volgen enkele voorbeelden van query's die kunnen worden geoptimaliseerd met een samengestelde index:

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Hier is de relevante samengestelde index:

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

## <a name="optimizations-that-reduce-query-latency"></a>Optimalisaties die de latentie van query's verminderen

In veel gevallen kan de RU-kosten aanvaardbaar zijn wanneer de querylatentie nog steeds te hoog is. In de volgende secties vindt u een overzicht van tips voor het verminderen van de latentie van query's. Als u dezelfde query meerdere keren uitvoert op dezelfde gegevensset, worden elke keer dezelfde RU-kosten in rekening gebracht. De querylatentie kan echter variëren tussen query-uitvoeringen.

### <a name="improve-proximity"></a>De nabijheid verbeteren

Query's die vanuit een andere regio worden uitgevoerd dan het Azure Cosmos DB-account, hebben een hogere latentie dan wanneer ze in dezelfde regio worden uitgevoerd. Als u bijvoorbeeld code op uw desktopcomputer uitvoert, moet u verwachten dat de latentie tientallen of honderden milliseconden hoger (of meer) is dan wanneer de query afkomstig is van een virtuele machine binnen dezelfde Azure-regio als Azure Cosmos DB. Het is eenvoudig om gegevens wereldwijd te [distribueren in Azure Cosmos DB](distribute-data-globally.md) om ervoor te zorgen dat u uw gegevens dichter bij uw app brengen.

### <a name="increase-provisioned-throughput"></a>Verhoging van de ingerichte doorvoer

In Azure Cosmos DB wordt uw ingerichte doorvoer gemeten in Request Units (RU's). Stel je hebt een query die 5 R's doorvoer verbruikt. Als u bijvoorbeeld 1.000 RU's indient, u die query 200 keer per seconde uitvoeren. Als u de query probeerde uit te voeren wanneer er niet genoeg doorvoer beschikbaar was, zou Azure Cosmos DB een HTTP 429-fout retourneren. Een van de huidige Core (SQL) API SDKs zal deze query automatisch opnieuw proberen na het wachten voor een korte tijd. Throttled-aanvragen duren langer, waardoor een toenemende ingerichte doorvoer de latentie van query's kan verbeteren. U het [totale aantal geweigerde aanvragen](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) observeren op het blade **metrics** van de Azure-portal.

### <a name="increase-maxconcurrency"></a>MaxConcurrency verhogen

Parallelle query's werken door meerdere partities parallel op te vragen. Maar gegevens uit een afzonderlijke partitieverzameling worden serieel opgehaald met betrekking tot de query. Dus, als u MaxConcurrency instelt op het aantal partities, hebt u de beste kans op het bereiken van de meest performante query, op voorwaarde dat alle andere systeemomstandigheden hetzelfde blijven. Als u het aantal partities niet kent, u MaxConcurrency (of MaxDegreesOfParallelism in oudere SDK-versies) instellen op een hoog aantal. Het systeem kiest het minimum (aantal partities, door de gebruiker geleverde input) als de maximale mate van parallellisme.

### <a name="increase-maxbuffereditemcount"></a>MaxBufferedItemCount verhogen

Query's zijn ontworpen om resultaten vooraf op te halen terwijl de huidige batch met resultaten wordt verwerkt door de client. Vooraf ophalen helpt om de algehele latentie van een query te verbeteren. Als u MaxBufferedItemCount instelt, wordt het aantal vooraf opgehaalde resultaten beperkt. Als u deze waarde instelt op het verwachte aantal geretourneerde resultaten (of een hoger aantal), kan de query het meeste voordeel halen uit vooraf ophalen. Als u deze waarde instelt op -1, bepaalt het systeem automatisch het aantal items dat moet worden gebufferd.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor informatie over het meten van INFO per query, uitvoeringsstatistieken om uw query's af te stemmen en meer:

* [Sql-queryuitvoeringsstatistieken downloaden met .NET SDK](profile-sql-api-query.md)
* [Queryprestaties afstemmen met Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Tips voor betere prestaties voor de .NET-SDK](performance-tips.md)
