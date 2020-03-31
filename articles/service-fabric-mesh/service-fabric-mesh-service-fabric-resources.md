---
title: Inleiding tot Azure Service Fabric Resource Model
description: Meer informatie over het Service Fabric Resource Model, een vereenvoudigde benadering voor het definiëren van Service Fabric Mesh-toepassingen.
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 0ae2ed163560aee4c0c3525ab31910e37afaa5b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259134"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Inleiding tot het Service Fabric-resourcemodel

Het Service Fabric Resource Model beschrijft een eenvoudige aanpak om resources te definiëren die bestaan uit een Service Fabric Mesh-toepassing. Individuele resources kunnen worden geïmplementeerd in elke Service Fabric-omgeving.  Het Service Fabric Resource Model is ook compatibel met het Azure Resource Manager-model. De volgende typen resources worden momenteel ondersteund in dit model:

- Toepassingen en services
- Netwerken
- Gateways
- Geheimen en geheimen/waarden
- Volumes

Elke resource wordt declaratief beschreven in een bronbestand, een eenvoudig YAML- of JSON-document dat de mesh-toepassing beschrijft en wordt ingericht door het Service Fabric-platform.

## <a name="applications-and-services"></a>Toepassingen en diensten

Een toepassingsbron is de eenheid van implementatie, versiebeheer en levensduur van een Mesh-toepassing. Het bestaat uit een of meer servicebronnen die een microservice vertegenwoordigen. Elke servicebron bestaat op zijn beurt uit een of meer codepakketten waarin alles wordt beschreven wat nodig is om de containerafbeelding uit te voeren die is gekoppeld aan het codepakket.

![Apps en services][Image1]

Een servicebron verklaart het volgende:

- Containernaam, versie en register
- CPU- en geheugenbronnen die nodig zijn voor elke container
- Netwerkeindpunten
- Verwijzingen naar andere bronnen, zoals netwerken, volumes en geheimen 

Alle codepakketten die zijn gedefinieerd als onderdeel van een Service-bron, worden samen geïmplementeerd en geactiveerd als een groep. De servicebron beschrijft ook hoeveel exemplaren van de service moet worden uitgevoerd en verwijst ook naar andere bronnen (bijvoorbeeld netwerkbron) waarvan deze afhankelijk is.

Als een Mesh-toepassing uit meer dan één service bestaat, wordt deze niet gegarandeerd samen uitgevoerd op hetzelfde knooppunt. Tijdens een upgrade van de toepassing leidt het niet upgraden van een enkele service ertoe dat alle Services worden teruggedraaid naar hun vorige versie.

Zoals eerder al is gezinspeeld, kan de levenscyclus van elke toepassingsinstantie onafhankelijk worden beheerd. De ene toepassingsinstantie kan bijvoorbeeld onafhankelijk van de andere toepassingsinstanties worden geüpgraded. Meestal houdt u het aantal services in een toepassing vrij klein, omdat hoe meer services u in een toepassing plaatst, hoe moeilijker het wordt om elke service onafhankelijk te beheren.

## <a name="networks"></a>Netwerken

Netwerkbron is individueel geïmplementeerde bron, onafhankelijk van een toepassing of servicebron die deze als hun afhankelijkheid kan verwijzen. Het wordt gebruikt om een netwerk voor uw toepassingen te creëren. Meerdere services van verschillende toepassingen kunnen deel uitmaken van hetzelfde netwerk.  Lees voor meer informatie over [netwerken in Service Fabric Mesh-toepassingen.](service-fabric-mesh-networks-and-gateways.md)

> [!NOTE]
> De huidige preview ondersteunt slechts één op één toewijzing tussen toepassingen en netwerken

![Netwerk en gateway][Image2]

## <a name="gateways"></a>Gateways
Een Gateway-bron verbindt twee netwerken en leidt verkeer.  Een gateway stelt uw services in staat om te communiceren met externe klanten en biedt een binnendringen in uw service(en).  Een gateway kan ook worden gebruikt om uw Mesh-toepassing te verbinden met uw eigen, bestaande virtuele netwerk. Lees voor meer informatie over [netwerken in Service Fabric Mesh-toepassingen.](service-fabric-mesh-networks-and-gateways.md)

![Netwerk en gateway][Image2]

## <a name="secrets"></a>Geheimen

Geheimenbronnen kunnen worden geïmplementeerd onafhankelijk van een toepassing of servicebron die deze mogelijk hun afhankelijkheid kan noemen. Het wordt gebruikt om veilig geheimen te leveren aan uw toepassingen. Meerdere services van verschillende toepassingen kunnen verwijzen naar waarden van hetzelfde geheim.

## <a name="volumes"></a>Volumes

Containers maken vaak tijdelijke schijven beschikbaar. Tijdelijke schijven zijn vluchtig, echter, dus je krijgt een nieuwe tijdelijke schijf en verliest de informatie wanneer een container crasht. Het is ook moeilijk om informatie over tijdelijke schijven te delen met andere containers. Volumes zijn mappen die in uw containerexemplaren worden gemonteerd die u gebruiken om de status aan te houden. Volumes geven u bestandsopslag voor algemene doeleinden en stellen u in staat om bestanden te lezen/schrijven met behulp van normale I/O-bestands-API's op schijf. De bron Volume is een declaratieve manier om te beschrijven hoe een map is gemonteerd en de back-up hiervoor (Azure Files Volume of Service Fabric Reliable Volume).  Lees voor meer informatie [de status opslaan](service-fabric-mesh-storing-state.md#volumes).

![Volumes][Image3]

## <a name="programming-models"></a>Programmeermodellen
Servicebron vereist alleen een containerafbeelding die moet worden uitgevoerd, waarnaar wordt verwezen in het codepakket(en) dat aan de resource is gekoppeld. U elke code, geschreven in elke taal, met behulp van elk framework in de container zonder te weten, of gebruik, Service Fabric Mesh specifieke API's. 

Uw toepassingscode blijft draagbaar, zelfs buiten Service Fabric Mesh en uw toepassingsimplementaties blijven consistent, ongeacht de taal of het framework dat wordt gebruikt om uw services te implementeren. Of uw toepassing nu ASP.NET Core, Go of slechts een set processen en scripts is, het implementatiemodel van de Service Fabric Mesh Resource blijft hetzelfde. 

## <a name="packaging-and-deployment"></a>Verpakking en implementatie

Service Fabric Mesh-toepassingen op basis van het resourcemodel worden verpakt als Docker-containers.  Service Fabric Mesh is een gedeelde omgeving met meerdere huurders en containers geven u een hoge isolatie.  Deze toepassingen worden beschreven met behulp van een JSON-indeling of een YAML-indeling (die vervolgens wordt geconverteerd naar JSON). Bij het implementeren van een Mesh-toepassing in Azure Service Fabric Mesh is de JSON die wordt gebruikt om de toepassing te beschrijven een Azure Resource Manager-sjabloon. Resources worden toegewezen aan Azure-resources.  Wanneer u een Mesh-toepassing implementeert in een cluster van Servicefabric (standalone of Azure-gehost), is de JSON die wordt gebruikt om de toepassing te beschrijven een indeling die vergelijkbaar is met een Azure Resource Manager-sjabloon.  Eenmaal geïmplementeerd kunnen Mesh-toepassingen worden beheerd via HTTP-interfaces of de Azure CLI. 


## <a name="next-steps"></a>Volgende stappen 
Lees het overzicht voor meer informatie over Service Fabric Mesh:
- [Service Fabric Mesh - overzicht](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
