---
title: 'Azure Cosmos DB: SQL Python API, SDK & resources'
description: Lees alles over de SQL Python API en SDK, inclusief releasedatums, pensioendatums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB Python SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: 6bc636b751d12bdb576e54f26536ac0045839229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70137343"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK voor SQL API: notities en bronnen vrijgeven
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-wijzigingsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Rest](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resourceprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulkexecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK downloaden**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-documentatie**|[Python API-referentiedocumentatie](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Installatie-instructies voor SDK**|[Installatie-instructies voor Python SDK](https://github.com/Azure/azure-cosmos-python)|
|**Bijdragen aan SDK**|[GitHub GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Slag**|[Aan de slag met de Python SDK](sql-api-python-application.md)|
|**Huidig ondersteund platform**|[Python 2.7](https://www.python.org/downloads/) en [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="302"></a><a name="3.0.2"/>3.0.2
* Ondersteuning toegevoegd voor multipolypolymeerinformatie
* Bug fix in sessie read retry policy
* Bug oplossing voor onjuiste opvulling problemen tijdens het decoderen van basis 64 strings

### <a name="301"></a><a name="3.0.1"/>3.0.1
* Bugfix in LocationCache
* Bug fix endpoint retry logica
* Vaste documentatie

### <a name="300"></a><a name="3.0.0"/>3.0.0
* Ondersteuning voor multi-regio schrijft.
* De naamruimte is veranderd in azure.cosmos.
* Verzamelings- en documentconcepten die zijn omgedoopt tot container en item, document_client omgedoopt tot cosmos_client. 

### <a name="233"></a><a name="2.3.3"/>2.3.3
* Ondersteuning toegevoegd voor proxy
* Ondersteuning toegevoegd voor het lezen van wijzigingsfeed
* Ondersteuning toegevoegd voor verzamelingsquotakoppen
* Bugfix voor groot sessietokensprobleem
* Bugfix voor ReadMedia API
* Bugfix in cache van partitiesleutelbereik

### <a name="232"></a><a name="2.3.2"/>2.3.2
* Ondersteuning toegevoegd voor standaardpogingen voor verbindingsproblemen.

### <a name="231"></a><a name="2.3.1"/>2.3.1
* Bijgewerkte documentatie om te verwijzen naar Azure Cosmos DB in plaats van Azure DocumentDB.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Deze SDK-versie vereist de nieuwste versie van https://aka.ms/cosmosdb-emulatorAzure Cosmos DB Emulator beschikbaar voor download van .

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Bug fix voor geaggregeerde woordenboek.
* Bug fix voor het bijsnijden van slashes in de resource link.
* Tests toegevoegd voor Unicode-codering.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Ondersteuning toegevoegd voor een nieuw consistentieniveau genaamd ConsistentPrefix.


### <a name="210"></a><a name="2.1.0"/>2.1.0
* Ondersteuning toegevoegd voor aggregatiequery's (COUNT, MIN, MAX, SOM en AVG).
* Added an option for disabling SSL verification when running against Cosmos DB Emulator.
* Verwijderde de beperking van afhankelijke verzoeken module om precies 2.10.0.
* Verlaagde minimale doorvoer op partitieverzamelingen van 10.100 RU/s naar 2500 RU/s.
* Added support for enabling script logging during stored procedure execution.
* REST API versie steeg naar '2017-01-19' met deze release.

### <a name="201"></a><a name="2.0.1"/>2.0.1
* Redactionele wijzigingen aangebracht in de reacties van documentatie.

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Ondersteuning toegevoegd voor Python 3.5.
* Ondersteuning toegevoegd voor het bundelen van verbindingen met behulp van een aanvragenmodule.
* Ondersteuning toegevoegd voor sessieconsistentie.
* Ondersteuning toegevoegd voor TOP/ORDERBY-query's voor partitieverzamelingen.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Ondersteuning voor het beleid opnieuw proberen voor geweigerde aanvragen. (Throttled-aanvragen ontvangen een aanvraagpercentage met een te grote uitzondering, foutcode 429.) Azure Cosmos DB probeert standaard negen keer voor elk verzoek wanneer foutcode 429 wordt aangetroffen, ter ere van de retryAfter-tijd in de antwoordkop. Een vaste intervaltijd voor opnieuw proberen kan nu worden ingesteld als onderdeel van de eigenschap RetryOptions op het object Verbindingsbeleid als u de nieuwe poging wilt negerenNa de tijd die door de server tussen de nieuwe pogingen is geretourneerd. Azure Cosmos DB wacht nu maximaal 30 seconden op elk verzoek dat wordt beperkt (ongeacht het aantal nieuwe try's) en retourneert het antwoord met foutcode 429. Deze tijd kan ook worden overschreven in de eigenschap RetryOptions op het object Verbindingsbeleid.
* Cosmos DB retourneert nu x-ms-throttle-retry-count en x-ms-throttle-retry-wait-time-ms als de responsheaders in elk verzoek om het gaspedaal opnieuw te tellen en de cumulatieve tijd die het verzoek wachtte tussen de pogingen aan te geven.
* Verwijderde de klasse RetryPolicy en de bijbehorende eigenschap (retry_policy) die in de klasse document_client is weergegeven en introduceerde in plaats daarvan een klasse RetryOptions waarin de eigenschap RetryOptions wordt blootgesteld aan de klasse ConnectionPolicy die kan worden gebruikt om een deel van de opties voor het opnieuw proberen.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* De ondersteuning voor databaseaccounts met meerdere regio's toegevoegd.

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Toegevoegd de ondersteuning voor Time To Live (TTL) functie voor documenten.

### <a name="161"></a><a name="1.6.1"/>1.6.1
* Bugfixes met betrekking tot server-side partitioning om speciale tekens in partitiesleutelpad toe te staan.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Geïmplementeerde [gepartitioneerde verzamelingen](partition-data.md) en [door de gebruiker gedefinieerde prestatieniveaus](performance-levels.md). 

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Voeg Hash & Range-partitieresolvers toe om te helpen bij het sharden van toepassingen in meerdere partities.

### <a name="142"></a><a name="1.4.2"/>1.4.2
* Implementeer Upsert. Nieuwe UpsertXXX-methoden zijn toegevoegd om de Upsert-functie te ondersteunen.
* Id-gebaseerde routering implementeren. Geen openbare API wijzigingen, alle wijzigingen intern.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Ondersteunt georuimtelijke index.
* Valideert id-eigenschap voor alle resources. Id's voor resources kunnen geen \, tekens bevatten ?, /, #, tekens of eindigen met een spatie.
* Hiermee voegt u nieuwe koptekst "indextransformatievoortgang" toe aan ResourceResponse.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implementeert V2 indexeringsbeleid.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Ondersteunt proxy-verbinding.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Release & pensioendata
Microsoft biedt ten minste **12 maanden** van tevoren een melding om een SDK met pensioen te laten gaan om de overgang naar een nieuwere/ondersteunde versie soepel te laten verlopen.

Nieuwe functies en functionaliteit en optimalisaties worden alleen toegevoegd aan de huidige SDK, als zodanig is het raadzaam dat u altijd upgraden naar de nieuwste SDK-versie zo vroeg mogelijk. 

Elk verzoek aan Cosmos DB met behulp van een gepensioneerde SDK wordt afgewezen door de service.

> [!WARNING]
> Alle versies van de Python SDK voor SQL API voorafgaand aan versie **1.0.0** werden op **29 februari 2016**met pensioen gegaan. 
> 
> 

> [!WARNING]
> Alle versies 1.x en 2.x van de Python SDK voor SQL API worden op **30 augustus 2020**buiten gebruik gesteld. 
> 
> 

<br/>

| Versie | Releasedatum | Pensioendatum |
| --- | --- | --- |
| [3.0.2](#3.0.2) |15 november 2018 |--- |
| [3.0.1](#3.0.1) |04 oktober 2018 |--- |
| [2.3.3](#2.3.3) |08 september 2018 |30 augustus 2020 |
| [2.3.2](#2.3.2) |08 mei 2018 |30 augustus 2020 |
| [2.3.1](#2.3.1) |21 december 2017 |30 augustus 2020 |
| [2.3.0](#2.3.0) |10 november 2017 |30 augustus 2020 |
| [2.2.1](#2.2.1) |29 sep 2017 |30 augustus 2020 |
| [2.2.0](#2.2.0) |10 mei 2017 |30 augustus 2020 |
| [2.1.0](#2.1.0) |01 mei 2017 |30 augustus 2020 |
| [2.0.1](#2.0.1) |30 oktober 2016 |30 augustus 2020 |
| [2.0.0](#2.0.0) |29 september 2016 |30 augustus 2020 |
| [1.9.0](#1.9.0) |07 juli 2016 |30 augustus 2020 |
| [1.8.0](#1.8.0) |14 juni 2016 |30 augustus 2020 |
| [1.7.0](#1.7.0) |26 april 2016 |30 augustus 2020 |
| [1.6.1](#1.6.1) |08 april 2016 |30 augustus 2020 |
| [1.6.0](#1.6.0) |29 maart 2016 |30 augustus 2020 |
| [1.5.0](#1.5.0) |03 januari 2016 |30 augustus 2020 |
| [1.4.2](#1.4.2) |06 oktober 2015 |30 augustus 2020 |
| 1.4.1 |06 oktober 2015 |30 augustus 2020 |
| [1.2.0](#1.2.0) |06 augustus 2015 |30 augustus 2020 |
| [1.1.0](#1.1.0) |09 juli 2015 |30 augustus 2020 |
| [1.0.1](#1.0.1) |25 mei 2015 |30 augustus 2020 |
| [1.0.0](#1.0.0) |07 april 2015 |30 augustus 2020 |
| 0.9.4-prelease |14 januari 2015 |29 februari 2016 |
| 0.9.3-prelease |09 december 2014 |29 februari 2016 |
| 0.9.2-prelease |25 november 2014 |29 februari 2016 |
| 0.9.1-prelease |23 september 2014 |29 februari 2016 |
| 0.9.0-prelease |21 augustus 2014 |29 februari 2016 |

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie Microsoft Azure Cosmos [DB-servicepagina](https://azure.microsoft.com/services/cosmos-db/) voor meer informatie over Cosmos DB. 

