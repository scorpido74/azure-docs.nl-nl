---
title: Inleiding tot Azure Service Fabric-netwerken
description: Meer informatie over netwerken, gateways en intelligente verkeersroutering in Service Fabric Mesh.
author: dkkapur
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: dc793e2991783cc9b7b46d92fcc8e0267feb529b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75459130"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Inleiding tot netwerken in Service Fabric Mesh-toepassingen
In dit artikel worden verschillende typen load balancers beschreven, hoe gateways het netwerk met uw toepassingen verbinden met andere netwerken en hoe verkeer wordt gerouteerd tussen de services in uw toepassingen.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Laag 4 vs laag 7 load balancers
Load balancing kan worden uitgevoerd op verschillende lagen in het [OSI-model](https://en.wikipedia.org/wiki/OSI_model) voor netwerken, vaak in laag 4 (L4) en laag 7 (L7).  Meestal zijn er twee soorten load balancers:

- Een L4 load balancer werkt op de netwerktransportlaag, die zich bezighoudt met de levering van pakketten zonder rekening te houden met de inhoud van de pakketten. Alleen pakketheaders worden geïnspecteerd door de load balancer, zodat de balanceringscriteria beperkt zijn tot IP-adressen en poorten. Een client maakt bijvoorbeeld een TCP-verbinding met de load balancer. De load balancer beëindigt de verbinding (door rechtstreeks op de SYN te reageren), selecteert een backend en maakt een nieuwe TCP-verbinding met de backend (stuurt een nieuwe SYN). Een L4-load balancer werkt doorgaans alleen op het niveau van de L4 TCP/UDP-verbinding of -sessie. De load balance leidt dus bytes om en zorgt ervoor dat bytes van dezelfde sessie op dezelfde backend terechtkomen. De L4 load balancer is niet op de hoogte van de details van de toepassing van de bytes die het beweegt. De bytes kunnen elk toepassingsprotocol zijn.

- Een L7 load balancer werkt op de toepassingslaag, die zich bezighoudt met de inhoud van elk pakket. Het inspecteert de inhoud van het pakket omdat het protocollen zoals HTTP, HTTPS of WebSockets begrijpt. Dit geeft de load balancer de mogelijkheid om geavanceerde routing uit te voeren. Een client maakt bijvoorbeeld één HTTP/2 TCP-verbinding met de load balancer. De load balancer gaat vervolgens over tot twee backendverbindingen. Wanneer de client twee HTTP/2-streams naar de load balancer stuurt, wordt stream one verzonden naar backend one en wordt stream twee verzonden naar backend twee. Dus, zelfs multiplexing klanten die hebben enorm verschillende aanvraag belastingen zullen efficiënt worden gebalanceerd over de backends. 

## <a name="networks-and-gateways"></a>Netwerken en gateways
In het [Resourcemodel ServiceFabric](service-fabric-mesh-service-fabric-resources.md)is een netwerkbron een afzonderlijk inzetbare bron, onafhankelijk van een toepassing of servicebron die deze als hun afhankelijkheid kan verwijzen. Het wordt gebruikt om een netwerk voor uw toepassingen te creëren dat open staat voor het internet. Meerdere services van verschillende toepassingen kunnen deel uitmaken van hetzelfde netwerk. Dit privénetwerk wordt gemaakt en beheerd door Service Fabric en is geen Extern netwerk (Azure). Toepassingen kunnen dynamisch worden toegevoegd en verwijderd uit de netwerkbron om VNET-connectiviteit in te schakelen en uit te schakelen. 

Een gateway wordt gebruikt om twee netwerken te overbruggen. De Gateway-bron implementeert een [Envoy-proxy](https://www.envoyproxy.io/) die L4-routering biedt voor elk protocol en L7-routering voor geavanceerde HTTP(S)-toepassingsroutering. De gateway leidt verkeer naar uw netwerk vanuit een extern netwerk en bepaalt naar welke service u verkeer moet leiden.  Het externe netwerk kan een open netwerk zijn (in wezen het openbare internet) of een virtueel Azure-netwerk, zodat u verbinding maken met uw andere Azure-toepassingen en -bronnen. 

![Netwerk en gateway][Image1]

Wanneer de netwerkbron `ingressConfig`wordt gemaakt met , wordt een openbaar IP toegewezen aan de netwerkbron. Het openbare IP-adres wordt gekoppeld aan de levensduur van de netwerkbron.

Wanneer een Mesh-toepassing wordt gemaakt, moet deze verwijzen naar een bestaande netwerkbron. Nieuwe openbare poorten kunnen worden toegevoegd of bestaande poorten kunnen worden verwijderd uit de invallende configuratie. Een verwijdering voor een netwerkbron mislukt als een toepassingsbron ernaar verwijst. Wanneer de toepassing wordt verwijderd, wordt de netwerkbron verwijderd.

## <a name="next-steps"></a>Volgende stappen 
Lees het overzicht voor meer informatie over Service Fabric Mesh:
- [Service Fabric Mesh - overzicht](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png