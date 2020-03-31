---
title: Queryparallelisatie en schaal gebruiken in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u Stream Analytics-taken schaalt door invoerpartities te configureren, de querydefinitie af te stemmen en taakstreamingeenheden in te stellen.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 31ac43ec796d305b8a8f4b62ea09481e262b6b3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256977"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Gebruik maken van queryparallelisatie in Azure Stream Analytics
In dit artikel ziet u hoe u profiteren van parallellen in Azure Stream Analytics. U leert hoe u Stream Analytics-taken schalen door invoerpartities te configureren en de definitie van analysequery's af te stemmen.
Als voorwaarde wilt u misschien vertrouwd zijn met het begrip Streaming Unit beschreven in [Begrijpen en aanpassen van streaming eenheden.](stream-analytics-streaming-unit-consumption.md)

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Wat zijn de onderdelen van een Stream Analytics-taak?
Een functiedefinitie van Stream Analytics bevat invoer, een query en uitvoer. Invoer is waar de taak de gegevensstroom van leest. De query wordt gebruikt om de gegevensinvoerstroom te transformeren en de uitvoer is waar de taak de taakresultaten naartoe stuurt.

Voor een taak is ten minste één invoerbron vereist voor het streamen van gegevens. De invoerbron voor gegevensstromen kan worden opgeslagen in een Azure-gebeurtenishub of in Azure blob-opslag. Zie [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md) en Aan de slag met Azure Stream [Analytics](stream-analytics-real-time-fraud-detection.md)voor meer informatie.

## <a name="partitions-in-sources-and-sinks"></a>Partities in bronnen en putten
Het schalen van een Stream Analytics-taak maakt gebruik van partities in de invoer of uitvoer. Met partitionering u gegevens verdelen in subsets op basis van een partitiesleutel. Een proces dat de gegevens verbruikt (zoals een streaming analytics-taak) kan verschillende partities parallel verbruiken en schrijven, waardoor de doorvoer toeneemt. 

### <a name="inputs"></a>Invoer
Alle Azure Stream Analytics-invoer kan profiteren van partitionering:
-   EventHub (noodzaak om de partitiesleutel expliciet in te stellen met partitie per trefwoord)
-   IoT-hub (moet de partitiesleutel expliciet instellen met partitie per trefwoord)
-   Blob Storage

### <a name="outputs"></a>Uitvoer

Wanneer u met Stream Analytics werkt, u profiteren van partitionering in de uitvoer:
-   Azure Data Lake Storage
-   Azure Functions
-   Azure Table
-   Blob-opslag (kan de partitiesleutel expliciet instellen)
-   Cosmos DB (noodzaak om de partitiesleutel expliciet in te stellen)
-   Gebeurtenishubs (de partitiesleutel expliciet instellen)
-   IoT-hub (de partitiesleutel expliciet instellen)
-   Service Bus
- SQL en SQL Data Warehouse met optionele partitionering: zie meer informatie over de [pagina Uitvoer naar Azure SQL Database](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

Power BI biedt geen ondersteuning voor partitionering. U de invoer echter nog steeds verdelen zoals beschreven in [deze sectie](#multi-step-query-with-different-partition-by-values) 

Zie de volgende artikelen voor meer informatie over partities:

* [Overzicht van functies van Event Hubs](../event-hubs/event-hubs-features.md#partitions)
* [Gegevenspartitionering](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Beschamend parallelle banen
Een *beschamend parallelle* taak is het meest schaalbare scenario dat we hebben in Azure Stream Analytics. Het verbindt één partitie van de invoer met één instantie van de query met één partitie van de uitvoer. Dit parallellisme heeft de volgende eisen:

1. Als uw querylogica afhankelijk is van dezelfde sleutel die door dezelfde query-instantie wordt verwerkt, moet u ervoor zorgen dat de gebeurtenissen naar dezelfde partitie van uw invoer gaan. Voor gebeurtenishubs of IoT Hub betekent dit dat de gebeurtenisgegevens de **partitionkey-waarde** moeten hebben ingesteld. U ook partitieafzenders gebruiken. Voor blobopslag betekent dit dat de gebeurtenissen naar dezelfde partitiemap worden verzonden. Als voor uw querylogica niet dezelfde sleutel vereist die door dezelfde query-instantie moet worden verwerkt, u deze vereiste negeren. Een voorbeeld van deze logica is een eenvoudige query selecteren-projectfilter.  

2. Zodra de gegevens aan de invoerzijde zijn ingedeeld, moet u ervoor zorgen dat uw query wordt gepartitioneerd. Dit vereist dat u **PARTITION BY** in alle stappen gebruikt. Meerdere stappen zijn toegestaan, maar ze moeten allemaal worden verdeeld door dezelfde sleutel. Onder compatibiliteitsniveau 1.0 en 1.1 moet de partitiesleutel worden ingesteld op **PartitionId,** zodat de taak volledig parallel is. Voor taken met compatibiliteitsniveau 1.2 en hoger kan aangepaste kolom worden opgegeven als partitiesleutel in de invoerinstellingen en wordt de taak automatisch geparalelliseerd, zelfs zonder partitie per component. Voor gebeurtenishub-uitvoer moet de eigenschap "Kolom partitiesleutel" zijn ingesteld op "PartitionId".

3. Het grootste deel van onze uitvoer kan profiteren van partitionering, maar als u een uitvoertype gebruikt dat het partitioneren van uw taak niet ondersteunt, is deze niet volledig parallel. Controleer bij gebeurtenishub-uitvoer of **de kolom Partitiesleutel** hetzelfde is ingesteld als de querypartitiesleutel. Raadpleeg de [uitvoersectie](#outputs) voor meer informatie.

4. Het aantal invoerpartities moet gelijk zijn aan het aantal uitvoerpartities. Blob-opslaguitvoer kan partities ondersteunen en het partitieschema van de upstreamquery overnemen. Wanneer een partitiesleutel voor Blob-opslag is opgegeven, worden gegevens verdeeld per invoerpartitie, zodat het resultaat nog steeds volledig parallel is. Hier volgen voorbeelden van partitiewaarden die een volledig parallelle taak mogelijk maken:

   * 8 gebeurtenishub-invoerpartities en 8 gebeurtenishub-uitvoerpartities
   * 8 gebeurtenishub-invoerpartities en blob-opslaguitvoer
   * 8 gebeurtenishub-invoerpartities en blobopslaguitvoer die is verdeeld door een aangepast veld met willekeurige kardinaliteit
   * Invoerpartities van 8 blob-opslag en blob-opslaguitvoer
   * 8 blob-opslaginvoerpartities en 8 gebeurtenishub-uitvoerpartities

In de volgende secties worden enkele voorbeeldscenario's besproken die in verlegenheid brengen.

### <a name="simple-query"></a>Eenvoudige query

* Invoer: gebeurtenishub met 8 partities
* Uitvoer: gebeurtenishub met 8 partities ('Partitiesleutelkolom' moet zijn ingesteld op 'PartitionId')

Query:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Deze query is een eenvoudig filter. Daarom hoeven we ons geen zorgen te maken over het partitioneren van de invoer die naar de gebeurtenishub wordt verzonden. Merk op dat taken met compatibiliteitsniveau vóór 1.2 **partitie per partitie-component** moeten bevatten, zodat het voldoet aan de vereiste #2 van vroeger. Voor de uitvoer moeten we de gebeurtenishub-uitvoer in de taak configureren om de partitiesleutel op **PartitionId**te hebben ingesteld. Een laatste controle is om ervoor te zorgen dat het aantal invoerpartities gelijk is aan het aantal uitvoerpartities.

### <a name="query-with-a-grouping-key"></a>Query met een groeperingssleutel

* Invoer: gebeurtenishub met 8 partities
* Uitvoer: Blob-opslag

Query:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Deze query heeft een groeperingssleutel. Daarom moeten de samengegroepeerde gebeurtenissen naar dezelfde gebeurtenishubpartitie worden verzonden. Aangezien we in dit voorbeeld groeperen op TollBoothID, moeten we er zeker van zijn dat TollBoothID wordt gebruikt als partitiesleutel wanneer de gebeurtenissen naar Event Hub worden verzonden. Vervolgens kunnen we in ASA **PARTITION BY PartitionId** gebruiken om van dit partitieschema te erven en volledige parallelisatie mogelijk te maken. Aangezien de uitvoer blobopslag is, hoeven we ons geen zorgen te maken over het configureren van een waarde van de partitiesleutel, per vereiste #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Voorbeeld van scenario's die *niet* beschamend parallel lopen

In het vorige deel, toonden we een aantal beschamend parallelle scenario's. In deze sectie bespreken we scenario's die niet voldoen aan alle eisen om gênant parallel te zijn. 

### <a name="mismatched-partition-count"></a>Niet op elkaar afgestemd partitieaantal
* Invoer: gebeurtenishub met 8 partities
* Uitvoer: gebeurtenishub met 32 partities

In dit geval maakt het niet uit wat de query is. Als het aantal invoerpartities niet overeenkomt met het aantal uitvoerpartities, is de topologie niet beschamend parallel.+ Maar we kunnen nog steeds een niveau of parallelisatie krijgen.

### <a name="query-using-non-partitioned-output"></a>Query met niet-gepartitioneerde uitvoer
* Invoer: gebeurtenishub met 8 partities
* Uitgang: Power BI

Power BI-uitgang ondersteunt momenteel geen partitionering. Daarom is dit scenario niet beschamend parallel.

### <a name="multi-step-query-with-different-partition-by-values"></a>Query met meerdere stappen met verschillende PARTITIE-door-waarden
* Invoer: gebeurtenishub met 8 partities
* Uitvoer: gebeurtenishub met 8 partities

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

Zoals u zien, gebruikt de tweede stap **TollBoothId** als de partitioneringssleutel. Deze stap is niet hetzelfde als de eerste stap, en het vereist daarom dat we een shuffle doen. 

De voorgaande voorbeelden tonen sommige Stream Analytics-taken die voldoen aan (of niet) een beschamend parallelle topologie. Als ze zich wel conformeren, hebben ze het potentieel voor maximale schaal. Voor taken die niet in een van deze profielen passen, zijn schaalrichtlijnen beschikbaar in toekomstige updates. Voor nu, gebruik maken van de algemene richtlijnen in de volgende secties.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Compatibiliteitsniveau 1.2 - Query met meerdere stappen met verschillende PARTITION BY-waarden 
* Invoer: gebeurtenishub met 8 partities
* Uitvoer: gebeurtenishub met 8 partities ("Partitiesleutelkolom" moet zijn ingesteld op "TollBoothId")

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

Compatibiliteitsniveau 1.2 maakt standaard parallelle query-uitvoering mogelijk. Query's uit de vorige sectie worden bijvoorbeeld verdeeld zolang de kolom 'TollBoothId' is ingesteld als invoerpartitiesleutel. PARTITIE DOOR PartitionId clausule is niet vereist.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>De maximale streaming-eenheden van een taak berekenen
Het totale aantal streamingeenheden dat kan worden gebruikt door een Stream Analytics-taak, is afhankelijk van het aantal stappen in de query die is gedefinieerd voor de taak en het aantal partities voor elke stap.

### <a name="steps-in-a-query"></a>Stappen in een query
Een query kan een of meer stappen hebben. Elke stap is een subquery gedefinieerd door het **trefwoord MET.** De query die buiten het trefwoord **MET** valt (slechts één query) wordt ook als stap geteld, zoals de instructie **SELECT** in de volgende query:

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

Deze query heeft twee stappen.

> [!NOTE]
> Deze query wordt later in het artikel nader besproken.
>  

### <a name="partition-a-step"></a>Een stap partitioneren
Voor het partitioneren van een stap zijn de volgende voorwaarden vereist:

* De invoerbron moet worden verdeeld. 
* De **select-instructie** van de query moet worden gelezen uit een partitiebron.
* De query in de stap moet het trefwoord **PARTITION BY** hebben.

Wanneer een query wordt verdeeld, worden de invoergebeurtenissen verwerkt en samengevoegd in afzonderlijke partitiegroepen en worden uitvoergebeurtenissen gegenereerd voor elk van de groepen. Als u een gecombineerd aggregaat wilt, moet u een tweede niet-partitiestap maken om samen te voegen.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>De maximale streaming-eenheden voor een taak berekenen
Alle niet-gepartitioneerde stappen samen kunnen maximaal zes streaming-eenheden (SU's) opschalen voor een Stream Analytics-taak. Daarnaast u 6 SU's voor elke partitie toevoegen in een partitiestap.
U enkele **voorbeelden** zien in de onderstaande tabel.

| Query’s uitvoeren                                               | Max SUs voor de baan |
| --------------------------------------------------- | ------------------- |
| <ul><li>De query bevat één stap.</li><li>De stap wordt niet verdeeld.</li></ul> | 6 |
| <ul><li>De invoergegevensstroom wordt verdeeld door 16.</li><li>De query bevat één stap.</li><li>De stap is verdeeld.</li></ul> | 96 (6 * 16 partities) |
| <ul><li>De query bevat twee stappen.</li><li>Geen van de stappen is verdeeld.</li></ul> | 6 |
| <ul><li>De invoergegevensstroom wordt verdeeld door 3.</li><li>De query bevat twee stappen. De invoerstap wordt verdeeld en de tweede stap niet.</li><li>De <strong>instructie SELECT</strong> leest uit de partitieinvoer.</li></ul> | 24 (18 voor partitiestappen + 6 voor niet-partitiestappen |

### <a name="examples-of-scaling"></a>Voorbeelden van schalen

De volgende query berekent het aantal auto's binnen een venster van drie minuten en gaat door een tolstation met drie tolpoortjes. Deze query kan worden opgeschaald naar zes SU's.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Als u meer SU's voor de query wilt gebruiken, moeten zowel de invoergegevensstroom als de query worden verdeeld. Aangezien de gegevensstroompartitie is ingesteld op 3, kan de volgende gewijzigde query worden opgeschaald naar 18 SU's:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Wanneer een query wordt verdeeld, worden de invoergebeurtenissen verwerkt en samengevoegd in afzonderlijke partitiegroepen. Uitvoergebeurtenissen worden ook gegenereerd voor elk van de groepen. Partitionering kan onverwachte resultaten veroorzaken wanneer het veld **GROEPEREN PER** niet de partitiesleutel in de invoergegevensstroom is. Het veld **TollBoothId** in de vorige query is bijvoorbeeld niet de partitiesleutel van **Input1.** Het resultaat is dat de gegevens van TollBooth #1 kunnen worden verspreid in meerdere partities.

Elk van de **Input1-partities** wordt afzonderlijk verwerkt door Stream Analytics. Als gevolg hiervan worden meerdere records van de auto telling voor dezelfde tolpoort in dezelfde Tumbling venster zal worden gemaakt. Als de invoerpartitiesleutel niet kan worden gewijzigd, kan dit probleem worden opgelost door een niet-partitiestap toe te voegen aan het samenvoegen van waarden tussen partities, zoals in het volgende voorbeeld:

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

Deze query kan worden geschaald naar 24 SU's.

> [!NOTE]
> Als u twee streams aansluit, moet u ervoor zorgen dat de streams worden verdeeld door de partitiesleutel van de kolom die u gebruikt om de joins te maken. Zorg er ook voor dat je hetzelfde aantal partities in beide streams hebt.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Hogere doorvoersnelheden op schaal

Een [beschamend parallelle](#embarrassingly-parallel-jobs) taak is noodzakelijk, maar niet voldoende om een hogere doorvoer op schaal te ondersteunen. Elk opslagsysteem en de bijbehorende Stream Analytics-uitvoer heeft variaties op het bereiken van de best mogelijke schrijfdoorvoer. Zoals met elk scenario op maat, zijn er een aantal uitdagingen die kunnen worden opgelost met behulp van de juiste configuraties. In deze sectie worden configuraties voor een paar veelvoorkomende uitvoerbesproken en worden voorbeelden gegeven voor het ondersteunen van opnamepercentages van 1K, 5K en 10K-gebeurtenissen per seconde.

De volgende observaties maken gebruik van een Stream Analytics-taak met stateloze (passthrough)-query, een basis-JavaScript UDF die schrijft naar Event Hub, Azure SQL DB of Cosmos DB.

#### <a name="event-hub"></a>Event Hub

|Opnamepercentage (gebeurtenissen per seconde) | Streaming-eenheden | Uitvoerbronnen  |
|--------|---------|---------|
| 1 K     |    1    |  2 TU   |
| 5 K     |    6    |  6 TU   |
| 10 K    |    12   |  10 TU  |

De [Event Hub-oplossing](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) schaalt lineair in termen van streaming-eenheden (SU) en doorvoer, waardoor het de meest efficiënte en performante manier is om gegevens uit Stream Analytics te analyseren en te streamen. Taken kunnen worden opgeschaald naar 192 SU, wat zich ruwweg vertaalt in het verwerken van maximaal 200 MB/s of 19 biljoen gebeurtenissen per dag.

#### <a name="azure-sql"></a>Azure SQL
|Opnamepercentage (gebeurtenissen per seconde) | Streaming-eenheden | Uitvoerbronnen  |
|---------|------|-------|
|    1 K   |   3  |  S3   |
|    5 K   |   18 |  P4   |
|    10 K  |   36 |  P6   |

[Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) ondersteunt parallel schrijven, inherit partitioning genaamd, maar het is niet standaard ingeschakeld. Het inschakelen van Inherit Partitioning, samen met een volledig parallelle query, is echter mogelijk niet voldoende om hogere doorvoersnelheden te bereiken. SQL-schrijfdoorvoer is aanzienlijk afhankelijk van uw SQL Azure-databaseconfiguratie en tabelschema. Het [sql-uitvoerprestatieartikel](./stream-analytics-sql-output-perf.md) heeft meer details over de parameters die uw schrijfdoorvoer kunnen maximaliseren. Zoals opgemerkt in het [Azure Stream Analytics-uitvoer-naar Azure SQL Database-artikel,](./stream-analytics-sql-output-perf.md#azure-stream-analytics) wordt deze oplossing niet lineair geschaald als een volledig parallelle pijplijn van meer dan 8 partities en moet deze mogelijk opnieuw worden gepartitionerd voordat SQL-uitvoer wordt uitgevoerd (zie [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). Premium SKU's zijn nodig om hoge IO-tarieven te ondersteunen, samen met overhead van log back-ups gebeurt om de paar minuten.

#### <a name="cosmos-db"></a>Cosmos DB
|Opnamepercentage (gebeurtenissen per seconde) | Streaming-eenheden | Uitvoerbronnen  |
|-------|-------|---------|
|  1 K   |  3    | 20.000 RU  |
|  5 K   |  24   | 60.000 RU  |
|  10 K  |  48   | 120.000 RU |

[Cosmos DB-uitvoer](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) van Stream Analytics is bijgewerkt om native integratie te gebruiken onder [compatibiliteitsniveau 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Compatibiliteitsniveau 1.2 maakt een aanzienlijk hogere doorvoer mogelijk en vermindert het RU-verbruik in vergelijking met 1.1, het standaardcompatibiliteitsniveau voor nieuwe taken. De oplossing maakt gebruik van CosmosDB containers verdeeld op / deviceId en de rest van de oplossing is identiek geconfigureerd.

Alle [Azure-voorbeelden streaming op schaal](https://github.com/Azure-Samples/streaming-at-scale) gebruiken een gebeurtenishub die wordt gevoed door het simuleren van testclients als invoer. Elke invoergebeurtenis is een JSON-document van 1 KB, dat geconfigureerde opnamesnelheden eenvoudig vertaalt naar doorvoersnelheden (1MB/s, 5MB/s en 10MB/s). Gebeurtenissen simuleren een IoT-apparaat dat de volgende JSON-gegevens verzendt (in een verkorte vorm) voor maximaal 1K-apparaten:

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
> De configuraties kunnen worden gewijzigd als gevolg van de verschillende componenten die in de oplossing worden gebruikt. Voor een nauwkeurigere schatting u de voorbeelden aanpassen aan uw scenario.

### <a name="identifying-bottlenecks"></a>Knelpunten identificeren

Gebruik het deelvenster Statistieken in uw Azure Stream Analytics-taak om knelpunten in uw pijplijn te identificeren. Controleer **invoer-/uitvoergebeurtenissen** voor doorvoer en ['Watermarkdelay'](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) of **Backlogged-gebeurtenissen** om te zien of de taak de invoersnelheid bijhoudt. Zoek voor gebeurtenishub-statistieken naar **geweigerde aanvragen** en pas de drempeleenheden dienovereenkomstig aan. Controleer voor Cosmos **DB-statistieken de verbruikte RU/s per partitiesleutelbereik** onder Doorvoer om ervoor te zorgen dat uw partitiesleutelbereiken gelijkmatig worden verbruikt. Controleer voor Azure SQL DB **Log IO** en **CPU**.

## <a name="get-help"></a>Help opvragen

Probeer ons Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

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

