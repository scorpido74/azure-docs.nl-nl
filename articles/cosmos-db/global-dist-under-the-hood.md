---
title: Wereldwijde distributie met Azure Cosmos DB- onder de motorkap
description: Dit artikel bevat technische details met betrekking tot de wereldwijde distributie van Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: a46a69476a2ad6550bc7b3a533fd09565d461db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872125"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Wereldwijde gegevensdistributie met Azure Cosmos DB - onder de motorkap

Azure Cosmos DB is een fundamentele service in Azure, dus het wordt geïmplementeerd in alle Azure-regio's wereldwijd, waaronder het openbare, soevereine, Ministerie van Defensie (DoD) en overheidsclouds. Binnen een datacenter implementeren en beheren we de Azure Cosmos DB op enorme stempels van machines, elk met speciale lokale opslag. Binnen een datacenter wordt Azure Cosmos DB geïmplementeerd in vele clusters, die elk mogelijk meerdere generaties hardware uitvoeren. Machines binnen een cluster zijn meestal verspreid over 10-20 foutdomeinen voor hoge beschikbaarheid binnen een regio. De volgende afbeelding toont de topologie van het globale distributiesysteem Cosmos DB:

![Systeemtopologie](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Globale distributie in Azure Cosmos DB is turnkey:** Op elk gewenst moment, met een paar klikken of programmatisch met één API-aanroep, u de geografische regio's toevoegen of verwijderen die zijn gekoppeld aan uw Cosmos-database. Een Cosmos database, op zijn beurt, bestaat uit een set van Cosmos containers. In Cosmos DB dienen containers als de logische eenheden van distributie en schaalbaarheid. De verzamelingen, tabellen en grafieken die u maakt, zijn (intern) alleen Cosmos-containers. Containers zijn volledig schema-agnostisch en bieden een ruimte voor een query. Gegevens in een Cosmos-container worden automatisch geïndexeerd bij inname. Automatische indexering stelt gebruikers in staat om de gegevens op te vragen zonder het gedoe van schema- of indexbeheer, vooral in een wereldwijd gedistribueerde installatie.  

- In een bepaald gebied worden gegevens binnen een container gedistribueerd met behulp van een partitiesleutel, die u verstrekt en transparant wordt beheerd door de onderliggende fysieke partities *(lokale distributie).*  

- Elke fysieke partitie wordt ook gerepliceerd tussen geografische regio's *(globale distributie).* 

Wanneer een app met Cosmos DB de doorvoer op een Cosmos-container elastisch schaalt of meer opslagruimte verbruikt, verwerkt Cosmos DB op transparante wijze partitiebeheerbewerkingen (splitsen, klonen, verwijderen) in alle regio's. Onafhankelijk van de schaal, distributie of fouten, blijft Cosmos DB één systeemafbeelding van de gegevens binnen de containers bieden, die wereldwijd over een willekeurig aantal regio's worden verdeeld.  

Zoals in de volgende afbeelding wordt weergegeven, worden de gegevens in een container verdeeld over twee dimensies - binnen een regio en over regio's, wereldwijd:  

![fysieke partities](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Een fysieke partitie wordt geïmplementeerd door een groep replica's, een *replicaset genoemd.* Elke machine host honderden replica's die overeenkomen met verschillende fysieke partities binnen een vaste set processen, zoals weergegeven in de afbeelding hierboven. Replica's die overeenkomen met de fysieke partities worden dynamisch geplaatst en worden in balans gebracht tussen de machines binnen een cluster en datacenters binnen een regio.  

Een replica behoort op unieke wijze tot een Azure Cosmos DB-tenant. Elke replica bevat een exemplaar van de [databaseengine](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)van Cosmos DB, die de resources en de bijbehorende indexen beheert. De Cosmos database engine werkt op een atom-record-sequence (ARS) gebaseerd type systeem. De engine is agnost voor het concept van een schema, waardoor de grens tussen de structuur en instantiewaarden van records vervaagt. Cosmos DB bereikt volledig schema-agnosticisme door alles automatisch op een efficiënte manier te indexeren op inname, waardoor gebruikers hun wereldwijd gedistribueerde gegevens kunnen opvragen zonder dat ze te maken hebben met schema- of indexbeheer.

De Cosmos-databaseengine bestaat uit componenten, waaronder de implementatie van verschillende coördinatieprimitieven, taalgebruikstijden, de queryverwerker en de opslag- en indexeringssubsystemen die verantwoordelijk zijn voor transactionele opslag en indexering van gegevens; Respectievelijk. Om duurzaamheid en hoge beschikbaarheid te bieden, houdt de databaseengine zijn gegevens en index op SSD's over en repliceert deze onder de databaseengine-exemplaren binnen respectievelijk de replicaset(s). Grotere tenants komen overeen met een hogere schaal van doorvoer en opslag en hebben grotere of meer replica's of beide. Elk onderdeel van het systeem is volledig asynchroon - geen draad ooit blokkeert, en elke draad doet kortstondig werk zonder onnodige draadschakelaars. Tariefbeperkende en back-druk worden over de hele stapel gekelderd, van de toegangscontrole tot alle I/O-paden. Cosmos database engine is ontworpen om fijnkorrelige gelijktijdigheid te benutten en om een hoge doorvoer te leveren terwijl het werken binnen zuinige hoeveelheden systeembronnen.

Cosmos DB's wereldwijde distributie is gebaseerd op twee belangrijke abstracties - *replica-sets* en *partitie-sets*. Een replica-set is een modulair Lego blok voor coördinatie, en een partitie-set is een dynamische overlay van een of meer geografisch gedistribueerde fysieke partities. Om te begrijpen hoe wereldwijde distributie werkt, moeten we deze twee belangrijke abstracties begrijpen. 

## <a name="replica-sets"></a>Replicasets

Een fysieke partitie wordt gematerialiseerd als een zelfbeheerde en dynamisch belaste groep replica's verspreid over meerdere foutdomeinen, een replica-set genoemd. Deze set implementeert gezamenlijk het protocol voor de gerepliceerde statusmachine om de gegevens binnen de fysieke partitie zeer beschikbaar, duurzaam en consistent te maken. De replica-set lidmaatschap *N* is dynamisch - het blijft fluctueren tussen *NMin* en *NMax* op basis van de fouten, administratieve bewerkingen, en de tijd voor mislukte replica's te regenereren / herstellen. Op basis van de lidmaatschapswijzigingen configureert het replicatieprotocol ook de grootte van lees- en schrijfquorums. Om de doorvoer die is toegewezen aan een bepaalde fysieke partitie gelijkmatig te verdelen, gebruiken we twee ideeën: 

- Ten eerste zijn de kosten voor het verwerken van de schrijfaanvragen op de leider hoger dan de kosten voor het toepassen van de updates op de volger. Overeenkomstig, de leider is gebudgetteerd meer systeembronnen dan de volgelingen. 

- Ten tweede bestaat het leesquorum voor een bepaald consistentieniveau zoveel mogelijk uitsluitend uit de replica's van de volger. We vermijden contact opnemen met de leider voor het serveren van leest, tenzij vereist. We gebruiken een aantal ideeën uit het onderzoek dat is gedaan naar de relatie tussen [belasting en capaciteit](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) in de quorumsystemen voor de vijf [consistentiemodellen](consistency-levels.md) die Cosmos DB ondersteunt.  

## <a name="partition-sets"></a>Partitiesets

Een groep fysieke partities, één van elk van de geconfigureerde met de cosmos-databasegebieden, is samengesteld om dezelfde set sleutels te beheren die zijn gerepliceerd in alle geconfigureerde regio's. Deze hogere coördinatie primitieve heet een *partitie-set* - een geografisch verspreide dynamische overlay van fysieke partities het beheer van een bepaalde set van sleutels. Terwijl een bepaalde fysieke partitie (een replica-set) binnen een cluster is ondergebracht, kan een partitieset clusters, datacenters en geografische regio's omvatten, zoals in de afbeelding hieronder wordt weergegeven:  

![Partitiesets](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

U een partitieset zien als een geografisch verspreide super replica-set, die bestaat uit meerdere replica-sets die dezelfde set sleutels bezitten. Net als bij een replica-set is het lidmaatschap van een partitieset ook dynamisch: het fluctueert op basis van impliciete fysieke partitiebeheerbewerkingen om nieuwe partities toe te voegen/verwijderen naar/van een bepaalde partitieset (bijvoorbeeld wanneer u de doorvoer op een container uitschaalt, een regio toevoegt/verwijdert in uw Cosmos-database of wanneer er fouten optreden). Op grond van het feit dat elk van de partities (van een partitie-set) beheren van de partitie-set lidmaatschap binnen de eigen replica-set, het lidmaatschap is volledig gedecentraliseerd en zeer beschikbaar. Tijdens de herconfiguratie van een partitieset wordt ook de topologie van de overlay tussen fysieke partities vastgesteld. De topologie wordt dynamisch geselecteerd op basis van het consistentieniveau, de geografische afstand en de beschikbare netwerkbandbreedte tussen de bron en de fysieke doelpartities.  

Met de service u uw Cosmos-databases configureren met één schrijfgebied of meerdere schrijfregio's, en afhankelijk van de keuze worden partitiesets geconfigureerd om schrijfbewerkingen in precies één of alle regio's te accepteren. Het systeem maakt gebruik van een twee-niveau, geneste consensus protocol - een niveau werkt binnen de replica's van een replica-set van een fysieke partitie accepteren van de schrijft, en de andere werkt op het niveau van een partitie-set om volledige bestelgaranties voor alle de betrokken schrijft binnen de partitie-set. Deze gelaagde, geneste consensus is van cruciaal belang voor de implementatie van onze strenge SLA's voor hoge beschikbaarheid, evenals de implementatie van de consistentiemodellen, die Cosmos DB aan haar klanten biedt.  

## <a name="conflict-resolution"></a>Conflictoplossing

Ons ontwerp voor de update propagatie, conflictoplossing en causaliteitstracking is geïnspireerd op het eerdere werk aan [epidemische algoritmen](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) en het [Bayou-systeem.](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) Hoewel de kernels van de ideeën hebben overleefd en bieden een handig referentiekader voor het communiceren van het systeemontwerp van de Cosmos DB, hebben ze ook een aanzienlijke transformatie ondergaan toen we ze toepasten op het Cosmos DB-systeem. Dit was nodig, omdat de vorige systemen niet waren ontworpen met de resource governance, noch met de schaal waarop Cosmos DB moet werken, noch om de mogelijkheden (bijvoorbeeld begrensde staleness consistentie) en de strenge en uitgebreide SLA's die Cosmos DB aan haar klanten levert.  

Bedenk dat een partitieset over meerdere regio's is verdeeld en het cosmos-dbs-replicatieprotocol (multimaster) volgt om de gegevens te repliceren tussen de fysieke partities die een bepaalde partitieset bevatten. Elke fysieke partitie (van een partitieset) accepteert schrijft en dient leest meestal aan de clients die lokaal zijn voor die regio. Schrijft geaccepteerd door een fysieke partitie binnen een regio zijn blijvend betrokken en zeer beschikbaar gemaakt binnen de fysieke partitie voordat ze worden erkend aan de klant. Dit zijn voorlopige schrijfbewerkingen en worden gepropageerd naar andere fysieke partities binnen de partitieset met behulp van een anti-entropiekanaal. Clients kunnen voorlopige of vastgelegde schrijfbewerkingen aanvragen door een aanvraagheader door te geven. De anti-entropiepropagatie (inclusief de frequentie van propagatie) is dynamisch, gebaseerd op de topologie van de partitie-reeks, regionale nabijheid van de fysieke partities, en het geconfigureerde consistentieniveau. Binnen een partitieset volgt Cosmos DB een primair commit-schema met een dynamisch geselecteerde arbiterpartitie. De arbiterselectie is dynamisch en maakt integraal deel uit van de herconfiguratie van de partitieset op basis van de topologie van de overlay. De vastgelegde schrijfbewerkingen (inclusief multi-row/batched updates) worden gegarandeerd besteld. 

We gebruiken gecodeerde vectorklokken (met regio-ID en logische klokken die overeenkomen met elk niveau van consensus op de replica-set en partitie-set, respectievelijk) voor causaliteit tracking en versie vectoren op te sporen en op te lossen update conflicten. De topologie en het peer selectie algoritme zijn ontworpen om vaste en minimale opslag en minimale netwerkoverhead van versievectoren te garanderen. Het algoritme garandeert de strikte convergentie eigendom.  

Voor de Cosmos-databases die zijn geconfigureerd met meerdere schrijfregio's, biedt het systeem een aantal flexibele automatische conflictoplossingsbeleidsregels waaruit de ontwikkelaars kunnen kiezen, waaronder: 

- **Last-Write-Wins (LWW),** die standaard een systeemgedefinieerde tijdstempeleigenschap gebruikt (die is gebaseerd op het tijdsynchronisatieklokprotocol). Met Cosmos DB u ook een andere aangepaste numerieke eigenschap opgeven die moet worden gebruikt voor conflictoplossing.  
- **Toepassingsgedefinieerd (Aangepast) conflictoplossingsbeleid** (uitgedrukt via samenvoegprocedures), dat is ontworpen voor toepassingsgedefinieerde semantiekafstemming van conflicten. Deze procedures worden ingeroepen bij de detectie van de schrijfconflicten onder auspiciën van een databasetransactie aan de serverzijde. Het systeem biedt precies één keer garantie voor de uitvoering van een samenvoegprocedure als onderdeel van het verbintenisprotocol. Er zijn verschillende voorbeelden voor [conflictoplossing](how-to-manage-conflicts.md) beschikbaar waarmee u spelen.  

## <a name="consistency-models"></a>Consistentiemodellen

Of u nu uw Cosmos-database configureert met één of meerdere schrijfgebieden, u kiezen uit de vijf goed gedefinieerde consistentiemodellen. Bij meerdere schrijfregio's zijn de volgende enkele opmerkelijke aspecten van de consistentieniveaus:  

De begrensde staleness consistentie garandeert dat *K* alle reads binnen K-voorvoegsels of *T-seconden* van de laatste schrijven in een van de regio's zullen zijn. Bovendien, leest met begrensde staleness consistentie zijn gegarandeerd monotoon en met consistente voorvoegsel garanties. Het anti-entropieprotocol werkt op een tariefbeperkte manier en zorgt ervoor dat de voorvoegsels zich niet ophopen en de tegendruk op de schrijfbewerkingen niet hoeft te worden toegepast. Sessie consistentie garandeert monotoon lezen, monotoon schrijven, lees je eigen schrijft, schrijf volgt lezen, en consistente prefix garanties, wereldwijd. Voor de databases die met een sterke consistentie zijn geconfigureerd, zijn de voordelen (lage schrijflatentie, hoge schrijfbeschikbaarheid) van meerdere schrijfregio's niet van toepassing vanwege synchrone replicatie tussen regio's.

De semantiek van de vijf consistentiemodellen in Cosmos DB worden [hier](consistency-levels.md)beschreven , en wiskundig beschreven met behulp van een high-level TLA + specificaties [hier](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Volgende stappen

Lees vervolgens hoe u globale distributie configureert met behulp van de volgende artikelen:

* [Regio's toevoegen aan of verwijderen uit uw databaseaccount](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Clients configureren voor multi-homing](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Een aangepast conflictoplossingsbeleid maken](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
