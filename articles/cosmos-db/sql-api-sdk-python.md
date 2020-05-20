---
title: 'Azure Cosmos DB: SQL python API, SDK & resources'
description: Meer informatie over de SQL python-API en SDK, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB python SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: ce74a7b02b9c450e07402d96a1aaba2a4b704788
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650712"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB python SDK voor SQL API: release opmerkingen en bronnen
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-wijzigings feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java-SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java-SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resourceprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk-uitvoerder-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk-uitvoerder-java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK downloaden**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-documentatie**|[Naslag documentatie voor python API](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**SDK-installatie-instructies**|[Installatie-instructies voor python SDK](https://github.com/Azure/azure-cosmos-python)|
|**Bijdragen aan SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Aan de slag**|[Aan de slag met de python-SDK](sql-api-python-application.md)|
|**Huidig ondersteund platform**|[Python 2,7](https://www.python.org/downloads/) en [python 3,5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="302"></a><a name="3.0.2"/>3.0.2
* Er is ondersteuning toegevoegd voor het data type multiveelhoek
* Fout oplossing in sessie beleid voor opnieuw proberen bij te lezen
* Fout oplossing voor onjuiste opvullings problemen tijdens het decoderen van basis teken reeksen van 64

### <a name="301"></a><a name="3.0.1"/>3.0.1
* Fout oplossing in LocationCache
* Fout bij het herstellen van de logica van het eind punt
* Vaste documentatie

### <a name="300"></a><a name="3.0.0"/>3.0.0
* Ondersteuning voor schrijf bewerkingen in meerdere regio's.
* De naam ruimte is gewijzigd in Azure. Cosmos.
* Verzamelings-en document concepten waarvan de naam is gewijzigd in container en item, worden document_client gewijzigd in cosmos_client. 

### <a name="233"></a><a name="2.3.3"/>2.3.3
* Ondersteuning voor proxy toegevoegd
* Ondersteuning toegevoegd voor het lezen van wijzigings invoer
* Ondersteuning toegevoegd voor verzamelings quotum headers
* Probleem met bugfix voor grote sessie-tokens
* Bugfix voor ReadMedia-API
* Bugfix in cache voor partitie sleutel bereik

### <a name="232"></a><a name="2.3.2"/>2.3.2
* Er is ondersteuning toegevoegd voor standaard nieuwe pogingen bij verbindings problemen.

### <a name="231"></a><a name="2.3.1"/>2.3.1
* Bijgewerkte documentatie om te verwijzen naar Azure Cosmos DB in plaats van met Azure DocumentDB.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Voor deze SDK-versie is de nieuwste versie van [Azure Cosmos DB emulator](https://aka.ms/cosmosdb-emulator)vereist.

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Fout oplossing voor cumulatieve woorden lijst.
* Fout oplossing voor het verkleinen van schuine strepen in de resource koppeling.
* Er zijn tests toegevoegd voor Unicode-code ring.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Er is ondersteuning toegevoegd voor een nieuw consistentie niveau met de naam ConsistentPrefix.


### <a name="210"></a><a name="2.1.0"/>2.1.0
* Er is ondersteuning toegevoegd voor aggregatie query's (aantal, MIN, MAX, SUM en AVG).
* Er is een optie toegevoegd voor het uitschakelen van TLS-verificatie wanneer deze wordt uitgevoerd op Cosmos DB emulator.
* De beperking van de module dependent requests is verwijderd zodat deze precies 2.10.0 kan zijn.
* Minder minimale door Voer voor gepartitioneerde verzamelingen van 10.100 RU/s naar 2500 RU/s.
* Er is ondersteuning toegevoegd voor het inschakelen van script logboek registratie tijdens de uitvoering van de opgeslagen procedure.
* REST API versie die is gebump naar ' 2017-01-19 ' met deze release.

### <a name="201"></a><a name="2.0.1"/>2.0.1
* Wijzigingen in documentatie zijn aangebracht.

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Er is ondersteuning toegevoegd voor python 3,5.
* Er is ondersteuning toegevoegd voor groepsgewijze verbindingen met behulp van een module aanvragen.
* Er is ondersteuning toegevoegd voor de consistentie van sessies.
* Er is ondersteuning toegevoegd voor TOP/ORDERBY-query's voor gepartitioneerde verzamelingen.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* De ondersteuning voor het beleid voor opnieuw proberen is toegevoegd voor vertraagde aanvragen. (Vertraagde aanvragen ontvangen een aanvraag frequentie te grote uitzonde ring, fout code 429.) Azure Cosmos DB worden standaard negen keer geprobeerd voor elke aanvraag wanneer de fout code 429 wordt aangetroffen, waarbij de retryAfter-tijd in de reactie header wordt nageleefd. Een vast interval voor opnieuw proberen kan nu worden ingesteld als onderdeel van de eigenschap RetryOptions van het object Connection Policy als u de retryAfter-tijd wilt negeren die door de server wordt geretourneerd tussen de nieuwe pogingen. Azure Cosmos DB wacht nu een maximum van 30 seconden voor elke aanvraag die wordt beperkt (ongeacht het aantal nieuwe pogingen) en retourneert de reactie met fout code 429. Deze tijd kan ook worden overschreven in de eigenschap RetryOptions van het object Connection Policy.
* Cosmos DB retourneert nu x-MS-Throttle-retry-Count en x-MS-Throttle-retry-Wait-Time-MS als de antwoord headers in elke aanvraag om het aantal nieuwe pogingen te geven en de cumulatieve tijd die de aanvraag tussen het opnieuw proberen.
* De RetryPolicy-klasse en de bijbehorende eigenschap (retry_policy) die op de document_client-klasse beschikbaar zijn gemaakt, zijn verwijderd en in plaats daarvan een RetryOptions-klasse geïntroduceerd waarmee de eigenschap RetryOptions op Connection Policy-klasse wordt weer gegeven die kan worden gebruikt om een aantal van de standaard opties voor opnieuw proberen te negeren.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* De ondersteuning voor meerdere regio's database accounts is toegevoegd.

### <a name="170"></a><a name="1.7.0"/>1.7.0
* De ondersteuning voor time to Live (TTL)-functie voor documenten is toegevoegd.

### <a name="161"></a><a name="1.6.1"/>1.6.1
* Oplossingen met betrekking tot partitioneren aan de server zijde om speciale tekens toe te staan in het pad van de partitie sleutel.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Geïmplementeerde [gepartitioneerde verzamelingen](partition-data.md) en door de [gebruiker gedefinieerde prestatie niveaus](performance-levels.md). 

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Voeg voor het toevoegen van & hashes voor het bereik van de sharding-toepassingen op meerdere partities hulp middelen toe.

### <a name="142"></a><a name="1.4.2"/>1.4.2
* Implementeer Upsert. Er zijn nieuwe UpsertXXX-methoden toegevoegd ter ondersteuning van de functie Upsert.
* Implementeer route ring op basis van ID'S. Geen wijzigingen in de open bare API, alle wijzigingen intern.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Ondersteunt georuimtelijke index.
* Valideert de eigenschap ID voor alle resources. Id's voor resources mogen niet de tekens?,/, #, bevatten \, of eindigen met een spatie.
* Hiermee wordt de voortgang van de index transformatie van de nieuwe header toegevoegd aan ResourceResponse.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implementeert v2-indexerings beleid.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Ondersteunt proxy verbinding.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Uittredings datums &
Micro soft biedt een kennisgeving van ten minste **twaalf maanden** voor het buiten gebruik stellen van een SDK om de overgang naar een nieuwere/ondersteunde versie te versoepelen.

Nieuwe functies en functionaliteiten en optimalisaties worden alleen toegevoegd aan de huidige SDK, omdat het raadzaam is om altijd zo snel mogelijk een upgrade naar de nieuwste SDK-versie uit te voeren. 

Alle aanvragen voor het Cosmos DB met behulp van een buiten gebruik gestelde SDK worden geweigerd door de service.

> [!WARNING]
> Alle versies van de python-SDK voor SQL API vóór versie **1.0.0** zijn buiten gebruik gesteld op **29 februari 2016**. 
> 
> 

> [!WARNING]
> Alle versies 1. x en 2. x van de python-SDK voor SQL-API zullen worden ingetrokken op **30 augustus 2020**. 
> 
> 

<br/>

| Versie | Release datum | Buitengebruikstellings datum |
| --- | --- | --- |
| [3.0.2](#3.0.2) |15 november 2018 |--- |
| [3.0.1](#3.0.1) |Okt 04, 2018 |--- |
| [2.3.3](#2.3.3) |Sept 08, 2018 |30 augustus 2020 |
| [2.3.2](#2.3.2) |08 mei 2018 |30 augustus 2020 |
| [2.3.1](#2.3.1) |21 december 2017 |30 augustus 2020 |
| [2.3.0](#2.3.0) |10 november 2017 |30 augustus 2020 |
| [2.2.1](#2.2.1) |29 sep, 2017 |30 augustus 2020 |
| [2.2.0](#2.2.0) |10 mei 2017 |30 augustus 2020 |
| [2.1.0](#2.1.0) |01 mei 2017 |30 augustus 2020 |
| [2.0.1](#2.0.1) |30 oktober 2016 |30 augustus 2020 |
| [2.0.0](#2.0.0) |29 september 2016 |30 augustus 2020 |
| [1.9.0](#1.9.0) |Juli 07, 2016 |30 augustus 2020 |
| [1.8.0](#1.8.0) |14 juni 2016 |30 augustus 2020 |
| [1.7.0](#1.7.0) |26 april 2016 |30 augustus 2020 |
| [1.6.1](#1.6.1) |08 april 2016 |30 augustus 2020 |
| [1.6.0](#1.6.0) |29 maart 2016 |30 augustus 2020 |
| [1.5.0](#1.5.0) |03 januari 2016 |30 augustus 2020 |
| [1.4.2](#1.4.2) |06 oktober 2015 |30 augustus 2020 |
| 1.4.1 |06 oktober 2015 |30 augustus 2020 |
| [1.2.0](#1.2.0) |06 van 6 augustus 2015 |30 augustus 2020 |
| [1.1.0](#1.1.0) |9 juli 2015 |30 augustus 2020 |
| [1.0.1](#1.0.1) |25 mei 2015 |30 augustus 2020 |
| [1.0.0](#1.0.0) |7 april 2015 |30 augustus 2020 |
| 0.9.4-prelease |14 januari 2015 |29 februari 2016 |
| 0.9.3-prelease |09 december 2014 |29 februari 2016 |
| 0.9.2-prelease |25 november 2014 |29 februari 2016 |
| 0.9.1 tot en-prelease |23 september 2014 |29 februari 2016 |
| 0.9.0-prelease |21 augustus 2014 |29 februari 2016 |

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-pagina voor meer informatie over Cosmos db. 

