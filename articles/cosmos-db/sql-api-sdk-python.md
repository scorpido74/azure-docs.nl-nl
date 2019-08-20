---
title: 'Azure Cosmos DB: SQL python-API, SDK-& resources'
description: Meer informatie over de SQL python-API en SDK, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB python SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: ed90c22fa8c5b94567a9886ca71c9b35fbb103f0
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624628"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB python-SDK voor SQL-API: Release opmerkingen en bronnen
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

| |  |
|---|---|
|**SDK downloaden**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-documentatie**|[Naslag documentatie voor python API](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**SDK-installatie-instructies**|[Installatie-instructies voor python SDK](https://github.com/Azure/azure-cosmos-python)|
|**Bijdragen aan de SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Aan de slag**|[Aan de slag met de python-SDK](sql-api-python-application.md)|
|**Huidig ondersteund platform**|[Python 2,7](https://www.python.org/downloads/) en [python 3,5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="a-name302302"></a><a name="3.0.2"/>3.0.2
* Er is ondersteuning toegevoegd voor het data type multiveelhoek
* Fout oplossing in sessie beleid voor opnieuw proberen bij te lezen
* Fout oplossing voor onjuiste opvullings problemen tijdens het decoderen van basis teken reeksen van 64

### <a name="a-name301301"></a><a name="3.0.1"/>3.0.1
* Fout oplossing in LocationCache
* Fout bij het herstellen van de logica van het eind punt
* Vaste documentatie

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0
* Ondersteuning voor schrijf bewerkingen in meerdere regio's.
* De naam ruimte is gewijzigd in Azure. Cosmos.
* Verzamelings-en document concepten waarvan de naam is gewijzigd in container en item, document_client gewijzigd in cosmos_client. 

### <a name="a-name233233"></a><a name="2.3.3"/>2.3.3
* Ondersteuning voor proxy toegevoegd
* Ondersteuning toegevoegd voor het lezen van wijzigings invoer
* Ondersteuning toegevoegd voor verzamelings quotum headers
* Probleem met bugfix voor grote sessie-tokens
* Bugfix voor ReadMedia-API
* Bugfix in cache voor partitie sleutel bereik

### <a name="a-name232232"></a><a name="2.3.2"/>2.3.2
* Er is ondersteuning toegevoegd voor standaard nieuwe pogingen bij verbindings problemen.

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Bijgewerkte documentatie om te verwijzen naar Azure Cosmos DB in plaats van met Azure DocumentDB.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Deze SDK-versie vereist de meest recente versie van Azure Cosmos DB Emulator beschikbaar voor downloaden van https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Fout oplossing voor cumulatieve woorden lijst.
* Fout oplossing voor het verkleinen van schuine strepen in de resource koppeling.
* Er zijn tests toegevoegd voor Unicode-code ring.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Er is ondersteuning toegevoegd voor een nieuwe consistentieniveau ConsistentPrefix genoemd.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Er is ondersteuning toegevoegd voor aggregatie-query's (aantal, MIN, MAX, som en gemiddelde).
* Er is een optie toegevoegd voor het uitschakelen van SSL-verificatie bij het uitvoeren van Cosmos DB emulator.
* De beperking van de module dependent requests is verwijderd zodat deze precies 2.10.0 kan zijn.
* Het minimale doorvoer op gepartitioneerde verzamelingen van 10,100 RU/s 2500 RU/s verlaagd.
* Er is ondersteuning toegevoegd voor het inschakelen van script logboek registratie tijdens de uitvoering van de opgeslagen procedure.
* REST API versie die is gebump naar ' 2017-01-19 ' met deze release.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Wijzigingen in documentatie zijn aangebracht.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Er is ondersteuning toegevoegd voor python 3,5.
* Er is ondersteuning toegevoegd voor groepsgewijze verbindingen met behulp van een module aanvragen.
* Er is ondersteuning toegevoegd voor de consistentie van sessies.
* Er is ondersteuning toegevoegd voor TOP/ORDERBY-query's voor gepartitioneerde verzamelingen.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* De ondersteuning voor het beleid voor opnieuw proberen is toegevoegd voor vertraagde aanvragen. (Vertraagde aanvragen ontvangen een aanvraag frequentie te grote uitzonde ring, fout code 429.) Azure Cosmos DB worden standaard negen keer geprobeerd voor elke aanvraag wanneer de fout code 429 wordt aangetroffen, waarbij de retryAfter-tijd in de reactie header wordt nageleefd. Een vast interval voor opnieuw proberen kan nu worden ingesteld als onderdeel van de eigenschap RetryOptions van het object Connection Policy als u de retryAfter-tijd wilt negeren die door de server wordt geretourneerd tussen de nieuwe pogingen. Azure Cosmos DB wacht nu een maximum van 30 seconden voor elke aanvraag die wordt beperkt (ongeacht het aantal nieuwe pogingen) en retourneert de reactie met fout code 429. Deze tijd kan ook worden overschreven in de eigenschap RetryOptions van het object Connection Policy.
* Cosmos DB retourneert nu x-MS-Throttle-retry-Count en x-MS-Throttle-retry-Wait-Time-MS als de antwoord headers in elke aanvraag om het aantal nieuwe pogingen te geven en de cumulatieve tijd die de aanvraag tussen het opnieuw proberen.
* De RetryPolicy-klasse en de bijbehorende eigenschap (retry_policy) die is weer gegeven in de document_client-klasse, zijn verwijderd en in plaats daarvan een klasse RetryOptions geïntroduceerd die de RetryOptions-eigenschap op de Connection Policy-klasse weergeeft die kan worden gebruikt om een aantal van de standaard opties voor opnieuw proberen.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* De ondersteuning voor databaseaccounts voor meerdere regio's toegevoegd.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* De ondersteuning voor time to Live (TTL)-functie voor documenten is toegevoegd.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Oplossingen met betrekking tot partitioneren aan de server zijde om speciale tekens toe te staan in het pad van de partitie sleutel.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Geïmplementeerd [gepartitioneerde verzamelingen](partition-data.md) en [niveau van de gebruiker gedefinieerde prestaties](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Toevoegen van hash- & bereik partitie-resolvers om te helpen met sharding-toepassingen voor meerdere partities.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Upsert implementeren. Er zijn nieuwe UpsertXXX-methoden toegevoegd ter ondersteuning van de functie Upsert.
* Implementatie-ID op basis van routering. Er zijn geen openbare API-wijzigingen, worden alle interne wijzigingen.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Ondersteunt georuimtelijke index.
* Hiermee valideert u de eigenschap id voor alle resources. Kunnen geen id's voor resources bevatten?, /, #, \, tekens bevatten of eindigen met een spatie.
* Nieuwe header 'index transformatie uitgevoerd' toevoegen aan ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementeert v2-indexerings beleid.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Ondersteunt proxy verbinding.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Uittredings datums &
Micro soft biedt een kennisgeving van ten minste **twaalf maanden** voor het buiten gebruik stellen van een SDK om de overgang naar een nieuwere/ondersteunde versie te versoepelen.

Nieuwe functies en functionaliteiten en optimalisaties worden alleen toegevoegd aan de huidige SDK, omdat het raadzaam is om altijd zo snel mogelijk een upgrade naar de nieuwste SDK-versie uit te voeren. 

Alle aanvragen voor het Cosmos DB met behulp van een buiten gebruik gestelde SDK worden geweigerd door de service.

> [!WARNING]
> Alle versies van de Azure SQL SDK voor python vóór versie **1.0.0** zijn ingetrokken op **29 februari 2016**. 
> 
> 

<br/>

| Version | Releasedatum | Vervaldatum |
| --- | --- | --- |
| [3.0.2](#3.0.2) |15 november 2018 |--- |
| [3.0.1](#3.0.1) |Okt 04, 2018 |--- |
| [2.3.3](#2.3.3) |Sept 08, 2018 |--- |
| [2.3.2](#2.3.2) |08 mei 2018 |--- |
| [2.3.1](#2.3.1) |21 december 2017 |--- |
| [2.3.0](#2.3.0) |10 november 2017 |--- |
| [2.2.1](#2.2.1) |29 sep, 2017 |--- |
| [2.2.0](#2.2.0) |10 mei 2017 |--- |
| [2.1.0](#2.1.0) |01 mei 2017 |--- |
| [2.0.1](#2.0.1) |30 oktober 2016 |--- |
| [2.0.0](#2.0.0) |29 september 2016 |--- |
| [1.9.0](#1.9.0) |Juli 07, 2016 |--- |
| [1.8.0](#1.8.0) |14 juni 2016 |--- |
| [1.7.0](#1.7.0) |26 april 2016 |--- |
| [1.6.1](#1.6.1) |08 april 2016 |--- |
| [1.6.0](#1.6.0) |29 maart 2016 |--- |
| [1.5.0](#1.5.0) |03 januari 2016 |--- |
| [1.4.2](#1.4.2) |06 oktober 2015 |--- |
| 1.4.1 |06 oktober 2015 |--- |
| [1.2.0](#1.2.0) |06 van 6 augustus 2015 |--- |
| [1.1.0](#1.1.0) |09 juli 2015 |--- |
| [1.0.1](#1.0.1) |25 mei 2015 |--- |
| [1.0.0](#1.0.0) |07 april 2015 |--- |
| 0.9.4-prelease |14 januari 2015 |En met 29 februari 2016 |
| 0.9.3-prelease |09 december 2014 |En met 29 februari 2016 |
| 0.9.2-prelease |25 november 2014 |En met 29 februari 2016 |
| 0.9.1-prelease |23 september 2014 |En met 29 februari 2016 |
| 0.9.0-prelease |21 augustus 2014 |En met 29 februari 2016 |

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie voor meer informatie over Cosmos DB, [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) servicepagina. 

