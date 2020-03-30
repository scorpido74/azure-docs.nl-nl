---
title: Maak kennis met de Cluster Resource Manager servicestructuur
description: Meer informatie over de Service Fabric Cluster Resource Manager, een manier om de orkestratie van de services van uw toepassing te beheren.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: da9205f5d95eaf1b4dc655ee727ab8a4fe90893d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563323"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Maak kennis met de clusterbronbeheerder van Service Fabric
Traditioneel beheer van IT-systemen of online diensten betekende wijden specifieke fysieke of virtuele machines aan die specifieke diensten of systemen. Diensten werden ontworpen als lagen. Er zou een "web" laag en een "data" of "opslag" laag. Toepassingen zouden een berichtenlaag hebben waar aanvragen in en uit stroomden, evenals een set machines die zijn gewijd aan caching. Elke laag of type werkbelasting had specifieke machines gewijd aan het: de database kreeg een paar machines gewijd aan het, de webservers een paar. Als een bepaald type werkbelasting ervoor zorgde dat de machines waarop de machines werden uitgevoerd te heet, hebt u meer machines met dezelfde configuratie aan die laag toegevoegd. Niet alle workloads kunnen echter zo gemakkelijk worden opgeschaald - vooral met de gegevenslaag die u machines doorgaans zou vervangen door grotere machines. Simpel. Als een machine uitviel, wordt dat deel van de totale toepassing uitgevoerd met een lagere capaciteit totdat de machine kan worden hersteld. Nog steeds vrij eenvoudig (zo niet per se leuk).

Nu echter de wereld van service en software architectuur is veranderd. Het komt vaker voor dat toepassingen een scale-out ontwerp hebben aangenomen. Het bouwen van toepassingen met containers of microservices (of beide) is gebruikelijk. Nu, terwijl u misschien nog maar een paar machines hebt, draaien ze niet slechts één exemplaar van een werkbelasting. Ze kunnen zelfs meerdere verschillende workloads tegelijk uitvoeren. U hebt nu tientallen verschillende soorten diensten (geen verbruiken van een volledige machine ter waarde van middelen), misschien honderden verschillende gevallen van deze diensten. Elke benoemde instantie heeft een of meer exemplaren of replica's voor HA (High Availability). Afhankelijk van de grootte van deze workloads, en hoe druk ze zijn, u zich bevinden met honderden of duizenden machines. 

Plotseling is het beheren van uw omgeving niet zo eenvoudig als het beheren van een paar machines gewijd aan afzonderlijke soorten workloads. Uw servers zijn virtueel en hebben geen namen meer (u bent toch overgestapt van [huisdieren naar vee).](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) Configuratie gaat minder over de machines en meer over de diensten zelf. Hardware die is gewijd aan een enkel exemplaar van een werkbelasting is grotendeels een ding van het verleden. Diensten zelf zijn geworden kleine gedistribueerde systemen die meerdere kleinere stukken van commodity hardware span.

Omdat uw app niet langer een reeks monolieten is, verspreid over verschillende lagen, hebt u nu veel meer combinaties om mee om te gaan. Wie bepaalt welke soorten workloads kunnen worden uitgevoerd op welke hardware, of hoeveel? Welke workloads werken goed op dezelfde hardware en welk conflict? Als er een machine uitvalt, hoe weet je dan wat er op die machine liep? Wie zorgt ervoor dat de werklast weer actief wordt? Wacht je tot de (virtuele?) machine terugkomt of mislukken je workloads automatisch naar andere machines en blijven ze draaien? Is menselijke tussenkomst vereist? Hoe zit het met upgrades in deze omgeving?

Als ontwikkelaars en operators die zich bezighouden met deze omgeving, zullen we hulp willen bij het beheren van deze complexiteit. Een inhuren binge en proberen om de complexiteit met mensen te verbergen is waarschijnlijk niet het juiste antwoord, dus wat doen we?

## <a name="introducing-orchestrators"></a>Maak kennis met orkestleden
Een "Orchestrator" is de algemene term voor een stukje software dat beheerders helpt bij het beheren van dit soort omgevingen. Orchestrators zijn de componenten die rekening houden met verzoeken als "Ik wil graag vijf exemplaren van deze dienst die in mijn omgeving worden uitgevoerd." Ze proberen de omgeving te laten overeenkomen met de gewenste staat, wat er ook gebeurt.

Orchestrators (niet mensen) zijn wat actie ondernemen wanneer een machine uitvalt of een werkbelasting eindigt om een onverwachte reden. De meeste orkestoren doen meer dan alleen omgaan met falen. Andere functies die ze hebben zijn het beheren van nieuwe implementaties, het afhandelen van upgrades en het omgaan met resourceverbruik en governance. Alle orchestrators zijn fundamenteel over het handhaven van een aantal gewenste staat van configuratie in de omgeving. U wilt in staat zijn om een orchestrator vertellen wat je wilt en hebben het doen het zware werk. Aurora bovenop Mesos, Docker Datacenter/Docker Swarm, Kubernetes en Service Fabric zijn allemaal voorbeelden van orchestrators. Deze orkestleden worden actief ontwikkeld om te voldoen aan de behoeften van echte workloads in productieomgevingen. 

## <a name="orchestration-as-a-service"></a>Orkestratie als dienst
De Clusterresourcemanager is de systeemcomponent die orkestratie in servicestructuur verwerkt. De taak van de clusterresourcemanager is onderverdeeld in drie delen:

1. Regels handhaven
2. Uw omgeving optimaliseren
3. Helpen met andere processen

### <a name="what-it-isnt"></a>Wat het niet is
In traditionele N-niveau toepassingen, is er altijd een [Load Balancer](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Meestal was dit een Network Load Balancer (NLB) of een Application Load Balancer (ALB), afhankelijk van waar het zat in de netwerkstack. Sommige load balancers zijn hardware-gebaseerde zoals F5's BigIP-aanbod, anderen zijn software-based, zoals Microsoft's NLB. In andere omgevingen ziet u mogelijk iets als HAProxy, nginx, Istio of Envoy in deze rol. In deze architecturen is het taak om ervoor te zorgen dat stateloze workloads (ongeveer) dezelfde hoeveelheid werk ontvangen. Strategieën voor het balanceren van de belasting gevarieerd. Sommige balancers zouden elk ander gesprek naar een andere server sturen. Anderen verstrekten sessie pinning/stickiness. Meer geavanceerde balancers gebruiken de werkelijke belastingschatting of rapportage om een oproep te routeren op basis van de verwachte kosten en de huidige machinebelasting.

Netwerkbalancers of berichtrouters probeerden ervoor te zorgen dat de web/worker-laag ongeveer in balans bleef. Strategieën voor het in evenwicht brengen van de gegevenslaag waren verschillend en waren afhankelijk van het mechanisme voor gegevensopslag. Het balanceren van de gegevenslaag was gebaseerd op gegevensschering, caching, beheerde weergaven, opgeslagen procedures en andere winkelspecifieke mechanismen.

Hoewel sommige van deze strategieën interessant zijn, is de Service Fabric Cluster Resource Manager niet zoiets als een netwerkload balancer of een cache. Een Network Load Balancer balanceert frontends door het verkeer over frontends te spreiden. De Service Fabric Cluster Resource Manager heeft een andere strategie. In principe verplaatst Service Fabric *services* naar de plaats waar ze het meest zinvol zijn, in de verwachting dat verkeer of belasting zullen volgen. Het kan bijvoorbeeld services verplaatsen naar knooppunten die momenteel koud zijn omdat de services die er zijn niet veel werk doen. De knooppunten kunnen koud zijn omdat de services die aanwezig waren, zijn verwijderd of elders zijn verplaatst. Als ander voorbeeld kan clusterbronbeheer een service ook van een machine verplaatsen. Misschien is de machine staat te worden opgewaardeerd, of is overbelast als gevolg van een piek in het verbruik door de diensten die op het. Als alternatief kunnen de resourcevereisten van de service zijn toegenomen. Als gevolg hiervan zijn er niet voldoende middelen op deze machine om door te gaan met het uitvoeren van het. 

Omdat clusterresourcebeheer verantwoordelijk is voor het verplaatsen van services, bevat deze een andere functieset in vergelijking met wat u in een netwerklastsbalans zou vinden. Dit komt omdat netwerkload balancers netwerkverkeer leveren naar waar de services al zijn, zelfs als die locatie niet ideaal is voor het uitvoeren van de service zelf. De Service Fabric Cluster Resource Manager hanteert fundamenteel verschillende strategieën om ervoor te zorgen dat de resources in het cluster efficiënt worden gebruikt.

## <a name="next-steps"></a>Volgende stappen
- Voor informatie over de architectuur en informatiestroom binnen de Cluster Resource Manager, bekijk [dit artikel](service-fabric-cluster-resource-manager-architecture.md)
- De Clusterresourcemanager heeft veel opties voor het beschrijven van het cluster. Voor meer informatie over statistieken, bekijk dit artikel over [het beschrijven van een Service Fabric-cluster](service-fabric-cluster-resource-manager-cluster-description.md)
- Meer informatie over het configureren van services, [meer informatie over het configureren van Services](service-fabric-cluster-resource-manager-configure-services.md)
- Statistieken zijn hoe de Clusterresourcemanger van de servicestructuur het verbruik en de capaciteit in het cluster beheert. Voor meer informatie over statistieken en hoe u deze configureren, [raadpleegt u dit artikel](service-fabric-cluster-resource-manager-metrics.md)
- De Cluster Resource Manager werkt met de beheermogelijkheden van Service Fabric. Lees [dit artikel](service-fabric-cluster-resource-manager-management-integration.md) voor meer informatie over die integratie
- Als u wilt weten hoe de clusterresourcemanager de belasting in het cluster beheert en in evenwicht brengt, raadpleegt u het artikel over [het balanceren van de belasting](service-fabric-cluster-resource-manager-balancing.md)
