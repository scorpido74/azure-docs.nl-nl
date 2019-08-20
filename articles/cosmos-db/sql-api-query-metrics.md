---
title: SQL-query metrische gegevens voor Azure Cosmos DB SQL API
description: Meer informatie over het instrumenteren en fouten opsporen in de prestaties van de SQL-query's van Azure Cosmos DB-aanvragen.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: ae1773ec1d470b9cff2efb00c200427b7b4c2fb4
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614818"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Afstemming van prestaties van query's met Azure Cosmos DB

Azure Cosmos DB biedt een [SQL-API voor het opvragen van gegevens](how-to-sql-query.md), zonder schema of secundaire indexen. In dit artikel bevat de volgende informatie voor ontwikkelaars:

* Details over de werking van Azure Cosmos DB SQL-query uitvoeren op hoog niveau
* Meer informatie over de aanvraag- en reactieheaders query en opties voor client-SDK
* Tips en aanbevolen procedures voor prestaties van query 's
* Voorbeelden van hoe u kunt gebruikmaken van SQL tot uitvoering van statistieken om op te sporen van de prestaties van query 's

## <a name="about-sql-query-execution"></a>Over het uitvoeren van de SQL-query 's

In Azure Cosmos DB, slaat u gegevens in containers, die kunnen worden uitgebreid om een [formaat of de aanvraag opslagdoorvoer](partition-data.md). Azure Cosmos DB schaalt naadloos gegevens over fysieke partities op de achtergrond te verwerken van de groei van gegevens verhoogd met ingerichte doorvoer. U kunt de SQL-query's naar een container met de REST-API of een van de ondersteunde opdracht [SQL-SDK's](sql-api-sdk-dotnet.md).

Een kort overzicht van partitionering: definieert u een partitiesleutel, zoals 'plaats', waarmee wordt bepaald hoe de gegevens is gesplitst over meerdere fysieke partities. Gegevens die behoren tot een enkele partitiesleutel (bijvoorbeeld 'plaats' == "Seattle") worden opgeslagen in een fysieke partitie, maar één fysieke partitie heeft doorgaans meerdere partitiesleutels. Wanneer u een partitie zijn opslaggrootte bereikt, de service naadloos de partitie worden gesplitst in twee nieuwe partities, en wordt de partitiesleutel gelijkmatig verdeeld over deze partities. Aangezien partities tijdelijk zijn, gebruiken de API's voor een abstractie van een 'partitiesleutelbereik', waarmee de bereiken van de belangrijkste hashes partitie. 

Wanneer u een query met Azure Cosmos DB, voert de SDK deze logische stappen uit:

* Parseren van de SQL-query om te bepalen het queryplan kan worden uitgevoerd. 
* Als de query een filter op basis van de partitiesleutel bevat, zoals `SELECT * FROM c WHERE c.city = "Seattle"`, wordt deze doorgestuurd naar één partitie. Als de query heeft geen een filter voor de partitiesleutel, wordt deze uitgevoerd in alle partities en resultaten worden samengevoegd clientzijde.
* De query wordt uitgevoerd binnen elke partitie in de reeks of parallel, op basis van de clientconfiguratie. Binnen elke partitie de query mogelijk een of meer retouren, afhankelijk van de complexiteit van de query, het formaat van pagina geconfigureerd en ingerichte doorvoer van de verzameling. Elke uitvoering retourneert het aantal [aanvraageenheden](request-units.md) verbruikt door het uitvoeren van query's en, optioneel, Uitvoeringsstatistieken query. 
* De SDK voert een samenvatting van de queryresultaten over meerdere partities. Bijvoorbeeld, als de query een ORDER BY over meerdere partities moet, zijn vervolgens resultaten van afzonderlijke partities voor het retourneren van resultaten in een wereldwijd gesorteerde volgorde gesorteerd samenvoegen. Als de query een aggregatie zoals is `COUNT`, waarin de aantallen van afzonderlijke partities te produceren van het totale aantal worden opgeteld.

De SDK's bieden verschillende opties voor het uitvoeren van query's. Bijvoorbeeld, in .NET deze opties zijn beschikbaar in de `FeedOptions` klasse. De volgende tabel beschrijft deze opties en hoe ze invloed op een moment dat de uitvoering van query. 

| Optie | Description |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | Moet worden ingesteld op true voor een query die is vereist om te worden uitgevoerd op meer dan één partitie. Dit is een expliciete vlag waarmee u kunt ontwikkelt zich bewust prestaties tijdens het ontwikkelen. |
| `EnableScanInQuery` | Moet worden ingesteld op true als u ervoor hebt gekozen buiten het indexeren, maar de query toch uitvoeren via een scan. Alleen is van toepassing als indexering voor het filterpad van het opgegeven uitgeschakeld. | 
| `MaxItemCount` | Het maximum aantal items om terug te keren per retour naar de server. Door in te stellen op-1 kunt u de server die het aantal items beheren. Of u kunt deze waarde om op te halen van slechts een beperkt aantal items per retour verlagen. 
| `MaxBufferedItemCount` | Dit is een optie voor de clientzijde, en gebruikt voor het beperken van het geheugenverbruik bij het uitvoeren van meerdere partities ORDER BY. Een hogere waarde vermindert de latentie voor het sorteren van meerdere partities. |
| `MaxDegreeOfParallelism` | Hiermee wordt het aantal gelijktijdige bewerkingen aan de client zijde opgehaald of ingesteld tijdens het uitvoeren van parallelle query's in de Azure Cosmos-database service. Een positieve waarde beperkt het aantal gelijktijdige bewerkingen aan de ingestelde waarde. Als deze is ingesteld op minder dan 0, besluit het systeem automatisch het aantal gelijktijdige bewerkingen om uit te voeren. |
| `PopulateQueryMetrics` | Hiermee gedetailleerde logboekregistratie van statistieken van de tijd die is doorgebracht in verschillende fasen van het uitvoeren van query's, zoals compilatietijd, index-Lustijd en document wordt de tijd. U kunt uitvoer van de querystatistieken delen met ondersteuning van Azure om prestatieproblemen van query. |
| `RequestContinuation` | U kunt uitvoeren van query's hervatten door te geven in de ondoorzichtige vervolgtoken dat wordt geretourneerd door elke query. Het vervolgtoken ingekapseld alle staat is vereist voor het uitvoeren van query's. |
| `ResponseContinuationTokenLimitInKb` | U kunt de maximale grootte van het vervolgtoken dat wordt geretourneerd door de server beperken. Mogelijk moet u dit instellen als uw toepassingshost limieten voor grootte van de antwoord-header heeft. Deze instelling kan vergroten de totale duur en de gebruikte Aanvraageenheden voor de query.  |

Bijvoorbeeld eens een voorbeeld van een query op de partitiesleutel is aangevraagd op een verzameling met `/city` als de partitie en de ingerichte met 100.000 RU/s aan doorvoer. U aanvragen met deze query met `CreateDocumentQuery<T>` in .NET als volgt:

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

De SDK-fragment hierboven, komt overeen met de volgende REST-API-aanvraag:

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

Elke pagina van de uitvoering van query komt overeen met een REST-API `POST` met de `Accept: application/query+json` koptekst en de SQL-query in de hoofdtekst. Elke query maakt een of meer retouren naar de server met de `x-ms-continuation` token gekopieerd tussen de client en server uitvoering te hervatten. De configuratieopties in FeedOptions worden doorgegeven aan de server in de vorm van aanvraagheaders. Bijvoorbeeld, `MaxItemCount` komt overeen met `x-ms-max-item-count`. 

De aanvraag wordt het volgende (afgekapt voor de leesbaarheid) antwoord geretourneerd:

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

De antwoord-headers die door de query zijn geretourneerd, zijn onder andere:

| Optie | Description |
| ------ | ----------- |
| `x-ms-item-count` | Het aantal items in het antwoord geretourneerd. Dit is afhankelijk van de opgegeven `x-ms-max-item-count`, het aantal items dat binnen de maximale reactiegrootte nettolading, de ingerichte doorvoer en de uitvoeringstijd van de query past. |  
| `x-ms-continuation:` | Het vervolgtoken uitvoering van de query te hervatten als er extra resultaten beschikbaar zijn. | 
| `x-ms-documentdb-query-metrics` | De querystatistieken voor de uitvoering. Dit is een tekenreeks met scheidingstekens met statistieken van de tijd besteed aan de verschillende fasen van het uitvoeren van query's. Geretourneerde als `x-ms-documentdb-populatequerymetrics` is ingesteld op `True`. | 
| `x-ms-request-charge` | Het aantal [aanvraageenheden](request-units.md) die worden gebruikt door de query. | 

Zie voor meer informatie over de REST-API-aanvraagheaders en opties [resources met behulp van de REST-API uitvoeren van query's](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api).

## <a name="best-practices-for-query-performance"></a>Aanbevolen procedures voor prestaties van query 's
Hier volgen de meest voorkomende factoren die invloed hebben op prestaties van Azure Cosmos DB-query's. We verdiepen in elk van de volgende onderwerpen in dit artikel.

| Multi-factor Authentication | Tip | 
| ------ | -----| 
| Ingerichte doorvoer | RU's per query meten en zorg ervoor dat u de vereiste ingerichte doorvoer voor uw query's hebt. | 
| Partitioneren en partitiesleutels | Voorkeur voor query's met de waarde voor de partitiesleutel in de filtercomponent voor lage latentie. |
| SDK en query-opties | Volg de aanbevolen procedures van de SDK, zoals directe connectiviteit en afstemmen van de opties voor client-side '-query kan worden uitgevoerd. |
| Netwerklatentie | Account voor netwerk overhead in metingen en multihoming-API's gebruiken om te lezen van de dichtstbijzijnde regio. |
| Indexeringsbeleid | Zorg ervoor dat u de vereiste paden/indexeringsbeleid voor de query hebt. |
| Query uitvoering van metrische gegevens | Analyseer de query-uitvoering van metrische gegevens voor het identificeren van mogelijke regeneraties van query's en vormen.  |

### <a name="provisioned-throughput"></a>Ingerichte doorvoer
In Cosmos DB maakt u containers van gegevens, elk met gereserveerde doorvoer, uitgedrukt in aanvraag aanvraageenheden (RU) per-seconde. Lezen van een document van 1 KB is 1 RU en elke bewerking (met inbegrip van query's) op een vast aantal ru's op basis van de complexiteit is genormaliseerd. Bijvoorbeeld, als er 1000 RU/s ingericht voor uw container en u hebt een query zoals `SELECT * FROM c WHERE c.city = 'Seattle'` die 5 ru's worden verbruikt, u kunt vervolgens (1000 RU/s) / (5 RU/query) = 200 query/s dergelijke query's per seconde. 

Als u meer dan 200 's/sec indient, start de service binnenkomende aanvragen frequentiebeperkende hierboven 200/s. In dit geval de SDK's automatisch verwerken door uit te voeren een uitstel/opnieuw proberen en daarom ziet u een hogere latentie voor deze query's. Vergroten van de ingerichte doorvoer voor de vereiste waarde verbetert uw Querylatentie en doorvoer. 

Zie voor meer informatie over aanvraageenheden, [Aanvraageenheden](request-units.md).

### <a name="partitioning-and-partition-keys"></a>Partitioneren en partitiesleutels
Met Azure Cosmos DB voeren normaal gesproken query's in de volgende volgorde van de snelste/meest efficiënt naar tragere/minder efficiënt. 

* OPHALEN op een enkele partitiesleutel en Itemsleutel
* Query's uitvoeren met een filterclausule op één partitiesleutel
* Query uitvoeren zonder dat een filtercomponent gelijkheid of het bereik op een eigenschap
* Query's uitvoeren zonder filters

Query's die u moeten raadplegen over alle partities moeten hogere latentie en hogere ru's kunnen gebruiken. Omdat elke partitie heeft de automatische indexering voor alle eigenschappen, kan de query efficiënt worden uitgevoerd in de index in dit geval. U kunt query's die sneller partities omvatten met behulp van de opties voor parallelle uitvoering.

Zie voor meer informatie over partitionering en partitiesleutels [partitionering in Azure Cosmos DB](partition-data.md).

### <a name="sdk-and-query-options"></a>SDK en query-opties
Zie [tips voor betere prestaties](performance-tips.md) en [prestatietests](performance-testing.md) voor informatie over het ophalen van de beste prestaties voor de clientzijde van Azure Cosmos DB. Dit omvat met de nieuwste SDK's, het configureren van platform-specifieke configuraties, zoals het standaardaantal verbindingen, frequentie van de garbagecollection, en met behulp van eenvoudige connectiviteitsopties, zoals Direct/TCP. 


#### <a name="max-item-count"></a>Maximum aantal items
Voor query's, de waarde van `MaxItemCount` kan een aanzienlijke invloed hebben op het uitvoeren van query's end-to-end. Elke retour naar de server retourneert niet vaker dan het aantal items in `MaxItemCount` (standaard 100 items). Deze instelling op een hogere waarde (-1 is maximum- en aanbevolen) uw totale queryduur van de wordt verbeterd door het beperken van het aantal retouren tussen server en client, met name voor query's met grote resultatensets.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>Maximale graad van parallelle uitvoering
Optimaliseren voor query's, de `MaxDegreeOfParallelism` voor het identificeren van de aanbevolen configuraties voor uw toepassing, met name als u een partitieoverkoepelende query's (zonder een filter op de waarde van de partitiesleutel) uitvoeren. `MaxDegreeOfParallelism`  Hiermee bepaalt u het maximum aantal parallelle taken, dat wil zeggen, het maximum van partities parallel wordt bezocht. 

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

Stel dat
* D = standaard Maximum aantal parallelle taken (= totaal aantal processor in de client-computer)
* P = gebruiker opgegeven maximale aantal parallelle taken
* N = aantal partities dat moet worden bezocht voor het beantwoorden van een query

Hieronder vindt u de gevolgen van hoe de parallelle query's voor verschillende waarden van P. zou werken
* (P == 0) = > seriële modus
* (P == 1) = > maximaal één taak
* (P > 1) = > parallelle taken Min (P, N) 
* (P < 1) = > parallelle taken Min (N, D)

SDK-releaseopmerkingen en Zie details over geïmplementeerde klassen en methoden [SQL-SDK's](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>Netwerklatentie
Zie [Azure DB Cosmos globale distributie](tutorial-global-distribution-sql-api.md) voor informatie over het instellen van wereldwijde distributie en maak verbinding met de dichtstbijzijnde regio. Wanneer u meerdere retouren maken of ophalen van de query een grote resultatenset nodig hebt, netwerklatentie een aanzienlijke invloed heeft op de prestaties van query's. 

De sectie over query uitvoering van metrische gegevens wordt uitgelegd hoe u de servertijden van de uitvoering van query's worden opgehaald ( `totalExecutionTimeInMs`), zodat u tijd besteed aan het uitvoeren van query's en -tijd besteed aan het netwerk onderweg kunt onderscheiden.

### <a name="indexing-policy"></a>Indexeringsbeleid
Zie [indexeringsbeleid configureren](index-policy.md) voor indexering paden, soorten en modi en hoe ze invloed op de uitvoering van de query. Het indexeringsbeleid gebruikt standaard hash-indexering voor tekenreeksen, die geldt voor gelijkheid query's, maar niet voor de bereik-query's / OrderBy-query's. Als u een bereik-query's nodig voor tekenreeksen, wordt u aangeraden het indextype bereik voor alle tekenreeksen op te geven. 

Azure Cosmos DB wordt standaard automatisch geïndexeerd toegepast op alle gegevens. Overweeg het uitsluiten van paden voor scenario's met hoge prestaties, omdat hierdoor de RU-kosten voor elke invoeg bewerking worden verminderd. 

## <a name="query-execution-metrics"></a>Query uitvoering van metrische gegevens
U kunt gedetailleerde metrische gegevens over het uitvoeren van query's verkrijgen door de optionele `x-ms-documentdb-populatequerymetrics` header (`FeedOptions.PopulateQueryMetrics` in de .NET SDK). De waarde die wordt geretourneerd `x-ms-documentdb-query-metrics` heeft de volgende sleutel-waardeparen bedoeld voor geavanceerde probleemoplossing van het uitvoeren van query's. 

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

| Gegevens | Eenheid | Description | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | milliseconden | Tijd waarop de query-uitvoering | 
| `queryCompileTimeInMs` | milliseconden | Query-compilatie  | 
| `queryLogicalPlanBuildTimeInMs` | milliseconden | Tijd voor het bouwen van logische queryplan | 
| `queryPhysicalPlanBuildTimeInMs` | milliseconden | Tijd voor het bouwen van fysieke queryplan | 
| `queryOptimizationTimeInMs` | milliseconden | Tijd besteed aan het in de optimalisatie van query | 
| `VMExecutionTimeInMs` | milliseconden | Tijd besteed aan de query-runtime | 
| `indexLookupTimeInMs` | milliseconden | Tijd besteed aan fysieke index laag | 
| `documentLoadTimeInMs` | milliseconden | Tijd besteed aan het laden van documenten  | 
| `systemFunctionExecuteTimeInMs` | milliseconden | Totale tijd besteed aan het uitvoerende system (ingebouwde) functies in milliseconden  | 
| `userFunctionExecuteTimeInMs` | milliseconden | Totale tijd besteed aan het uitvoeren door de gebruiker gedefinieerde functies in milliseconden | 
| `retrievedDocumentCount` | count | Totaal aantal opgehaalde documenten  | 
| `retrievedDocumentSize` | bytes | Totale grootte van de opgehaalde documenten in bytes  | 
| `outputDocumentCount` | count | Aantal uitvoerdocumenten dat | 
| `writeOutputTimeInMs` | milliseconden | Tijd in milliseconden voor uitvoering van query | 
| `indexUtilizationRatio` | verhouding (< = 1) | Verhouding van het aantal documenten dat overeenkomt met het filter aan het aantal documenten worden geladen  | 

De client-SDK's kunnen meerdere querybewerkingen voor het bieden van de query binnen elke partitie intern aanbrengen. Maakt de client meer dan één aanroep per partitie als het totale aantal resultaten overschrijdt `x-ms-max-item-count`, als de query de ingerichte doorvoer voor de partitie overschrijdt of als de nettolading van de query de maximale grootte per pagina bereikt, of als de query heeft bereikt van het systeem toegewezen time-outlimiet. Elke uitvoering van de gedeeltelijke query retourneert een `x-ms-documentdb-query-metrics` voor die pagina. 

Hier volgen enkele voorbeeldquery's en over het interpreteren van de metrische gegevens die door uitvoering van de query zijn geretourneerd: 

| Query’s uitvoeren | Voorbeeld van metrische gegevens | Description | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | Het aantal documenten die zijn opgehaald, is meer dan 100 1 zodat deze overeenkomen met de TOP-component. Querytijd is voornamelijk besteed in `WriteOutputTime` en `DocumentLoadTime` omdat het een scan. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount is nu hoger (500 + 1 zodat deze overeenkomen met de TOP-component). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | Over 0,9 ms is besteed in IndexLookupTime voor een opzoeken van een sleutel, omdat het opzoeken van een index op `/N/?`. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | Iets meer tijd (versie 1.7 ms) die is doorgebracht in IndexLookupTime via een scan bereik omdat het opzoeken van een index op `/N/?`. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | Dezelfde tijd besteed aan `DocumentLoadTime` als de voorgaande query's, maar lagere `WriteOutputTime` omdat we bij het projecteren van slechts één eigenschap. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | Over 213 ms is besteed in `UserDefinedFunctionExecutionTime` de UDF uitvoeren op elke waarde van `c.N`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | Ongeveer 0,6 ms is besteed in `IndexLookupTime` op `/Name/?`. De query-uitvoeringstijd (ms ~ 7) in de meeste `SystemFunctionExecutionTime`. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | Query wordt uitgevoerd als een scan omdat hierbij `LOWER`, en 500 buiten 2491 opgehaalde documenten worden geretourneerd. |


## <a name="next-steps"></a>Volgende stappen
* Zie voor meer informatie over de ondersteunde SQL-query-operators en trefwoorden, [SQL-query](sql-query-getting-started.md). 
* Zie voor meer informatie over aanvraageenheden, [aanvraageenheden](request-units.md).
* Zie voor meer informatie over het indexeringsbeleid, [indexeringsbeleid](index-policy.md) 


