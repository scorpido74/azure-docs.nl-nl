---
title: 'Azure Cosmos DB: SQL node. js-API, SDK-& resources'
description: Meer informatie over de SQL node. js-API en SDK, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB node. js-SDK.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 27b548d03e6b05179da744e636a5c887e6b01ad5
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624680"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB node. js SDK voor SQL-API: Release opmerkingen en bronnen
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-Wijzigingenfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resourceprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk-uitvoerder-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk-uitvoerder-java](sql-api-sdk-bulk-executor-java.md)

|Resource  |Koppelen  |
|---------|---------|
|SDK downloaden  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API-documentatie  |  [Naslag documentatie voor Java script SDK](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|SDK-installatie-instructies  |  [Installatie-instructies](https://github.com/Azure/azure-cosmos-js#installation)
|Bijdragen aan SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Voorbeelden | [Voor beelden van node. js-code](sql-api-nodejs-samples.md)
| Zelf studie aan de slag | [Aan de slag met de Java script-SDK](sql-api-nodejs-get-started.md)
| Zelf studie voor web-apps | [Een node. js-webtoepassing bouwen met Azure Cosmos DB](sql-api-nodejs-application.md)
| Huidig ondersteund platform | [Node. js V12. x](https://nodejs.org/en/blog/release/v12.7.0/) -SDK versie 3. x. x<br/>[Node. js V10 toevoegen. x](https://nodejs.org/en/blog/release/v10.6.0/) -SDK versie 3. x. x<br/>[Node. js V8. x](https://nodejs.org/en/blog/release/v8.16.0/) -SDK versie 3. x. x<br/>[Node. js V6. x](https://nodejs.org/en/blog/release/v6.10.3/) -SDK versie 2. x. x<br/>[Node. js v 4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)-SDK-versie 1. x. x<br/> [Node. js v 0.12](https://nodejs.org/en/blog/release/v0.12.0/)-SDK-versie 1. x. x<br/> [Node. js v 0,10](https://nodejs.org/en/blog/release/v0.10.0/)-SDK-versie 1. x. x

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="3.1.0"/>3.1.0</a>
* Stel de standaard ResponseContinuationTokenLimitInKB in op 1 KB. Standaard wordt dit in 1 KB om te voor komen dat lange headers (node. js een globale header-grootte heeft). Een gebruiker kan dit veld zo instellen dat er langere kopteksten worden toegestaan. Dit kan helpen bij het optimaliseren van query's.
* Verwijder disableSSLVerification. Bij deze optie worden nieuwe alternatieven beschreven in [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name="3.0.4"/>3.0.4</a>
* InitialHeaders toestaan om de partitie sleutel header expliciet in te stellen
* Package. json #-bestanden gebruiken om te voor komen dat externe bestanden worden gepubliceerd
* Sorteer fout voor routerings toewijzing herstellen voor oudere versie van knoop punt + V8
* Lost een probleem op wanneer de gebruiker opties voor gedeeltelijke nieuwe pogingen oplevert

### <a name="3.0.3"/>3.0.3</a>
* Voor komen dat webpakket voor het omzetten van modules met de naam vereist

### <a name="3.0.2"/>3.0.2</a>
* Hiermee wordt een lange uitmuntende bug opgelost waarbij RUs altijd werd gerapporteerd als 0 voor aggregatie query's

### <a name="3.0.0"/>3.0.0</a>

🎉 V3-release. 🎉 veel nieuwe functies, fout oplossingen en enkele belang rijke wijzigingen. Primaire doel stellingen van deze release:

* De belangrijkste nieuwe functies implementeren
  * DISTINCT-query's
  * LIMIET/verschuiving query's
  * Aanvragen door gebruiker geannuleerd
* Bijwerken naar de nieuwste Cosmos-versie van REST API, waarbij alle containers een onbeperkte schaal hebben
* Het gebruik van Cosmos in de browser eenvoudiger maken
* Beter uitlijnen met de nieuwe Azure JS SDK-richt lijnen

#### <a name="migration-guide-for-breaking-changes"></a>Migratie handleiding voor belang rijke wijzigingen
##### <a name="improved-client-constructor-options"></a>Verbeterde opties voor client constructie

De opties voor de constructor zijn vereenvoudigd:

* de naam van hoofd sleutel is gewijzigd en is verplaatst naar het hoogste niveau
* Eigenschappen die eerder onder Options. auth staan, zijn verplaatst naar het hoogste niveau

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

##### <a name="simplified-queryiterator-api"></a>Vereenvoudigde QueryIterator-API
In v2 zijn er veel verschillende manieren om de resultaten van een query te herhalen of op te halen. We hebben geprobeerd de V3 API te vereenvoudigen en vergelijk bare of dubbele Api's te verwijderen:

* Verwijder iterator. Next () en iterator. Current (). Gebruik fetchNext () om pagina's met resultaten op te halen.
* Verwijder iterator. forEach (). Gebruik in plaats daarvan async-iterators.
* iterator. executeNext () heeft de naam gewijzigd in iterator. fetchNext ()
* iterator. toArray () heeft de naam gewijzigd in iterator. fetchAll ()
* Pagina's zijn nu goede antwoord objecten in plaats van gewone JS-objecten
* Const-container = client. data base (dbId). container (containerId)

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

##### <a name="fixed-containers-are-now-partitioned"></a>De vaste containers zijn nu gepartitioneerd
De Cosmos-service ondersteunt nu partitie sleutels op alle containers, inclusief de services die eerder zijn gemaakt als vaste containers. De V3 SDK wordt bijgewerkt naar de nieuwste API-versie die deze wijziging implementeert, maar niet wordt verbroken. Als u geen partitie sleutel voor bewerkingen opgeeft, wordt standaard een systeem sleutel gebruikt die werkt met al uw bestaande containers en documenten.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert verwijderd voor opgeslagen procedures
Voorheen was upsert toegestaan voor niet-gepartitioneerde verzamelingen, maar met de versie-update van de API, worden alle verzamelingen gepartitioneerd, zodat ze volledig zijn verwijderd.

##### <a name="item-reads-will-not-throw-on-404"></a>Item Lees bewerkingen worden niet op 404
Const-container = client. data base (dbId). container (containerId)

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

##### <a name="default-multi-region-write"></a>Standaard schrijven voor meerdere regio's
De SDK wordt nu standaard naar meerdere regio's geschreven als uw Cosmos-configuratie dit ondersteunt. Dit was voorheen opt-in werking.

##### <a name="proper-error-objects"></a>Juiste fout objecten
Mislukte aanvragen genereren nu de juiste fout of subklassen van de fout. Voorheen werden er gewoon JS-objecten gegenereerd.

#### <a name="new-features"></a>Nieuwe functies
##### <a name="user-cancelable-requests"></a>Aanvragen die door de gebruiker worden geannuleerd
Met de stap voor het ophalen van intern kunnen we de API browser AbortController gebruiken om bewerkingen die door de gebruiker kunnen worden geannuleerd, te ondersteunen. In het geval van bewerkingen waarbij meerdere aanvragen mogelijk worden uitgevoerd (zoals query's op meerdere partities), worden alle aanvragen voor de bewerking geannuleerd. Moderne browser gebruikers hebben al AbortController. Node. js-gebruikers moeten een polyfill-bibliotheek gebruiken

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>De door Voer instellen als onderdeel van de bewerking DB/container maken
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
Het genereren van het header token is opgesplitst in @azure/cosmos-signeen nieuwe bibliotheek. Iedereen die de REST API Cosmos aanroept, kan deze direct gebruiken om headers te ondertekenen met dezelfde code @azure/cosmosdie we aanroepen.

##### <a name="uuid-for-generated-ids"></a>UUID voor gegenereerde Id's
v2 bevat aangepaste code voor het genereren van item-Id's. We zijn overgeschakeld naar de bekende en beheerde uuid van de Community-bibliotheek.

##### <a name="connection-strings"></a>Verbindingsreeksen
Het is nu mogelijk om een connection string door te geven dat is gekopieerd van de Azure Portal:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Verbeterde browser ervaring
Hoewel het mogelijk is de v2 SDK in de browser te gebruiken, was het geen ideale ervaring. U moet meerdere ingebouwde tape wisselaars. JS-bibliotheken hebben gevuld en een bundel zoals webpack of pakje gebruiken. De V3 SDK maakt de out-of-Box-ervaring veel beter voor gebruikers van de browser.

* De aanvraag-interne aanvragen vervangen door ophalen (#245)
* Gebruik van buffer verwijderen (#330)
* Het op het knoop punt ingebouwde gebruik verwijderen voor de universele pakketten/Api's (#328)
* Overschakelen naar knoop punt afbreken-controller (#294)

#### <a name="bug-fixes"></a>Opgeloste fouten
* Fix aanbod testen voor lezen en terugbrengen (#224)
* EnableEndpointDiscovery herstellen (#207)
* Ontbrekend RUs corrigeren op gepagineerde resultaten (#360)
* SQL query-parameter type (#346) uitvouwen
* TTL toevoegen aan ItemDefinition (#341)
* Metrische gegevens van CP-query corrigeren (#311)
* ActivityId toevoegen aan FeedResponse (#293)
* _Ts type wijzigen van teken reeks naar getal (#252) (#295)
* Aggregatie van aanvragen kosten (#289) oplossen
* Lege teken reeks partitie sleutels toestaan (#277)
* Teken reeks toevoegen aan type voor conflict query (#237)
* UniqueKeyPolicy toevoegen aan container (#234)

#### <a name="engineering-systems"></a>Technische systemen
Niet altijd de meest zicht bare wijzigingen, maar ze helpen ons team sneller meer code te leveren.

* Rollup gebruiken voor productie-builds (#104)
* Bijwerken naar type script 3,5 (#327)
* Converteren naar TS-project verwijzingen. Test map uitpakken (#270)
* NoUnusedLocals en noUnusedParameters (#275) inschakelen
* Azure pipelines YAML voor CI-builds (#298)

### <a name="2.1.5"/>versie 2.1.5</a>
* Er worden geen code wijzigingen doorgevoerd. Hiermee wordt een probleem opgelost waarbij sommige extra bestanden zijn opgenomen in punt 2.1.4 van het pakket.

### <a name="2.1.4"/>2.1.4</a>
* Regionale failover herstellen in beleid voor opnieuw proberen
* ChangeFeed hasMoreResults eigenschap herstellen
* Updates voor dev dependency
* PolicheckExclusions. txt toevoegen

### <a name="2.1.3"/>2.1.3</a>
* _Ts type wijzigen van teken reeks naar getal
* Standaard indexerings tests corrigeren
* Backport uniqueKeyPolicy naar v2
* Oplossingen voor demo-en demo fouten

### <a name="2.1.2"/>2.1.2</a>
* Backport bieden oplossingen van de V3-vertakking
* De hand tekening van het type executeNext () corrigeren
* Type correcties

### <a name="2.1.1"/>2.1.1</a>
* Bouw herstructurering. Maakt het mogelijk de SDK-versie tijdens het bouwen te halen.

### <a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>Nieuwe functies
* Ondersteuning voor ChangeFeed (#196) toegevoegd
* Het multiveelhoeke gegevens type voor indexering is toegevoegd (#191)
* De eigenschap Key toevoegen aan de constructor als alias voor hoofd sleutel (#202)

#### <a name="fixes"></a>Oplossingen
* Bug corrigeren waarbij Next () onjuiste waarde heeft geretourneerd op iterator

#### <a name="engineering-improvements"></a>Verbeteringen in de engineering
* Integratie test voor type script-verbruik toevoegen (#199)
* Installeren rechtstreeks vanuit GitHub inschakelen (#194)

### <a name="2.0.5"/>2.0.5</a>
* Voegt interface toe voor het type knooppunt agent. Type script-gebruikers hoeven niet meer te @types/node installeren als afhankelijkheid
* Voorkeurs locaties worden nu op de juiste wijze nageleefd
* Verbeteringen in de documentatie van bijdragende ontwikkel aars
* Verschillende type fouten opgelost

### <a name="2.0.4"/>2.0.4</a>
* Hiermee wordt een probleem met het type definitie opgelost dat is geïntroduceerd in 2.0.3

### <a name="2.0.3"/>2.0.3</a>
* Afhankelijkheid verwijderen `big-integer`
* Overschakelen naar referentie richtlijnen voor het type AsyncIterable. Type script-gebruikers hoeven hun "lib"-instelling niet meer aan te passen.
* Type correcties

### <a name="2.0.2"/>2.0.2</a>
* Leesmij-koppelingen oplossen

### <a name="2.0.1"/>2.0.1</a>
* Implementatie van interface opnieuw proberen herstellen

### <a name="2.0.0"/>2.0.0</a>
* GA van versie 2.0.0 van de Java script-SDK
* Er is ondersteuning toegevoegd voor schrijf bewerkingen in meerdere regio's.

### <a name="2.0.0-3"/>2.0.0-3</a>
* RC1 van versie 2.0.0 van de Java script-SDK voor open bare preview.
* Nieuw object model, met CosmosClient en-methoden op het hoogste niveau, worden verdeeld over de relevante data base-, container-en item klassen. 
* Ondersteuning voor [beloofde](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK geconverteerd naar type script.

### <a name="1.14.4"/>1.14.4</a>
* NPM-documentatie is opgelost.

### <a name="1.14.3"/>1.14.3</a>
* Er is ondersteuning toegevoegd voor standaard nieuwe pogingen bij verbindings problemen.
* Ondersteuning toegevoegd om feed voor het wijzigen van de verzameling te lezen.
* Fout bij de consistentie van de sessie, waarbij de ' Lees sessie niet beschikbaar ' wordt veroorzaakt.
* Er is ondersteuning toegevoegd voor metrische gegevens van de query.
* Het maximum aantal verbindingen van de http-agent is gewijzigd.

### <a name="1.14.2"/>1.14.2</a>
* Bijgewerkte documentatie om te verwijzen naar Azure Cosmos DB in plaats van met Azure DocumentDB.
* Er is ondersteuning toegevoegd voor de instelling proxyUrl in Connection Policy.

### <a name="1.14.1"/>1.14.1</a>
* Kleine oplossing voor hoofdletter gevoelige bestands systemen.

### <a name="1.14.0"/>1.14.0</a>
* Voegt ondersteuning toe voor sessie consistentie.
* Deze SDK-versie vereist de meest recente versie van Azure Cosmos DB Emulator beschikbaar voor downloaden van https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Gesplitste query's voor kruis partities splitsen.
* Voegt ondersteuning toe voor de resource koppeling met voor loop-en volg spaties (en bijbehorende tests).

### <a name="1.12.2"/>1.12.2</a>
*   NPM-documentatie is opgelost.

### <a name="1.12.1"/>1.12.1</a>
* Er is een fout opgelost in executeStoredProcedure waarbij de betrokken documenten speciale Unicode-tekens (LS, PS) hadden.
* Er is een fout opgelost bij het verwerken van documenten met Unicode-tekens in de partitie sleutel.
* Vaste ondersteuning voor het maken van verzamelingen met de naam media. GitHub-probleem #114.
* Vaste ondersteuning voor machtigings autorisatie token. GitHub-probleem #178.

### <a name="1.12.0"/>1.12.0</a>
* Er is ondersteuning toegevoegd voor een nieuw [consistentie niveau](consistency-levels.md) met de naam ConsistentPrefix.
* Er is ondersteuning toegevoegd voor UriFactory.
* Er is een fout met Unicode-ondersteuning opgelost. GitHub-probleem #171.

### <a name="1.11.0"/>1.11.0</a>
* De ondersteuning voor aggregatie query's (aantal, MIN, MAX, SUM en AVG) is toegevoegd.
* De optie voor het beheren van de mate van parallelle uitvoering voor kruis partitie query's is toegevoegd.
* De optie voor het uitschakelen van SSL-verificatie is toegevoegd bij het uitvoeren van Azure Cosmos DB emulator.
* Het minimale doorvoer op gepartitioneerde verzamelingen van 10,100 RU/s 2500 RU/s verlaagd.
* De vervolg token fout voor de verzameling met één partitie is opgelost. GitHub-probleem #107.
* De executeStoredProcedure-fout in de verwerking van 0 als enkele param is opgelost. GitHub-probleem #155.

### <a name="1.10.2"/>1.10.2</a>
* Vaste gebruikers Agent-header voor het insluiten van de SDK-versie.
* Secundaire code opschonen.

### <a name="1.10.1"/>1.10.1</a>
* SSL-verificatie uitschakelen wanneer de SDK wordt gebruikt om de emulator te richten (hostnaam = localhost).
* Er is ondersteuning toegevoegd voor het inschakelen van script logboek registratie tijdens de uitvoering van de opgeslagen procedure.

### <a name="1.10.0"/>1.10.0</a>
* Ondersteuning toegevoegd voor cross-partitie parallelle query's.
* Ondersteuning toegevoegd voor bovenste/ORDER BY-query's voor gepartitioneerde verzamelingen.

### <a name="1.9.0"/>1.9.0</a>
* De ondersteuning voor het beleid voor opnieuw proberen is toegevoegd voor vertraagde aanvragen. (Vertraagde aanvragen ontvangen een aanvraag frequentie te grote uitzonde ring, fout code 429.) Azure Cosmos DB worden standaard negen keer geprobeerd voor elke aanvraag wanneer de fout code 429 wordt aangetroffen, waarbij de retryAfter-tijd in de reactie header wordt nageleefd. Een vast interval voor opnieuw proberen kan nu worden ingesteld als onderdeel van de eigenschap RetryOptions van het object Connection Policy als u de retryAfter-tijd wilt negeren die door de server wordt geretourneerd tussen de nieuwe pogingen. Azure Cosmos DB wacht nu een maximum van 30 seconden voor elke aanvraag die wordt beperkt (ongeacht het aantal nieuwe pogingen) en retourneert de reactie met fout code 429. Deze tijd kan ook worden overschreven in de eigenschap RetryOptions van het object Connection Policy.
* Cosmos DB retourneert nu x-MS-Throttle-retry-Count en x-MS-Throttle-retry-Wait-Time-MS als de antwoord headers in elke aanvraag om het aantal nieuwe pogingen te geven en de cumulatieve tijd die de aanvraag tussen het opnieuw proberen.
* De klasse RetryOptions is toegevoegd, waardoor de eigenschap RetryOptions wordt weer gegeven op de klasse Connection Policy die kan worden gebruikt om een aantal van de standaard opties voor opnieuw proberen te negeren.

### <a name="1.8.0"/>1.8.0</a>
* De ondersteuning voor databaseaccounts voor meerdere regio's toegevoegd.

### <a name="1.7.0"/>1.7.0</a>
* De ondersteuning voor time to Live (TTL)-functie voor documenten is toegevoegd.

### <a name="1.6.0"/>1.6.0</a>
* Geïmplementeerd [gepartitioneerde verzamelingen](partition-data.md) en [niveau van de gebruiker gedefinieerde prestaties](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Er is een fout opgetreden bij het herstellen van RangePartitionResolver. resolveForRead, waarbij er geen koppelingen zijn geretourneerd vanwege een onjuiste concat van resultaten.

### <a name="1.5.5"/>1.5.5</a>
* Vaste hashPartitionResolver resolveForRead (): Als er geen partitie sleutel is opgegeven, wordt een uitzonde ring gegenereerd, in plaats van een lijst met alle geregistreerde koppelingen te retour neren.

### <a name="1.5.4"/>1.5.4</a>
* Opgelost probleem [#100](https://github.com/Azure/azure-documentdb-node/issues/100) -toegewezen https-agent: Vermijd het wijzigen van de globale agent voor Azure Cosmos DB doel einden. Gebruik een toegewezen agent voor alle aanvragen van de lib.

### <a name="1.5.3"/>1.5.3</a>
* Opgelost probleem [#81](https://github.com/Azure/azure-documentdb-node/issues/81) -afbreek streepjes in Media-id's afhandelen.

### <a name="1.5.2"/>1.5.2</a>
* Corrigeert problemen [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -EventEmitter.

### <a name="1.5.1"/>1.5.1</a>
* Oplossen van problemen [#92](https://github.com/Azure/azure-documentdb-node/issues/90) -de naam van de map-hash wijzigen naar hash voor hoofdletter gevoelige systemen.

### <a name="1.5.0"/>1.5.0</a>
* Implementeer sharding-ondersteuning door het toevoegen van & voor het bereik van hash-partitionering.

### <a name="1.4.0"/>1.4.0</a>
* Upsert implementeren. Nieuwe upsertXXX-methoden op documentClient.

### <a name="1.3.0"/>1.3.0</a>
* Overgeslagen om versie nummers in overeenstemming met andere Sdk's te brengen.

### <a name="1.2.2"/>1.2.2</a>
* Split-Q belooft wrapper naar nieuwe opslag plaats.
* Update naar pakket bestand voor NPM-REGI ster.

### <a name="1.2.1"/>1.2.1</a>
* Implementeert route ring op basis van ID'S.
* Oplossen van problemen [#49](https://github.com/Azure/azure-documentdb-node/issues/49) -huidige eigenschaps conflicten met methode Current ().

### <a name="1.2.0"/>1.2.0</a>
* Er is ondersteuning toegevoegd voor georuimtelijke index.
* Hiermee valideert u de eigenschap id voor alle resources. Id's voor resources mogen niet de tekens?,/, &#47; &#47;#,, bevatten of eindigen met een spatie.
* Nieuwe header 'index transformatie uitgevoerd' toevoegen aan ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>
* Implementeert v2-indexerings beleid.

### <a name="1.0.3"/>1.0.3</a>
* Geef [#40](https://github.com/Azure/azure-documentdb-node/issues/40) -geïmplementeerde eslint-en grunt-configuraties in de core-en Promise-SDK.

### <a name="1.0.2"/>1.0.2</a>
* Probleem [#45](https://github.com/Azure/azure-documentdb-node/issues/45) -belooft wrapper bevat geen header met fout.

### <a name="1.0.1"/>1.0.1</a>
* De mogelijkheid om conflicten op te vragen, is geïmplementeerd door readConflicts, readConflictAsync en queryConflicts toe te voegen.
* Bijgewerkte API-documentatie.
* Geef een fout [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -client. createDocumentAsync.

### <a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Release & datums buiten gebruik stellen
Micro soft biedt een kennisgeving van ten minste **twaalf maanden** voor het buiten gebruik stellen van een SDK om de overgang naar een nieuwere/ondersteunde versie te versoepelen.

Nieuwe functies en functionaliteit en -optimalisatie worden alleen toegevoegd aan de huidige SDK, daarom is het raadzaam dat u altijd een upgrade uitvoert naar de nieuwste SDK versie zo vroeg mogelijk.

Een aanvraag voor het Cosmos DB met behulp van een buiten gebruik gestelde SDK worden geweigerd door de service.

<br/>

| Versie | Releasedatum | Vervaldatum |
| --- | --- | --- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 augustus 2018 |--- |
| [1.14.4](#1.14.4) |03 mei 2018 |--- |
| [1.14.3](#1.14.3) |03 mei 2018 |--- |
| [1.14.2](#1.14.2) |21 december 2017 |--- |
| [1.14.1](#1.14.1) |10 november 2017 |--- |
| [1.14.0](#1.14.0) |9 november 2017 |--- |
| [1.13.0](#1.13.0) |11 oktober 2017 |--- |
| [1.12.2](#1.12.2) |10 augustus 2017 |--- |
| [1.12.1](#1.12.1) |10 augustus 2017 |--- |
| [1.12.0](#1.12.0) |10 mei 2017 |--- |
| [1.11.0](#1.11.0) |16 maart 2017 |--- |
| [1.10.2](#1.10.2) |27 januari 2017 |--- |
| [1.10.1](#1.10.1) |22 december 2016 |--- |
| [1.10.0](#1.10.0) |03 oktober 2016 |--- |
| [1.9.0](#1.9.0) |Juli 07, 2016 |--- |
| [1.8.0](#1.8.0) |14 juni 2016 |--- |
| [1.7.0](#1.7.0) |26 april 2016 |--- |
| [1.6.0](#1.6.0) |29 maart 2016 |--- |
| [1.5.6](#1.5.6) |08 maart 2016 |--- |
| [1.5.5](#1.5.5) |02 februari 2016 |--- |
| [1.5.4](#1.5.4) |1 februari 2016 |--- |
| [1.5.2](#1.5.2) |26 januari 2016 |--- |
| [1.5.2](#1.5.2) |22 januari 2016 |--- |
| [1.5.1](#1.5.1) |4 januari 2016 |--- |
| [1.5.0](#1.5.0) |En met 31 december 2015 |--- |
| [1.4.0](#1.4.0) |06 oktober 2015 |--- |
| [1.3.0](#1.3.0) |06 oktober 2015 |--- |
| [1.2.2](#1.2.2) |10 september 2015 |--- |
| [1.2.1](#1.2.1) |15 augustus 2015 |--- |
| [1.2.0](#1.2.0) |05 augustus 2015 |--- |
| [1.1.0](#1.1.0) |09 juli 2015 |--- |
| [1.0.3](#1.0.3) |Juni 04, 2015 |--- |
| [1.0.2](#1.0.2) |23 mei 2015 |--- |
| [1.0.1](#1.0.1) |15 mei 2015 |--- |
| [1.0.0](#1.0.0) |08 april 2015 |--- |

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie voor meer informatie over Cosmos DB, [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) servicepagina.

