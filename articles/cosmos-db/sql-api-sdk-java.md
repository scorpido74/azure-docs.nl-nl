---
title: 'Azure Cosmos DB: SQL Java API, SDK & resources'
description: Leer alles over de SQL Java API en SDK, inclusief releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL Java SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 02/21/2020
ms.author: sngun
ms.openlocfilehash: e57029e53365fbf99054e2d926296ccca3360663
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983599"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK voor SQL API: notities en bronnen vrijgeven
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

De SQL API Java SDK ondersteunt synchrone bewerkingen. Gebruik de [SQL API Async Java SDK](sql-api-sdk-async-java.md)voor asynchrone ondersteuning. 

| |  |
|---|---|
|**SDK downloaden**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-documentatie**|[Java API-referentiedocumentatie](/java/api/com.microsoft.azure.documentdb)|
|**Bijdragen aan SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Aan de slag**|[Aan de slag met de Java SDK](sql-api-java-get-started.md)|
|**Zelfstudie voor web-apps**|[Webtoepassingsontwikkeling met Azure Cosmos DB](sql-api-java-application.md)|
|**Minimale ondersteunde runtime**|[Java Development Kit (JDK) 7+](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="247"></a><a name="2.4.7"/>2.4.7
* Hiermee wordt een time-out probleem voor de koppelingspool opgelost.
* Hiermee worden de vernieuwing van de auth-token op interne nieuwe pogingen verholpen.

### <a name="246"></a><a name="2.4.6"/>2.4.6
* Bijgewerkte juiste client side replica policy tag op databaseAccount en maakte databaseAccount configuratie leest uit cache.

### <a name="245"></a><a name="2.4.5"/>2.4.5
* Als de gebruiker pkRangeId levert, opnieuw proberen op ongeldige fout in het partitiesleutelbereik, wordt opnieuw geprobeerd.

### <a name="244"></a><a name="2.4.4"/>2.4.4
* Geoptimaliseerde cachecache van de partitiesleutel wordt vernieuwd.
* Hiermee wordt het scenario verhelpt waarin de SDK de gesplitste hint van de partitie van de server niet vermaakt en resulteert in een onjuiste clientroutingcaches die worden vernieuwd.

### <a name="242"></a><a name="2.4.2"/>2.4.2
* Geoptimaliseerde collectiecache wordt vernieuwd.

### <a name="241"></a><a name="2.4.1"/>2.4.1
* Ondersteuning toegevoegd om het bericht met een binnenste uitzondering op te halen uit de diagnostische tekenreeks van de aanvraag.

### <a name="240"></a><a name="2.4.0"/>2.4.0
* Geïntroduceerde versie-api op PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Aparte time-out ondersteuning toegevoegd voor de directe modus.

### <a name="223"></a><a name="2.2.3"/>2.2.3
* Het consumeren van null foutbericht van service en het produceren van documentclient uitzondering.

### <a name="222"></a><a name="2.2.2"/>2.2.2
* Socket verbinding verbetering, het toevoegen van SoKeepAlive standaard true true.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Ondersteuning voor de tekenreeks voor het aanvragen van aanvragen toegevoegd.

### <a name="213"></a><a name="2.1.3"/>2.1.3
* Fixed bug in PartitionKey for Hash V2.

### <a name="212"></a><a name="2.1.2"/>2.1.2
* Ondersteuning toegevoegd voor samengestelde indexen.
* Fixed bug in global endpoint manager to force refresh.
* Fixed bug for upserts with pre-conditions in direct mode.

### <a name="211"></a><a name="2.1.1"/>2.1.1
* Fixed bug in gateway address cache.

### <a name="210"></a><a name="2.1.0"/>2.1.0
* Schrijfondersteuning met meerdere regio's toegevoegd voor de directe modus.
* Added support for handling IOExceptions thrown as ServiceUnavailable exceptions, from a proxy.
* Fixed a bug in endpoint discovery retry policy.
* Fixed a bug to ensure null pointer exceptions are not thrown in BaseDatabaseAccountConfigurationProvider.
* Fixed a bug to ensure QueryIterator does not return nulls.
* Fixed a bug to ensure large PartitionKey is allowed

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Schrijfondersteuning met meerdere regio's toegevoegd voor de gatewaymodus.

### <a name="1164"></a><a name="1.16.4"/>1.16.4
* Fixed a bug in Read partition Key ranges for a query.

### <a name="1163"></a><a name="1.16.3"/>1.16.3
* Fixed a bug in setting continuation token header size in DirectHttps mode.

### <a name="1162"></a><a name="1.16.2"/>1.16.2
* Toegevoegde streaming mislukken over ondersteuning.
* Ondersteuning toegevoegd voor aangepaste metagegevens.
* Verbeterde logica voor sessieafhandeling.
* Fixed a bug in partition key range cache.
* Fixed a NPE bug in direct mode.

### <a name="1161"></a><a name="1.16.1"/>1.16.1
* Ondersteuning toegevoegd voor unieke index.
* Added support for limiting continuation token size in feed-options.
* Fixed a bug in Json Serialization (timestamp).
* Fixed a bug in Json Serialization (enum).
* Afhankelijkheid van com.fasterxml.jackson.core:jackson-databind opgewaardeerd naar 2.9.5.

### <a name="1160"></a><a name="1.16.0"/>1.16.0
* Verbeterde verbindingspooling voor de directe modus.
* Verbeterde Prefetch-verbetering voor niet-ordererende kruispartitiequery.
* Verbeterde UUID-generatie.
* Verbeterde consistentielogica voor de sessie.
* Ondersteuning toegevoegd voor multipolygoon.
* Ondersteuning toegevoegd voor partition key range statistics voor verzameling.
* Fixed a bug in Multi-region support.

### <a name="1150"></a><a name="1.15.0"/>1.15.0
* Verbeterde Json Serialization prestaties.
* Deze SDK-versie vereist de nieuwste versie van [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator).

### <a name="1140"></a><a name="1.14.0"/>1.14.0
* Interne wijzigingen voor Microsoft-vriendenbibliotheken.

### <a name="1130"></a><a name="1.13.0"/>1.13.0
* Probleem opgelost bij het lezen van afzonderlijke partitiesleutelbereiken.
* Fixed an issue in ResourceID parsing that s ontgaat database with short names.
* Fixed an issue cause by partition key encoding.

### <a name="1120"></a><a name="1.12.0"/>1.12.0
* Kritieke bugfixes voor het aanvragen van verwerking tijdens partitiesplitsingen.
* Fixed an issue with the Strong and BoundedStaleness consistency levels.

### <a name="1110"></a><a name="1.11.0"/>1.11.0
* Ondersteuning toegevoegd voor een nieuw consistentieniveau genaamd ConsistentPrefix.
* Fixed a bug in reading collection in session mode.

### <a name="1100"></a><a name="1.10.0"/>1.10.0
* Ingeschakelde ondersteuning voor gepartitioneerde verzameling met maar liefst 2.500 RU/sec en schaal in stappen van 100 RU/sec.
* Fixed a bug in the native assembly which can cause NullRef exception in some queries.

### <a name="196"></a><a name="1.9.6"/>1.9.6
* Een bug in de configuratie van de queryengine opgelost die uitzonderingen kan veroorzaken voor query's in de gatewaymodus.
* Fixed a few bugs in the session container that may cause an "Owner resource not found" exception for requests immediately after collection creation.

### <a name="195"></a><a name="1.9.5"/>1.9.5
* Ondersteuning toegevoegd voor aggregatiequery's (COUNT, MIN, MAX, SOM en AVG). Zie [Ondersteuning voor aggregatie](sql-query-aggregates.md).
* Ondersteuning toegevoegd voor change feed.
* Ondersteuning toegevoegd voor het verzamelen van quota-informatie via RequestOptions.setPopulateQuotaInfo.
* Ondersteuning toegevoegd voor opgeslagen procedurescriptlogboekregistratie via RequestOptions.setScriptLoggingIngeschakeld.
* Fixed a bug where query in DirectHttps mode may stop responding when encountering throttle failures.
* Fixed a bug in session consistency mode.
* Fixed a bug which may cause NullReferenceException in HttpContext when request rate is high.
* Verbeterde prestaties van de DirectHttps-modus.

### <a name="194"></a><a name="1.9.4"/>1.9.4
* Eenvoudige clientinstantiegebaseerde proxy-ondersteuning toegevoegd met ConnectionPolicy.setProxy() API.
* De API DocumentClient.close() toegevoegd om de instantie DocumentClient correct af te sluiten.
* Verbeterde queryprestaties in de modus directe connectiviteit door het queryplan af te leiden van de native assemblage in plaats van de Gateway.
* Stel FAIL_ON_UNKNOWN_PROPERTIES = false in, zodat gebruikers JsonIgnoreProperties niet hoeven te definiëren in hun POJO.
* Refactored logging om SLF4J te gebruiken.
* Fixed a few other bugs in consistency reader.

### <a name="193"></a><a name="1.9.3"/>1.9.3
* Fixed a bug in the connection management to prevent connection leaks in direct connectivity mode.
* Fixed a bug in the TOP query where it may throw NullReference exception.
* Verbeterde prestaties door het aantal netwerkoproepen voor de interne caches te verminderen.
* Toegevoegd statuscode, ActivityID en Vraag URI aan in DocumentClientException voor een betere probleemoplossing.

### <a name="192"></a><a name="1.9.2"/>1.9.2
* Fixed an issue in the connection management for stability.

### <a name="191"></a><a name="1.9.1"/>1.9.1
* Added support for BoundedStaleness consistency level.
* Added support for direct connectivity for CRUD operations for partitioned collections.
* Fixed a bug in querying a database with SQL.
* Fixed a bug in the session cache where session token may be fixed incorrectly.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Ondersteuning toegevoegd voor parallelle kruispartitiequery's.
* Ondersteuning toegevoegd voor TOP/ORDER BY-query's voor partitieverzamelingen.
* Extra ondersteuning voor een sterke consistentie.
* Ondersteuning toegevoegd voor op naam gebaseerde aanvragen bij het gebruik van directe connectiviteit.
* Opgelost om ActivityId consistent te maken voor alle aanvragen.
* Fixed a bug related to the session cache when recreating a collection with the same name.
* Veelhoek- en linestringgegevenstypen toegevoegd terwijl het indexeringsbeleid voor geo-fencing wordt opgegeven.
* Problemen met Java Doc voor Java 1.8 opgelost.

### <a name="181"></a><a name="1.8.1"/>1.8.1
* Fixed a bug in PartitionKeyDefinitionMap to cache single partition collections and not make extra fetch partition key requests.
* Fixed a bug to not retry when an incorrect partition key value is provided.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* De ondersteuning voor databaseaccounts met meerdere regio's toegevoegd.
* Added support for automatic retry on throttled requests with options to customize the max retry attempts and max retry wait time.  Zie RetryOptions en ConnectionPolicy.getRetryOptions().
* Afgeschafte ipartitionresolver gebaseerde aangepaste partitioneringcode. Gebruik partitieverzamelingen voor hogere opslag en doorvoer.

### <a name="171"></a><a name="1.7.1"/>1.7.1
* Toegevoegd opnieuw proberen beleidsondersteuning voor tariefbeperking.  

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Toegevoegd time to live (TTL) ondersteuning voor documenten.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Geïmplementeerde [gepartitioneerde verzamelingen](partition-data.md) en [door de gebruiker gedefinieerde prestatieniveaus](performance-levels.md).

### <a name="151"></a><a name="1.5.1"/>1.5.1
* Fixed a bug in HashPartitionResolver to generate hash values in little-endian to be consistent with other SDKs.

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Voeg Hash & Range-partitieresolvers toe om te helpen bij het sharden van toepassingen in meerdere partities.

### <a name="140"></a><a name="1.4.0"/>1.4.0
* Implementeer Upsert. Nieuwe upsertXXX-methoden die zijn toegevoegd om de Upsert-functie te ondersteunen.
* Id-gebaseerde routering implementeren. Geen openbare API wijzigingen, alle wijzigingen intern.

### <a name="130"></a><a name="1.3.0"/>1.3.0
* Release overgeslagen om versienummer in lijn te brengen met andere SDK's

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Ondersteunt georuimtelijke index
* Valideert id-eigenschap voor alle resources. Id's voor resources kunnen geen \, tekens bevatten ?, /, #, tekens of eindigen met een spatie.
* Hiermee voegt u nieuwe koptekst "indextransformatievoortgang" toe aan ResourceResponse.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implementeert V2-indexeringsbeleid

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Release- en pensioendata
Microsoft zal ten minste **12 maanden** van tevoren een SDK melden om de overgang naar een nieuwere/ondersteunde versie soepel te laten verlopen.

Nieuwe functies en functionaliteit en optimalisaties worden alleen toegevoegd aan de huidige SDK, als zodanig is het raadzaam dat u altijd upgraden naar de nieuwste SDK-versie zo vroeg mogelijk.

Elk verzoek aan Cosmos DB met behulp van een gepensioneerde SDK wordt door de service afgewezen.

> [!WARNING]
> Alle versies **1.x** van de SQL SDK voor Java worden op **30 mei 2020**van de band teruggetrokken.
> 
>

> [!WARNING]
> Alle versies van de SQL SDK voor Java voorafgaand aan versie **1.0.0** werden op **29 februari 2016**met pensioen gegaan.
> 
> 

<br/>

| Versie | Releasedatum | Pensioendatum |
| --- | --- | --- |
| [2.4.7](#2.4.7) |20 februari 2020 |--- |
| [2.4.6](#2.4.6) |24 januari 2020 |--- |
| [2.4.5](#2.4.5) |10 november 2019 |--- |
| [2.4.4](#2.4.4) |24 okt 2019 |--- |
| [2.4.2](#2.4.2) |26 sep 2019 |--- |
| [2.4.1](#2.4.1) |18 juli 2019 |--- |
| [2.4.0](#2.4.0) |04 mei 2019 |--- |
| [2.3.0](#2.3.0) |24 april 2019 |--- |
| [2.2.3](#2.2.3) |16 april 2019 |--- |
| [2.2.2](#2.2.2) |05.05 uur 2019 |--- |
| [2.2.0](#2.2.0) |27 maart 2019 |--- |
| [2.1.3](#2.1.3) |13 maart 2019 |--- |
| [2.1.2](#2.1.2) |09 maart 2019 |--- |
| [2.1.1](#2.1.1) |13 december 2018 |--- |
| [2.1.0](#2.1.0) |20 november 2018 |--- |
| [2.0.0](#2.0.0) |21 september 2018 |--- |
| [1.16.4](#1.16.4) |10 september 2018 |30 mei 2020 |
| [1.16.3](#1.16.3) |09 september 2018 |30 mei 2020 |
| [1.16.2](#1.16.2) |29 juni 2018 |30 mei 2020 |
| [1.16.1](#1.16.1) |16 mei 2018 |30 mei 2020 |
| [1.16.0](#1.16.0) |15 maart 2018 |30 mei 2020 |
| [1.15.0](#1.15.0) |14 november 2017 |30 mei 2020 |
| [1.14.0](#1.14.0) |28 okt 2017 |30 mei 2020 |
| [1.13.0](#1.13.0) |25 augustus 2017 |30 mei 2020 |
| [1.12.0](#1.12.0) |11 juli 2017 |30 mei 2020 |
| [1.11.0](#1.11.0) |10 mei 2017 |30 mei 2020 |
| [1.10.0](#1.10.0) |11 maart 2017 |30 mei 2020 |
| [1.9.6](#1.9.6) |21 februari 2017 |30 mei 2020 |
| [1.9.5](#1.9.5) |31 januari 2017 |30 mei 2020 |
| [1.9.4](#1.9.4) |24 november 2016 |30 mei 2020 |
| [1.9.3](#1.9.3) |30 oktober 2016 |30 mei 2020 |
| [1.9.2](#1.9.2) |28 oktober 2016 |30 mei 2020 |
| [1.9.1](#1.9.1) |26 oktober 2016 |30 mei 2020 |
| [1.9.0](#1.9.0) |03 oktober 2016 |30 mei 2020 |
| [1.8.1](#1.8.1) |30 juni 2016 |30 mei 2020 |
| [1.8.0](#1.8.0) |14 juni 2016 |30 mei 2020 |
| [1.7.1](#1.7.1) |30 april 2016 |30 mei 2020 |
| [1.7.0](#1.7.0) |27 april 2016 |30 mei 2020 |
| [1.6.0](#1.6.0) |29 maart 2016 |30 mei 2020 |
| [1.5.1](#1.5.1) |31 december 2015 |30 mei 2020 |
| [1.5.0](#1.5.0) |04 december 2015 |30 mei 2020 |
| [1.4.0](#1.4.0) |05 oktober 2015 |30 mei 2020 |
| [1.3.0](#1.3.0) |05 oktober 2015 |30 mei 2020 |
| [1.2.0](#1.2.0) |05 augustus 2015 |30 mei 2020 |
| [1.1.0](#1.1.0) |09 juli 2015 |30 mei 2020 |
| 1.0.1 |12 mei 2015 |30 mei 2020 |
| [1.0.0](#1.0.0) |07 april 2015 |30 mei 2020 |
| 0.9.5-prelease |09 maart 2015 |29 februari 2016 |
| 0.9.4-prelease |17 februari 2015 |29 februari 2016 |
| 0.9.3-prelease |13 januari 2015 |29 februari 2016 |
| 0.9.2-prelease |19 december 2014 |29 februari 2016 |
| 0.9.1-prelease |19 december 2014 |29 februari 2016 |
| 0.9.0-prelease |10 december 2014 |29 februari 2016 |

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie Microsoft Azure Cosmos [DB-servicepagina](https://azure.microsoft.com/services/cosmos-db/) voor meer informatie over Cosmos DB.

