---
title: Overzicht van Azure Service Fabric Monitoring and Diagnostics
description: Meer informatie over monitoring en diagnostiek voor Azure Service Fabric-clusters, -toepassingen en -services.
author: srrengar
ms.topic: conceptual
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: ef77810adfab213845c7824740effc3416d85407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282482"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitoring en diagnostiek voor Azure Service Fabric

In dit artikel vindt u een overzicht van monitoring en diagnostiek voor Azure Service Fabric. Monitoring en diagnostiek zijn essentieel voor het ontwikkelen, testen en implementeren van workloads in elke cloudomgeving. U bijvoorbeeld bijhouden hoe uw toepassingen worden gebruikt, welke acties het Service Fabric-platform onderneemt, uw resourcegebruik met prestatiemeteritems en de algehele status van uw cluster. U deze informatie gebruiken om problemen te diagnosticeren en te corrigeren en te voorkomen dat deze zich in de toekomst voordoen. In de volgende secties wordt kort uitgelegd hoe elk gebied van servicefabric-monitoring rekening moet houden met productieworkloads. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Toepassingsbewaking
Toepassingsbewaking houdt bij hoe functies en onderdelen van uw toepassing worden gebruikt. U wilt uw toepassingen controleren om ervoor te zorgen dat problemen die van invloed zijn op gebruikers worden opgevangen. De verantwoordelijkheid van applicatie monitoring is op de gebruikers het ontwikkelen van een applicatie en haar diensten, omdat het uniek is voor de zakelijke logica van uw toepassing. Het bewaken van uw toepassingen kan nuttig zijn in de volgende scenario's:
* Hoeveel verkeer ervaart mijn toepassing? - Moet u uw services schalen om aan de eisen van gebruikers te voldoen of een mogelijk knelpunt in uw toepassing aanpakken?
* Is mijn service voor servicegesprekken succesvol en bijgehouden?
* Welke acties worden door de gebruikers van mijn toepassing ondernomen? - Het verzamelen van telemetrie kan toekomstige functieontwikkeling en betere diagnostiek voor toepassingsfouten leiden
* Is mijn aanvraag gooien onverwerkte uitzonderingen? 
* Wat gebeurt er binnen de diensten die in mijn containers draaien?

Het grote ding over applicatie monitoring is dat ontwikkelaars kunnen gebruiken welke tools en kader ze willen, omdat het leeft in de context van uw toepassing! U meer informatie vinden over de Azure-oplossing voor toepassingsbewaking met Azure Monitor - Application Insights in [Event-analyse met Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
We hebben ook een tutorial met hoe dit in te [stellen voor .NET Applications](service-fabric-tutorial-monitoring-aspnet.md). In deze zelfstudie wordt uitgelegd hoe u de juiste hulpprogramma's installeren, een voorbeeld om aangepaste telemetrie in uw toepassing te schrijven en de toepassingsdiagnostiek en telemetrie in de Azure-portal te bekijken. 


## <a name="platform-cluster-monitoring"></a>Platform (Cluster) monitoring
Een gebruiker heeft de controle over welke telemetrie afkomstig is van zijn of haar toepassing, omdat een gebruiker de code zelf schrijft, maar hoe zit het met de diagnostiek van het Service Fabric-platform? Een van de doelstellingen van Service Fabric is om toepassingen bestand te houden tegen hardwarefouten. Dit doel wordt bereikt door de mogelijkheid van de systeemservices van het platform om infrastructuurproblemen op te sporen en workloads snel te mislukken naar andere knooppunten in het cluster. Maar wat als de systeemdiensten zelf problemen hebben? Of als bij een poging om een werkbelasting te implementeren of te verplaatsen, regels voor de plaatsing van services worden geschonden? Service Fabric biedt diagnoses voor deze en meer om ervoor te zorgen dat u op de hoogte bent van activiteiten die plaatsvinden in uw cluster. Enkele voorbeeldscenario's voor clusterbewaking zijn:

Service Fabric biedt een uitgebreide set van evenementen out of the box. Deze [Service Fabric-gebeurtenissen](service-fabric-diagnostics-events.md) zijn toegankelijk via de EventStore of het operationele kanaal (gebeurteniskanaal dat door het platform wordt blootgesteld). 

* Service Fabric-gebeurteniskanalen - Op Windows zijn Service Fabric-gebeurtenissen beschikbaar bij `logLevelKeywordFilters` één ETW-provider met een set relevante kanalen om te kiezen tussen operationele en gegevens & Messaging-kanalen - dit is de manier waarop we uitgaande Service Fabric-gebeurtenissen scheiden waarop we moeten worden gefilterd als dat nodig is. Op Linux, Service Fabric gebeurtenissen komen via LTTng en worden in een opslag tabel, van waaruit ze kunnen worden gefilterd als dat nodig is. Deze kanalen bevatten samengestelde, gestructureerde gebeurtenissen die kunnen worden gebruikt om de status van uw cluster beter te begrijpen. Diagnostische gegevens zijn standaard ingeschakeld op de tijd voor het maken van het cluster, waarbij een Azure Storage-tabel wordt gemaakt waarin de gebeurtenissen van deze kanalen worden verzonden om u in de toekomst te kunnen opvragen. 

* EventStore - De EventStore is een functie die wordt aangeboden door het platform dat Service Fabric-platformgebeurtenissen biedt die beschikbaar zijn in de Service Fabric Explorer en via REST API. U een momentopnameweergave zien van wat er in uw cluster aan de hand is voor elke entiteit, bijvoorbeeld knooppunt, service, toepassing en query op basis van het tijdstip van de gebeurtenis. U ook meer lezen over de EventStore in het [EventStore-overzicht.](service-fabric-diagnostics-eventstore.md)    

![EventStore (EventStore)](media/service-fabric-diagnostics-overview/eventstore.png)

De verstrekte diagnostiek is in de vorm van een uitgebreide set van gebeurtenissen out of the box. Deze [Service Fabric-gebeurtenissen](service-fabric-diagnostics-events.md) illustreren acties die door het platform worden uitgevoerd op verschillende entiteiten, zoals knooppunten, toepassingen, services, partities enz. In het laatste scenario hierboven, als een knooppunt naar beneden `NodeDown` zou gaan, zou het platform een gebeurtenis uitzenden en u onmiddellijk worden gewaarschuwd door uw monitoringtool naar keuze. Andere veelvoorkomende `ApplicationUpgradeRollbackStarted` voorbeelden `PartitionReconfigured` zijn of tijdens een failover. **Dezelfde evenementen zijn beschikbaar op zowel Windows- als Linux-clusters.**

De gebeurtenissen worden verzonden via standaardkanalen op zowel Windows als Linux en kunnen worden gelezen door elke monitoringtool die deze ondersteunt. De Azure Monitor-oplossing is Azure Monitor-logboeken. Lees gerust meer over onze [Azure Monitor-logboekenintegratie](service-fabric-diagnostics-event-analysis-oms.md) met een aangepast operationeel dashboard voor uw cluster en enkele voorbeeldquery's waaruit u waarschuwingen maken. Meer cluster monitoring concepten zijn beschikbaar op [Platform niveau event en log generatie](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Statuscontrole
Het Service Fabric-platform bevat een gezondheidsmodel, dat uitbreidbare statusrapportage biedt voor de status van entiteiten in een cluster. Elk knooppunt, toepassing, service, partitie, replica of instantie heeft een voortdurend updatable status. De status kan 'OK', 'Waarschuwing' of 'Fout' zijn. Denk aan Service Fabric-gebeurtenissen als werkwoorden die door het cluster worden gedaan aan verschillende entiteiten en status als een bijvoeglijk naamwoord voor elke entiteit. Telkens wanneer de status van een bepaalde entiteit wordt overgedragen, wordt ook een gebeurtenis uitgezonden. Op deze manier u query's en waarschuwingen voor gezondheidsgebeurtenissen instellen in uw monitoringtool naar keuze, net als elke andere gebeurtenis. 

Daarnaast laten we gebruikers zelfs de status voor entiteiten overschrijven. Als uw toepassing een upgrade doormaakt en u validatietests hebt mislukt, u naar Service Fabric Health schrijven met behulp van de Health API om aan te geven dat uw toepassing niet langer in orde is en Service Fabric de upgrade automatisch terugdraait! Voor meer informatie over het gezondheidsmodel, bekijk de [inleiding tot Service Fabric health monitoring](service-fabric-health-introduction.md)

![SFX-statusdashboard](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Waakhonden
Over het algemeen is een waakhond een afzonderlijke service die de status en belasting van services kan bekijken, eindpunten kan pingen en de status voor alles in het cluster kan rapporteren. Dit kan helpen voorkomen dat fouten die niet worden gedetecteerd op basis van de weergave van een enkele service. Waakhonden zijn ook een goede plek om code te hosten die corrigerende acties uitvoert zonder interactie van de gebruiker (bijvoorbeeld het opruimen van logbestanden in opslag op bepaalde tijdstippen). U vindt een voorbeeld waakhond service implementatie [hier](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Monitoring van infrastructuur (performance)
Nu we de diagnostiek in uw toepassing en het platform hebben behandeld, hoe weten we dat de hardware werkt zoals verwacht? Het bewaken van uw onderliggende infrastructuur is een belangrijk onderdeel van het begrijpen van de status van uw cluster en het gebruik van uw resources. Het meten van de systeemprestaties is afhankelijk van vele factoren die subjectief kunnen zijn, afhankelijk van uw workloads. Deze factoren worden meestal gemeten door middel van prestatiemeteritems. Deze prestatiemeteritems kunnen afkomstig zijn uit verschillende bronnen, waaronder het besturingssysteem, het .NET-framework of het Service Fabric-platform zelf. Sommige scenario's waarin ze nuttig zouden zijn, zijn

* Gebruik ik mijn hardware efficiënt? Wilt u uw hardware te gebruiken op 90% CPU of 10% CPU. Dit is handig bij het schalen van uw cluster of het optimaliseren van de processen van uw toepassing.
* Kan ik infrastructuurproblemen proactief voorspellen? - veel problemen worden voorafgegaan door plotselinge veranderingen (dalingen) in de prestaties, zodat u prestatiemeteritems zoals netwerk-I/O- en CPU-gebruik gebruiken om de problemen proactief te voorspellen en te diagnosticeren.

Een lijst met prestatiemeteritems die op infrastructuurniveau moeten worden verzameld, is te vinden op [Prestatiestatistieken](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric biedt ook een set prestatiemeteritems voor de programmeermodellen Reliable Services and Actors. Als u een van deze modellen gebruikt, kunnen deze prestatiemeteritems informatie bevatten om ervoor te zorgen dat uw acteurs correct op en neer draaien, of dat uw betrouwbare serviceverzoeken snel genoeg worden afgehandeld. Zie [Monitoring for Reliable Service Remoting](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) and Performance monitoring for Reliable Actors [voor](service-fabric-reliable-actors-diagnostics.md#performance-counters)meer informatie. 

De Azure Monitor-oplossing om deze te verzamelen is Azure Monitor-logboeken, net als monitoring op platformniveau. U moet de [agent Log Analytics](service-fabric-diagnostics-oms-agent.md) gebruiken om de juiste prestatiemeteritems te verzamelen en deze weer te geven in Azure Monitor-logboeken.

## <a name="recommended-setup"></a>Aanbevolen installatie
Nu we elk gebied van monitoring- en voorbeeldscenario's hebben doorlopen, volgt hier een overzicht van de Azure-bewakingshulpprogramma's en is het instellen dat nodig is om alle bovenstaande gebieden te controleren. 

* Applicatiebewaking met [Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Clusterbewaking met [diagnostics agent](service-fabric-diagnostics-event-aggregation-wad.md) en Azure [Monitor logs](service-fabric-diagnostics-oms-setup.md)
* Infrastructuurbewaking met [Azure Monitor-logboeken](service-fabric-diagnostics-oms-agent.md)

U ook de voorbeeldARM-sjabloon [hier](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) gebruiken en wijzigen om de implementatie van alle benodigde resources en agents te automatiseren. 

## <a name="other-logging-solutions"></a>Andere logging-oplossingen

Hoewel de twee oplossingen die we hebben aanbevolen, [Azure Monitor-logboeken](service-fabric-diagnostics-event-analysis-oms.md) en [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) ingebouwde integratie met Service Fabric, zijn veel gebeurtenissen uitgeschreven via ETW-providers en zijn ze uitbreidbaar met andere logging-oplossingen. U moet ook kijken naar de [Elastic Stack](https://www.elastic.co/products) (vooral als u overweegt een cluster in een offline omgeving uit te voeren), [Dynatrace](https://www.dynatrace.com/)of een ander platform van uw voorkeur. We hebben [hier](service-fabric-diagnostics-partners.md)een lijst van geïntegreerde partners beschikbaar.

De belangrijkste punten voor elk platform dat u kiest, moeten zijn hoe comfortabel u zich voelt met de gebruikersinterface, de querymogelijkheden, de aangepaste visualisaties en dashboards die beschikbaar zijn en de extra tools die ze bieden om uw bewakingservaring te verbeteren. 

## <a name="next-steps"></a>Volgende stappen

* Zie [Gebeurtenis op toepassingsniveau en logboekgeneratie](service-fabric-diagnostics-event-generation-app.md)voor aan de slag om aan de slag te gaan met het bewerken van uw toepassingen.
* Ga door de stappen om Application Insights voor uw toepassing in te stellen met [Monitor en een diagnose te stellen van een ASP.NET Core-toepassing op Service Fabric.](service-fabric-tutorial-monitoring-aspnet.md)
* Meer informatie over het monitoren van het platform en de evenementen die Service Fabric voor u biedt op [platformniveau event en log generatie.](service-fabric-diagnostics-event-generation-infra.md)
* De Azure Monitor-logboeken-integratie met ServiceFabric configureren bij [Azure Monitor-logboeken instellen voor een cluster](service-fabric-diagnostics-oms-setup.md)
* Meer informatie over het instellen van Azure Monitor-logboeken voor het bewaken van containers - [Monitoring en diagnostische gegevens voor Windows-containers in Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Zie voorbeeld diagnostische problemen en oplossingen met Service Fabric bij [het diagnosticeren van veelvoorkomende scenario's](service-fabric-diagnostics-common-scenarios.md)
* Bekijk andere diagnostische producten die integreren met Service Fabric in [servicefabric-diagnostische partners](service-fabric-diagnostics-partners.md)
* Meer informatie over algemene controleaanbevelingen voor Azure-resources - [Aanbevolen procedures - Monitoring en diagnose](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 