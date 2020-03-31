---
title: Inleiding tot Azure Cosmos DB
description: Lees hier alles over Azure Cosmos DB. Deze wereldwijd gedistribueerde database met meerdere modellen is gebouwd voor lage latentie, elastische schaalbaarheid, hoge beschikbaarheid en voor het bieden van systeemeigen ondersteuning voor NoSQL-gegevens.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: 2a09d5af013e804f33327855fb7b9f2104bc225c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240392"
---
# <a name="welcome-to-azure-cosmos-db"></a>Welkom bij Azure Cosmos DB

Van toepassingen wordt tegenwoordig vereist dat ze zeer responsief en altijd online zijn. Voor het bereiken van lage latentie en hoge beschikbaarheid moeten de instanties van deze toepassingen worden geïmplementeerd in datacenters die zich dicht bij de gebruikers ervan bevinden. Toepassingen moeten in realtime reageren op grote fluctuaties in het gebruik tijdens piekuren, moeten steeds toenemende gegevensvolumes kunnen opslaan en deze gegevens in milliseconden beschikbaar kunnen maken voor gebruikers.

Azure Cosmos DB is de globaal gedistribueerde multimodel-databaseservice van Microsoft. Met een klik op de knop u met Cosmos DB de doorvoer en opslag in elk aantal Azure-regio's wereldwijd elastisch en onafhankelijk schalen. U de doorvoer en opslag elastisch schalen en profiteren van snelle gegevenstoegang met één cijfer met behulp van uw favoriete API, waaronder: SQL, MongoDB, Cassandra, Tables of Gremlin. Cosmos DB biedt uitgebreide [service level agreements](https://aka.ms/acdbsla) (SLA's) voor doorvoer, latentie, beschikbaarheid en consistentiegaranties, iets wat geen enkele andere databaseservice biedt.

U [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement, gratis en verplichtingen of de gratis azure cosmos [DB-laag](optimize-dev-test.md#azure-cosmos-db-free-tier) gebruiken om een account te krijgen met de eerste 400 RU/s en 5 GB opslag gratis.

> [!div class="nextstepaction"]
> [Probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB is de service van Microsoft voor wereldwijd gedistribueerde databases met mogelijkheden voor elastisch uitschalen, een gegarandeerde lage latentie, vijf consistentiemodellen en uitgebreide, gegarandeerde SLA's](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Belangrijkste voordelen

### <a name="turnkey-global-distribution"></a>Kant-en-klare wereldwijde distributie

Met Cosmos DB bent u in staat om wereldwijd zeer responsieve en maximaal beschikbare toepassingen te bouwen. Cosmos DB repliceert uw gegevens op transparante wijze, waar uw gebruikers zich ook bevinden, zodat uw gebruikers kunnen communiceren met een replica van de gegevens die het dichtst bij hen staan.

Met Cosmos DB kunt u op elk gewenst moment met een klik op een knop elke willekeurige Azure-regio aan uw Cosmos-account toevoegen of eruit verwijderen. Cosmos DB zal uw gegevens naadloos repliceren naar alle regio's die zijn gekoppeld aan uw Cosmos-account, terwijl uw toepassing nog steeds zeer beschikbaar is, dankzij de *multi-homing* mogelijkheden van de service. Zie het artikel [Wereldwijde distributie](distribute-data-globally.md) voor meer informatie.

### <a name="always-on"></a>AlwaysOn

Op grond van diepe integratie met [Azure-infrastructuur en transparante multi-master replicatie](global-dist-under-the-hood.md)biedt Cosmos DB [99,999% hoge beschikbaarheid](high-availability.md) voor zowel lezen als schrijven. Cosmos DB biedt u ook de mogelijkheid om via een programma (of via de portal) de regionale failover van uw Cosmos-account aan te roepen. Deze mogelijkheid helpt ervoor te zorgen dat uw toepassing is ontworpen om failover in het geval van regionale ramp.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Doorvoer en opslag wereldwijd elastisch schalen

Cosmos DB is ontwikkeld met transparante horizontale partitionering en replicatie voor meerdere masters en biedt nog nooit eerder vertoonde elastische schaalbaarheid voor uw schrijf- en leesbewerkingen, overal ter wereld. U kunt met een enkele API-aanroep elastisch opschalen van duizenden tot honderden miljoenen aanvragen per seconde over de hele wereld, en toch u betaalt u alleen voor de doorvoer (en opslag) die u nodig hebt. Deze mogelijkheid helpt u om onverwachte pieken in uw werkbelastingen te kunnen verwerken zonder dat u zich met excessieve inrichting hoeft bezig te houden om de piek aan te kunnen. Zie voor meer informatie [partitionering in Cosmos DB](partitioning-overview.md), [ingerichte doorvoer op containers en databases](set-throughput.md)en wereldwijd schalen van de [ingerichte doorvoer](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Gegarandeerde lage latentie in het 99e percentiel, wereldwijd

Met Cosmos DB kunt u hoog-responsieve wereldwijde toepassingen bouwen. Met zijn nieuwe multi-master replicatie protocol en latch-vrije en [schrijf-geoptimaliseerde database engine](index-policy.md), Cosmos DB garandeert minder dan 10-ms latencies voor beide, leest (geïndexeerd) en schrijft op de 99e percentiel, over de hele wereld. Op deze manier kunt u rekenen op een doorlopende opname van gegevens en uiterst snelle verwerking van query's voor zeer responsieve apps.

### <a name="precisely-defined-multiple-consistency-choices"></a>Nauwkeurig gedefinieerde, meerdere consistentiekeuzes

Bij het bouwen van wereldwijd gedistribueerde toepassingen in Cosmos DB hoeft u geen extreme afwegingen meer te maken [tussen consistentie, beschikbaarheid, latentie en doorvoer.](consistency-levels-tradeoffs.md) Cosmos DB's multi-master replicatieprotocol is zorgvuldig ontworpen om [vijf goed gedefinieerde consistentiekeuzes](consistency-levels.md) - *te bieden,* *begrensd*, *sessie*, *consistent voorvoegsel*en *uiteindelijk* - voor een intuïtief programmeermodel met lage latentie en hoge beschikbaarheid voor uw wereldwijd gedistribueerde toepassing.

### <a name="no-schema-or-index-management"></a>Geen schema- of indexbeheer

Vooral voor wereldwijd gedistribueerde apps is het uiterst moeilijk om databaseschema's en indexen synchroon te houden met het schema van een toepassing. Met Cosmos DB hoeft u niet te maken te krijgen met schema- of indexbeheer. De database-engine is volledig schemaneutraal.  Omdat er geen schema- en indexbeheer is vereist, hoeft u zich ook geen zorgen te maken over downtime van toepassingen tijdens het migreren van schema's. Cosmos DB [indexeert automatisch alle gegevens](index-policy.md) en zorgt dat query's snel worden uitgevoerd.

### <a name="battle-tested-database-service"></a>Intensief geteste databaseservice

Cosmos DB is een fundamentele service in Azure. Cosmos DB wordt al bijna tien jaar gebruikt door veel van Microsoft-producten voor bedrijfskritieke toepassingen op wereldwijde schaal, waaronder Skype, Xbox, Office 365, Azure en vele anderen. Vandaag de dag is Cosmos DB een van de snelst groeiende services op Azure, die worden gebruikt door veel externe klanten en bedrijfskritische toepassingen die elastische schaal, kant-en-klare wereldwijde distributie, multi-master replicatie vereisen voor lage latentie en hoge beschikbaarheid van beide leest en schrijft.

### <a name="ubiquitous-regional-presence"></a>Alomtegenwoordige regionale aanwezigheid

Cosmos DB is beschikbaar in alle Azure-regio's wereldwijd, waaronder 54+ regio's in de openbare cloud, [Azure China 21Vianet,](https://www.azure.cn/en-us/)Azure Germany, Azure Government en Azure Government for Department of Defense (DoD). Zie [Regionale aanwezigheid van Cosmos DB](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Standaard beveiligd en geschikt voor gebruik door bedrijven

Cosmos DB is gecertificeerd voor een [groot aantal verschillende nalevingsstandaarden](compliance.md). Bovendien worden alle gegevens in Cosmos DB tijdens opslag en verzending versleuteld. Cosmos DB biedt autorisatie op rijniveau en voldoet aan strenge beveiligingsstandaarden.

### <a name="significant-tco-savings"></a>Aanzienlijke TCO-besparingen

Aangezien Cosmos DB een volledig beheerde service is, hoeft u geen complexe multidatacenterimplementaties en upgrades van uw databasesoftware meer te beheren en te beheren, te betalen voor de ondersteuning, licenties of bewerkingen of uw database in te richten voor de piekworkload. Zie [Kosten optimaliseren met Cosmos DB voor](total-cost-ownership.md)meer informatie.

### <a name="industry-leading-comprehensive-slas"></a>Toonaangevende uitgebreide SLA's

Cosmos DB is de eerste en enige service die [toonaangevende SLA's](https://azure.microsoft.com/support/legal/sla/cosmos-db/) biedt voor 99,999% hoge beschikbaarheid, latentie voor lees- en schrijfbewerkingen in het 99e percentiel, gegarandeerde doorvoer en consistentie.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Wereldwijd gedistribueerde operationele analyses en AI met native ingebouwde Apache Spark

U kunt [Spark](spark-connector.md) rechtstreeks uitvoeren op gegevens die zijn opgeslagen in Cosmos DB. Op deze manier kunt u op wereldwijde schaal operationele analyses met een lage latentie uitvoeren zonder dat dit gevolgen heeft voor transactionele workloads die rechtstreeks van Cosmos DB gebruikmaken. Zie [Wereldwijd gedistribueerde operationele analyses](lambda-architecture.md)voor meer informatie.

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Ontwikkel applicaties op Cosmos DB met behulp van populaire Open Source Software (OSS) API's

Cosmos DB biedt een keuze aan API's om te werken met uw gegevens die zijn opgeslagen in uw Cosmos-database. Standaard [kunt u SQL](how-to-sql-query.md) (een core API) gebruiken voor het opvragen van uw Cosmos-database. Cosmos DB implementeert ook API's voor [Cassandra,](cassandra-introduction.md) [MongoDB,](mongodb-introduction.md) [Gremlin](graph-introduction.md) en [Azure Table Storage](table-introduction.md). U clientstuurprogramma's (en hulpprogramma's) voor de veelgebruikte NoSQL (bijvoorbeeld MongoDB, Cassandra, Gremlin) rechtstreeks naar uw Cosmos-database wijzen. Door de draadprotocollen van veelgebruikte NoSQL API's te ondersteunen, u met Cosmos DB:

* U kunt uw toepassing eenvoudig migreren naar Cosmos DB met behoud van belangrijke onderdelen van uw toepassingslogica.
* Behoud de overdraagbaarheid van uw toepassing zonder dat u de vrijheid verliest om van cloudleverancier te veranderen.
* Krijg een volledig beheerde cloudservice met toonaangevende, financieel gesteunde SLA's voor de gemeenschappelijke NoSQL API's. 
* U kunt de ingerichte doorvoer en opslag voor uw databases elastisch schalen op basis van uw behoeften, en u betaalt alleen voor de doorvoer en opslag die u nodig hebt. Dit leidt tot aanzienlijke kostenbesparingen.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Oplossingen die profiteren van Azure Cosmos DB

Elke [web-, mobiele, gaming- en IoT-toepassing](use-cases.md) die enorme hoeveelheden gegevens moet verwerken, leest en schrijft op [wereldwijde schaal](distribute-data-globally.md) met bijna reële responstijden voor een verscheidenheid aan gegevens, zal profiteren van de gegarandeerde hoge [beschikbaarheid](https://azure.microsoft.com/support/legal/sla/cosmos-db/)van Cosmos DB, hoge doorvoer, lage latentie en tunable consistentie. Kom meer te weten over hoe Cosmos DB kan worden gebruikt om toepassingen voor [IoT en telematica](use-cases.md#iot-and-telematics), [detailhandel en marketing](use-cases.md#retail-and-marketing), [gaming](use-cases.md#gaming) en [web en mobiel](use-cases.md#web-and-mobile-applications) te maken.

## <a name="next-steps"></a>Volgende stappen

Lees meer over cosmos DB's kernconcepten [turnkey wereldwijde distributie](distribute-data-globally.md) en [partitionering](partitioning-overview.md) en [ingerichte doorvoer](request-units.md).

Lees onze snelstartgidsen om snel aan de slag te gaan met Azure Cosmos DB:

* [Aan de slag met de SQL-API van Azure Cosmos DB](create-sql-api-dotnet.md)
* [Aan de slag met de API van Azure Cosmos DB voor MongoDB](create-mongodb-nodejs.md)
* [Aan de slag met de Cassandra-API van Azure Cosmos DB](create-cassandra-dotnet.md)
* [Aan de slag met de Gremlin-API van Azure Cosmos DB](create-graph-dotnet.md)
* [Aan de slag met de Table-API van Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Probeer Azure Cosmos DB gratis uit](https://azure.microsoft.com/try/cosmosdb/)
