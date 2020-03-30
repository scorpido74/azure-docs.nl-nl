---
title: Veelvoorkomende use cases en scenario's voor Azure Cosmos DB
description: 'Meer informatie over de vijf belangrijkste use cases voor Azure Cosmos DB: door gebruikers gegenereerde inhoud, logboekregistratie van gebeurtenissen, catalogusgegevens, gebruikersvoorkeuren gegevens en Internet of Things (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: de2bc551547706fb820813e57996e77bf49148d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888937"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Algemene gebruiksvoorbeelden van Azure Cosmos DB
In dit artikel vindt u een overzicht van verschillende veelvoorkomende use cases voor Azure Cosmos DB.  De aanbevelingen in dit artikel dienen als uitgangspunt bij het ontwikkelen van uw toepassing met Cosmos DB.   

Na het lezen van dit artikel u de volgende vragen beantwoorden: 

* Wat zijn de gangbare use cases voor Azure Cosmos DB?
* Wat zijn de voordelen van het gebruik van Azure Cosmos DB voor retailtoepassingen?
* Wat zijn de voordelen van het gebruik van Azure Cosmos DB als gegevensarchief voor IoT-systemen (Internet of Things).
* Wat zijn de voordelen van het gebruik van Azure Cosmos DB voor web- en mobiele toepassingen?

## <a name="introduction"></a>Inleiding
[Azure Cosmos DB](../cosmos-db/introduction.md) is de wereldwijd gedistribueerde databaseservice van Microsoft. De service is ontworpen om klanten in staat te stellen de doorvoer en opslag elastisch (en onafhankelijk) te schalen in een willekeurig aantal geografische regio's. Azure Cosmos DB is de eerste wereldwijd gedistribueerde databaseservice op de markt die vandaag uitgebreide [servicelevelovereenkomsten](https://azure.microsoft.com/support/legal/sla/cosmos-db/) aanbiedt, waaronder doorvoer, latentie, beschikbaarheid en consistentie. 

Azure Cosmos DB is een wereldwijd gedistribueerde database met meerdere modellen die wordt gebruikt in een breed scala aan toepassingen en use cases. Het is een goede keuze voor elke [serverloze](https://azure.com/serverless) toepassing die lage responstijden van de orde van milliseconde nodig heeft en snel en wereldwijd moet schalen. Het ondersteunt meerdere gegevensmodellen (key-value, documenten, grafieken en zuilar) en veel API's voor gegevenstoegang, waaronder [Azure Cosmos DB's API voor MongoDB,](mongodb-introduction.md) [SQL API,](documentdb-introduction.md) [Gremlin API](graph-introduction.md)en Tables [API](table-introduction.md) native, en op een uitbreidbare manier. 

Hieronder volgen enkele kenmerken van Azure Cosmos DB die het goed geschikt maken voor krachtige toepassingen met wereldwijde ambitie.

* Azure Cosmos DB partitiet uw gegevens native voor hoge beschikbaarheid en schaalbaarheid. Azure Cosmos DB biedt 99,99% garanties voor beschikbaarheid, doorvoer, lage latentie en consistentie op alle accounts met één regio en alle multi-region accounts met ontspannen consistentie, en 99,999% lees beschikbaarheid op alle multi-region database accounts.
* Azure Cosmos DB heeft SSD-back-opslag met responstijden met een lage latentie van de responstijden van de milliseconde.
* Azure Cosmos DB's ondersteuning voor consistentieniveaus zoals het uiteindelijke, consistente voorvoegsel, sessie en begrensde staleness zorgt voor volledige flexibiliteit en een lage cost-to-performance ratio. Geen enkele databaseservice biedt zoveel flexibiliteit als Azure Cosmos DB in niveausconsistentie. 
* Azure Cosmos DB heeft een flexibel datavriendelijk prijsmodel dat opslag en doorvoer onafhankelijk meet.
* Met het gereserveerde doorvoermodel van Azure Cosmos DB u denken in termen van aantal lees-/schrijfbewerkingen in plaats van CPU/geheugen/IoPs van de onderliggende hardware.
* Met het ontwerp van Azure Cosmos DB u schalen naar enorme aanvraagvolumes in de orde van triljoenen aanvragen per dag.

Deze kenmerken zijn gunstig in web-, mobiele, gaming- en IoT-toepassingen die lage responstijden nodig hebben en enorme hoeveelheden reads en schrijft moeten verwerken.

## <a name="iot-and-telematics"></a>IoT en telematica
IoT-usecases delen vaak bepaalde patronen in hoe ze gegevens innemen, verwerken en opslaan.  Ten eerste moeten deze systemen uitbarstingen van gegevens opnemen van apparaatsensoren van verschillende locaties. Vervolgens verwerken en analyseren deze systemen streaminggegevens om realtime inzichten te verkrijgen. De gegevens worden vervolgens gearchiveerd naar koude opslag voor batch-analyse. Microsoft Azure biedt uitgebreide services die kunnen worden toegepast voor IoT-gebruikscases, waaronder Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight en Power BI. 

![Azure Cosmos DB IoT-referentiearchitectuur](./media/use-cases/iot.png)

Bursts van gegevens kunnen worden ingenomen door Azure Event Hubs omdat het een hoge doorvoergegevensopname biedt met lage latentie. Gegevens die moeten worden verwerkt voor realtime inzicht, kunnen worden doorgesluisd naar Azure Stream Analytics voor realtime analyses. Gegevens kunnen worden geladen in Azure Cosmos DB voor adhoc query's. Zodra de gegevens in Azure Cosmos DB zijn geladen, kunnen de gegevens worden opgevraagd. Daarnaast kunnen nieuwe gegevens en wijzigingen in bestaande gegevens worden gelezen op change feed. Change feed is een persistent, toevoegen alleen logboek dat wijzigingen in Cosmos containers opslaat in opeenvolgende volgorde. Alle gegevens of alleen wijzigingen in gegevens in Azure Cosmos DB kunnen worden gebruikt als referentiegegevens als onderdeel van realtime analyses. Bovendien kunnen gegevens verder worden verfijnd en verwerkt door Azure Cosmos DB-gegevens te koppelen aan HDInsight voor taken voor varkens, kasten of kaarten/verkleinen.  Verfijnde gegevens worden vervolgens teruggeladen naar Azure Cosmos DB voor rapportage.   

Zie de [hdinsight-storm-voorbeelden repository op GitHub](https://github.com/hdinsight/hdinsight-storm-examples/)voor een voorbeeld iot-oplossing met Azure Cosmos DB, EventHubs en Storm.

Zie Het internet van uw spullen maken voor meer informatie over [Azure-aanbiedingen](https://www.microsoft.com/en-us/internet-of-things)voor IoT. 

## <a name="retail-and-marketing"></a>Retail en marketing
Azure Cosmos DB wordt veel gebruikt in microsofts eigen e-commerceplatforms, die de Windows Store en XBox Live uitvoeren. Het wordt ook gebruikt in de detailhandel voor het opslaan van catalogusgegevens en voor event sourcing in orderverwerkingspijplijnen.

Scenario's voor het gebruik van catalogusgegevens omvatten het opslaan en opvragen van een set kenmerken voor entiteiten zoals personen, plaatsen en producten. Enkele voorbeelden van catalogusgegevens zijn gebruikersaccounts, productcatalogi, Iot-apparaatregisters en materiaalfacturliters. Kenmerken voor deze gegevens kunnen variëren en kunnen in de loop van de tijd worden gewijzigd om aan de toepassingsvereisten te voldoen.

Neem een voorbeeld van een productcatalogus voor een leverancier van auto-onderdelen. Elk onderdeel kan zijn eigen kenmerken hebben naast de algemene kenmerken die alle onderdelen delen. Bovendien kunnen kenmerken voor een specifiek onderdeel het volgende jaar veranderen wanneer een nieuw model wordt uitgebracht. Azure Cosmos DB ondersteunt flexibele schema's en hiërarchische gegevens en is daarom zeer geschikt voor het opslaan van productcatalogusgegevens.

![Referentiearchitectuur voor Azure Cosmos DB-detailhandelscatalogus](./media/use-cases/product-catalog.png)

Azure Cosmos DB wordt vaak gebruikt voor event sourcing om event driven architecturen van stroom te voorzien met behulp van de [change feed-functionaliteit.](change-feed.md) De wijzigingsfeed biedt downstream microservices de mogelijkheid om inserts en updates (bijvoorbeeld ordergebeurtenissen) die zijn gemaakt naar een Azure Cosmos DB, betrouwbaar en stapsgewijs te lezen. Deze functionaliteit kan worden gebruikt om een permanente gebeurtenisarchief te bieden als berichtenmakelaar voor statuswijzigende gebeurtenissen en de werkstroom voor orderverwerking tussen veel microservices (die kunnen worden geïmplementeerd als [serverloze Azure-functies).](https://azure.com/serverless)

![Azure Cosmos DB-referentiearchitectuur voor pijplijnen](./media/use-cases/event-sourcing.png)

Bovendien kunnen gegevens die zijn opgeslagen in Azure Cosmos DB worden geïntegreerd met HDInsight voor big data-analyses via Apache Spark-taken. Zie [Een Spark-taak uitvoeren met Cosmos DB en HDInsight](spark-connector.md)voor meer informatie over de Spark-connector voor Azure Cosmos DB.

## <a name="gaming"></a>Gaming
De databaselaag is een cruciaal onderdeel van gamingtoepassingen. Moderne games voeren grafische verwerking uit op mobiele/console-clients, maar vertrouwen op de cloud om aangepaste en gepersonaliseerde inhoud te leveren, zoals in-game statistieken, integratie van sociale media en leaderboards met hoge score. Games vereisen vaak latencies van één milliseconde voor lezen en schrijven om een boeiende in-game-ervaring te bieden. Een spel database moet snel zijn en in staat zijn om enorme pieken in de aanvraag tarieven te behandelen tijdens nieuwe game lanceringen en functie-updates.

Azure Cosmos DB wordt gebruikt door games zoals [The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) by Next [Games](https://www.nextgames.com/), en [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos DB biedt de volgende voordelen voor game-ontwikkelaars:

* Azure Cosmos DB maakt het mogelijk om prestaties elastisch op te schalen of te dalen. Hierdoor kunnen games het bijwerken van profiel en statistieken van tientallen tot miljoenen gelijktijdige gamers verwerken door één API-aanroep te maken.
* Azure Cosmos DB ondersteunt milliseconde leest en schrijft om te voorkomen dat vertragingen tijdens het spelen.
* Azure Cosmos DB's automatische indexering maakt het mogelijk om te filteren tegen meerdere verschillende eigenschappen in real-time, bijvoorbeeld, spelers te lokaliseren door hun interne speler-id's, of hun GameCenter, Facebook, Google ID's of query op basis van speler lidmaatschap in een gilde. Dit is mogelijk zonder complexe indexering of sharding infrastructuur te bouwen.
* Sociale functies, waaronder in-game chatberichten, leden van spelersgilden, voltooide uitdagingen, high-score leaderboards en sociale grafieken zijn gemakkelijker te implementeren met een flexibel schema.
* Azure Cosmos DB als managed platform-as-a-service (PaaS) vereiste minimale installatie- en beheerwerkzaamheden om snelle iteratie mogelijk te maken en de time-to-market te verkorten.

![Azure Cosmos DB-referentiearchitectuur voor gaming](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Web- en mobiele toepassingen
Azure Cosmos DB wordt vaak gebruikt in web- en mobiele toepassingen en is zeer geschikt voor het modelleren van sociale interacties, het integreren met services van derden en voor het bouwen van rijke gepersonaliseerde ervaringen. De Cosmos DB SDKs kan worden gebruikt bouwen rijke iOS en Android-applicaties met behulp van de populaire [Xamarin kader](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Sociale toepassingen
Een veelgebruikte use case voor Azure Cosmos DB is het opslaan en opvragen van door gebruikers gegenereerde inhoud (UGC) voor web-, mobiele en sociale mediatoepassingen. Enkele voorbeelden van UGC zijn chatsessies, tweets, blogposts, beoordelingen en opmerkingen. Vaak is de UGC in sociale media toepassingen is een mix van vrije vorm tekst, eigenschappen, tags en relaties die niet worden begrensd door rigide structuur. Inhoud zoals chats, opmerkingen en berichten kan worden opgeslagen in Cosmos DB zonder dat er transformaties of complexe objecten nodig zijn voor relationele toewijzingslagen.  Gegevenseigenschappen kunnen eenvoudig worden toegevoegd of aangepast aan de vereisten als ontwikkelaars die de toepassingscode herhalen, waardoor een snelle ontwikkeling wordt bevorderd.  

Toepassingen die integreren met sociale netwerken van derden, moeten reageren op veranderende schema's van deze netwerken. Aangezien gegevens standaard automatisch worden geïndexeerd in Cosmos DB, kunnen gegevens op elk gewenst moment worden opgevraagd. Daarom hebben deze toepassingen de flexibiliteit om prognoses op te halen volgens hun respectieve behoeften.

Veel van de sociale toepassingen draaien op wereldwijde schaal en kunnen onvoorspelbare gebruikspatronen vertonen. Flexibiliteit bij het schalen van het gegevensarchief is essentieel omdat de toepassingslaag wordt geschaald om aan de gebruiksvraag te voldoen.  U uitschalen door extra gegevenspartities toe te voegen onder een Cosmos DB-account.  Daarnaast u ook extra Cosmos DB-accounts maken in meerdere regio's. Zie [Azure-regio's](https://azure.microsoft.com/regions/#services)voor de beschikbaarheid van cosmos DB-serviceregio's .

![Referentiearchitectuur voor Azure Cosmos DB-web-apps](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personalisatie
Tegenwoordig komen moderne toepassingen met complexe weergaven en ervaringen. Deze zijn meestal dynamisch, catering aan voorkeuren van de gebruiker of stemmingen en branding behoeften. Daarom moeten toepassingen gepersonaliseerde instellingen effectief kunnen ophalen om ui-elementen en -ervaringen snel weer te geven. 

JSON, een formaat dat wordt ondersteund door Cosmos DB, is een effectief formaat om ui-lay-outgegevens weer te geven, omdat het niet alleen lichtgewicht is, maar ook gemakkelijk kan worden geïnterpreteerd door JavaScript. Cosmos DB biedt tunable consistentie niveaus die het mogelijk maken snel leest met lage latentie schrijft. Daarom is het opslaan van ui-lay-outgegevens, inclusief gepersonaliseerde instellingen als JSON-documenten in Cosmos DB, een effectief middel om deze gegevens over de draad te krijgen.

![Referentiearchitectuur voor Azure Cosmos DB-web-apps](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Volgende stappen

* Om aan de slag te gaan met Azure Cosmos DB, volg je onze [snelle start,](create-sql-api-dotnet.md)die je door het maken van een account en aan de slag met Cosmos DB.

* Als u meer wilt lezen over klanten die Azure Cosmos DB gebruiken, raadpleegt u de pagina [met casestudy's voor klanten.](https://azure.microsoft.com/case-studies/?service=cosmos-db)
