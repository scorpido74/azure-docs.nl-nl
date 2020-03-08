---
title: Inleiding tot Azure Cosmos DB
description: Lees hier alles over Azure Cosmos DB. Deze wereldwijd gedistribueerde database met meerdere modellen is gebouwd voor lage latentie, elastische schaalbaarheid, hoge beschikbaarheid en voor het bieden van systeemeigen ondersteuning voor NoSQL-gegevens.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: 7fee3a1e2c26bc9fa89a4766c771a3715398b1a2
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673360"
---
# <a name="welcome-to-azure-cosmos-db"></a>Welkom bij Azure Cosmos DB

Van toepassingen wordt tegenwoordig vereist dat ze zeer responsief en altijd online zijn. Voor het bereiken van lage latentie en hoge beschikbaarheid moeten de instanties van deze toepassingen worden geïmplementeerd in datacenters die zich dicht bij de gebruikers ervan bevinden. Toepassingen moeten in realtime reageren op grote fluctuaties in het gebruik tijdens piekuren, moeten steeds toenemende gegevensvolumes kunnen opslaan en deze gegevens in milliseconden beschikbaar kunnen maken voor gebruikers.

Azure Cosmos DB is de globaal gedistribueerde multimodel-databaseservice van Microsoft. Met een klik op een knop kunt Cosmos DB u op een flexibele en onafhankelijke manier door Voer en opslag over elk wille keurig aantal Azure-regio's wereld wijd schalen. U kunt de door Voer en opslag elastisch schalen en profiteren van snelle, single-milliseconden gegevens toegang met uw favoriete API, zoals SQL, MongoDB, Cassandra, Tables of Gremlin. Cosmos DB voorziet in uitgebreide [Service Level Agreements](https://aka.ms/acdbsla) (sla's) voor doorvoer-, latentie-, Beschik baarheid en consistentie garanties, iets wat geen andere database service biedt.

U kunt [Azure Cosmos DB gratis proberen](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement, gratis en toezeg gingen, of de [Azure Cosmos DB gratis laag](optimize-dev-test.md#azure-cosmos-db-free-tier) gebruiken om een account te krijgen met de eerste 400 ru/s en 5 GB aan opslag ruimte.

> [!div class="nextstepaction"]
> [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB is de service van Microsoft voor wereldwijd gedistribueerde databases met mogelijkheden voor elastisch uitschalen, een gegarandeerde lage latentie, vijf consistentiemodellen en uitgebreide, gegarandeerde SLA's](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Belangrijkste voordelen

### <a name="turnkey-global-distribution"></a>Kant-en-klare wereldwijde distributie

Met Cosmos DB bent u in staat om wereldwijd zeer responsieve en maximaal beschikbare toepassingen te bouwen. Cosmos DB worden uw gegevens op transparante wijze gerepliceerd waar uw gebruikers zich bevinden, zodat uw gebruikers kunnen communiceren met een replica van de gegevens die er het dichtst bij staan.

Met Cosmos DB kunt u op elk gewenst moment met een klik op een knop elke willekeurige Azure-regio aan uw Cosmos-account toevoegen of eruit verwijderen. Cosmos DB worden uw gegevens naadloos gerepliceerd naar alle regio's die zijn gekoppeld aan uw Cosmos-account terwijl uw toepassing Maxi maal beschikbaar blijft, dankzij de mogelijkheden voor *meerdere multihoming* van de service. Zie het artikel [Wereldwijde distributie](distribute-data-globally.md) voor meer informatie.

### <a name="always-on"></a>AlwaysOn

In het kader van een diep gaande integratie met Azure-infra structuur en [transparante replicatie van meerdere masters](global-dist-under-the-hood.md)biedt Cosmos DB [99,999% hoge Beschik baarheid](high-availability.md) voor zowel lees-als schrijf bewerkingen. Cosmos DB biedt u ook de mogelijkheid om via een programma (of via de portal) de regionale failover van uw Cosmos-account aan te roepen. Met deze functie kunt u ervoor zorgen dat uw toepassing is ontworpen voor failover in het geval van regionale nood gevallen.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Doorvoer en opslag wereldwijd elastisch schalen

Cosmos DB is ontwikkeld met transparante horizontale partitionering en replicatie voor meerdere masters en biedt nog nooit eerder vertoonde elastische schaalbaarheid voor uw schrijf- en leesbewerkingen, overal ter wereld. U kunt met een enkele API-aanroep elastisch opschalen van duizenden tot honderden miljoenen aanvragen per seconde over de hele wereld, en toch u betaalt u alleen voor de doorvoer (en opslag) die u nodig hebt. Deze mogelijkheid helpt u om onverwachte pieken in uw werkbelastingen te kunnen verwerken zonder dat u zich met excessieve inrichting hoeft bezig te houden om de piek aan te kunnen. Zie [partitioneren in Cosmos DB](partitioning-overview.md), [ingerichte door Voer voor containers en data bases](set-throughput.md)en [ingerichte door Voer globaal schalen](scaling-throughput.md)voor meer informatie.

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Gegarandeerde lage latentie in het 99e percentiel, wereldwijd

Met Cosmos DB kunt u hoog-responsieve wereldwijde toepassingen bouwen. Dankzij het nieuwe multi-master-replicatie protocol en de vergrendelde [Data base-engine](index-policy.md)zonder vergren deling, Cosmos DB gegarandeerd minder dan 10 MS latenties voor zowel lees bewerkingen (geïndexeerd) als schrijf bewerkingen in het 99e percentiel, overal ter wereld. Op deze manier kunt u rekenen op een doorlopende opname van gegevens en uiterst snelle verwerking van query's voor zeer responsieve apps.

### <a name="precisely-defined-multiple-consistency-choices"></a>Nauwkeurig gedefinieerde, meerdere consistentiekeuzes

Wanneer u wereld wijd gedistribueerde toepassingen in Cosmos DB bouwt, hoeft u geen extreme [afweging te maken tussen consistentie, Beschik baarheid, latentie en door Voer](consistency-levels-tradeoffs.md). Het replicatie protocol voor meerdere masters van Cosmos DB is zorgvuldig ontworpen om [vijf goed gedefinieerde consistentie mogelijkheden](consistency-levels.md) te bieden - *sterke*, *gebonden veroudering*, *sessie*, *consistent voor voegsel*en *uiteindelijk* : voor een intuïtief programmeer model met lage latentie en hoge Beschik baarheid voor uw wereld wijd gedistribueerde toepassing.

### <a name="no-schema-or-index-management"></a>Geen schema- of indexbeheer

Vooral voor wereldwijd gedistribueerde apps is het uiterst moeilijk om databaseschema's en indexen synchroon te houden met het schema van een toepassing. Met Cosmos DB hoeft u geen gebruik te maken van schema-en index beheer. De database-engine is volledig schemaneutraal.  Omdat er geen schema- en indexbeheer is vereist, hoeft u zich ook geen zorgen te maken over downtime van toepassingen tijdens het migreren van schema's. Cosmos DB [indexeert automatisch alle gegevens](index-policy.md) en zorgt dat query's snel worden uitgevoerd.

### <a name="battle-tested-database-service"></a>Intensief geteste databaseservice

Cosmos DB is een fundamentele service in Azure. Voor bijna een decennium is Cosmos DB gebruikt door veel van de producten van micro soft voor essentiële toepassingen op wereld wijde schaal, waaronder Skype, Xbox, Office 365, Azure en vele andere. Vandaag de dag is Cosmos DB een van de snelste groeiende Services op Azure, gebruikt door veel externe klanten en essentiële toepassingen waarvoor elastische schaling is vereist, kant-en-klare replicatie van meerdere masters voor lage latentie en hoge Beschik baarheid van beide leest en schrijft.

### <a name="ubiquitous-regional-presence"></a>Alomtegenwoordige regionale aanwezigheid

Cosmos DB is beschikbaar in alle Azure-regio's wereld wijd, inclusief 54 + regio's in de open bare Cloud, [Azure China 21vianet](https://www.azure.cn/en-us/), Azure duitsland, Azure Government en Azure Government voor ministerie van defensie (DoD). Zie [Regionale aanwezigheid van Cosmos DB](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Standaard beveiligd en geschikt voor gebruik door bedrijven

Cosmos DB is gecertificeerd voor een [groot aantal verschillende nalevingsstandaarden](compliance.md). Bovendien worden alle gegevens in Cosmos DB tijdens opslag en verzending versleuteld. Cosmos DB biedt autorisatie op rijniveau en voldoet aan strenge beveiligingsstandaarden.

### <a name="significant-tco-savings"></a>Aanzienlijke TCO-besparingen

Omdat Cosmos DB een volledig beheerde service is, hoeft u geen complexe implementaties en upgrades van meerdere data centers meer te beheren en te kunnen uitvoeren, moet u betalen voor de ondersteuning, licenties of bewerkingen of moet u uw data base inrichten voor de piek belasting. Zie [kosten optimaliseren met Cosmos DB](total-cost-ownership.md)voor meer informatie.

### <a name="industry-leading-comprehensive-slas"></a>Toonaangevende uitgebreide SLA's

Cosmos DB is de eerste en enige service die [toonaangevende SLA's](https://azure.microsoft.com/support/legal/sla/cosmos-db/) biedt voor 99,999% hoge beschikbaarheid, latentie voor lees- en schrijfbewerkingen in het 99e percentiel, gegarandeerde doorvoer en consistentie.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Wereld wijd gedistribueerde operationele analyse en AI met ingebouwde Apache Spark

U kunt [Spark](spark-connector.md) rechtstreeks uitvoeren op gegevens die zijn opgeslagen in Cosmos DB. Op deze manier kunt u op wereldwijde schaal operationele analyses met een lage latentie uitvoeren zonder dat dit gevolgen heeft voor transactionele workloads die rechtstreeks van Cosmos DB gebruikmaken. Zie [globaal gedistribueerde operationele analyses](lambda-architecture.md)voor meer informatie.

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Toepassingen ontwikkelen op Cosmos DB met behulp van populaire OSS-Api's (open source software)

Cosmos DB biedt een keuze uit Api's voor het werken met uw gegevens die zijn opgeslagen in uw Cosmos-data base. Standaard [kunt u SQL](how-to-sql-query.md) (een core API) gebruiken voor het uitvoeren van query's op uw Cosmos-data base. Cosmos DB implementeert ook Api's voor [Cassandra](cassandra-introduction.md), [MongoDb](mongodb-introduction.md), [Gremlin](graph-introduction.md) en [Azure Table Storage](table-introduction.md). U kunt client Stuur programma's (en hulpprogram ma's) voor de veelgebruikte NoSQL (bijvoorbeeld MongoDB, Cassandra, Gremlin) rechtstreeks naar uw Cosmos-data base verwijzen. Door de bedradings protocollen van veelgebruikte NoSQL-Api's te ondersteunen, kunt Cosmos DB het volgende:

* U kunt uw toepassing eenvoudig migreren naar Cosmos DB met behoud van belangrijke onderdelen van uw toepassingslogica.
* Behoud de overdraagbaarheid van uw toepassing zonder dat u de vrijheid verliest om van cloudleverancier te veranderen.
* Profiteer van een volledig beheerde Cloud service met toonaangevende, financieel ondersteunde Sla's voor de algemene NoSQL-Api's. 
* U kunt de ingerichte doorvoer en opslag voor uw databases elastisch schalen op basis van uw behoeften, en u betaalt alleen voor de doorvoer en opslag die u nodig hebt. Dit leidt tot aanzienlijke kostenbesparingen.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Oplossingen die profiteren van Azure Cosmos DB

Elke [Web-, mobiele, gaming-en IOT-toepassing](use-cases.md) die enorme hoeveel heden gegevens, lees-en schrijf bewerkingen op een [wereld wijde schaal](distribute-data-globally.md) met bijna-echte reactie tijden voor een verscheidenheid aan gegevens moet afhandelen, heeft voor deel Cosmos DB van de [gegarandeerd hoge Beschik baarheid](https://azure.microsoft.com/support/legal/sla/cosmos-db/), hoge door Voer, lage latentie en instel bare consistentie. Kom meer te weten over hoe Cosmos DB kan worden gebruikt om toepassingen voor [IoT en telematica](use-cases.md#iot-and-telematics), [detailhandel en marketing](use-cases.md#retail-and-marketing), [gaming](use-cases.md#gaming) en [web en mobiel](use-cases.md#web-and-mobile-applications) te maken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de basis concepten van Cosmos DB vindt u [wereld wijde distributie](distribute-data-globally.md) en [partitionering](partitioning-overview.md) en [ingerichte door Voer](request-units.md).

Lees onze snelstartgidsen om snel aan de slag te gaan met Azure Cosmos DB:

* [Aan de slag met de SQL-API van Azure Cosmos DB](create-sql-api-dotnet.md)
* [Aan de slag met de API van Azure Cosmos DB voor MongoDB](create-mongodb-nodejs.md)
* [Aan de slag met de Cassandra-API van Azure Cosmos DB](create-cassandra-dotnet.md)
* [Aan de slag met de Gremlin-API van Azure Cosmos DB](create-graph-dotnet.md)
* [Aan de slag met de Table-API van Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/)
