---
title: 'Azure Cosmos DB: SQL Node.js API, SDK & resources'
description: Lees alles over de SQL Node.js API en SDK, inclusief releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB Node.js SDK.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 03f79535b3a62fbb4d0309ae86a142bd842cc308
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982885"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK voor SQL API: notities en bronnen vrijgeven
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-wijzigingsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resourceprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulkexecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk executor - Java](sql-api-sdk-bulk-executor-java.md)

|Resource  |Koppeling  |
|---------|---------|
|SDK downloaden  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API-documentatie  |  [JavaScript SDK-referentiedocumentatie](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Installatie-instructies voor SDK  |  [Installatie-instructies](https://github.com/Azure/azure-cosmos-js#installation)
|Bijdragen aan SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Voorbeelden | [Node.js code samples](sql-api-nodejs-samples.md)
| Zelfstudie aan de slag | [Aan de slag met de JavaScript SDK](sql-api-nodejs-get-started.md)
| Zelfstudie voor web-apps | [Een Node.js-webtoepassing bouwen met Azure Cosmos DB](sql-api-nodejs-application.md)
| Huidig ondersteund platform | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) - SDK-versie 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - SDK-versie 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - SDK-versie 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - SDK-versie 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)- SDK Versie 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)- SDK Versie 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)- SDK Versie 1.x.x

## <a name="release-notes"></a>Releaseopmerkingen

### <a name=""></a><a name="3.1.0"/>3.1.0</a>
* Stel standaard ResponseContinuationTokenLimitInKB in op 1 kb. Standaard beperken we dit tot 1 kb om lange headers te voorkomen (Node.js heeft een globale header grootte limiet). Een gebruiker kan dit veld zo instellen dat er langere kopteksten zijn, waardoor de backend de query-uitvoering kan optimaliseren.
* DisableSSLVerification verwijderen. Deze optie heeft nieuwe alternatieven beschreven in [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name=""></a><a name="3.0.4"/>3.0.4</a>
* InitialHeaders expliciet de hoofdtekst van de partitiesleutel toestaan
* Gebruik package.json#files om te voorkomen dat externe bestanden worden gepubliceerd
* Fout met het sorteren van routeringskaarten oplossen op oudere versie van knooppunt+v8
* Lost bug op wanneer gebruiker gedeeltelijke opties voor nieuwe poging levert

### <a name=""></a><a name="3.0.3"/>3.0.3</a>
* Voorkomen dat Webpack modules oplost die met vereisen worden aangeroepen

### <a name=""></a><a name="3.0.2"/>3.0.2</a>
* Lost een lange uitstaande bug waar RU's werden altijd gerapporteerd als 0 voor geaggregeerde query's

### <a name=""></a><a name="3.0.0"/>3.0.0</a>

🎉 v3 release! 🎉 Veel nieuwe functies, bugfixes en een paar baanbrekende wijzigingen. Primaire doelen van deze release:

* Belangrijke nieuwe functies implementeren
  * AFZONDERLIJKE query's
  * LIMIT/OFFSET query's
  * Gebruiker annuleerbare verzoeken
* Update naar de nieuwste Cosmos REST API-versie waar alle containers onbeperkt schalen
* Maak het gebruik van Cosmos gemakkelijker vanuit de browser
* Beter afstemmen op de nieuwe Azure JS SDK-richtlijnen

#### <a name="migration-guide-for-breaking-changes"></a>Migratiegids voor het verbreken van wijzigingen
##### <a name="improved-client-constructor-options"></a>Verbeterde opties voor clientconstructor

Constructor opties zijn vereenvoudigd:

* masterKey is omgedoopt tot sleutel en verplaatst naar het hoogste niveau
* Eigenschappen die voorheen onder options.auth staan, zijn verplaatst naar het hoogste niveau

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>Vereenvoudigde API voor queryiterator
In v2 waren er veel verschillende manieren om resultaten van een query te herhalen of op te halen. We hebben geprobeerd om de v3-API te vereenvoudigen en vergelijkbare of dubbele API's te verwijderen:

* Verwijder iterator.next() en iterator.current(). Gebruik fetchNext() om pagina's met resultaten te krijgen.
* Verwijder iterator.forEach(). Gebruik in plaats daarvan async iterators.
* iterator.executeNext() omgedoopt tot iterator.fetchNext()
* iterator.toArray() omgedoopt tot iterator.fetchAll()
* Pagina's zijn nu de juiste antwoordobjecten in plaats van platte JS-objecten
* const container = client.database(dbId).container(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>Vaste containers zijn nu verdeeld
De Cosmos-service ondersteunt nu partitiesleutels op alle containers, inclusief de containers die eerder als vaste containers zijn gemaakt. De v3 SDK werkt bij aan de nieuwste API-versie die deze wijziging implementeert, maar deze is niet brekend. Als u geen partitiesleutel voor bewerkingen levert, worden we standaard gebruikt voor een systeemsleutel die werkt met al uw bestaande containers en documenten.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert verwijderd voor opgeslagen procedures
Voorheen was upsert toegestaan voor niet-partitieverzamelingen, maar met de API-versie-update worden alle verzamelingen verdeeld, zodat we deze volledig hebben verwijderd.

##### <a name="item-reads-will-not-throw-on-404"></a>Item leest zal niet gooien op 404
const container = client.database(dbId).container(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Standaard schrijven in meerdere regio's
De SDK schrijft nu standaard naar meerdere regio's als uw Cosmos-configuratie dit ondersteunt. Dit was eerder opt-in gedrag.

##### <a name="proper-error-objects"></a>Juiste foutobjecten
Mislukte aanvragen gooien nu de juiste fout of subklassen van fout. Voorheen gooiden ze platte JS-objecten.

#### <a name="new-features"></a>Nieuwe functies
##### <a name="user-cancelable-requests"></a>Door de gebruiker te annuleren aanvragen
De move to fetch intern stelt ons in staat om de browser AbortController API te gebruiken om door de gebruiker te annuleren bewerkingen te ondersteunen. In het geval van bewerkingen waarbij mogelijk meerdere aanvragen worden uitgevoerd (zoals kruispartitiequery's), worden alle aanvragen voor de bewerking geannuleerd. Moderne browsergebruikers hebben al AbortController. Node.js-gebruikers moeten een polyfill-bibliotheek gebruiken

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Doorvoer instellen als onderdeel van db/container create-bewerking
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
Header token generatie werd opgesplitst in @azure/cosmos-signeen nieuwe bibliotheek, . Iedereen die de Cosmos REST API rechtstreeks aanroept, kan dit @azure/cosmosgebruiken om kopteksten te ondertekenen met dezelfde code die we binnenin aanroepen.

##### <a name="uuid-for-generated-ids"></a>UUID voor gegenereerde id's
v2 had aangepaste code om artikel-id's te genereren. We zijn overgestapt naar de bekende en onderhouden gemeenschapsbibliotheek uuid.

##### <a name="connection-strings"></a>Verbindingsreeksen
Het is nu mogelijk om een verbindingstekenreeks te passeren die is gekopieerd van de Azure-portal:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Verbeterde browserervaring
Hoewel het mogelijk was om de v2 SDK in de browser te gebruiken, was het geen ideale ervaring. Je moest polyfill verschillende node.js ingebouwde bibliotheken en gebruik maken van een bundeling zoals Webpack of Parcel. De v3 SDK maakt de out of the box ervaring veel beter voor browsergebruikers.

* Aanvraaginternals vervangen door ophalen (#245)
* Gebruik van buffer verwijderen (#330)
* Verwijder node builtin gebruik ten gunste van universele pakketten / API's (#328)
* Overschakelen naar node-abort-controller (#294)

#### <a name="bug-fixes"></a>Opgeloste fouten
* Fix aanbieding lezen en terug te brengen aanbieding tests (#224)
* EnableEndpointDiscovery oplossen (#207)
* Ontbrekende R's herstellen op paginated resultaten (#360)
* SQL-queryparametertype uitvouwen (#346)
* Ttl toevoegen aan ObjectDefinition (#341)
* Cp-querystatistieken (#311) oplossen
* ActivityId toevoegen aan FeedResponse (#293)
* _ts type van tekenreeks naar getal (#252)(#295)
* Aggregatie van aanvraagkosten (#289)
* Lege tekenreekspartitietoetsen toestaan (#277)
* Tekenreeks toevoegen aan conflictquerytype (#237)
* Uniek KeyPolicy toevoegen aan container (#234)

#### <a name="engineering-systems"></a>Technische systemen
Niet altijd de meest zichtbare veranderingen, maar ze helpen ons team schip betere code, sneller.

* Rollup gebruiken voor productiebuilds (#104)
* Update naar Typescript 3.5 (#327)
* Converteren naar TS-projectreferenties. Testmap extraheren (#270)
* NoUnusedLocals en noUnusedParameters (#275) inschakelen
* Azure Pipelines YAML voor CI-builds (#298)

### <a name=""></a><a name="2.1.5"/>2.1.5</a>
* Geen code wijzigingen. Lost een probleem op waarbij sommige extra bestanden in het 2.1.4-pakket zijn opgenomen.

### <a name=""></a><a name="2.1.4"/>2.1.4</a>
* Regionale failover oplossen binnen het beleid voor nieuwe try's
* Fix ChangeFeed hasMoreResults, eigenschap
* Dev-afhankelijkheidsupdates
* PolicheckExclusions.txt toevoegen

### <a name=""></a><a name="2.1.3"/>2.1.3</a>
* _ts type van tekenreeks naar getal schakelen
* Standaardindexeringstests oplossen
* Backport uniqueKeyPolicy naar v2
* Demo- en demofoutfoutoplossingen

### <a name=""></a><a name="2.1.2"/>2.1.2</a>
* Backport bieden fixes van v3-tak
* Bug oplossen in typehandtekening executeNext()
* Typofixes

### <a name=""></a><a name="2.1.1"/>2.1.1</a>
* Bouw herstructurering. Hiermee u de SDK-versie tijdens het bouwen trekken.

### <a name=""></a><a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>Nieuwe functies
* ChangeFeed-ondersteuning toegevoegd (#196)
* MultiPolygon-gegevenstype toegevoegd voor indexering (#191)
* Eigenschap 'sleutel' toevoegen aan constructor als alias voor masterKey (#202)

#### <a name="fixes"></a>Oplossingen
* Bug oplossen waarbij next() onjuiste waarde op iterator retourafmeerde

#### <a name="engineering-improvements"></a>Technische verbeteringen
* Integratietest toevoegen voor typescriptverbruik (#199)
* Direct installeren vanaf GitHub inschakelen (#194)

### <a name=""></a><a name="2.0.5"/>2.0.5</a>
* Hiermee voegt u de interface voor het type knooppuntagent toe. Typescriptgebruikers hoeven niet @types/node langer als afhankelijkheid te installeren
* Voorkeurslocaties zijn nu goed gehonoreerd
* Verbeteringen aan het bijdragen van ontwikkelaarsdocumentatie
* Verschillende typofixes

### <a name=""></a><a name="2.0.4"/>2.0.4</a>
* Oplossingstypedefinitieprobleem geïntroduceerd in punt 2.0.3

### <a name=""></a><a name="2.0.3"/>2.0.3</a>
* Afhankelijkheid `big-integer` verwijderen
* Overschakelen naar referentierichtlijnen voor AsyncIterable-type. Typescriptgebruikers hoeven hun 'lib'-instelling niet langer aan te passen.
* Typo Fixes

### <a name=""></a><a name="2.0.2"/>2.0.2</a>
* Leesme-koppelingen oplossen

### <a name=""></a><a name="2.0.1"/>2.0.1</a>
* Implementatie van de interface opnieuw proberen oplossen

### <a name=""></a><a name="2.0.0"/>2.0.0</a>
* GA van versie 2.0.0 van de JavaScript SDK
* Added support for multi-region writes.

### <a name=""></a><a name="2.0.0-3"/>2.0.0-3</a>
* RC1 van versie 2.0.0 van de JavaScript SDK voor openbare preview.
* Nieuw objectmodel, met cosmosclient op het hoogste niveau en methoden die zijn verdeeld over relevante database-, container- en itemklassen. 
* Steun voor [beloften.](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises) 
* SDK geconverteerd naar TypeScript.

### <a name=""></a><a name="1.14.4"/>1.14.4</a>
* npm documentatie vastgesteld.

### <a name=""></a><a name="1.14.3"/>1.14.3</a>
* Ondersteuning toegevoegd voor standaardpogingen voor verbindingsproblemen.
* Ondersteuning toegevoegd voor het lezen van de feed voor het wijzigen van verzamelingen.
* Fixed session consistency bug that intermittently caused "read session not available".
* Ondersteuning toegevoegd voor querystatistieken.
* Gewijzigd http Agent's maximum aantal verbindingen.

### <a name=""></a><a name="1.14.2"/>1.14.2</a>
* Bijgewerkte documentatie om te verwijzen naar Azure Cosmos DB in plaats van Azure DocumentDB.
* Ondersteuning toegevoegd voor de instelling ProxyUrl in Verbindingsbeleid.

### <a name=""></a><a name="1.14.1"/>1.14.1</a>
* Kleine oplossing voor hoofdlettergevoelige bestandssystemen.

### <a name=""></a><a name="1.14.0"/>1.14.0</a>
* Voegt ondersteuning toe voor sessieconsistentie.
* Deze SDK-versie vereist de nieuwste versie van [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator).

### <a name=""></a><a name="1.13.0"/>1.13.0</a>
* Gesplitste kruispartitiequery's.
* Voegt ondersteuning toe voor resourcekoppeling met voorloop- en slepende slashes (en bijbehorende tests).

### <a name=""></a><a name="1.12.2"/>1.12.2</a>
*    npm documentatie vastgesteld.

### <a name=""></a><a name="1.12.1"/>1.12.1</a>
* Fixed a bug in executeStoredProcedure where documents involved had special Unicode characters (LS, PS).
* Fixed a bug in handling documents with Unicode characters in the partition key.
* Vaste ondersteuning voor het maken van collecties met de naammedia. GitHub probleem #114.
* Fixed support for permission authorization token. GitHub probleem #178.

### <a name=""></a><a name="1.12.0"/>1.12.0</a>
* Ondersteuning toegevoegd voor een nieuw [consistentieniveau](consistency-levels.md) genaamd ConsistentPrefix.
* Ondersteuning toegevoegd voor UriFactory.
* Fixed a Unicode support bug. GitHub probleem #171.

### <a name=""></a><a name="1.11.0"/>1.11.0</a>
* De ondersteuning voor aggregatiequery's toegevoegd (COUNT, MIN, MAX, SOM en AVG).
* Toegevoegd de optie voor het regelen van de mate van parallellisme voor cross partitie query's.
* De optie toegevoegd voor het uitschakelen van TLS-verificatie wanneer u deze uitvoert tegen Azure Cosmos DB Emulator.
* Verlaagde minimale doorvoer op partitieverzamelingen van 10.100 RU/s naar 2500 RU/s.
* Fixed the continuation token bug for single partition collection. GitHub probleem #107.
* Fixed the executeStoredProcedure bug in handling 0 as single param. GitHub probleem #155.

### <a name=""></a><a name="1.10.2"/>1.10.2</a>
* Fixed user-agent header to include the SDK version.
* Kleine code opruimen.

### <a name=""></a><a name="1.10.1"/>1.10.1</a>
* TLS-verificatie uitschakelen wanneer u de SDK gebruikt om de emulator (hostname=localhost) te targeten.
* Added support for enabling script logging during stored procedure execution.

### <a name=""></a><a name="1.10.0"/>1.10.0</a>
* Ondersteuning toegevoegd voor parallelle kruispartitiequery's.
* Ondersteuning toegevoegd voor TOP/ORDER BY-query's voor partitieverzamelingen.

### <a name=""></a><a name="1.9.0"/>1.9.0</a>
* Ondersteuning voor het beleid opnieuw proberen voor geweigerde aanvragen. (Throttled-aanvragen ontvangen een aanvraagpercentage met een te grote uitzondering, foutcode 429.) Azure Cosmos DB probeert standaard negen keer voor elk verzoek wanneer foutcode 429 wordt aangetroffen, ter ere van de retryAfter-tijd in de antwoordkop. Een vaste intervaltijd voor opnieuw proberen kan nu worden ingesteld als onderdeel van de eigenschap RetryOptions op het object Verbindingsbeleid als u de nieuwe poging wilt negerenNa de tijd die door de server tussen de nieuwe pogingen is geretourneerd. Azure Cosmos DB wacht nu maximaal 30 seconden op elk verzoek dat wordt beperkt (ongeacht het aantal nieuwe try's) en retourneert het antwoord met foutcode 429. Deze tijd kan ook worden overschreven in de eigenschap RetryOptions op het object Verbindingsbeleid.
* Cosmos DB retourneert nu x-ms-throttle-retry-count en x-ms-throttle-retry-wait-time-ms als de responsheaders in elk verzoek om het gaspedaal opnieuw te tellen en de cumulatieve tijd die het verzoek wachtte tussen de pogingen aan te geven.
* De klasse RetryOptions is toegevoegd, waarbij de eigenschap RetryOptions wordt weergegeven in de klasse ConnectionPolicy die kan worden gebruikt om een aantal opties voor het opnieuw proberen te overschrijven.

### <a name=""></a><a name="1.8.0"/>1.8.0</a>
* De ondersteuning voor databaseaccounts met meerdere regio's toegevoegd.

### <a name=""></a><a name="1.7.0"/>1.7.0</a>
* Toegevoegd de ondersteuning voor Time To Live (TTL) functie voor documenten.

### <a name=""></a><a name="1.6.0"/>1.6.0</a>
* Geïmplementeerde [gepartitioneerde verzamelingen](partition-data.md) en [door de gebruiker gedefinieerde prestatieniveaus](performance-levels.md).

### <a name=""></a><a name="1.5.6"/>1.5.6</a>
* Fixed RangePartitionResolver.resolveForRead bug where it was not return links due to a bad concat of results.

### <a name=""></a><a name="1.5.5"/>1.5.5</a>
* Fixed hashPartitionResolver resolveForRead(): When no partition key supplied was throwing exception, instead of returning a list of all registered links.

### <a name=""></a><a name="1.5.4"/>1.5.4</a>
* Probleem [oplossen #100](https://github.com/Azure/azure-documentdb-node/issues/100) - Dedicated HTTPS Agent: Vermijd het wijzigen van de globale agent voor Azure Cosmos DB-doeleinden. Gebruik een speciale agent voor alle verzoeken van de lib.

### <a name=""></a><a name="1.5.3"/>1.5.3</a>
* Oplossing son [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - Goed verwerken streepjes in media-id's.

### <a name=""></a><a name="1.5.2"/>1.5.2</a>
* Lost probleem [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter listener lek waarschuwing.

### <a name=""></a><a name="1.5.1"/>1.5.1</a>
* Oplossing se [probleem #92](https://github.com/Azure/azure-documentdb-node/issues/90) - hernoem map Hash naar hash voor case-sensitive systemen.

### <a name=""></a><a name="1.5.0"/>1.5.0</a>
* Implementeer ondersteuning voor sharding door hash toe te voegen & bereikpartitieresolvers.

### <a name=""></a><a name="1.4.0"/>1.4.0</a>
* Implementeer Upsert. Nieuwe upsertXXX-methoden op documentClient.

### <a name=""></a><a name="1.3.0"/>1.3.0</a>
* Overgeslagen om versienummers in lijn te brengen met andere SDK's.

### <a name=""></a><a name="1.2.2"/>1.2.2</a>
* Split Q belooft wrapper naar nieuwe repository.
* Update naar pakketbestand voor npm-register.

### <a name=""></a><a name="1.2.1"/>1.2.1</a>
* Implementeert ID Based Routing.
* Oplossing se [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - huidige eigenschap in strijd is met de huidige methode().

### <a name=""></a><a name="1.2.0"/>1.2.0</a>
* Ondersteuning toegevoegd voor de GeoSpatial-index.
* Valideert id-eigenschap voor alle resources. Id's voor resources kunnen geen ?, /, #, &#47;&#47;, tekens bevatten of eindigen met een spatie.
* Hiermee voegt u nieuwe koptekst "indextransformatievoortgang" toe aan ResourceResponse.

### <a name=""></a><a name="1.1.0"/>1.1.0</a>
* Implementeert V2 indexeringsbeleid.

### <a name=""></a><a name="1.0.3"/>1.0.3</a>
* Probleem [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - Geïmplementeerde eslint- en gruntconfiguraties in de kern en beloven SDK.

### <a name=""></a><a name="1.0.2"/>1.0.2</a>
* Probleem [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - De wrapper Beloften bevat geen header met fout.

### <a name=""></a><a name="1.0.1"/>1.0.1</a>
* Geïmplementeerde mogelijkheid om conflicten op te vragen door leesconflicten, leesConflictAsync en queryConflicten toe te voegen.
* Bijgewerkte API-documentatie.
* Probleem [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync-fout.

### <a name=""></a><a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Release & pensioendata
Microsoft biedt ten minste **12 maanden** van tevoren een melding om een SDK met pensioen te laten gaan om de overgang naar een nieuwere/ondersteunde versie soepel te laten verlopen.

Nieuwe functies en functionaliteit en optimalisaties worden alleen toegevoegd aan de huidige SDK, als zodanig wordt aanbevolen dat u altijd zo vroeg mogelijk een upgrade uitvoert naar de nieuwste SDK-versie.

Elk verzoek aan Cosmos DB met behulp van een gepensioneerde SDK wordt door de service afgewezen.

> [!WARNING]
> Alle versies **1.x** van de Node client SDK voor SQL API worden op **30 augustus 2020**buiten gebruik gesteld. Dit heeft alleen invloed op de Client-side Node SDK en heeft geen invloed op server-side scripts (opgeslagen procedures, triggers en UDFs).
> 
>
<br/>

| Versie | Releasedatum | Pensioendatum |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26 juli 2019 |--- |
| [3.0.4](#3.0.4) |22 juli 2019 |--- |
| [3.0.3](#3.0.3) |17 juli 2019 |--- |
| [3.0.2](#3.0.2) |9 juli 2019 |--- |
| [3.0.0](#3.0.0) |28 juni 2019 |--- |
| [2.1.5](#2.1.5) |20 maart 2019 |--- |
| [2.1.4](#2.1.4) |15 maart 2019 |--- |
| [2.1.3](#2.1.3) |8 maart 2019 |--- |
| [2.1.2](#2.1.2) |28 januari 2019 |--- |
| [2.1.1](#2.1.1) |5 december 2018 |--- |
| [2.1.0](#2.1.0) |4 december 2018 |--- |
| [2.0.5](#2.0.5) |7 november 2018 |--- |
| [2.0.4](#2.0.4) |30 oktober 2018 |--- |
| [2.0.3](#2.0.3) |30 oktober 2018 |--- |
| [2.0.2](#2.0.2) |10 oktober 2018 |--- |
| [2.0.1](#2.0.1) |25 september 2018 |--- |
| [2.0.0](#2.0.0) |September 24, 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 augustus 2018 |--- |
| [1.14.4](#1.14.4) |03 mei 2018 |30 augustus 2020 |
| [1.14.3](#1.14.3) |03 mei 2018 |30 augustus 2020 |
| [1.14.2](#1.14.2) |21 december 2017 |30 augustus 2020 |
| [1.14.1](#1.14.1) |10 november 2017 |30 augustus 2020 |
| [1.14.0](#1.14.0) |9 november 2017 |30 augustus 2020 |
| [1.13.0](#1.13.0) |11 oktober 2017 |30 augustus 2020 |
| [1.12.2](#1.12.2) |10 augustus 2017 |30 augustus 2020 |
| [1.12.1](#1.12.1) |10 augustus 2017 |30 augustus 2020 |
| [1.12.0](#1.12.0) |10 mei 2017 |30 augustus 2020 |
| [1.11.0](#1.11.0) |16 maart 2017 |30 augustus 2020 |
| [1.10.2](#1.10.2) |27 januari 2017 |30 augustus 2020 |
| [1.10.1](#1.10.1) |22 december 2016 |30 augustus 2020 |
| [1.10.0](#1.10.0) |03 oktober 2016 |30 augustus 2020 |
| [1.9.0](#1.9.0) |07 juli 2016 |30 augustus 2020 |
| [1.8.0](#1.8.0) |14 juni 2016 |30 augustus 2020 |
| [1.7.0](#1.7.0) |26 april 2016 |30 augustus 2020 |
| [1.6.0](#1.6.0) |29 maart 2016 |30 augustus 2020 |
| [1.5.6](#1.5.6) |08 maart 2016 |30 augustus 2020 |
| [1.5.5](#1.5.5) |02 februari 2016 |30 augustus 2020 |
| [1.5.4](#1.5.4) |01 februari 2016 |30 augustus 2020 |
| [1.5.2](#1.5.2) |26 januari 2016 |30 augustus 2020 |
| [1.5.2](#1.5.2) |22 januari 2016 |30 augustus 2020 |
| [1.5.1](#1.5.1) |4 januari 2016 |30 augustus 2020 |
| [1.5.0](#1.5.0) |31 december 2015 |30 augustus 2020 |
| [1.4.0](#1.4.0) |06 oktober 2015 |30 augustus 2020 |
| [1.3.0](#1.3.0) |06 oktober 2015 |30 augustus 2020 |
| [1.2.2](#1.2.2) |10 september 2015 |30 augustus 2020 |
| [1.2.1](#1.2.1) |15 augustus 2015 |30 augustus 2020 |
| [1.2.0](#1.2.0) |05 augustus 2015 |30 augustus 2020 |
| [1.1.0](#1.1.0) |09 juli 2015 |30 augustus 2020 |
| [1.0.3](#1.0.3) |04 juni 2015 |30 augustus 2020 |
| [1.0.2](#1.0.2) |23 mei 2015 |30 augustus 2020 |
| [1.0.1](#1.0.1) |15 mei 2015 |30 augustus 2020 |
| [1.0.0](#1.0.0) |08 april 2015 |30 augustus 2020 |

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie Microsoft Azure Cosmos [DB-servicepagina](https://azure.microsoft.com/services/cosmos-db/) voor meer informatie over Cosmos DB.

