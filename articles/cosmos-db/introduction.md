---
title: Inleiding tot Azure Cosmos DB
description: Lees hier alles over Azure Cosmos DB. Deze wereldwijd gedistribueerde database met meerdere modellen is gebouwd voor lage latentie, elastische schaalbaarheid, hoge beschikbaarheid en voor het bieden van systeemeigen ondersteuning voor NoSQL-gegevens.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: 2301219db250b97fd7ea2eb3814f5eba2c70148a
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569860"
---
# <a name="welcome-to-azure-cosmos-db"></a>Welkom bij Azure Cosmos DB

Van toepassingen wordt tegenwoordig vereist dat ze zeer responsief en altijd online zijn. Voor het bereiken van lage latentie en hoge beschikbaarheid moeten de instanties van deze toepassingen worden geïmplementeerd in datacenters die zich dicht bij de gebruikers ervan bevinden. Toepassingen moeten in realtime reageren op grote fluctuaties in het gebruik tijdens piekuren, moeten steeds toenemende gegevensvolumes kunnen opslaan en deze gegevens in milliseconden beschikbaar kunnen maken voor gebruikers.

Azure Cosmos DB is de globaal gedistribueerde multimodel-databaseservice van Microsoft. Cosmos DB maakt het mogelijk om met één muisklik doorvoer en opslag flexibel en onafhankelijk te schalen binnen een onbeperkt aantal Azure-regio’s over de hele wereld. U kunt de doorvoer en opslag elastisch schalen en zo profiteren van snelle gegevenstoegang met snelheden van niet meer dan enkele milliseconden met behulp van uw favoriete API, zoals: SQL, MongoDB, Cassandra, Tables of Gremlin. Cosmos DB biedt uitgebreide [serviceovereenkomsten](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) (SLA's) waarin gegarandeerde doorvoer, latentie, beschikbaarheid en consistentie zijn vastgelegd - iets wat geen enkele andere databaseservice kan bieden.

U kunt [Azure Cosmos DB gratis proberen](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement en geheel kosteloos en zonder verplichtingen, of de [gratis Azure Cosmos DB-laag](optimize-dev-test.md#azure-cosmos-db-free-tier) gebruiken om een account te maken waarbij de eerste 400 RU/s en 5 GB aan opslagruimte gratis zijn.

> [!div class="nextstepaction"]
> [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB is de service van Microsoft voor wereldwijd gedistribueerde databases met mogelijkheden voor elastisch uitschalen, een gegarandeerde lage latentie, vijf consistentiemodellen en uitgebreide, gegarandeerde SLA's" border="false":::

## <a name="key-benefits"></a>Belangrijkste voordelen

### <a name="turnkey-global-distribution"></a>Kant-en-klare wereldwijde distributie

Met Cosmos DB bent u in staat om wereldwijd zeer responsieve en maximaal beschikbare toepassingen te bouwen. Cosmos DB repliceert uw gegevens transparant naar ongeacht welke locatie waarop zich uw gebruikers bevinden, zodat zij kunnen werken met een replica van de gegevens die zich het dichtst bij hen in de buurt bevindt.

Met Cosmos DB kunt u op elk gewenst moment met een klik op een knop elke willekeurige Azure-regio aan uw Cosmos-account toevoegen of eruit verwijderen. Cosmos DB repliceert naadloos uw gegevens naar alle regio's die zijn gekoppeld aan uw Cosmos-account, terwijl uw toepassing maximaal beschikbaar blijft dankzij de mogelijkheden tot *multihoming* van de service. Zie het artikel [Wereldwijde distributie](distribute-data-globally.md) voor meer informatie.

### <a name="always-on"></a>AlwaysOn

Door de hechte integratie met de infrastructuur van Azure en de [transparante replicatie van schrijfbewerkingen voor meerdere regio's](global-dist-under-the-hood.md) biedt Cosmos DB een [hoge beschikbaarheid](high-availability.md) van 99,999% voor zowel lees- als schrijfbewerkingen. Cosmos DB biedt u ook de mogelijkheid om via een programma (of via de portal) de regionale failover van uw Cosmos-account aan te roepen. Met deze functie kunt u ervoor zorgen dat uw toepassing is ontworpen voor failover in het geval van regionale noodgevallen.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Doorvoer en opslag wereldwijd elastisch schalen

Cosmos DB is ontwikkeld met transparante horizontale partitionering en replicatie voor schrijfbewerking in meerdere regio's en biedt nog nooit eerder vertoonde elastische schaalbaarheid voor uw schrijf- en leesbewerkingen, overal ter wereld. U kunt met een enkele API-aanroep elastisch opschalen van duizenden tot honderden miljoenen aanvragen per seconde over de hele wereld, en toch u betaalt u alleen voor de doorvoer (en opslag) die u nodig hebt. Deze mogelijkheid helpt u om onverwachte pieken in uw werkbelastingen te kunnen verwerken zonder dat u zich met excessieve inrichting hoeft bezig te houden om de piek aan te kunnen. Zie [Partitionering in Cosmos DB](partitioning-overview.md), [Ingerichte doorvoer op containers en databases](set-throughput.md) en [Doorvoer wereldwijd inrichten](scaling-throughput.md) voor meer informatie.

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Gegarandeerde lage latentie in het 99e percentiel, wereldwijd

Met Cosmos DB kunt u hoog-responsieve wereldwijde toepassingen bouwen. Dankzij het moderne replicatieprotocol voor meerdere regio's en de vergrendelingsvrije en [voor schrijven geoptimaliseerde database-engine](index-policy.md) garandeert Cosmos DB een latentie van minder dan 10 ms voor leesbewerkingen (geïndexeerd) en schrijfbewerkingen in het 99e percentiel, wereldwijd. Op deze manier kunt u rekenen op een doorlopende opname van gegevens en uiterst snelle verwerking van query's voor zeer responsieve apps.

### <a name="precisely-defined-multiple-consistency-choices"></a>Nauwkeurig gedefinieerde, meerdere consistentiekeuzes

Wanneer u wereldwijd gedistribueerde toepassingen in Cosmos DB maakt, hoeft u niet langer verregaande [compromissen tussen consistentie, beschikbaarheid, latentie en doorvoer](consistency-levels-tradeoffs.md) te sluiten. Het replicatieprotocol voor meerdere regio's van Cosmos DB biedt [vijf duidelijk gedefinieerde consistentieniveaus](consistency-levels.md) - *sterk*, *gebonden veroudering*, *sessie*, *consistent voorvoegsel* en *uiteindelijk* – voor een intuïtief programmeermodel met lage latentie en hoge beschikbaarheid voor uw wereldwijde toepassing.

### <a name="no-schema-or-index-management"></a>Geen schema- of indexbeheer

Vooral voor wereldwijd gedistribueerde apps is het uiterst moeilijk om databaseschema's en indexen synchroon te houden met het schema van een toepassing. Met Cosmos DB hoeft u zich niet te bekommeren om het beheer van schema's of indexen. De database-engine is volledig schemaneutraal.  Omdat er geen schema- en indexbeheer is vereist, hoeft u zich ook geen zorgen te maken over downtime van toepassingen tijdens het migreren van schema's. Cosmos DB [indexeert automatisch alle gegevens](index-policy.md) en zorgt dat query's snel worden uitgevoerd.

### <a name="battle-tested-database-service"></a>Intensief geteste databaseservice

Cosmos DB is een fundamentele service in Azure. Cosmos DB wordt al bijna een decennium lang gebruikt door veel van de Microsoft-producten voor missiekritische toepassingen op wereldwijde schaal, zoals Skype, Xbox, Microsoft 365, Azure en veel meer. Vandaag de dag is Cosmos DB een van de snelst groeiende services op Azure die wordt gebruikt door veel externe klanten en missiekritische toepassingen die elastisch schalen, kant-en-klare wereldwijde distributie en replicatie van meerdere regio's met een lage latentie en hoge beschikbaarheid voor zowel lees- als schrijfbewerkingen, vereisen.

### <a name="ubiquitous-regional-presence"></a>Alomtegenwoordige regionale aanwezigheid

Cosmos DB is beschikbaar in alle Azure-regio's wereldwijd, waaronder meer dan 54 regio's in de openbare cloud, [Azure China 21Vianet](https://www.azure.cn/en-us/), Azure Duitsland, Azure Government en Azure Government for Department of Defense (DoD). Zie [Regionale aanwezigheid van Cosmos DB](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Standaard beveiligd en geschikt voor gebruik door bedrijven

Cosmos DB is gecertificeerd voor een [groot aantal verschillende nalevingsstandaarden](compliance.md). Bovendien worden alle gegevens in Cosmos DB tijdens opslag en verzending versleuteld. Cosmos DB biedt autorisatie op rijniveau en voldoet aan strenge beveiligingsstandaarden.

### <a name="significant-tco-savings"></a>Aanzienlijke TCO-besparingen

Aangezien Cosmos DB een volledig beheerde service is, hoeft u zich niet langer bezig te houden met het beheer of de werking van complexe implementaties op meerdere datacenters, met upgrades van uw databasesoftware, met betalingen voor ondersteuning, licenties of voor het uitvoeren van bewerkingen of uw database in te richten voor de hoogste werkbelasting. Zie [Kosten optimaliseren met Cosmos DB](total-cost-ownership.md) voor meer informatie.

### <a name="industry-leading-comprehensive-slas"></a>Toonaangevende uitgebreide SLA's

Cosmos DB is de eerste en enige service die [toonaangevende SLA's](https://azure.microsoft.com/support/legal/sla/cosmos-db/) biedt voor 99,999% hoge beschikbaarheid, latentie voor lees- en schrijfbewerkingen in het 99e percentiel, gegarandeerde doorvoer en consistentie.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Wereldwijd gedistribueerde operationele analyse en AI met ingebouwde Apache Spark

U kunt [Spark](spark-connector.md) rechtstreeks uitvoeren op gegevens die zijn opgeslagen in Cosmos DB. Op deze manier kunt u op wereldwijde schaal operationele analyses met een lage latentie uitvoeren zonder dat dit gevolgen heeft voor transactionele workloads die rechtstreeks van Cosmos DB gebruikmaken. Zie [Wereldwijd gedistribueerde operationele analyse](lambda-architecture.md)voor meer informatie.

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Toepassingen ontwikkelen op Cosmos DB met behulp van populaire OSS-API's (open source software)

Cosmos DB biedt een keuze uit API's voor het werken met uw gegevens die zijn opgeslagen in uw Cosmos-database. U kunt standaard [SQL gebruiken](how-to-sql-query.md) (een kern-API) voor het uitvoeren van een query op uw Cosmos-database. Cosmos DB implementeert ook API’s voor[Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) en [Azure Table Storage](table-introduction.md). U kunt stuurprogramma's (en hulpprogramma's) van clients voor de meestgebruikte NoSQL-API's (zoals MongoDB, Cassandra en Gremlin) rechtstreeks naar uw Cosmos-database verwijzen. Door ondersteuning te bieden voor wire-protocollen van veelgebruikte NoSQL-API's kan Cosmos DB u het volgende bieden:

* U kunt uw toepassing eenvoudig migreren naar Cosmos DB met behoud van belangrijke onderdelen van uw toepassingslogica.
* Behoud de overdraagbaarheid van uw toepassing zonder dat u de vrijheid verliest om van cloudleverancier te veranderen.
* Ontvang een volledig beheerde cloudservice met toonaangevende SLA's met een solide financiële basis voor de meestgebruikte NoSQL-API's. 
* U kunt de ingerichte doorvoer en opslag voor uw databases elastisch schalen op basis van uw behoeften, en u betaalt alleen voor de doorvoer en opslag die u nodig hebt. Dit leidt tot aanzienlijke kostenbesparingen.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Oplossingen die profiteren van Azure Cosmos DB

Alle [toepassingen voor web, mobiel, games en IoT](use-cases.md) die op [wereldwijde](distribute-data-globally.md) schaal en met zeer snelle reactietijden zeer grote hoeveelheden gegevens en lees- en schrijfbewerkingen moeten verwerken voor een verscheidenheid aan gegevens, profiteren van de [gegarandeerde](https://azure.microsoft.com/support/legal/sla/cosmos-db/) hoge beschikbaarheid, hoge doorvoer, lage latentie en instelbare consistentie van Cosmos DB. Kom meer te weten over hoe Cosmos DB kan worden gebruikt om toepassingen voor [IoT en telematica](use-cases.md#iot-and-telematics), [detailhandel en marketing](use-cases.md#retail-and-marketing), [gaming](use-cases.md#gaming) en [web en mobiel](use-cases.md#web-and-mobile-applications) te maken.

## <a name="next-steps"></a>Volgende stappen

Kom meer te weten over de kernconcepten van Cosmos DB op het gebied van [wereldwijde turnkeydistributie](distribute-data-globally.md) en [partitionering](partitioning-overview.md) en [ingerichte doorvoer](request-units.md).

Lees onze snelstartgidsen om snel aan de slag te gaan met Azure Cosmos DB:

* [Aan de slag met de SQL-API van Azure Cosmos DB](create-sql-api-dotnet.md)
* [Aan de slag met de API van Azure Cosmos DB voor MongoDB](create-mongodb-nodejs.md)
* [Aan de slag met de Cassandra-API van Azure Cosmos DB](create-cassandra-dotnet.md)
* [Aan de slag met de Gremlin-API van Azure Cosmos DB](create-graph-dotnet.md)
* [Aan de slag met de Table-API van Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/)
