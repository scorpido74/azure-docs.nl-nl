---
title: Inzicht in de verschillen tussen Azure Cosmos DB NoSQL en relationele databases
description: In dit artikel worden de verschillen tussen NoSQL en relationele databases opgesomd
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896621"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Inzicht in de verschillen tussen NoSQL en relationele databases

In dit artikel worden enkele van de belangrijkste voordelen van NoSQL-databases opeenvat in relationele databases. We zullen ook enkele van de uitdagingen in het werken met NoSQL bespreken. Voor een diepgaande blik op de verschillende data stores die er bestaan, neem een kijkje op ons artikel over [het kiezen van de juiste data store](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview).

## <a name="high-throughput"></a>Hoge doorvoersnelheid

Een van de meest voor de hand liggende uitdagingen bij het onderhouden van een relationeel databasesysteem is dat de meeste relationele engines sloten en vergrendelingen toepassen om strikte [ACID semantiek](https://en.wikipedia.org/wiki/ACID)af te dwingen. Deze aanpak heeft voordelen in termen van het waarborgen van een consistente gegevensstatus in de database. Er zijn echter zware trade-offs met betrekking tot gelijktijdigheid, latentie en beschikbaarheid. Als gevolg van deze fundamentele architectonische beperkingen, hoge transactionele volumes kunnen resulteren in de noodzaak om handmatig shard gegevens. Het implementeren van handmatige sharding kan een tijdrovende en pijnlijke oefening zijn.

In deze scenario's kunnen [gedistribueerde databases](https://en.wikipedia.org/wiki/Distributed_database) een meer schaalbare oplossing bieden. Onderhoud kan echter nog steeds een kostbare en tijdrovende exercitie zijn. Beheerders moeten mogelijk extra werk doen om ervoor te zorgen dat het gedistribueerde karakter van het systeem transparant is. Ze kunnen ook rekening moeten houden met de "losgekoppelde" aard van de database.

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) vereenvoudigt deze uitdagingen door wereldwijd te worden geïmplementeerd in alle Azure-regio's. Partitiebereiken kunnen dynamisch worden onderverdeeld om de database naadloos te laten groeien in lijn met de toepassing, terwijl tegelijkertijd een hoge beschikbaarheid behouden blijft. Fijnmazige multi-tenancy en strak gecontroleerde cloud-native resource governance vergemakkelijkt [verbazingwekkende latentiegaranties](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency) en voorspelbare prestaties. Partitioneren wordt volledig beheerd, zodat beheerders geen code hoeven te schrijven of partities hoeven te beheren.

Als uw transactionele volumes extreme niveaus bereiken, zoals vele duizenden transacties per seconde, moet u een gedistribueerde NoSQL-database overwegen. Overweeg Azure Cosmos DB voor maximale efficiëntie, onderhoudsgemak en lagere eigendomskosten.

![Back-end](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>Hiërarchische gegevens

Er zijn een aanzienlijk aantal use cases waarbij transacties in de database veel bovenliggende-onderliggende relaties kunnen bevatten. Deze relaties kunnen aanzienlijk groeien na verloop van tijd, en blijken moeilijk te beheren. In de jaren tachtig ontstonden er wel vormen van [hiërarchische databanken,](https://en.wikipedia.org/wiki/Hierarchical_database_model) maar waren ze niet populair vanwege inefficiëntie in de opslag. Ze verloren ook tractie als [Ted Codd's relationele model](https://en.wikipedia.org/wiki/Relational_model) werd de de facto standaard gebruikt door vrijwel alle mainstream database management systemen.

Vandaag de dag is de populariteit van databases in documentstijl echter aanzienlijk toegenomen. Deze databases kunnen worden beschouwd als een heruitvinden van de hiërarchische database paradigma, nu ongeremd door bezorgdheid rond de kosten van het opslaan van gegevens op schijf. Als gevolg hiervan kan het onderhouden van veel complexe bovenliggende-onderliggende entiteitsrelaties in een relationele database nu worden beschouwd als een antipatroon in vergelijking met moderne documentgeoriënteerde benaderingen.

De opkomst van [object georiënteerd ontwerp](https://en.wikipedia.org/wiki/Object-oriented_design), en de [impedantie mismatch](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) die ontstaat bij het combineren met relationele modellen, benadrukt ook een anti-patroon in relationele databases voor bepaalde use cases. Verborgen maar vaak aanzienlijke onderhoudskosten kunnen ontstaan als gevolg. Hoewel [ORM-benaderingen](https://en.wikipedia.org/wiki/Object-relational_mapping) zijn geëvolueerd om dit gedeeltelijk te beperken, vloeien documentgeoriënteerde databases toch veel beter samen met objectgeoriënteerde benaderingen. Met deze aanpak worden ontwikkelaars niet gedwongen zich in te zetten voor ORM-stuurprogramma's of op maat gemaakte taalspecifieke [OO Database-engines.](https://en.wikipedia.org/wiki/Object_database) Als uw gegevens veel bovenliggende-onderliggende relaties en diepgaande hiërarchieniveaus bevatten, u overwegen een NoSQL-documentdatabase te gebruiken, zoals de [Azure Cosmos DB SQL API.](https://docs.microsoft.com/azure/cosmos-db/introduction)

![Besteldetails](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>Complexe netwerken en relaties

Ironisch genoeg, gezien hun naam, relationele databases presenteren een minder dan optimale oplossing voor het modelleren van diepe en complexe relaties. De reden hiervoor is dat relaties tussen entiteiten niet daadwerkelijk bestaan in een relationele database. Ze moeten worden berekend tijdens runtime, met complexe relaties die cartesiaans joins vereisen om toewijzing met query's mogelijk te maken. Als gevolg hiervan worden bewerkingen exponentieel duurder in termen van berekening naarmate relaties toenemen. In sommige gevallen wordt een relationele database die dergelijke entiteiten probeert te beheren onbruikbaar.

Verschillende vormen van "Netwerk" databases ontstonden in de tijd dat relationele databases ontstonden, maar net als bij hiërarchische databases, hadden deze systemen moeite om aan populariteit te winnen. Trage adoptie was te wijten aan een gebrek aan use cases op het moment, en opslag inefficiënties. Vandaag, grafiek database engines kunnen worden beschouwd als een re-opkomst van het netwerk database paradigma. Het belangrijkste voordeel van deze systemen is dat relaties worden opgeslagen als "eersteklas burgers" in de database. Dus, het doorkruisen van relaties kan worden gedaan in constante tijd, in plaats van het verhogen van de tijd complexiteit met elke nieuwe join of cross product.

Als u een complex netwerk van relaties in uw database onderhoudt, u een grafiekdatabase overwegen, zoals de [Azure Cosmos DB Gremlin API](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) voor het beheren van deze gegevens.

![Graph](./media/relational-or-nosql/graph.png)

Azure Cosmos DB is een databaseservice met meerdere modellen, die een API-projectie biedt voor alle belangrijke NoSQL-modeltypen; Kolom-familie, Document, Grafiek en Sleutelwaarde. De [lagen Gremlin (graph)](https://docs.microsoft.com/azure/cosmos-db/gremlin-support) en SQL (Core) Document API zijn volledig interoperabel. Dit heeft voordelen voor het schakelen tussen verschillende modellen op het niveau van de programmeerbaarheid. Grafiekopslag kan worden opgevraagd in termen van zowel complexe netwerktraversals als transacties gemodelleerd als documentrecords in dezelfde winkel.

## <a name="fluid-schema"></a>Vloeiend schema

Een ander bijzonder kenmerk van relationele databases is dat schema's moeten worden gedefinieerd op het ontwerptijd. Dit heeft voordelen in termen van referentiële integriteit en conformiteit van gegevens. Het kan echter ook beperkend zijn naarmate de toepassing groeit. Reageren op wijzigingen in het schema in logisch verschillende modellen die dezelfde tabel- of databasedefinitie delen, kan in de loop van de tijd complex worden. Dergelijke use cases profiteren vaak van het schema dat wordt overgedragen aan de toepassing om per record te beheren. Dit vereist dat de database "schema agnostisch" is en dat records "zelfbeschrijvend" kunnen zijn in termen van de gegevens die erin zijn opgenomen.

Als u gegevens beheert waarvan de structuren voortdurend in een hoog tempo veranderen, vooral als transacties afkomstig kunnen zijn van externe bronnen waar het moeilijk is om overeenstemming in de database af te dwingen, u een meer schema-agnostische benadering overwegen met behulp van een beheerde NoSQL-databaseservice zoals Azure Cosmos DB.

## <a name="microservices"></a>Microservices

Het [microservices](https://en.wikipedia.org/wiki/Microservices) patroon is de laatste jaren aanzienlijk gegroeid. Dit patroon heeft zijn wortels in [service-georiënteerde architectuur.](https://en.wikipedia.org/wiki/Service-oriented_architecture) De de-facto standaard voor gegevensoverdracht in deze moderne microservices architecturen is [JSON](https://en.wikipedia.org/wiki/JSON), die toevallig ook het opslagmedium is voor de overgrote meerderheid van documentgeoriënteerde NoSQL-databases. Dit maakt NoSQL-documentopslag een veel naadlozere pasvorm voor zowel de persistentie als de synchronisatie (met behulp van [event sourcing-patronen)](https://en.wikipedia.org/wiki/Event-driven_architecture)in complexe Microservice-implementaties. Meer traditionele relationele databases kunnen veel complexer te onderhouden in deze architecturen. Dit is te wijten aan de grotere hoeveelheid transformatie die nodig is voor zowel status als synchronisatie tussen API's. Azure Cosmos DB in het bijzonder heeft een aantal functies die het een nog naadlozere pasvorm maken voor JSON-gebaseerde Microservices Architecturen dan veel NoSQL-databases:

* een keuze uit pure JSON-gegevenstypen
* een JavaScript-engine en [query-API](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api) die in de database zijn ingebouwd.
* een state-of-the-art [change feed](https://docs.microsoft.com/azure/cosmos-db/change-feed) waarop klanten zich kunnen abonneren om op de hoogte te worden gesteld van wijzigingen in een container.

## <a name="some-challenges-with-nosql-databases"></a>Enkele uitdagingen met NoSQL-databases

Hoewel er enkele duidelijke voordelen zijn bij het implementeren van NoSQL-databases, zijn er ook enkele uitdagingen waarmee u mogelijk rekening wilt houden. Deze mogen niet in dezelfde mate aanwezig zijn bij het werken met het relationele model:

* transacties met veel relaties die naar dezelfde entiteit verwijzen.
* transacties die een sterke consistentie in de hele gegevensset vereisen.

Kijkend naar de eerste uitdaging, de vuistregel in NoSQL-databases is over het algemeen denormalisatie, die zoals eerder verwoord, produceert efficiënter leest in een gedistribueerd systeem. Er zijn echter een aantal ontwerpuitdagingen die met deze aanpak in het spel komen. Laten we een voorbeeld nemen van een product dat is gerelateerd aan één categorie en meerdere tags:

![Samenvoegingen](./media/relational-or-nosql/many-joins.png)

Een best practice-benadering in een NoSQL-documentdatabase zou zijn om de categorienaam en tagnamen rechtstreeks in een "productdocument" te denormaliseren. Echter, om categorieën, tags en producten synchroon te houden, hebben de ontwerpopties om dit te vergemakkelijken onderhoudscomplexiteit toegevoegd, omdat de gegevens worden gedupliceerd in meerdere records in het product, in plaats van een eenvoudige update te zijn in een "één-op-veel" relatie en een join om de gegevens op te halen. 

De trade-off is dat reads efficiënter zijn in het gedenormaliseerde record en steeds efficiënter worden naarmate het aantal conceptueel samengevoegde entiteiten toeneemt. Echter, net zoals de leesefficiëntie toeneemt met een toenemend aantal samengevoegde entiteiten in een denormaliseren record, zo ook het onderhoud complexiteit van het houden van entiteiten in sync. Een manier om deze afweging te verzachten is het creëren van een [hybride datamodel.](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models)

Hoewel er meer flexibiliteit beschikbaar is in NoSQL-databases om deze afwegingen aan te pakken, kan meer flexibiliteit ook leiden tot meer ontwerpbeslissingen. Raadpleeg ons artikel [over het modelleren en beheren van gegevens op Azure Cosmos DB met behulp van een voorbeeld in de echte wereld,](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)waaronder een benadering voor het synchroon houden [van gedenormaliseerde gebruikersgegevens](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames) waarbij gebruikers niet alleen in verschillende partities zitten, maar in verschillende containers.

Met betrekking tot een sterke consistentie is het zeldzaam dat dit nodig is voor de gehele gegevensset. In gevallen waarin dit nodig is, kan het echter een uitdaging zijn in gedistribueerde databases. Om een sterke consistentie te garanderen, moeten gegevens worden gesynchroniseerd tussen alle replica's en regio's voordat clients deze kunnen lezen. Dit kan de latentie van reads verhogen.

Nogmaals, Azure Cosmos DB biedt meer flexibiliteit dan relationele databases voor de verschillende afwegingen die hier relevant zijn, maar voor kleinschalige implementaties kan deze aanpak meer ontwerpoverwegingen toevoegen. Raadpleeg ons artikel over [consistentie, beschikbaarheid en prestatieafwegingen](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs) voor meer details over dit onderwerp.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van uw Azure Cosmos-account en andere concepten:

* [Uw Azure Cosmos-account beheren](how-to-manage-database-account.md)
* [Wereldwijde distributie](distribute-data-globally.md)
* [Consistentieniveaus](consistency-levels.md)
* [Werken met Azure Cosmos-containers en -items](databases-containers-items.md)
* [VNET-serviceeindpunt voor uw Azure Cosmos-account](vnet-service-endpoint.md)
* [IP-firewall voor uw Azure Cosmos-account](firewall-support.md)
* [Azure-regio's toevoegen en verwijderen aan uw Azure Cosmos-account](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA's](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
