---
title: SQL-querystatistieken voor Azure Cosmos DB SQL API
description: Meer informatie over het implementeren en debuggen van de SQL-queryprestaties van Azure Cosmos DB-aanvragen.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: ae1773ec1d470b9cff2efb00c200427b7b4c2fb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69614818"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Queryprestaties afstemmen met Azure Cosmos DB

Azure Cosmos DB biedt een [SQL-API voor het opvragen van gegevens,](how-to-sql-query.md)zonder dat schema of secundaire indexen nodig zijn. In dit artikel vindt u de volgende informatie voor ontwikkelaars:

* Details op hoog niveau over hoe de SQL-queryuitvoering van Azure Cosmos DB werkt
* Details over queryaanvraag- en antwoordkoppen en SDK-opties voor client
* Tips en aanbevolen procedures voor queryprestaties
* Voorbeelden van het gebruik van SQL-uitvoeringsstatistieken om queryprestaties te debuggen

## <a name="about-sql-query-execution"></a>Over SQL-query-uitvoering

In Azure Cosmos DB slaat u gegevens op in containers, die kunnen groeien tot elke [opslaggrootte of doorvoer aanvragen.](partition-data.md) Azure Cosmos DB schaalt gegevens naadloos op over fysieke partities onder de hoezen om gegevensgroei of een toename van de ingerichte doorvoer te verwerken. U SQL-query's uitgeven aan elke container met behulp van de REST API of een van de ondersteunde [SQL SDKs.](sql-api-sdk-dotnet.md)

Een kort overzicht van partitionering: u definieert een partitiesleutel als 'stad', die bepaalt hoe gegevens worden verdeeld over fysieke partities. Gegevens die behoren tot één partitiesleutel (bijvoorbeeld 'stad' == 'Seattle') worden opgeslagen in een fysieke partitie, maar meestal heeft één fysieke partitie meerdere partitiesleutels. Wanneer een partitie zijn opslaggrootte bereikt, splitst de service de partitie naadloos op in twee nieuwe partities en verdeelt de partitiesleutel gelijkmatig over deze partities. Aangezien partities van voorbijgaande aard zijn, gebruiken de API's een abstractie van een "partitiesleutelbereik", dat de bereiken van partitiesleutelhashes aangeeft. 

Wanneer u een query uitgeeft aan Azure Cosmos DB, voert de SDK de volgende logische stappen uit:

* Ontwende de SQL-query om het queryuitvoeringsplan te bepalen. 
* Als de query een filter bevat `SELECT * FROM c WHERE c.city = "Seattle"`tegen de partitiesleutel, zoals , wordt deze doorgestuurd naar één partitie. Als de query geen filter op partitiesleutel heeft, wordt deze uitgevoerd in alle partities en worden de resultaten samengevoegd clientzijde.
* De query wordt uitgevoerd binnen elke partitie in serie of parallel, op basis van clientconfiguratie. Binnen elke partitie kan de query een of meer retourritten maken, afhankelijk van de complexiteit van de query, de geconfigureerde paginagrootte en de ingerichte doorvoer van de verzameling. Elke uitvoering retourneert het aantal [aanvraageenheden](request-units.md) dat wordt verbruikt door queryuitvoering en optioneel queryuitvoeringsstatistieken. 
* De SDK voert een samenvatting van de queryresultaten uit tussen partities. Als de query bijvoorbeeld een order door voor partities omvat, worden de resultaten van afzonderlijke partities samengevoegd om resultaten in een wereldwijd gesorteerde volgorde te retourneren. Als de query een `COUNT`aggregatie is zoals, worden de tellingen van afzonderlijke partities samengevat om het totale aantal te produceren.

De SDK's bieden verschillende opties voor query-uitvoering. Deze opties zijn bijvoorbeeld beschikbaar in `FeedOptions` .NET in de klasse. In de volgende tabel worden deze opties beschreven en hoe deze van invloed zijn op de uitvoeringstijd van query's. 

| Optie | Beschrijving |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Moet worden ingesteld op true voor elke query die moet worden uitgevoerd in meer dan een partitie. Dit is een expliciete vlag om u in staat te stellen bewuste prestaties afwegingen te maken tijdens de ontwikkelingstijd. |
| `EnableScanInQuery` | Moet worden ingesteld op true als u zich hebt afgemeld voor indexering, maar de query toch via een scan wilt uitvoeren. Alleen van toepassing als indexering voor het aangevraagde filterpad is uitgeschakeld. | 
| `MaxItemCount` | Het maximum aantal items dat per retournaar de server moet worden geretourneerd. Door in te stellen op -1, u de server het aantal items laten beheren. U deze waarde ook verlagen om slechts een klein aantal items per heen- en terugreis op te halen. 
| `MaxBufferedItemCount` | Dit is een client-side optie, en gebruikt om het geheugenverbruik te beperken bij het uitvoeren van cross-partition ORDER BY. Een hogere waarde helpt de latentie van cross-partitiesortering te verminderen. |
| `MaxDegreeOfParallelism` | Hiermee wordt het aantal gelijktijdige bewerkingen uitgevoerd clientzijde tijdens parallelle queryuitvoering in de Azure Cosmos-databaseservice. Een positieve eigenschapswaarde beperkt het aantal gelijktijdige bewerkingen tot de ingestelde waarde. Als het systeem is ingesteld op minder dan 0, bepaalt het systeem automatisch het aantal gelijktijdige bewerkingen dat moet worden uitgevoerd. |
| `PopulateQueryMetrics` | Hiermee u gedetailleerde logboekregistratie van statistieken van de tijd die is besteed in verschillende fasen van queryuitvoering, zoals compilatietijd, indexlustijd en laadtijd van documenten. U uitvoer uit querystatistieken delen met Azure Support om problemen met queryprestaties te diagnosticeren. |
| `RequestContinuation` | U de uitvoering van query's hervatten door het ondoorzichtige vervolgtoken door te geven dat door een query wordt geretourneerd. Het vervolgtoken bevat alle status die nodig is voor query-uitvoering. |
| `ResponseContinuationTokenLimitInKb` | U de maximale grootte van het vervolgtoken dat door de server wordt geretourneerd, beperken. Mogelijk moet u dit instellen als de host van uw toepassing beperkingen heeft op de grootte van de antwoordkop. Als u dit instelt, kan de totale duur en de verbruikte AMERIKAANSE kosten voor de query toenemen.  |

Laten we bijvoorbeeld een voorbeeldquery uitvoeren op partitiesleutel die `/city` is aangevraagd op een verzameling met als partitiesleutel en ingericht met 100.000 RU/s doorvoer. U vraagt deze `CreateDocumentQuery<T>` query aan in .NET als volgt:

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

Het hierboven getoonde SDK-fragment komt overeen met de volgende REST API-aanvraag:

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

Elke queryuitvoeringspagina komt overeen `POST` met `Accept: application/query+json` een REST-API met de koptekst en de SQL-query in de hoofdtekst. Elke query maakt een of meer retouren naar de server met het `x-ms-continuation` token echode tussen de client en de server om de uitvoering te hervatten. De configuratieopties in FeedOptions worden doorgegeven aan de server in de vorm van aanvraagkoppen. Bijvoorbeeld, `MaxItemCount` komt overeen met `x-ms-max-item-count`. 

De aanvraag retourneert het volgende antwoord (afgekapt voor leesbaarheid):

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

De belangrijkste antwoordkoppen die uit de query zijn geretourneerd, bevatten de volgende:

| Optie | Beschrijving |
| ------ | ----------- |
| `x-ms-item-count` | Het aantal artikelen dat in het antwoord is geretourneerd. Dit is afhankelijk van `x-ms-max-item-count`de meegeleverde , het aantal artikelen dat kan worden aangepast binnen de maximale respons payload grootte, de ingerichte doorvoer, en query uitvoeringstijd. |  
| `x-ms-continuation:` | Het vervolgtoken om de uitvoering van de query te hervatten, als er aanvullende resultaten beschikbaar zijn. | 
| `x-ms-documentdb-query-metrics` | De querystatistieken voor de uitvoering. Dit is een afgebakende tekenreeks met statistieken over de tijd die is besteed aan de verschillende fasen van query-uitvoering. Geretourneerd `x-ms-documentdb-populatequerymetrics` als ingesteld `True`is op . | 
| `x-ms-request-charge` | Het aantal [aanvraageenheden](request-units.md) dat door de query wordt verbruikt. | 

Zie [Resources opvragen met de REST API](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api)voor meer informatie over de headers en opties voor de REST API.

## <a name="best-practices-for-query-performance"></a>Aanbevolen procedures voor queryprestaties
Hieronder volgen de meest voorkomende factoren die van invloed zijn op de prestaties van Azure Cosmos DB-query's. We graven dieper in elk van deze onderwerpen in dit artikel.

| Factor | Tip | 
| ------ | -----| 
| Ingerichte doorvoer | Meet RU per query en zorg ervoor dat u over de vereiste ingerichte doorvoer voor uw query's beschikt. | 
| Partitie- en partitiesleutels | Bevoordeel query's met de partitiesleutelwaarde in de filterclausule voor lage latentie. |
| SDK- en queryopties | Volg SDK-aanbevolen procedures zoals directe connectiviteit en stem queryuitvoeringsopties aan clientzijde af. |
| Netwerklatentie | Reken op netwerkoverhead bij metingen en gebruik multi-homing API's om uit de dichtstbijzijnde regio te lezen. |
| Indexeringsbeleid | Zorg ervoor dat u over de vereiste indexeringspaden/-beleid voor de query beschikt. |
| Statistieken voor queryuitvoering | Analyseer de queryuitvoeringsstatistieken om potentiële herschrijft van query- en gegevensvormen te identificeren.  |

### <a name="provisioned-throughput"></a>Ingerichte doorvoer
In Cosmos DB maakt u containers met gegevens, elk met gereserveerde doorvoer uitgedrukt in aanvraageenheden (RU) per seconde. Een lezing van een document van 1 KB is 1 RU en elke bewerking (inclusief query's) wordt genormaliseerd tot een vast aantal RU's op basis van de complexiteit ervan. Als u bijvoorbeeld 1000 RU/s hebt ingericht voor uw container `SELECT * FROM c WHERE c.city = 'Seattle'` en u een zoekopdracht als die 5 RU's verbruikt, u uitvoeren (1000 RU/s) / (5 RU/query) = 200 query's per seconde. 

Als u meer dan 200 query's per seconde indient, wordt gestart met het beperken van binnenkomende aanvragen boven de 200/s. De SDK's behandelen deze aanvraag automatisch door een backoff/retry uit te voeren, daarom merkt u mogelijk een hogere latentie voor deze query's. Als u de ingerichte doorvoer verhoogt naar de vereiste waarde, verbetert uw querylatentie en doorvoer. 

Zie [Eenheden aanvragen voor](request-units.md)meer informatie over aanvraageenheden.

### <a name="partitioning-and-partition-keys"></a>Partitie- en partitiesleutels
Met Azure Cosmos DB worden query's meestal in de volgende volgorde uitgevoerd, van snelste/meest efficiënte tot langzamere/ minder efficiënte query's. 

* GET op één partitiesleutel en itemsleutel
* Query met een filterclausule op één partitiesleutel
* Query zonder een gelijkheids- of bereikfilterclausule op een eigenschap
* Query zonder filters

Query's die alle partities moeten raadplegen, hebben een hogere latentie nodig en kunnen hogere RU's verbruiken. Aangezien elke partitie automatische indexering heeft ten opzichte van alle eigenschappen, kan de query in dit geval efficiënt worden weergegeven vanuit de index. U query's die partities overspannen sneller maken met behulp van de parallelleopties.

Zie [Partitioneren in Azure Cosmos DB](partition-data.md)voor meer informatie over partitionering en partitiesleutels.

### <a name="sdk-and-query-options"></a>SDK- en queryopties
Bekijk [prestatietips](performance-tips.md) en [prestatietests](performance-testing.md) voor het behalen van de beste client-side prestaties van Azure Cosmos DB. Dit omvat het gebruik van de nieuwste SDKs, het configureren van platformspecifieke configuraties zoals standaard aantal verbindingen, frequentie van garbage collection en het gebruik van lichtgewicht connectiviteitsopties zoals Direct/TCP. 


#### <a name="max-item-count"></a>Maximaal aantal objecten
Voor query's kan `MaxItemCount` de waarde van een aanzienlijke invloed hebben op de end-to-end querytijd. Elke retourvlucht naar de server zal niet meer `MaxItemCount` dan het aantal items in (Standaard van 100 items) retourneren. Als u dit instelt op een hogere waarde (-1 is maximaal en aanbevolen) wordt uw queryduur in het algemeen verbeterd door het aantal retourritten tussen server en client te beperken, met name voor query's met grote resultaatsets.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Maximale mate van parallellisme
Voor query's `MaxDegreeOfParallelism` moet u afstemmen op de beste configuraties voor uw toepassing, vooral als u kruispartitiequery's uitvoert (zonder een filter op de waarde van de partitiesleutel). `MaxDegreeOfParallelism`hiermee wordt het maximum aantal parallelle taken, d.w.z. het maximum aantal partities dat parallel moet worden bezocht, gecontroleerd. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

Laten we aannemen dat
* D = Standaard Maximum aantal parallelle taken (= totaal aantal processor in de clientmachine)
* P = Door de gebruiker opgegeven maximumaantal parallelle taken
* N = Aantal partities dat moet worden bezocht voor het beantwoorden van een query

Hieronder volgen implicaties van hoe de parallelle query's zich zouden gedragen voor verschillende waarden van P.
* (P == 0) => seriële modus
* (P == 1) => Maximaal één taak
* (P > 1) => Min (P, N) parallelle taken 
* (P < 1) => Min (N, D) parallelle taken

Voor SDK-releasenotes en details over geïmplementeerde klassen en methoden zie [SQL SDK's](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Netwerklatentie
Zie de globale distributie van [Azure Cosmos DB](tutorial-global-distribution-sql-api.md) voor het instellen van wereldwijde distributie en verbinding maken met de dichtstbijzijnde regio. Netwerklatentie heeft een aanzienlijke invloed op de queryprestaties wanneer u meerdere retourritten moet maken of een groot resultaatuit de query moet ophalen. 

In de sectie queryuitvoeringsstatistieken wordt uitgelegd hoe u `totalExecutionTimeInMs`de serveruitvoeringstijd van query's ophalen ( ), zodat u onderscheid maken tussen tijd die wordt besteed aan query-uitvoering en tijd die wordt besteed aan netwerkdoorvoer.

### <a name="indexing-policy"></a>Indexeringsbeleid
Zie [Indexeringsbeleid configureren](index-policy.md) voor het indexeren van paden, soorten en modi en hoe deze van invloed zijn op de uitvoering van query's. Standaard gebruikt het indexeringsbeleid Hash-indexering voor tekenreeksen, wat effectief is voor gelijkheidsquery's, maar niet voor bereikquery's/volgorde op query's. Als u bereikquery's voor tekenreeksen nodig hebt, raden we u aan het type bereikindex voor alle tekenreeksen op te geven. 

Azure Cosmos DB past standaard automatische indexering toe op alle gegevens. Voor scenario's voor het invoegen met hoge prestaties u paden uitsluiten, aangezien hierdoor de RU-kosten voor elke invoegbewerking worden verminderd. 

## <a name="query-execution-metrics"></a>Statistieken voor queryuitvoering
U gedetailleerde statistieken over queryuitvoering `x-ms-documentdb-populatequerymetrics` verkrijgen`FeedOptions.PopulateQueryMetrics` door de optionele koptekst (in de .NET SDK) door te geven. De waarde `x-ms-documentdb-query-metrics` die wordt geretourneerd in de volgende sleutelwaardeparen is bedoeld voor geavanceerde probleemoplossing voor queryuitvoering. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| Gegevens | Eenheid | Beschrijving | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | milliseconden | Uitvoeringstijd query's | 
| `queryCompileTimeInMs` | milliseconden | Querycompilertijd  | 
| `queryLogicalPlanBuildTimeInMs` | milliseconden | Tijd om een logisch queryplan op te bouwen | 
| `queryPhysicalPlanBuildTimeInMs` | milliseconden | Tijd om een fysiek queryplan op te bouwen | 
| `queryOptimizationTimeInMs` | milliseconden | Tijd besteed aan het optimaliseren van query's | 
| `VMExecutionTimeInMs` | milliseconden | Tijd besteed aan queryruntijd | 
| `indexLookupTimeInMs` | milliseconden | Tijd doorgebracht in fysieke indexlaag | 
| `documentLoadTimeInMs` | milliseconden | Tijd besteed aan het laden van documenten  | 
| `systemFunctionExecuteTimeInMs` | milliseconden | Totale tijd besteed aan het uitvoeren van systeemfuncties (ingebouwd) in milliseconden  | 
| `userFunctionExecuteTimeInMs` | milliseconden | Totale tijd besteed aan het uitvoeren van door de gebruiker gedefinieerde functies in milliseconden | 
| `retrievedDocumentCount` | count | Totaal aantal opgehaalde documenten  | 
| `retrievedDocumentSize` | Bytes | Totale grootte van opgehaalde documenten in bytes  | 
| `outputDocumentCount` | count | Aantal uitvoerdocumenten | 
| `writeOutputTimeInMs` | milliseconden | Uitvoeringstijd query in milliseconden | 
| `indexUtilizationRatio` | verhouding (<=1) | Verhouding tussen het aantal documenten dat door het filter wordt gekoppeld aan het aantal geladen documenten  | 

De client-SDK's kunnen intern meerdere querybewerkingen uitvoeren om de query binnen elke partitie weer te geven. De client voert meer dan één aanroep `x-ms-max-item-count`per partitie uit als de totale resultaten hoger zijn dan , als de query de ingerichte doorvoer voor de partitie overschrijdt of als de querypayload de maximale grootte per pagina bereikt of als de query de door het systeem toegewezen time-outlimiet bereikt. Elke uitvoering van `x-ms-documentdb-query-metrics` elke gedeeltelijke query retourneert een voor die pagina. 

Hier volgen enkele voorbeeldquery's en hoe u een aantal statistieken interpreteren die zijn geretourneerd uit queryuitvoering: 

| Query’s uitvoeren | Voorbeeldstatistiek | Beschrijving | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Het aantal opgehaalde documenten is 100+1 dat overeenkomt met de TOP-clausule. Querytijd wordt meestal `WriteOutputTime` `DocumentLoadTime` besteed in en omdat het een scan. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount is nu hoger (500+1 om overeen te komen met de TOP-component). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Ongeveer 0,9 ms wordt besteed in IndexLookupTime voor een belangrijke lookup, want het is een index lookup op `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Iets meer tijd (1,7 ms) besteed in IndexLookupTime over een bereik scan, want het is een index lookup op `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Dezelfde tijd `DocumentLoadTime` besteed aan als vorige `WriteOutputTime` query's, maar lager omdat we slechts één eigenschap projecteren. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Ongeveer 213 ms `UserDefinedFunctionExecutionTime` wordt besteed aan het uitvoeren `c.N`van de UDF op elke waarde van . |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Ongeveer 0,6 ms `IndexLookupTime` wordt `/Name/?`besteed aan . Het grootste deel van de queryuitvoeringstijd (~ 7 ms) in `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Query wordt uitgevoerd als een `LOWER`scan omdat deze wordt gebruikt en 500 van de 2491 opgehaalde documenten worden geretourneerd. |


## <a name="next-steps"></a>Volgende stappen
* Zie [SQL-query](sql-query-getting-started.md)voor meer informatie over de ondersteunde SQL-queryoperators en trefwoorden. 
* Zie [Aanvraageenheden voor](request-units.md)meer informatie over aanvraageenheden.
* Zie indexeringsbeleid voor meer informatie over [indexeringsbeleid](index-policy.md) 


