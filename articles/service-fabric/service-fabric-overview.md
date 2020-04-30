---
title: Overzicht van Service Fabric op Azure
description: Een overzicht van Service Fabric, waar de toepassingen zijn samengesteld uit talloze microservices die schaalbaarheid en flexibiliteit mogelijk maken. Service Fabric is een platform bestaande uit gedistribueerde systemen, dat wordt gebruikt voor het bouwen van schaalbare, betrouwbare en eenvoudig te beheren toepassingen voor de cloud.
ms.topic: overview
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 3c8eb7ead7851c311c79c2f9e9bdc7e703c3af71
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75747501"
---
# <a name="overview-of-azure-service-fabric"></a>Overzicht van Azure Service Fabric

Azure Service Fabric is een platform voor gedistribueerde systemen waarmee u gemakkelijk schaalbare en betrouwbare microservices en containers verpakt, implementeert en beheert. Service Fabric biedt ook een oplossing voor de grote uitdaging van het ontwikkelen en beheren van systeemeigen cloudtoepassingen. Ontwikkelaars en beheerders kunnen complexe infrastructuurproblemen voorkomen en zich concentreren op het implementeren van bedrijfsspecifieke, veeleisende werkbelastingen die schaalbaar, betrouwbaar en beheerbaar zijn. Service Fabric is het platform van de nieuwe generatie voor het ontwikkelen en beheren van deze hoogwaardige zakelijke tier-1-toepassingen op cloudschaal die in containers worden uitgevoerd.

In deze korte video worden Service Fabric en microservices geïntroduceerd:
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

## <a name="compliance"></a>Naleving

Azure Service Fabric resource provider is beschikbaar in alle Azure-regio's en voldoet aan alle nalevings certificaten die Azure heeft. Dit omvat het volgende: SOC, ISO, PCI DSS, HIPAA en AVG. Raadpleeg het volgende voor een volledige lijst met compliantie certificaten: [nalevings aanbod](https://www.microsoft.com/trustcenter/compliance/complianceofferings)

## <a name="applications-composed-of-microservices"></a>Toepassingen die uit microservices zijn samengesteld

Met Service Fabric kunt u schaal bare en betrouw bare toepassingen bouwen en beheren die bestaan uit micro Services. Deze gedistribueerde micro services worden uitgevoerd met hoge dichtheid op een gedeelde groep machines, die een cluster wordt genoemd. Service Fabric biedt een geavanceerde, licht gewicht runtime die stateless en stateful micro Services ondersteunt. Het biedt ook uitgebreide mogelijkheden voor toepassings beheer voor het inrichten, implementeren, bewaken, bijwerken en verwijderen van geïmplementeerde toepassingen.

Service Fabric is afgestemd voor het maken van cloud-eigen services die zo nodig klein kunnen beginnen en tot grote schaal kunnen worden uitgebreid tot honderden of duizenden computers. De huidige services op internetschaal zijn gebouwd op basis van microservices. Voorbeelden van microservices zijn protocolgateways, gebruikersprofielen, winkelwagentjes, voorraadverwerking, wachtrijen caches.

Op Service Fabric kunnen tegenwoordig talloze Microsoft-services draaien, waaronder Azure SQL Database, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, Skype voor Bedrijven en vele Azure-kernservices.

Service Fabric host micro services binnen containers die zijn geïmplementeerd en geactiveerd via het Service Fabric cluster. Door over te stappen van virtuele machines op containers wordt een dichtheid bewerkstelligt die een orde van grootte hoger ligt. Een dergelijke dichtheidsverhoging is mogelijk als u overstapt van containers op microservices in deze containers. Zo bestaat één cluster voor Azure SQL Database uit honderden computers waarop duizenden containers worden uitgevoerd die op hun beurt honderdduizenden databases hosten. Elke database is een stateful Service Fabric-microservice.

Zie [Waarom een microservices-benadering voor het ontwikkelen van toepassingen?](service-fabric-overview-microservices.md) voor meer informatie

## <a name="container-deployment-and-orchestration"></a>Implementatie en indeling van containers

Service Fabric is de [containerorchestrator](service-fabric-cluster-resource-manager-introduction.md) van Microsoft waarmee microservices in een computercluster worden geïmplementeerd. Microservices kunnen op tal van manieren worden ontwikkeld met behulp van de [Service Fabric-programmeermodellen](service-fabric-choose-framework.md), [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), voor het implementeren van [code naar keuze](service-fabric-guest-executables-introduction.md). U kunt zelfs beide services onderbrengen in processen en services in containers binnen dezelfde toepassing. Als u slechts [containers wilt implementeren en beheren](service-fabric-containers-overview.md), is Service Fabric de perfecte keuze als containerorchestrator.

## <a name="any-os-any-cloud"></a>Elk besturingssysteem, elke cloud

Service Fabric kan overal worden uitgevoerd. U kunt in diverse omgevingen clusters maken voor Service Fabric, waaronder Azure of on-premises, Windows Server of Linux. U kunt zelfs clusters maken in andere openbare clouds. Bovendien is de ontwikkelomgeving in de SDK **identiek** aan de productieomgeving, zonder dat er emulatoren bij betrokken zijn. Met andere woorden, wat u uitvoert op uw lokale ontwikkelcluster, wordt gebruikt op de clusters in andere omgevingen.

![Service Fabric-platform][Image1]

Voor ontwikkeling onder Windows is de SDK van Service Fabric .NET geïntegreerd met Visual Studio en Powershell. Zie [Uw ontwikkelomgeving voorbereiden in Windows](service-fabric-get-started.md). Voor ontwikkeling onder Linux is de SDK van Service Fabric Java geïntegreerd met Eclipse, en wordt Yeoman gebruikt voor het genereren van sjablonen voor Java, .NET Core en containertoepassingen. Zie [Uw ontwikkelomgeving voorbereiden in Linux](service-fabric-get-started-linux.md)

Zie [Service Fabric-clusters maken onder Windows Server of Linux](service-fabric-deploy-anywhere.md) voor meer informatie, of voor Azure voor het maken van een cluster [via Azure Portal](service-fabric-cluster-creation-via-portal.md).

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Staatloze en stateful microservices voor Service Fabric

Met Service Fabric kunt u toepassingen bouwen die uit microservices of containers bestaan. Staatloze microservices (zoals protocolgateways en webproxy's) handhaven buiten een aanvraag en de reactie erop van de service geen veranderlijke status. Azure Cloud Services-werkrollen zijn een voorbeeld van een staatloze service. Stateful microservices (zoals gebruikersaccounts, databases, apparaten, winkelwagentjes en wachtrijen) handhaven een veranderlijke, gezaghebbende status, buiten de aanvraag en de reactie erop. De huidige toepassingen op internetschaal bestaan uit een combinatie van staatloze en stateful microservices. 

Een belangrijk aspect van Service Fabric is de sterke focus op het bouwen van stateful apparaten, met de [ingebouwde programmeermodellen](service-fabric-choose-framework.md) of met stateful services in containers. De [toepassingsscenario's](service-fabric-application-scenarios.md) beschrijven de scenario's waarin stateful services worden gebruikt.

## <a name="application-lifecycle-management"></a>Beheer van toepassingslevenscyclus

Service Fabric biedt ondersteuning voor de volledige toepassingslevenscyclus en CI/CD van cloudtoepassingen, met inbegrip van containers. Deze levenscyclus omvat de ontwikkeling tot en met implementatie, het dagelijks beheer, onderhoud en het uiteindelijk buiten gebruik stellen.

Dankzij de mogelijkheden die het beheer van de Service Fabric-toepassingslevenscyclus biedt, kunnen toepassingsbeheerders en IT-operators eenvoudige werkstromen voor het inrichten, implementeren, patchen en bewaken van toepassingen. Deze ingebouwde werkstromen betekenen een aanzienlijke reductie van de werkbelasting voor IT-operators bij het continu beschikbaar maken van toepassingen.

De meeste toepassingen bestaan uit een combinatie van staatloze en stateful microservices, containers en andere uitvoerbare bestanden die gezamenlijk zijn geïmplementeerd. Dankzij krachtige typen voor de toepassingen maakt Service Fabric de implementatie mogelijk van meerdere exemplaren van de toepassingen. Elk exemplaar wordt beheerd, en onafhankelijk bijgewerkt. Bovendien kan Service Fabric containers of elk uitvoerbaar bestand implementeren en ze betrouwbaar maken. Service Fabric kunt bijvoorbeeld .NET, ASP.NET Core, Python, node. js, Windows-containers, Linux-containers, java virtual machines, scripts, hoek of letterlijk alles dat uw toepassing vormt, implementeren.

Service Fabric is geïntegreerd met CI/CD-hulpprogramma's als [Azure Pipelines](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) en [Octopus Deploy](https://octopus.com/), en kan worden gebruikt met elk ander bekend CI/CD-hulpprogramma.

Zie [Toepassingslevenscyclus](service-fabric-application-lifecycle.md) voor meer informatie. Zie [Een toepassing implementeren die door een gast kan worden uitgevoerd](service-fabric-deploy-existing-app.md) voor meer informatie over het implementeren van code.

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Als u Service Fabric gebruikt, kunt u:

* Een implementatie uitvoeren op Azure of on-premises datacenters waarop Windows of Linux wordt uitgevoerd, en zonder wijzigingen in de code. Toepassingen eenmaal schrijven en vervolgens ergens op een Service Fabric-cluster implementeren.
* Schaalbare toepassingen ontwikkelen die uit microservices bestaan door middel van de Service Fabric-programmeermodellen, containers of code.
* Uiterst betrouwbare staatloze en stateful microservices ontwikkelen. Het ontwerp van uw toepassing vereenvoudigen met behulp van stateful microservices. 
* Het nieuwe Reliable Actors-programmeermodel gebruiken om cloudobjecten met zelfstandige code en status te maken.
* Containers implementeren en indelen die Windows- en Linux-containers bevatten. Service Fabric is een data-aware, stateful containerorchestrator.
* Toepassingen binnen enkele seconden bij hoge dichtheid implementeren met honderden of duizenden toepassingen of containers per computer.
* Verschillende versies van dezelfde toepassing naast elkaar implementeren en elke toepassing onafhankelijk upgraden.
* De levenscyclus van uw toepassingen zonder downtime beheren, waaronder upgrades met en zonder onderbreking.
* Het aantal knooppunten in een cluster omhoog of omlaag schalen. Tijdens het schalen van knooppunten worden uw toepassingen automatisch geschaald.
* De status van uw toepassingen bewaken en er diagnoses op uitvoeren, en beleidsregels instellen voor het uitvoeren van automatische reparaties.
* Erop toezien dat de Resource Balancer de herverdeling van toepassingen indeelt voor de hele cluster. Service Fabric herstelt van storingen en optimaliseert de verdeling van de belasting op basis van beschikbare resources.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Volgende stappen

* Voor meer informatie:
  * [Waarom een microservices-benadering voor het ontwikkelen van toepassingen?](service-fabric-overview-microservices.md)
  * [Overzicht terminologie](service-fabric-technical-overview.md)
* Een [ontwikkelomgeving voor Windows](service-fabric-get-started.md) instellen  
* Een [ontwikkelomgeving voor Linux](service-fabric-get-started-linux.md) instellen
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md)

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
