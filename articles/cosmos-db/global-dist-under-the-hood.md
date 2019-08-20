---
title: Wereld wijde distributie met Azure Cosmos DB-onder de motorkap
description: In dit artikel vindt u technische gegevens met betrekking tot wereldwijde distributie van Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: ce943fbed0774667100f6de4c60f91c0b02de6c3
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615345"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Distributie van globale gegevens met Azure Cosmos DB-onder de motorkap

Azure Cosmos DB is een Foundational service in azure, zodat deze wereld wijd wordt geïmplementeerd in alle Azure-regio's, inclusief de open bare, soevereine, ministerie van defensie (DoD) en overheids Clouds. Binnen een datacenter, we implementeren en beheren van de Azure Cosmos DB op zeer grote stempels van machines, elk met lokale opslag toegewezen. Binnen een datacenter, is Azure Cosmos DB geïmplementeerd op veel clusters is elk mogelijk meerdere verschillende hardwaregeneraties. Computers in een cluster zijn doorgaans verdeeld over 10-20-fout domeinen voor hoge Beschik baarheid binnen een regio. De volgende afbeelding toont de topologie van Cosmos DB globale distributie system:

![-Topologie](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Wereld wijde distributie in Azure Cosmos DB is kant-en-klare:** U kunt op elk gewenst moment, met enkele klikken of programmatisch met één API-aanroep, de geografische regio's toevoegen of verwijderen die zijn gekoppeld aan uw Cosmos-data base. Een Cosmos-data base bestaat op zijn beurt uit een set Cosmos-containers. In Cosmos DB fungeren containers als de logische eenheden van de distributie en schaalbaarheid. De verzamelingen, tabellen en grafieken die u maakt zijn (intern) alleen Cosmos-containers. Containers zijn volledig schema-neutraal en bieden een bereik voor een query. Gegevens in een Cosmos-container automatisch geïndexeerd bij opname. Met automatisch indexeren kunnen gebruikers de gegevens opvragen zonder dat er problemen zijn met schema-en index beheer, met name in een wereld wijd gedistribueerde configuratie.  

- In een bepaalde regio worden gegevens in een container gedistribueerd met behulp van een partitie sleutel, die u opgeeft en die transparant wordt beheerd door de onderliggende fysieke partities (*lokale distributie*).  

- Elke fysieke partitie wordt ook gerepliceerd tussen geografische regio's (*globale distributie*). 

Wanneer een app die Cosmos DB, elastisch de door Voer op een Cosmos-container schaalt of meer opslag gebruikt, Cosmos DB transparante bewerkingen voor partitie beheer (splitsen, klonen, verwijderen) in alle regio's transparant afhandelen. Onafhankelijk van de schaal, distributie of storingen, Cosmos DB altijd één integraal beeld van de gegevens in de containers die wereldwijd worden gedistribueerd naar een willekeurig aantal regio's bieden.  

Zoals in de volgende afbeelding wordt weer gegeven, worden de gegevens binnen een container verdeeld over twee dimensies: binnen een regio en in verschillende regio's, over het hele land.  

![Fysieke partities](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Een fysieke partitie wordt geïmplementeerd met een groep replica's, een zogenaamde replicaset. Elke machine fungeert als host voor honderden replica's die overeenkomen met verschillende fysieke partities binnen een vaste set processen, zoals wordt weer gegeven in de bovenstaande afbeelding. Replica's die overeenkomt met de fysieke partities dynamisch worden geplaatst en taakverdeling tussen de computers binnen een cluster en datacentrums binnen een regio.  

Een replica wordt een unieke behoort tot een Azure Cosmos DB-tenant. Elke replica als host fungeert voor een exemplaar van de Cosmos-DB [database-engine](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), die de resources, evenals de bijbehorende indexen beheert. De Cosmos-data base-engine wordt uitgevoerd op een ARS-systeem (Atom-record-Sequence). De engine is neutraal met het concept van een schema, waardoor de grens tussen de waarden van de structuur en het exemplaar van records wordt vervaagd. Cosmos DB realiseert volledige schema agnosticism door alles bij opname automatisch te indexeren op een efficiënte manier, waardoor gebruikers kunnen hun wereldwijd gedistribueerde gegevens op te vragen zonder dat u hoeft te bekommeren om schema's of indexbeheer.

De Cosmos-data base-engine bestaat uit onderdelen, zoals de implementatie van verschillende coördinatie primitieven, taal runtimes, de query processor en de opslag-en indexerings subsystemen die verantwoordelijk zijn voor transactionele opslag en het indexeren van gegevens, respectievelijk. Voor duurzaamheid en hoge beschikbaarheid, de database-engine zich blijft voordoen de gegevens en index op SSD's en repliceert dit met de database-engine-exemplaren in de replica-vormen respectievelijk. Grotere tenants komen overeen met een hogere schaal van door Voer en opslag en hebben een grotere of meer replica's of beide. Elk onderdeel van het systeem is volledig asynchroon: Er is geen thread blokkeert ooit en elke thread werkt tijdelijke zonder dat er geen onnodige thread-switches. Frequentielimiet en back-druk zijn in de hele stack van het toegangsbeheer tot alle i/o-paden gekoppeld. De Cosmos-data base-engine is ontworpen om gebruik te maken van nauw keurige gelijktijdigheid en om hoge door voer te bieden terwijl u binnen frugale hoeveel heden systeem bronnen werkt.

De wereld wijde distributie van Cosmos DB is afhankelijk van twee belang rijke abstracties: *replica sets* en *partitie sets*. Een replica-set is een modulaire Lego-blok voor coördinatie en een partitie-set is een dynamische overlay van een of meer geografisch verspreide fysieke partities. Om te begrijpen hoe wereldwijde distributie werkt, moeten we deze twee belangrijke abstracties begrijpen. 

## <a name="replica-sets"></a>Replicasets

Een fysieke partitie wordt gematerialeerd als een met zichzelf beheerde en dynamisch taak verdeling van de gespreide groep replica's over meerdere fout domeinen, een zogenaamde replicaset. Deze verzameling implementeert gezamenlijk het computer Protocol van de gerepliceerde status om de gegevens in de fysieke partitie Maxi maal beschikbaar, duurzaam en consistent te maken. Het lidmaatschap van de replicaset *N* is dynamisch; het zorgt voor schommeling tussen *Nmin.* en *NMAX.* op basis van de fouten, administratieve bewerkingen en de tijd voor mislukte replica's om opnieuw te genereren/te herstellen. Op basis van de wijzigingen in het lidmaatschap, de replicatie-protocol ook configureren de grootte van lezen en schrijven quorum. Om de door Voer die is toegewezen aan een bepaalde fysieke partitie uniform te distribueren, hebben we twee ideeën ondergaan: 

- Ten eerste zijn de kosten voor het verwerken van de schrijf aanvragen op de leider hoger dan de kosten voor het Toep assen van de updates op de volger. Dienovereenkomstig, de leider is gebudgetteerde meer systeembronnen dan de Volgers. 

- Ten tweede zoveel mogelijk het quorum lezen voor een bepaalde consistentieniveau bestaat uitsluitend uit de hobbyist replica's. Vermijden we contact opnemen met de leider voor het uitvoeren van leesbewerkingen, tenzij dit vereist. We hebben een aantal ideeën ondergaan van het onderzoek dat is uitgevoerd op de relatie van [belasting en capaciteit](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) in de op quorum gebaseerde systemen voor de [vijf consistentie modellen](consistency-levels.md) die Cosmos DB ondersteunt.  

## <a name="partition-sets"></a>Partitie-sets

Een groep fysieke partities, één van elk geconfigureerd met de Cosmos-database regio's, bestaat uit het beheren van dezelfde set sleutels die worden gerepliceerd in alle geconfigureerde regio's. Deze hogere coördinatie primitieve wordt een partitieset genoemd: een geografisch gedistribueerde dynamische overlay van fysieke partities die een bepaalde set sleutels beheert. Terwijl een bepaalde fysieke partitie (een replicaset) binnen een cluster ligt, kan een partitieset clusters, data centers en geografische regio's omvatten, zoals wordt weer gegeven in de onderstaande afbeelding:  

![Partitiesets](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

U kunt een partitie-set beschouwen als een geografisch verspreide "super-replicaset', die bestaat uit meerdere replicasets die eigenaar is dezelfde set sleutels. Net als bij een replicaset is het lidmaatschap van een partitieset ook dynamisch; het wordt geschommeld op basis van de impliciete bewerkingen voor fysieke partitie beheer om nieuwe partities toe te voegen aan of te verwijderen uit een bepaalde partitieset (bijvoorbeeld wanneer u de door Voer uitschaalt op een container, een regio toevoegen aan of verwijderen uit uw Cosmos-data base of wanneer er fouten optreden). Omdat elk van de partities (van een partitieset) het lidmaatschap van de partitieset binnen een eigen replicaset beheert, wordt het lidmaatschap volledig gedecentraliseerd en Maxi maal beschikbaar. De topologie van de overlay tussen fysieke partities wordt ook ingesteld tijdens de herconfiguratie van een partitie-set. De topologie wordt dynamisch geselecteerd op basis van het consistentie niveau, de geografische afstand en de beschik bare netwerk bandbreedte tussen de bron-en doel-fysieke partities.  

De service kunt u uw Cosmos-databases configureren met een enkele schrijfregio of meerdere regio's voor schrijven en afhankelijk van uw keuze, partitie-sets zijn geconfigureerd voor het accepteren van schrijfbewerkingen in exact één of alle regio's. Het systeem maakt gebruik van een geneste consensus-protocol op twee niveaus: één niveau werkt binnen de replica's van een fysieke partitie die de schrijf bewerkingen accepteert, en de andere werkt op het niveau van een partitie, zodat de volledige best ordenings garanties worden geboden de vastgelegde schrijf bewerkingen binnen de partitieset. Deze consensus met meerdere lagen, geneste is essentieel voor de implementatie van onze strengere Sla's voor hoge beschikbaarheid, evenals de uitvoering van de consistentiemodellen, Cosmos DB biedt aan klanten.  

## <a name="conflict-resolution"></a>Conflictoplossing

Onze ontwerp voor het doorgeven van de update, conflictoplossing en oorzakelijke bijhouden is laat u inspireren van de voorafgaande werkzaamheden op [epizoötie algoritmen](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) en de [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) system. Terwijl de kernels ideeën hebt hebt en een handige referentiekader bieden voor de communicatie van de Cosmos-DB-systeemontwerp, hebben ze ook aanzienlijke transformatie ondergaan omdat we deze op het systeem Cosmos DB toegepast. Dit was nodig omdat de vorige systemen niet zijn ontworpen voor de resource governance, noch met de schaal waarop Cosmos DB moet worden uitgevoerd, noch om de mogelijkheden te bieden (bijvoorbeeld gebonden verouderde consistentie) en de strikte en uitgebreide Sla's die Cosmos DB levert aan de klanten.  

Intrekken dat een partitie-set is verdeeld over meerdere regio's en Cosmos DB's (meerdere masters) replicatie protocol om te repliceren van de gegevens tussen de fysieke partities die bestaat uit een bepaalde partitie-set volgt. Elke fysieke partitie (van een partitieset) accepteert schrijf bewerkingen en verzendt normaal gesp roken Lees bewerkingen naar de clients die lokaal zijn voor die regio. Schrijf bewerkingen die worden geaccepteerd door een fysieke partitie binnen een regio worden blijvend vastgelegd en Maxi maal beschikbaar gemaakt in de fysieke partitie voordat ze worden bevestigd aan de client. Deze voorlopig schrijfbewerkingen en zijn doorgegeven aan andere fysieke partities binnen de partitie-set met behulp van een kanaal anti entropie. Clients kunnen voorlopig of vastgelegde schrijfbewerkingen aanvragen door een aanvraagheader te verwerken. Het anti entropie doorgeven (met inbegrip van de frequentie van doorgifte) is dynamisch, op basis van de topologie van de partitie-set, regionale nabijheid van de fysieke partities, en de consistentie niveau geconfigureerd. Binnen een partitie-set volgt Cosmos DB een primaire commit-schema met een partitie dynamisch geselecteerde instellingen. De instellingen-selectie is dynamische en is een integraal onderdeel van de nieuwe configuratie van de partitie-set op basis van de topologie van de overlay. De vastgelegde schrijfbewerkingen (inclusief meerdere-row/batchgewijs updates) worden worden besteld gegarandeerd. 

We maken gebruik van gecodeerde vector klokken (regio-ID en de logische klokken die overeenkomt met elk niveau van de consensus op de replica-set en partitie-set met respectievelijk) voor oorzakelijke bijhouden en versie vectoren te detecteren en oplossen van conflicten bijwerken. De topologie en de algoritme van de peer-selectie zijn ontworpen om te controleren of de vaste en minimale-opslag en netwerk minimale overhead van vectoren. Het algoritme voor de eigenschap strikte convergentie wordt gegarandeerd.  

Voor de Cosmos-databases is geconfigureerd met meerdere regio's voor schrijven en biedt het systeem een aantal flexibele automatische conflict resolutie beleidsregels voor de ontwikkelaars om de verkeersbelasting, met inbegrip van: 

- **Last-write-WINS (LWW)** , die standaard gebruikmaakt van een door het systeem gedefinieerde tijds tempel eigenschap (die is gebaseerd op het klok protocol voor tijd synchronisatie). Cosmos DB kunt u een andere aangepaste numerieke eigenschap moet worden gebruikt voor conflictoplossing opgeven.  
- **Beleid voor conflict oplossing door toepassing gedefinieerd (aangepast)** (uitgedrukt in samenvoeg procedures), dat is ontworpen voor het afstemmen van een door de toepassing gedefinieerde semantiek van conflicten. Deze procedures ophalen aangeroepen tijdens de detectie van het schrijven / schrijven veroorzaakt een conflict in het kader van een databasetransactie op de server. Het systeem biedt precies eenmaal garantie voor het uitvoeren van een samenvoeg procedure als onderdeel van het toezeggings protocol. Er zijn [verschillende voor beelden van conflict oplossing](how-to-manage-conflicts.md) beschikbaar waarmee u kunt spelen met.  

## <a name="consistency-models"></a>Consistentiemodellen

Of u uw Cosmos-data base met één of meerdere schrijf regio's configureert, u kunt kiezen uit de vijf goed gedefinieerde consistentie modellen. Bij meerdere schrijf regio's zijn het volgende enkele van de belangrijkste aspecten van de consistentie niveaus:  

Met de consistentie van de gebonden veroudering wordt gegarandeerd dat alle Lees bewerkingen binnen *K* -voor voegsels vallen of *T* seconden van de laatste schrijf bewerking in een van de regio's. Bovendien zijn Lees bewerkingen met gebonden verouderde consistentie gegarandeerd een monotone en met consistente voorvoegsel garanties. Het protocol anti entropie werkt op een manier beperkt in de snelheid en zorgt ervoor dat de voorvoegsels niet worden en de tegendruk op de schrijfbewerkingen niet hoeft te worden toegepast. Met sessie consistentie wordt de monotone Lees-, monotone schrijf-, lees-en schrijf bewerkingen gegarandeerd. Voor de data bases die zijn geconfigureerd met sterke consistentie, zijn de voor delen (lage schrijf latentie, hoge schrijf Beschik baarheid) van meerdere schrijf regio's niet van toepassing, vanwege synchrone replicatie tussen regio's.

De semantiek van de vijf consistentie modellen in Cosmos DB worden [hier](consistency-levels.md)beschreven en mathematisch beschreven met behulp van een TLA-specificatie op hoog [](https://github.com/Azure/azure-cosmos-tla)niveau.

## <a name="next-steps"></a>Volgende stappen

Naast informatie over het configureren van wereldwijde distributie met behulp van de volgende artikelen:

* [Regio's van uw databaseaccount toevoegen/verwijderen](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Clients configureren voor multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Een aangepast beleid voor conflict oplossing maken](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
