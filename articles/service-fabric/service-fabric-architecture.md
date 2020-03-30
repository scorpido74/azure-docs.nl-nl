---
title: Architectuur van Azure Service Fabric
description: In dit artikel wordt de architectuur van Service Fabric uitgelegd, een gedistribueerd systeemplatform dat wordt gebruikt om schaalbare, betrouwbare en eenvoudig beheerde applicaties voor de cloud te bouwen.
services: service-fabric
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 972700dded1841994de9252b4aa4bbc8eaefeaf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76024704"
---
# <a name="service-fabric-architecture"></a>Service Fabric-architectuur

Service Fabric is gebouwd met gelaagde subsystemen. Met deze subsystemen u toepassingen schrijven die:

* Zeer beschikbaar
* Schaalbaar
* Beheersbaar
* Toetsbaar

Het volgende diagram toont de belangrijkste subsystemen van Service Fabric.

![Diagram van de architectuur van de stof van de dienst](media/service-fabric-architecture/service-fabric-architecture.png)

In een gedistribueerd systeem is de mogelijkheid om veilig te communiceren tussen knooppunten in een cluster van cruciaal belang. Aan de basis van de stack bevindt zich het transportsubsysteem, dat zorgt voor veilige communicatie tussen knooppunten. Boven het subsysteem vervoer bevindt zich het subsysteem federatie, dat de verschillende knooppunten samenbundelt in één entiteit (clusters genoemd), zodat Service Fabric fouten kan detecteren, leader-verkiezing kan uitvoeren en consistente routering kan bieden. Het subsysteem betrouwbaarheid, gelaagd bovenop het subsysteem federatie, is verantwoordelijk voor de betrouwbaarheid van Service Fabric-services via mechanismen zoals replicatie, resourcebeheer en failover. Het subsysteem federation ligt ook ten grondslag aan het subsysteem hosting en activering, dat de levenscyclus van een toepassing op één knooppunt beheert. Het managementsubsysteem beheert de levenscyclus van applicaties en services. Het subsysteem testability helpt applicatieontwikkelaars hun services te testen via gesimuleerde fouten voor en na het implementeren van toepassingen en services in productieomgevingen. Service Fabric biedt de mogelijkheid om servicelocaties op te lossen via het subsysteem communicatie. De toepassingsprogrammeermodellen die aan ontwikkelaars worden blootgesteld, worden naast deze subsystemen gelaagd, samen met het toepassingsmodel om tooling mogelijk te maken.

## <a name="transport-subsystem"></a>Subsysteem Vervoer

Het subsysteem transport implementeert een point-to-point datagram communicatiekanaal. Dit kanaal wordt gebruikt voor communicatie binnen service fabric clusters en communicatie tussen de service fabric cluster en clients. Het ondersteunt eenrichtings- en antwoordcommunicatiepatronen, die de basis vormen voor het implementeren van broadcast en multicast in de Federatielaag. Het subsysteem transport beveiligt de communicatie met Behulp van X509-certificaten of Windows-beveiliging. Dit subsysteem wordt intern gebruikt door Service Fabric en is niet direct toegankelijk voor ontwikkelaars voor applicatieprogrammering.

## <a name="federation-subsystem"></a>Federatiesubsysteem

Om te redeneren over een set knooppunten in een gedistribueerd systeem, moet u een consistente weergave van het systeem hebben. Het subsysteem federatie maakt gebruik van de communicatie primitieven die door het transport subsysteem en hecht de verschillende knooppunten in een uniforme cluster dat het kan redeneren over. Het biedt de gedistribueerde systemen primitieven die nodig zijn door de andere subsystemen - fout detectie, leider verkiezing, en consistente routing. Het subsysteem federatie is gebouwd bovenop gedistribueerde hashtabellen met een 128-bits tokenruimte. Het subsysteem maakt een ringtopologie over de knooppunten, waarbij elk knooppunt in de ring een subset van de tokenruimte voor eigendom krijgt toegewezen. Voor foutdetectie maakt de laag gebruik van een leasemechanisme op basis van hartslag en arbitrage. Het subsysteem van de federatie garandeert ook door ingewikkelde join- en vertrekprotocollen dat er op elk moment slechts één eigenaar van een token bestaat. Dit biedt leidersverkiezingen en consistente routegaranties.

## <a name="reliability-subsystem"></a>Subsysteem betrouwbaarheid

Het subsysteem betrouwbaarheid biedt het mechanisme om de status van een Service Fabric-service zeer beschikbaar te maken door het gebruik van de *Replicator*, *Failover Manager*en *Resource Balancer*.

* De Replicator zorgt ervoor dat statuswijzigingen in de primaire servicereplica automatisch worden gerepliceerd naar secundaire replica's, zodat de consistentie tussen de primaire en secundaire replica's in een servicereplicaset behouden blijft. De replicator is verantwoordelijk voor het quorumbeheer tussen de replica's in de replicaset. Het werkt samen met de failover-eenheid om de lijst met bewerkingen te repliceren en de herconfiguratieagent biedt deze met de configuratie van de replicaset. Die configuratie geeft aan welke replica's de bewerkingen moeten worden gerepliceerd. Service Fabric biedt een standaard replicator genaamd Fabric Replicator, die kan worden gebruikt door het programmeermodel API om de servicestatus zeer beschikbaar en betrouwbaar te maken.
* De Failovermanager zorgt ervoor dat wanneer knooppunten worden toegevoegd aan of verwijderd uit het cluster, de belasting automatisch wordt herverdeeld over de beschikbare knooppunten. Als een knooppunt in het cluster uitvalt, configureert het cluster de servicereplica's automatisch opnieuw om de beschikbaarheid te behouden.
* De Resourcemanager plaatst servicereplica's voor foutdomeinen in het cluster en zorgt ervoor dat alle failover-eenheden operationeel zijn. Met Resource Manager worden ook serviceresources in evenwicht gebracht in de onderliggende gedeelde groep clusterknooppunten om een optimale uniforme belastingsverdeling te bereiken.

## <a name="management-subsystem"></a>Subsysteem Beheer

Het managementsubsysteem biedt end-to-end service- en applicatielevenscyclusbeheer. Met PowerShell-cmdlets en administratieve API's u toepassingen inrichten, implementeren, patchen, upgraden en de-provisionen zonder verlies van beschikbaarheid. Het managementsubsysteem voert dit uit via de volgende services.

* **Clusterbeheer:** dit is de primaire service die samenwerkt met failoverbeheer vanuit betrouwbaarheid om de toepassingen op de knooppunten te plaatsen op basis van de beperkingen voor serviceplaatsing. Het subsysteem Resourcebeheer in het subsysteem Failover zorgt ervoor dat de beperkingen nooit worden verbroken. De clusterbeheerder beheert de levenscyclus van de toepassingen van voorziening tot de-provision. Het integreert met de health manager om ervoor te zorgen dat de beschikbaarheid van toepassingen niet verloren gaat vanuit een semantisch gezondheidsperspectief tijdens upgrades.
* **Health Manager**: Deze service maakt statusbewaking van toepassingen, services en clusterentiteiten mogelijk. Clusterentiteiten (zoals knooppunten, servicepartities en replica's) kunnen statusgegevens rapporteren, die vervolgens worden samengevoegd in het gecentraliseerde statusarchief. Deze statusinformatie biedt een algemene point-in-time statusmomentopname van de services en knooppunten die zijn verdeeld over meerdere knooppunten in het cluster, zodat u alle benodigde corrigerende maatregelen nemen. Met API's voor statusquery's u de statusgebeurtenissen opvragen die zijn gerapporteerd aan het subsysteem Gezondheid. De API's voor statusquery's retourneren de ruwe statusgegevens die zijn opgeslagen in het statusarchief of de geaggregeerde, geïnterpreteerde statusgegevens voor een specifieke clusterentiteit.
* **Image Store**: Deze service biedt opslag en distributie van de toepassingsbinaries. Deze service biedt een eenvoudig gedistribueerd bestand op waar de toepassingen worden geüpload naar en gedownload van.

## <a name="hosting-subsystem"></a>Hostingsubsysteem

De clusterbeheerder informeert het hostingsubsysteem (dat op elk knooppunt wordt uitgevoerd) welke services het moet beheren voor een bepaald knooppunt. Het hostingsubsysteem beheert vervolgens de levenscyclus van de toepassing op dat knooppunt. Het werkt samen met de betrouwbaarheid en gezondheid componenten om ervoor te zorgen dat de replica's goed zijn geplaatst en gezond zijn.

## <a name="communication-subsystem"></a>Subsysteem Communicatie

Dit subsysteem biedt betrouwbare berichten binnen het cluster- en servicedetectie via de naamgevingsservice. De naamgevingsservice lost servicenamen op naar een locatie in het cluster en stelt gebruikers in staat servicenamen en -eigenschappen te beheren. Met de naamgevingsservice kunnen clients veilig communiceren met elk knooppunt in het cluster om een servicenaam op te lossen en servicemetagegevens op te halen. Met behulp van een eenvoudige Naming client API kunnen gebruikers van Service Fabric services en clients ontwikkelen die de huidige netwerklocatie kunnen oplossen, ondanks de dynamiek van het knooppunt of de hergrootte van het cluster.

## <a name="testability-subsystem"></a>Subsysteem Testabiliteit

Testability is een reeks tools die speciaal zijn ontworpen voor het testen van services die zijn gebouwd op Service Fabric. De tools laten een ontwikkelaar gemakkelijk zinvolle fouten veroorzaken en testscenario's uitvoeren om de talrijke toestanden en overgangen uit te oefenen en te valideren die een service gedurende zijn hele levensduur zal ervaren, allemaal op een gecontroleerde en veilige manier. Testabiliteit biedt ook een mechanisme om langere tests uit te voeren die verschillende mogelijke storingen kunnen herhalen zonder de beschikbaarheid te verliezen. Dit biedt u een test-in-productie omgeving.
