---
title: Overzicht van Azure Service Fabric mesh
description: Meer informatie over Azure Service Fabric Mesh. Met Azure Service Fabric Mesh kunt u uw toepassing implementeren en schalen zonder u zorgen te maken over de infrastructuurbehoeften van uw toepassing.
author: dkkapur
ms.author: dekapur
ms.date: 10/1/2018
ms.topic: overview
ms.openlocfilehash: d6522d417556104a1ece703c725f3fbeab49d683
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458985"
---
# <a name="what-is-service-fabric-mesh"></a>Wat is Service Fabric?

Deze video geeft een kort overzicht van Service Fabric Mesh.
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Azure Service Fabric Mesh is een volledig beheerde service waarmee ontwikkelaars microservices-toepassingen kunnen implementeren zonder virtuele machines, opslag of netwerken hoeven te beheren. U kunt toepassingen die worden gehost in Service Fabric Mesh uitvoeren en schalen zonder u zorgen te maken over de infrastructuur die dit mogelijk maakt.  Service Fabric Mesh bestaat uit clusters met duizenden machines.  Alle clusterbewerkingen zijn verborgen voor de ontwikkelaar. Upload uw code en geef de resources op die u nodig hebt, de vereisten voor de beschik baarheid en de resource limieten.  Service Fabric Mesh wijst automatisch de infrastructuur toe en handelt problemen met de infrastructuur af, waarbij ervoor wordt gezorgd dat uw toepassingen maximaal beschikbaar zijn. U hoeft zich alleen bezig te houden met de status en de reactietijd van uw toepassing, niet met de infrastructuur.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Dit artikel bevat een overzicht van de belangrijkste voordelen van Service Fabric Mesh.

## <a name="great-developer-experience"></a>Geweldige ontwikkelervaring

Service Fabric Mesh ondersteunt alle programmeertalen en frameworks die kunnen worden uitgevoerd in een container. Ondersteuning voor Visual Studio 2019 en Visual Studio code tool biedt een krachtige methode voor het bewerken en opsporen van fouten voor .NET-en .NET core-toepassingen. 

Met Service Fabric Mesh kunt u het volgende:

- Bestaande toepassingen via 'lift and shift' verplaatsen naar containers om uw huidige toepassingen te moderniseren en op schaal uit te voeren.
- Nieuwe microservices-toepassingen op schaal bouwen en implementeren in Azure.  Integreren met andere Azure-services of bestaande toepassingen die in containers worden uitgevoerd. Elke micro service maakt deel uit van een veilige, geïsoleerde netwerk toepassing. De micro service heeft het beheer beleid voor resources dat is gedefinieerd voor CPU-kernen, geheugen, schijf ruimte en meer.
- Integreren en uitbreiden van bestaande toepassingen zonder wijzigingen aan te brengen in deze toepassingen. Uw eigen virtuele netwerk gebruiken om bestaande toepassingen met de nieuwe toepassing te verbinden.  
- Uw bestaande Cloud Services-toepassingen moderniseren door te migreren naar Service Fabric Mesh.  

## <a name="simple-operational-lifecycle"></a>Eenvoudige operationele levenscyclus

U kunt eenvoudig actieve toepassingen, bewakings toepassingen en fout opsporing in productie omgevingen beheren. Dit beheer omvat upgrades van toepassingen en versies. Deze toepassingen kunnen bestaan uit een enkele microservice of meerdere microservices geïsoleerd binnen hun eigen netwerk. Toepassingen worden efficiënt uitgevoerd met snelle implementatie, plaatsing en failovertijden.

Met Service Fabric Mesh kunt u het volgende:

- Implementeren en beheren van toepassingen zonder expliciete inrichting en beheer van de infrastructuur.  Door Service Fabric Mesh wordt de onderliggende infrastructuur voor u ingericht, bijgewerkt en van patches voorzien.
- Continue integratie instellen met behulp van de geïntegreerde hulpprogramma's om toepassingen gemakkelijk te verpakken en te implementeren.
- Maak gebruik van alle functies van Azure Resource Manager resources. Voor beelden van deze functies zijn onder andere controle-Trail en [op rollen gebaseerd toegangs beheer](/azure/role-based-access-control/overview)). Alle resources die u in azure implementeert voor de Service Fabric mesh-service, zijn Azure Resource Manager resources. Deze resources zijn onder andere toepassingen, services, geheimen, enzovoort.
- Implementeren en beheren van resources met behulp van [Azure Portal](https://portal.azure.com), Resource Manager-sjablonen of Azure CLI/PowerShell-bibliotheken.
- Instellen van operationele bewaking en waarschuwingen met behulp van [Application Insights](/azure/application-insights/) (of een hulpprogramma naar keuze) om de operationele en diagnostische traceringen van het platform vast te leggen.
- Toegang krijgen tot diagnostische gegevens van de toepassing die afkomstig zijn uit het toepassingmodel via [Application Insights](/azure/application-insights/) of een hulpprogramma naar keuze.
- Resourcegebruik optimaliseren door regels voor automatisch schalen voor de services op te geven in de definitie van de toepassing.

## <a name="mission-critical-platform-capabilities"></a>Bedrijfskritieke platformmogelijkheden

Service Fabric Mesh maakt een verzameling clusters die [Azure-beschikbaarheidszones](/azure/availability-zones/az-overview) en/of geo-politieke regionale grenzen omspannen. Service Fabric mesh beschrijft toepassingen met een set intenties, zoals schaal, hardwarevereisten, duurzaamheids vereisten en beveiligings beleid.  Wanneer de toepassing wordt geïmplementeerd, zoekt Service Fabric Mesh naar de optimale plaats om deze uit te voeren.

Met Service Fabric Mesh kunt u het volgende:

- Profiteren van hoge beschikbaarheid, omhoog/omlaag schalen, indeling, berichtroutering, betrouwbare uitwisseling van berichten, upgrades zonder downtime, beheer van beveiliging/geheimen, herstel na noodgevallen, statusbeheer, configuratiebeheer en gedistribueerde transacties.
- Kiezen tussen meerdere toepassingsmodellen bij het maken van toepassingen.
- Platformmogelijkheden die toegankelijk zijn via REST-eindpunten door de gebruikte taalspecifieke bindingen die zijn gegenereerd met Swagger te gebruiken.
- Toepassingen implementeren via [Beschikbaarheidszones](/azure/availability-zones/az-overview) en meerdere regio's voor geografische betrouwbaarheid.
- Gebruikmaken van alle beveiligings- en nalevingsfuncties die Azure biedt.

## <a name="next-steps"></a>Volgende stappen

Het kost slechts enkele stappen om een voorbeeldproject met Visual Studio te implementeren. Zie [Een ASP.NET Core-website maken](service-fabric-mesh-quickstart-dotnet-core.md) voor meer informatie. 

Zoek antwoorden op [veelgestelde vragen](service-fabric-mesh-faq.md).


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
