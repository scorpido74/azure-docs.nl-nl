---
title: Algemene scenario's en scenario's voor Azure Cosmos DB
description: 'Meer informatie over de top vijf use cases voor Azure Cosmos DB: door de gebruiker gegenereerde inhoud, logboekregistratie, gegevens in de catalogus, gebruikersgegevens voor de voorkeuren en Internet of Things (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 04c1466c47d28f638bf7461050cb06b59c7d2396
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614222"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Algemene scenario's Azure Cosmos DB
Dit artikel bevat een overzicht van enkele algemene scenario's voor Azure Cosmos DB.  De aanbevelingen in dit artikel fungeren als een beginpunt tijdens het ontwikkelen van uw toepassing met Cosmos DB.   

Na het lezen van dit artikel, zal het mogelijk om de volgende vragen te beantwoorden: 

* Wat zijn de algemene scenario's voor Azure Cosmos DB?
* Wat zijn de voordelen van het gebruik van Azure Cosmos DB voor retail toepassingen?
* Wat zijn de voordelen van het gebruik van Azure Cosmos DB als een gegevensarchief voor Internet of Things (IoT)-systemen?
* Wat zijn de voordelen van het gebruik van Azure Cosmos DB voor webtoepassingen en mobiele toepassingen?

## <a name="introduction"></a>Inleiding
[Azure Cosmos DB](../cosmos-db/introduction.md) is de wereldwijd gedistribueerde databaseservice van Microsoft. De service is ontworpen om te kunnen klanten elastisch (en onafhankelijk) doorvoer en opslag schalen naar een willekeurig aantal geografische regio's. Azure Cosmos DB is de eerste wereldwijd gedistribueerde databaseservice in de markt vandaag te bieden uitgebreide [serviceovereenkomsten](https://azure.microsoft.com/support/legal/sla/cosmos-db/) dat doorvoer, latentie, beschikbaarheid en consistentie. 

Azure Cosmos DB is een wereldwijd gedistribueerde, multi-model database die wordt gebruikt in een breed scala aan toepassingen en use-cases. Het is een goede keuze voor een [serverloze](https://azure.com/serverless) toepassing die moet snelle reactietijden volgorde van een, en moet om snel en wereldwijd te schalen. Het biedt ondersteuning voor meerdere gegevens modellen (sleutel-waarde, documenten, grafieken en kolommen) en veel Api's voor gegevens toegang, waaronder [de API van Azure Cosmos DB voor MongoDb](mongodb-introduction.md), [SQL API](documentdb-introduction.md), [Gremlin API](graph-introduction.md)en [Table API](table-introduction.md) native, en in een uitbreidbaar redelijke. 

Hier volgen enkele kenmerken van Azure Cosmos DB waardoor deze zeer geschikt voor krachtige toepassingen met wereldwijde ambities reiken.

* Azure Cosmos DB partities systeemeigen van uw gegevens voor hoge beschikbaarheid en schaalbaarheid. Azure Cosmos DB biedt gegarandeerd 99,99% beschikbaarheid, doorvoer, lage latentie en consistentie op alle accounts voor één regio en alle accounts voor meerdere regio's met soepele consistentie en leesbeschikbaarheid van 99,999% beschikbaarheid voor alle databaseaccounts voor meerdere regio's.
* Azure Cosmos DB heeft SSD-opslag met lage latentie volgorde van een reactietijden.
* Azure Cosmos DB-ondersteuning voor consistentieniveaus zoals uiteindelijke, consistent voorvoegsel, sessie en gebonden veroudering kan volledige flexibiliteit en lage kosten op prestaties verhouding. Er is geen database-service biedt zo veel flexibiliteit als Azure Cosmos DB in de niveaus consistentie. 
* Azure Cosmos DB is een flexibele gegevens-vriendelijk-prijsmodel dat meters opslag en doorvoer onafhankelijk van elkaar.
* Azure Cosmos DB de gereserveerde doorvoer model kunt u denken in termen van het aantal lees-/ schrijfbewerkingen in plaats van de CPU/geheugen/IOPs van de onderliggende hardware.
* Azure Cosmos DB van ontwerp kunt die u op aanvraag voor grote volumes in volgorde van Biljoenen aanvragen per dag schalen.

Deze kenmerken zijn nuttig in web, mobiel, games en IoT-toepassingen die snelle reactietijden nodig hebt en nodig voor het verwerken van grote hoeveelheden lees- en schrijfbewerkingen.

## <a name="iot-and-telematics"></a>IoT en telematica
IoT use cases zijn vaak van sommige patronen in de manier waarop ze, verwerken opnemen, delen en opslaan van gegevens.  Deze systemen moeten eerst, om op te nemen bursts met gegevens van apparaat sensoren van diverse landinstellingen. Vervolgens worden deze systemen verwerken en analyseren van streaming gegevens realtime statistieken. De gegevens vervolgens naar koude opslag voor batchanalyse gearchiveerd. Microsoft Azure biedt uitgebreide services die kunnen worden toegepast voor IoT use cases, inclusief Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight en Power BI. 

![Azure Cosmos DB-IoT-referentiearchitectuur](./media/use-cases/iot.png)

Pieken van gegevens kunnen worden opgenomen met Azure Event Hubs als gegevensopname hoge doorvoer met lage latentie biedt. Gegevens die zijn opgenomen die moet worden verwerkt voor realtime inzicht kunt met Azure Stream Analytics worden softwareproducten voor realtime analyses. Gegevens kunnen worden geladen in Azure Cosmos DB voor ad-hoc uitvoeren van query's. Zodra de gegevens zijn geladen in Azure Cosmos DB, zijn de gegevens zijn gereed om te worden opgevraagd. Bovendien kunnen nieuwe gegevens en wijzigingen in bestaande gegevens worden gelezen op wijzigingenfeed. Wijzigings invoer is een permanent, alleen-toevoegen logboek waarmee wijzigingen in Cosmos-containers in sequentiële volg orde worden opgeslagen. De alle gegevens of alleen wijzigingen aan gegevens in Azure Cosmos DB kunnen worden gebruikt als verwijzingsgegevens als onderdeel van realtime analyses. Bovendien kunnen gegevens verder worden verfijnd en verwerkt door Azure Cosmos DB-gegevens verbinden met HDInsight voor Pig, Hive of toewijzen/verminderen taken.  Getransformeerde gegevens wordt vervolgens geladen terug naar Azure Cosmos DB voor rapportage.   

Zie voor een voorbeeld van IoT-oplossing met behulp van Azure Cosmos DB, Event hubs en Storm, de [opslagplaats van de hdinsight-storm-voorbeelden op GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Zie voor meer informatie over Azure-producten voor IoT, [maken van het Internet van uw dingen](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Detailhandel en marketing
Azure Cosmos DB wordt veel gebruikt in Microsoft eigen e-commerce-platformen, met de Windows Store en XBox Live. Dit wordt ook gebruikt in de detailhandel voor het opslaan van gegevens in de catalogus en voor gebeurtenisbronnen in volgorde van verwerking van pijplijnen.

Gebruiksscenario's voor data Catalog hebben betrekking op Opslaan en opvragen van een set kenmerken voor entiteiten zoals personen, plaatsen en producten. Enkele voorbeelden van gegevens in de catalogus zijn gebruikersaccounts, productcatalogi, IoT-apparaatregisters en factuur van materiaal systemen. Kenmerken voor deze gegevens kunnen variëren en na verloop van tijd om te voldoen aan de toepassingsvereisten kunnen wijzigen.

Bekijk een voorbeeld van een productcatalogus voor een leverancier auto-onderdelen. Elk onderdeel mag een eigen kenmerken naast de algemene kenmerken die delen van alle onderdelen hebben. Kenmerken voor een specifiek deel kunnen daarnaast het volgende jaar wanneer een nieuw model wordt vrijgegeven wijzigen. Azure Cosmos DB ondersteunt flexibele schema's en hiërarchische gegevens en het is dus zeer geschikt voor het opslaan van gegevens in de productcatalogus.

![Azure Cosmos DB retail catalogus-referentiearchitectuur](./media/use-cases/product-catalog.png)

Azure Cosmos DB wordt vaak gebruikt voor gebeurtenisbronnen aan power op gebeurtenissen gebaseerde architecturen met behulp van de [wijzigingenfeed](change-feed.md) functionaliteit. De wijzigingenfeed bevat downstream microservices de mogelijkheid om betrouwbaar en incrementeel lezen invoegingen en updates (bijvoorbeeld volgorde gebeurtenissen) gemaakt met een Azure Cosmos DB. Deze functionaliteit kan worden gebruikt om een permanente gebeurtenissenarchief opgeven als een berichtenbroker voor status-gebeurtenissen en werkstroom van het bestellingsproces station tussen talloze microservices (die kan worden geïmplementeerd als [serverloze Azure Functions](https://azure.com/serverless)).

![Azure Cosmos DB bestellen pijplijn-referentiearchitectuur](./media/use-cases/event-sourcing.png)

Bovendien kunnen gegevens die zijn opgeslagen in Azure Cosmos DB worden geïntegreerd met HDInsight voor big data-analyses via Apache Spark-taken. Zie voor meer informatie over de Spark-Connector voor Azure Cosmos DB, [een Spark-taak uitvoeren met Cosmos DB en HDInsight](spark-connector.md).

## <a name="gaming"></a>Gaming
De databaselaag is een essentieel onderdeel van games. Moderne games grafische verwerking uitvoeren op clients van mobiele/console, maar zijn afhankelijk van de cloud om aangepaste en gepersonaliseerde inhoud, zoals gamestatistieken, integratie van sociale media en hoge score scoreborden te leveren. Games vereisen vaak latenties van één milliseconde voor leesbewerkingen en schrijfbewerkingen voor een aantrekkelijke in-game ervaring. Een game-database moet snel en enorme pieken in aanvraagsnelheden verwerken tijdens nieuwe game wordt gestart en functie-updates.

Azure Cosmos DB wordt gebruikt door games als [de doorlopende actieve richting: Geen van de man](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) door de [volgende games](https://www.nextgames.com/)en [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos DB biedt de volgende voordelen voor game-ontwikkelaars:

* Azure Cosmos DB kunt u prestaties kunnen worden geschaald omhoog of omlaag flexibel. Hiermee kunt games voor het afhandelen van bijwerken-profiel en statistieken van tientallen miljoenen gelijktijdige gamers door één API-aanroep.
* Azure Cosmos DB biedt ondersteuning voor milliseconde leest en schrijft om te voorkomen dat een lag tijdens het spel.
* Automatische indexering van Azure Cosmos-DB kunt u filteren op basis van meerdere verschillende eigenschappen in realtime, bijvoorbeeld vinden spelers door hun interne player-id's of hun GameCenter, Facebook, Google-id's of query op basis van het lidmaatschap van de speler in een vereniging van. Dit is mogelijk zonder het bouwen van complexe te indexeren of sharding-infrastructuur.
* Sociale functies, waaronder van in-game, chatberichten, player vereniging lidmaatschappen uitdagingen voltooid, hoge score topscores en sociaal diagram zijn gemakkelijker te implementeren met een flexibel schema.
* Azure Cosmos DB als een beheerde platform-as-a-service (PaaS) vereist minimale installatie en het beheer werken om toe te staan voor snelle herhaling en beperk de marktintroductietijd.

![Azure Cosmos DB-referentiearchitectuur voor gaming](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Web- en mobiele toepassingen
Azure Cosmos DB wordt meestal gebruikt in de web- en mobiele toepassingen, en is geschikt voor het modelleren van sociale interacties, integratie met services van derden, en voor het bouwen van rijke gepersonaliseerde ervaringen. De Cosmos DB SDK's kunnen worden gebruikt build uitgebreide iOS en Android-toepassingen met behulp van de populaire [Xamarin framework](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Sociale-toepassingen
Een veelvoorkomende use-case voor Azure Cosmos DB is voor het opslaan en opvragen van gebruikers gegenereerde inhoud (UGC) voor webtoepassingen, mobiele en sociale media-toepassingen. Enkele voorbeelden van UGC zijn chatsessies, tweets, blogberichten, beoordelingen en opmerkingen. De UGC in sociale media-toepassingen is vaak een combinatie van vrije tekst, eigenschappen, labels en relaties die niet beperkt Star structuur tot zijn. Inhoud zoals Chatgesprekken, opmerkingen en berichten kunnen worden opgeslagen in Cosmos DB zonder transformaties of complexe object op relationele gegevens lagen.  Gegevenseigenschappen kunnen worden toegevoegd of gewijzigd eenvoudig zodat deze overeenkomt met vereisten als ontwikkelaars de toepassingscode herhalen, dus bevordering van snelle ontwikkeling.  

Toepassingen die zijn geïntegreerd met sociale netwerken van derden moeten reageren op het wijzigen van schema's van deze netwerken. Als gegevens in Cosmos DB standaard automatisch geïndexeerd, is gegevens gereed om te worden opgevraagd op elk gewenst moment. Daarom kan hebben deze toepassingen de flexibiliteit om op te halen projecties aan de hand van hun respectieve behoeften.

Veel van de sociale toepassingen uitvoeren op wereldwijde schaal en onvoorspelbare gebruikspatronen kunnen vertonen. Flexibiliteit bij het schalen van het gegevensarchief is essentieel als het niveau van de toepassing kan worden geschaald zodat deze overeenkomen met de aanvraag voor gebruik.  U kunt uitschalen door extra gegevenspartities onder een Cosmos DB-account toe te voegen.  Bovendien kunt u ook aanvullende Cosmos DB-accounts maken in meerdere regio's. Zie voor Cosmos DB-servicebeschikbaarheid regio [Azure-regio's](https://azure.microsoft.com/regions/#services).

![Azure Cosmos DB web-app-referentiearchitectuur](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personalisatie
Moderne toepassingen geleverd tegenwoordig met complexe weergaven en ervaringen. Dit zijn doorgaans dynamisch is, catering gebruikersvoorkeuren of stemmingen en huisstijl van uw behoeften. Toepassingen moeten daarom kunnen persoonlijke instellingen efficiënt voor het renderen van UI-elementen en ervaringen snel op te halen. 

JSON, een indeling die wordt ondersteund door Cosmos DB, is een effectieve indeling voor gebruikersinterface lay-outgegevens als deze niet alleen lichtgewicht is, maar ook kan worden eenvoudig geïnterpreteerd door JavaScript. Cosmos DB biedt instelbare consistentieniveaus waarmee snelle leesbewerkingen met lage latentie van schrijfbewerkingen. Opslaan van gegevens van UI-indeling met inbegrip van persoonlijke instellingen als JSON-documenten in Cosmos DB is daarom een efficiënte manier om op te halen van deze gegevens via de kabel.

![Azure Cosmos DB web-app-referentiearchitectuur](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Volgende stappen
Als u wilt aan de slag met Azure Cosmos DB, volgt u ons [quick starts voor een](create-sql-api-dotnet.md), die u helpt bij het maken van een account en aan de slag met Cosmos DB. 

Of, als u wilt meer informatie over klanten met Cosmos DB, de volgende verhalen van klanten beschikbaar zijn:

* [Jet.com](https://jet.com). E-commerceproduct de toppositie, wordt uitgevoerd op de Microsoft-cloud, maakt gebruik van Cosmos DB op wereldwijde schaal.
* [Asos.com](https://www.asos.com/). Asos.com is een Britse online winkel voor manier en mooie. Asos is voornamelijk gericht op jonge volwassenen, verkoopt meer dan 850 merken, evenals een eigen reeks kleding en accessoires.
* [Toyota](https://www.toyota.com/). Toyota Motor Corporation is een Japanse autobranche fabrikant. Cosmos DB Toyota gebruikt voor een algemene IoT-app.
* [Citrix](https://customers.microsoft.com/story/citrix). Citrix ontwikkelt single-sign-on oplossing met behulp van Azure Service Fabric en Azure Cosmos DB
* [TEXA](https://customers.microsoft.com/story/texaspa) TEXA van revolutionaire IoT-oplossing voor eigenaren van het voertuig bespaart u tijd, geld, gas- en mogelijk zich bevinden.
* [De Domino Pizza](https://www.dominos.com). De Domino Pizza Inc. is een Amerikaanse pizza restaurantketen.
* [Hiermee bepaalt u Johnson](https://www.johnsoncontrols.com). Johnson Controls is een wereld wijde en meervoudige markt leider voor een breed scala aan klanten in meer dan 150 landen/regio's.
* [Microsoft Windows, universele Store, Azure IoT Hub, Xbox Live en andere services internetschaal](https://azure.microsoft.com/blog/how-azure-documentdb-planet-scale-nosql-helps-run-microsoft-s-own-businesses/). Hoe Microsoft maakt zeer schaalbare services met Azure Cosmos DB.
* [Microsoft Data- en analyseteam](https://customers.microsoft.com/story/microsoftdataandanalytics). Microsoft gegevens- en analyseteam bereikt een verzameling van big data op planetaire schaal met Azure Cosmos DB
* [Sulekha.com](https://customers.microsoft.com/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha maakt gebruik van Azure Cosmos DB voor het aansluiten van klanten en bedrijven in India.
* [NewOrbit](https://customers.microsoft.com/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit duurt vlucht met Azure Cosmos DB.
* [Affinio](https://customers.microsoft.com/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). Affinio stapt over van AWS naar Azure Cosmos DB om sociale gegevens op schaal.
* [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). De lopende doorlopende richting: Er is geen schiet van de mens om #1 ondersteund door Azure Cosmos DB.
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Hoe Halo 5 spelen implementeerde met Azure Cosmos DB.
* [Cortana Analytics-galerie](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Cortana Analytics-galerie - een schaalbare communitysite die is gebouwd op Azure Cosmos DB.
* [Koud kunstje](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Toonaangevende Integrator biedt multinationale ondernemingen globale inzicht in enkele minuten met flexibele Cloud-technologieën.
* [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Intelligence toevoegen aan het nieuws om informatie te geven met een plan voor de betrokken burgers. 
* [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Voor de kleur van de consistente over de hele wereld zet belangrijke merken op Beveiligingsgroepen. En schakelt u de Beveiligingsgroepen in Azure.
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Wereldwijd leider Telenor gebruikt de cloud om de snelheid van een startup. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). Het archief van de toekomst wordt uitgevoerd op snelle zoeken en de eenvoudige stroom van gegevens.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Op basis van een Azure-softwareplatform neemt hindernis tussen bedrijven en klanten
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Slimme koelkast van weka worden vaccins beter beheerd, zodat er meer mensen kunnen worden beveiligd tegen ziektes
* [Oranje Tribes](https://customers.microsoft.com/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Er is meer food van de App niet voldoet aan de ogen of de mond vandaan te houden.
* [Real Madrid](https://customers.microsoft.com/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Real Madrid brengt het stadion dichter bij 450 miljoen fans over de hele wereld, met de Microsoft Cloud.
* [Tuku](https://customers.microsoft.com/story/tuku-makes-car-buying-fun-with-help-from-azure-services). TUKU maakt auto plezier met behulp van Azure-services kopen
