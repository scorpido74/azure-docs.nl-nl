---
title: Query-parallellisatie en schalen in Azure Stream Analytics gebruiken
description: In dit artikel wordt beschreven hoe u Stream Analytics-taken schalen door het configureren van invoer partities, afstemmen van de definitie van de query en het instellen van de taak streaming-eenheden.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: d1afb6037b5fc290de93faba405982ebd1fb68ea
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364553"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Maak gebruik van query-parallellisatie in Azure Stream Analytics
Dit artikel ziet u hoe u kunt profiteren van parallelle uitvoering in Azure Stream Analytics. Leert u hoe u Stream Analytics-taken schalen door invoer partities configureren en afstemmen van de definitie van de analytics-query.
U kunt het beste bekend zijn met het begrip van de streaming-eenheid die wordt beschreven in [begrijpen en streaming-eenheden aanpassen](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Wat zijn de onderdelen van een Stream Analytics-taak?
De definitie van een Stream Analytics-taak bevat invoer, een query- en uitvoer. Invoer is waar de taak leest de gegevensstroom uit. De query wordt gebruikt om te zetten van de invoerstroom van gegevens en de uitvoer is waar de taak de resultaten van de taak te verzenden.

Een taak moet ten minste één invoerbron voor het streamen van gegevens. De stream-invoerbron gegevens kan worden opgeslagen in een Azure event hub of Azure blob-opslag. Zie [Inleiding tot Azure stream Analytics](stream-analytics-introduction.md) en [aan de slag met Azure stream Analytics](stream-analytics-real-time-fraud-detection.md)voor meer informatie.

## <a name="partitions-in-sources-and-sinks"></a>Partities in de bronnen en sinks
Schalen van een Stream Analytics-taak maakt gebruik van partities in de invoer of uitvoer. Hiermee kunt die u gegevens in subsets op basis van een partitiesleutel onderverdelen partitioneren. Een proces waarbij de gegevens (zoals een stream Analytics-taak) kunt gebruiken en het schrijven van verschillende partities parallel verhoogt de doorvoer. 

### <a name="inputs"></a>Invoer
Alle Azure Stream Analytics-invoer kan profiteren van het partitioneren van:
-   Event hub (u moet de partitiesleutel expliciet met PARTITION BY trefwoord instellen)
-   IoT Hub (u moet de partitiesleutel expliciet met PARTITION BY trefwoord instellen)
-   Blob Storage

### <a name="outputs"></a>Uitvoer

Wanneer u met Stream Analytics werkt, kunt u profiteren van partitionering in de uitvoer:
-   Azure Data Lake Storage
-   Azure Functions
-   Azure Table
-   BLOB-opslag (kan de partitiesleutel expliciet ingesteld)
-   Cosmos DB (moet de partitie sleutel expliciet instellen)
-   Event Hubs (moet de partitie sleutel expliciet instellen)
-   IoT Hub (u moet expliciet de partitiesleutel instellen)
-   Service Bus
- SQL en SQL Data Warehouse met optionele partitionering: Zie voor meer informatie de [pagina uitvoer naar Azure SQL database](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

Power BI biedt geen ondersteuning voor partitioneren. U kunt echter nog steeds de invoer partitioneren zoals beschreven in [deze sectie](#multi-step-query-with-different-partition-by-values) 

Zie voor meer informatie over partities, de volgende artikelen:

* [Overzicht van functies van Event Hubs](../event-hubs/event-hubs-features.md#partitions)
* [Gegevenspartitionering](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Perfect parallelle taken
Een *verlegen parallelle* taak is het meest schaal bare scenario dat in azure stream Analytics. Deze verbinding één partitie van de invoer naar één exemplaar van de query maakt met één partitie van de uitvoer. Deze parallelle uitvoering heeft de volgende vereisten:

1. Als uw querylogica is afhankelijk van dezelfde sleutel door hetzelfde exemplaar van de query wordt verwerkt, moet u ervoor zorgen dat de gebeurtenissen gaat u naar dezelfde partitie van uw invoer. Voor Event Hubs of IoT Hub houdt dit in dat de waarde voor de gebeurtenis gegevens moet zijn ingesteld op **PartitionKey** . U kunt ook gepartitioneerde afzenders gebruiken. Voor blob storage betekent dit dat de gebeurtenissen worden verzonden naar de map met dezelfde partitie. Als uw querylogica dezelfde sleutel kan worden verwerkt door de dezelfde query-instantie niet vereist, kunt u deze vereiste negeren. Een voorbeeld van deze logica is een eenvoudige select-project-filter-query.  

2. Zodra de gegevens wordt weergegeven op de invoer-zijde, moet u ervoor zorgen dat uw query is gepartitioneerd. Hiervoor moet u in alle stappen **partitie** gebruiken. Meerdere stappen zijn toegestaan, maar ze allemaal moeten worden gepartitioneerd op basis van dezelfde sleutel. Onder compatibiliteits niveau 1,0 en 1,1 moet de partitie sleutel worden ingesteld op **PartitionId** zodat de taak volledig parallel is. Voor taken met Compatility niveau 1,2 en hoger kan een aangepaste kolom worden opgegeven als partitie sleutel in de invoer instellingen en de taak wordt automatisch paralellized, zelfs zonder de component PARTITION BY. Voor Event Hub uitvoer moet de eigenschap "partitie sleutel kolom" worden ingesteld op het gebruik van "PartitionId".

3. De meeste van onze uitvoer kunt profiteren van het partitioneren, maar als u een uitvoertype die biedt geen ondersteuning voor partitioneren uw taak geen volledig parallelle. Raadpleeg de [sectie uitvoer](#outputs) voor meer informatie.

4. Het aantal invoer partities moet gelijk zijn aan het aantal partities van de uitvoer. BLOB storage-uitvoer partities kan ondersteunen en neemt het partitieschema van de upstream-query. Wanneer een partitiesleutel voor Blob storage is opgegeven, gegevens zijn gepartitioneerd per invoerpartitie waardoor is het resultaat nog steeds volledig parallelle. Hier volgen enkele voorbeelden van waarden van partitie die een volledig parallelle taak toestaan:

   * 8 event hub-invoer partities en 8 event hub-partities uitvoer
   * 8 event hub-invoer partities en blob storage-uitvoer
   * 8 invoer event hub-partities en blob storage-uitvoer gepartitioneerd op basis van een aangepast veld met een willekeurige kardinaliteit
   * 8 blob storage-invoer partities en blob storage-uitvoer
   * 8 blob storage invoer partities en 8 event hub-uitvoer-partities

De volgende secties worden enkele voorbeeldscenario's die perfect parallelle zijn besproken.

### <a name="simple-query"></a>Eenvoudige query

* Invoer: Event hub met 8 partities
* Uitvoer: Event hub met 8 partities ("partitie sleutel kolom" moet worden ingesteld op het gebruik van "PartitionId")

Query:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Deze query is een eenvoudige filter. Daarom moet we geen zorgen over het partitioneren van de invoer die worden verzonden naar de event hub. Taken met een compatibiliteits niveau van vóór 1,2 moeten de component **Partition by PartitionId** bevatten, zodat de vereiste #2 van eerder is voldaan. Voor de uitvoer moeten we de Event Hub uitvoer in de taak configureren zodat de partitie sleutel is ingesteld op **PartitionId**. Er is één sinds laatste controle om ervoor te zorgen dat het aantal invoer partities gelijk aan het aantal partities van de uitvoer is.

### <a name="query-with-a-grouping-key"></a>Query's uitvoeren met een groeperingssleutel

* Invoer: Event hub met 8 partities
* De uitvoer: Blob storage

Query:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Deze query heeft een groeperingssleutel. Daarom moeten de gebeurtenissen gegroepeerd worden verzonden naar dezelfde Event Hub-partitie. Omdat in dit voorbeeld we op TollBoothID groeperen, moeten we ervoor dat TollBoothID als de partitiesleutel wordt gebruikt als de gebeurtenissen naar Event Hub worden verzonden worden. In ASA kunnen we **Partition by PartitionId** gebruiken om de eigenschappen van dit partitie schema over te nemen en volledige parallel Lise ring in te scha kelen. Omdat de uitvoer is een blob-opslag, moet we geen zorgen over het configureren van een waarde voor de partitiesleutel, aan de hand van vereiste #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Voor beeld van scenario's die *niet* verlegen parallel zijn

In de vorige sectie hebt gezien hoe sommige perfect parallelle scenario's. In deze sectie wordt besproken hoe scenario's die niet voldoen aan alle vereisten voor het perfect parallelle worden. 

### <a name="mismatched-partition-count"></a>Aantal niet-overeenkomende partities
* Invoer: Event hub met 8 partities
* De uitvoer: Event hub met 32 partities

In dit geval het maakt niet uit wat de query is. Als het aantal partities invoer komt niet overeen met het aantal uitvoer, de topologie is niet perfect parallel. + echter we nog steeds bepaalde niveau of parallellisering krijgen.

### <a name="query-using-non-partitioned-output"></a>Query's uitvoeren met behulp van niet-gepartitioneerde uitvoer
* Invoer: Event hub met 8 partities
* Uitvoer: Power BI

Power BI-uitvoer biedt momenteel geen ondersteuning voor partitioneren. In dit scenario is daarom niet perfect parallelle.

### <a name="multi-step-query-with-different-partition-by-values"></a>WebTest met meerdere stappen query's uitvoeren met verschillende waarden PARTITION BY
* Invoer: Event hub met 8 partities
* De uitvoer: Event hub met 8 partities

Query:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Zoals u kunt zien, gebruikt de tweede stap **TollBoothId** als de partitie sleutel. Deze stap is niet hetzelfde als de eerste stap en daarom moeten we doen een willekeurige volgorde. 

In de voorgaande voorbeelden sommige Stream Analytics-taken die voldoen aan (of niet) op een perfect parallelle topologie. Als die in overeenstemming zijn, hebben ze de mogelijkheden voor maximale schaal. Voor taken die niet zijn voor een van deze profielen geschikt, schalen richtlijnen beschikbaar in toekomstige zijn updates. Gebruik nu de algemene richtlijnen in de volgende secties.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Compatibiliteits niveau 1,2: query's met meerdere stappen met verschillende partities per waarde 
* Invoer: Event hub met 8 partities
* Uitvoer: Event hub met 8 partities ("partitie sleutel kolom" moet worden ingesteld op het gebruik van "TollBoothId")

Query:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Compatibiliteits niveau 1,2 maakt standaard parallelle uitvoering van query's mogelijk. Query uit de vorige sectie wordt bijvoorbeeld parttioned, zolang de kolom ' TollBoothId ' is ingesteld als de invoer partitie sleutel. De component PARTITION BY ParttionId is niet vereist.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>De maximale streaming-eenheden van een taak berekenen
Het totale aantal streamingeenheden dat kan worden gebruikt door een Stream Analytics-taak is afhankelijk van het aantal stappen in de query die is gedefinieerd voor de taak en het aantal partities voor elke stap.

### <a name="steps-in-a-query"></a>Stappen in een query
Een query kan een of meer stappen bevatten. Elke stap is een subquery die is gedefinieerd met het sleutel woord **with** . De query die zich buiten het sleutel woord **with** bevindt (één query alleen), telt ook als een stap, zoals de **Select** -instructie in de volgende query:

Query:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId
```

Deze query bestaat uit twee stappen.

> [!NOTE]
> Deze query wordt later in het artikel in meer detail besproken.
>  

### <a name="partition-a-step"></a>Een stap partitioneren
Een stap partitioneren, moet de volgende voorwaarden:

* De invoerbron moet worden gepartitioneerd. 
* De instructie **Select** van de query moet van een gepartitioneerde invoer bron worden gelezen.
* De query in de stap moet het sleutel woord **Partition by** hebben.

Wanneer een query is gepartitioneerd, de invoer gebeurtenissen worden verwerkt en samengevoegd in afzonderlijke partitiegroepen en uitvoer gebeurtenissen worden gegenereerd voor elk van de groepen. Als u wilt dat een gecombineerde statistische functie, moet u een tweede stap van niet-gepartitioneerde maken om samen te voegen.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Het maximum aantal streaming-eenheden voor een taak berekenen
Alle niet-gepartitioneerde stappen kunnen samen worden geschaald tot zes streaming-eenheden (su's) voor een Stream Analytics-taak. Daarnaast kunt u 6 su's voor elke partitie in een gepartitioneerde stap toevoegen.
In de onderstaande tabel ziet u enkele **voor beelden** .

| Query's uitvoeren                                               | Max su's voor de taak |
| --------------------------------------------------- | ------------------- |
| <ul><li>De query bevat één stap.</li><li>De stap niet is gepartitioneerd.</li></ul> | 6 |
| <ul><li>De invoer van de gegevensstroom is gepartitioneerd op basis van 16.</li><li>De query bevat één stap.</li><li>De stap is gepartitioneerd.</li></ul> | 96 (6 * 16 partities) |
| <ul><li>De query bevat twee stappen.</li><li>Geen van de stappen is gepartitioneerd.</li></ul> | 6 |
| <ul><li>De stroom inkomende gegevens zijn gepartitioneerd met 3.</li><li>De query bevat twee stappen. De invoer stap is gepartitioneerd en de tweede stap is het niet.</li><li>De <strong>Select</strong> -instructie leest uit de gepartitioneerde invoer.</li></ul> | 24 uur per dag (18 voor gepartitioneerde stappen + 6 voor niet-gepartitioneerde stappen |

### <a name="examples-of-scaling"></a>Voorbeelden van schaal

De volgende query berekent het aantal auto's binnen een drie minuten een gratis-station met drie tollbooths te doorlopen. Deze query kan worden geschaald tot maximaal zes su's.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Voor het gebruik van meer su's voor de query, moeten zowel de invoer gegevensstroom als de query worden gepartitioneerd. Omdat de partitie van de stroom is ingesteld op 3, kan de volgende gewijzigde query worden geschaald tot maximaal 18 su's:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Wanneer een query is gepartitioneerd, worden de invoergebeurtenissen verwerkt en samengevoegd in een afzonderlijke partitiegroepen. Uitvoergebeurtenissen worden ook gegenereerd voor elk van de groepen. Partitioneren kan onverwachte resultaten veroorzaken wanneer het veld **groeperen op** niet de partitie sleutel in de gegevens stroom voor invoer is. Het veld **TollBoothId** in de vorige query is bijvoorbeeld niet de partitie sleutel van **input1**. Het resultaat is dat de gegevens van stencil #1 in meerdere partities kunnen worden verspreid.

Elk van de **input1** -partities wordt afzonderlijk verwerkt door stream Analytics. Als gevolg hiervan wordt meerdere records van het aantal auto's voor de dezelfde stencil in de dezelfde tumblingvenster gemaakt. Als de invoer van de partitiesleutel kan niet worden gewijzigd, kan dit probleem worden opgelost door een niet-partitie stap toe te voegen aan samengevoegde waarden over meerdere partities, zoals in het volgende voorbeeld:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Deze query kan worden geschaald tot 24 su's.

> [!NOTE]
> Als u van twee streams samenvoegen, ervoor zorgen dat de stromen worden gepartitioneerd op basis van de partitiesleutel van de kolom die u kunt de joins maken. Zorg ook dat u hetzelfde aantal partities in beide stromen hebben.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Hoge door Voer op schaal bereiken

Een [verlegen parallelle](#embarrassingly-parallel-jobs) taak is nodig, maar niet voldoende voor een hogere door Voer op schaal. Elk opslag systeem en de bijbehorende Stream Analytics uitvoer hebben afwijkingen voor het behalen van de best mogelijke schrijf doorvoer. Net als bij een schaal bare scenario zijn er enkele uitdagingen die kunnen worden opgelost met de juiste configuraties. In deze sectie worden de configuraties voor enkele veelvoorkomende uitvoer beschreven en worden voor beelden geboden om de opname snelheid van 1K, 5K en 10K-gebeurtenissen per seconde te ondervangen.

De volgende opmerkingen gebruiken een Stream Analytics-taak met stateless (passthrough) query, een eenvoudige Java script-UDF die schrijft naar Event hub, Azure SQL DB of Cosmos DB.

#### <a name="event-hub"></a>Event Hub

|Opname frequentie (gebeurtenissen per seconde) | Streaming-eenheden | Uitvoer resources  |
|--------|---------|---------|
| 1     |    1    |  2 DI   |
| 5K     |    6    |  6 DI   |
| 10K    |    12   |  10 DI  |

De [Event hub](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) -oplossing schaalt lineair in termen van streaming-eenheden (su) en door Voer, waardoor het de meest efficiënte en beste manier is om gegevens uit stream Analytics te analyseren en te streamen. Taken kunnen tot 192 SU worden geschaald, wat ongeveer vertaalt om tot 200 MB/s of 19.000.000.000.000 gebeurtenissen per dag te verwerken.

#### <a name="azure-sql"></a>Azure SQL
|Opname frequentie (gebeurtenissen per seconde) | Streaming-eenheden | Uitvoer resources  |
|---------|------|-------|
|    1   |   3  |  S3   |
|    5K   |   18 |  P4   |
|    10K  |   36 |  P6   |

[Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) biedt ondersteuning voor het parallel schrijven, het overnemen van partitionering, maar is niet standaard ingeschakeld. Het inschakelen van partitionering, samen met een volledig parallelle query, kan echter niet voldoende zijn voor hogere door voer. Het schrijven van SQL-door Voer is aanzienlijk afhankelijk van uw SQL Azure database configuratie en tabel schema. Het artikel over [SQL-uitvoer prestaties](./stream-analytics-sql-output-perf.md) bevat meer details over de para meters die uw schrijf doorvoer kunnen maximaliseren. Zoals vermeld in de [Azure stream Analytics uitvoer naar Azure SQL database](./stream-analytics-sql-output-perf.md#azure-stream-analytics) artikel, wordt deze oplossing niet lineair geschaald als een volledig parallelle pijp lijn van meer dan 8 partities en moet deze mogelijk opnieuw worden gepartitioneerd vóór SQL-uitvoer [(zie)](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count). Premium-Sku's zijn vereist om te voorzien in hoge i/o-tarieven, samen met de overhead van logboek back-ups die om de paar minuten worden uitgevoerd.

#### <a name="cosmos-db"></a>Cosmos DB
|Opname frequentie (gebeurtenissen per seconde) | Streaming-eenheden | Uitvoer resources  |
|-------|-------|---------|
|  1   |  3    | 20.000 RU  |
|  5K   |  24   | 60K RU  |
|  10K  |  48   | 120K RU |

[Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) uitvoer van stream Analytics is bijgewerkt voor het gebruik van systeem eigen integratie onder [compatibiliteits niveau 1,2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Compatibiliteits niveau 1,2 maakt aanzienlijk hogere door Voer en vermindert het gebruik van RU in vergelijking met 1,1. Dit is het standaard compatibiliteits niveau voor nieuwe taken. De oplossing maakt gebruik van CosmosDB-containers die zijn gepartitioneerd op/deviceId en de rest van de oplossing is identiek geconfigureerd.

Alle [streaming bij Azure-voor beelden van gegevens stromen](https://github.com/Azure-Samples/streaming-at-scale) gebruiken een event hub die wordt gevoed door test clients te simuleren als invoer. Elke invoer gebeurtenis is een 1 KB JSON-document, waarmee geconfigureerde opname tarieven worden omgezet in doorvoer tarieven (1 MB/s, 5 MB/s en 10 MB/s). Gebeurtenissen simuleren een IoT-apparaat dat de volgende JSON-gegevens verzendt (in een Inge kort formulier) voor Maxi maal 1K apparaten:

```
{
    "eventId": "b81d241f-5187-40b0-ab2a-940faf9757c0",
    "complexData": {
        "moreData0": 51.3068118685458,
        "moreData22": 45.34076957651598
    },
    "value": 49.02278128887753,
    "deviceId": "contoso://device-id-1554",
    "type": "CO2",
    "createdAt": "2019-05-16T17:16:40.000003Z"
}
```

> [!NOTE]
> De configuraties kunnen worden gewijzigd als gevolg van de verschillende onderdelen die in de oplossing worden gebruikt. Voor een nauw keuriger schatting past u de voor beelden aan uw scenario aan.

### <a name="identifying-bottlenecks"></a>Knel punten identificeren

Gebruik het deel venster metrieken in uw Azure Stream Analytics-taak om knel punten in uw pijp lijn te identificeren. Bekijk **invoer-en uitvoer gebeurtenissen** voor door Voer en [' watermerk vertraging '](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) of achterstands **gebeurtenissen** om te zien of de taak de invoer snelheid bijhoudt. Voor metrische gegevens van Event hub zoekt u naar **vertraagde aanvragen** en past u de drempel waarden dienovereenkomstig aan. Bekijk voor Cosmos DB metrische gegevens het **maximum aantal gebruikte ru/s per partitie sleutel bereik** onder door Voer om ervoor te zorgen dat de partitie sleutel bereik op gelijke wijze worden verbruikt. Bewaak **logboek-io** en **CPU**voor Azure SQL DB.

## <a name="get-help"></a>Help opvragen

Probeer het [Azure stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

