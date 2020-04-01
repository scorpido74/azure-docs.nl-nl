---
title: Inleiding tot ASE v1
description: Meer informatie over de functies V1-appserviceomgeving. Dit document is alleen bedoeld voor klanten die de verouderde v1 ASE gebruiken.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: cd3881617227430488f8dd3f2f3d24072b24b8ce
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478725"
---
# <a name="introduction-to-app-service-environment-v1"></a>Inleiding tot App Service Environment v1

> [!NOTE]
> Dit artikel gaat over de App Service Environment v1.  Er is een nieuwere versie van de App Service-omgeving die gemakkelijker te gebruiken is en draait op een krachtigere infrastructuur. Voor meer informatie over de nieuwe versie start u met de [inleiding tot de app-serviceomgeving.](intro.md)

## <a name="overview"></a>Overzicht

Een App-serviceomgeving [Premium][PremiumTier] is een Premium-serviceplanoptie van [Azure App Service](../overview.md) die een volledig geïsoleerde en speciale omgeving biedt voor het veilig uitvoeren van Azure App Service-apps op grote schaal, waaronder Web Apps, mobiele apps en API-apps.  

App-serviceomgevingen zijn ideaal voor toepassingsworkloads waarvoor:

* Zeer hoog schaal
* Isolatie en beveiligde netwerktoegang

Klanten kunnen meerdere App-serviceomgevingen maken binnen één Azure-regio en in meerdere Azure-regio's.  Dit maakt App Service Omgevingen ideaal voor horizontaal schalen state-less applicatie lagen ter ondersteuning van hoge RPS-workloads.

App-serviceomgevingen zijn geïsoleerd voor het uitvoeren van slechts één klant toepassingen, en worden altijd geïmplementeerd in een virtueel netwerk.  Klanten hebben een fijnmazige controle over zowel inkomend als uitgaand netwerkverkeer voor toepassingen en toepassingen kunnen snelle beveiligde verbindingen tot stand brengen via virtuele netwerken met on-premises bedrijfsresources.

Zie de [AzureCon Deep Dive][AzureConDeepDive] on App-serviceomgevingen voor een overzicht van hoe app-serviceomgevingen op grote schaal en beveiligde netwerktoegang mogelijk maken!

Voor een diepe duik op horizontaal schalen met meerdere App-serviceomgevingen zie het artikel over het instellen van een [geo-gedistribueerde app-voetafdruk.][GeodistributedAppFootprint]

Zie het artikel over het implementeren van een [gelaagde beveiligingsarchitectuur](app-service-app-service-environment-layered-security.md) met App Service-omgevingen om te zien hoe de beveiligingsarchitectuur in de AzureCon Deep Dive is geconfigureerd.

Apps die worden uitgevoerd op App-serviceomgevingen, kunnen hun toegang laten worden geblokkeerd door upstream-apparaten, zoals firewalls voor webtoepassingen (WAF).  Het artikel over [het configureren van een WAF for App Service Environments](app-service-app-service-environment-web-application-firewall.md) behandelt dit scenario.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Speciale rekenbronnen

Alle compute resources in een App Service-omgeving zijn uitsluitend gewijd aan één abonnement en een App Service-omgeving kan worden geconfigureerd met maximaal vijftig (50) compute resources voor exclusief gebruik door één toepassing.

Een App Service-omgeving bestaat uit een front-end compute resource pool, evenals een tot drie werknemer compute resource pools.

De front-end pool bevat compute resources die verantwoordelijk zijn voor TLS-beëindiging en automatische taakverdeling van app-aanvragen binnen een App Service-omgeving.

Elke groep werknemers bevat rekenresources die zijn toegewezen aan [App-serviceplannen][AppServicePlan], die op hun beurt een of meer Azure App Service-apps bevatten.  Aangezien er maximaal drie verschillende werknemersgroepen in een App-serviceomgeving kunnen zijn, hebt u de flexibiliteit om verschillende rekenresources voor elke werknemersgroep te kiezen.  

Hiermee u bijvoorbeeld één werknemersgroep maken met minder krachtige rekenbronnen voor App-serviceplannen die zijn bedoeld voor ontwikkeling of test-apps.  Een tweede (of zelfs derde) werknemersgroep kan krachtigere rekenbronnen gebruiken die zijn bedoeld voor app-serviceplannen waarop productie-apps worden uitgevoerd.

Zie [Een app-serviceomgeving configureren][HowToConfigureanAppServiceEnvironment]voor meer informatie over de hoeveelheid rekenresources die beschikbaar zijn voor de front-end- en werknemersgroepen.  

Raadpleeg de pagina [Prijzen voor app-serviceprijzen][AppServicePricing] en bekijk de beschikbare opties voor App-serviceomgevingen in de prijscategorie Premium voor meer informatie over de beschikbare compute resource-formaten die worden ondersteund in een App-serviceomgeving.

## <a name="virtual-network-support"></a>Ondersteuning voor virtueel netwerk

Er kan **een** App-serviceomgeving worden gemaakt in een virtueel azure resourcemanager-netwerk **of** een virtueel netwerk voor een klassiek implementatiemodel[(meer informatie over virtuele netwerken).][MoreInfoOnVirtualNetworks]  Aangezien een App Service-omgeving altijd bestaat in een virtueel netwerk en meer precies binnen een subnet van een virtueel netwerk, u de beveiligingsfuncties van virtuele netwerken gebruiken om zowel inkomende als uitgaande netwerkcommunicatie te beheren.  

Een App-serviceomgeving kan internet zijn met een openbaar IP-adres of intern met alleen een ILB-adres (Internal Load Balancer) van Azure.

U [netwerkbeveiligingsgroepen][NetworkSecurityGroups] gebruiken om inkomende netwerkcommunicatie te beperken tot het subnet waar een App Service-omgeving zich bevindt.  Hiermee u apps uitvoeren achter upstream-apparaten en -services, zoals firewalls voor webapplicaties en netwerkSaaS-providers.

Apps hebben ook vaak toegang nodig tot bedrijfsresources zoals interne databases en webservices.  Een gemeenschappelijke aanpak is om deze eindpunten alleen beschikbaar te maken voor intern netwerkverkeer dat binnen een virtueel Azure-netwerk stroomt.  Zodra een App-serviceomgeving is verbonden met hetzelfde virtuele netwerk als de interne services, kunnen apps die in de omgeving worden uitgevoerd, er toegang toe krijgen, inclusief eindpunten die bereikbaar zijn via [site-to-site-][SiteToSite] en [Azure ExpressRoute-verbindingen.][ExpressRoute]

Raadpleeg de volgende artikelen over [netwerkarchitectuur,][NetworkArchitectureOverview] [het beheren van binnenkomend verkeer][ControllingInboundTraffic]en veilig verbinding maken met [backends][SecurelyConnectingToBackends]voor meer informatie over hoe app-serviceomgevingen werken met virtuele netwerken en on-premises netwerken . 

## <a name="getting-started"></a>Aan de slag

Zie [Een app-serviceomgeving maken][HowToCreateAnAppServiceEnvironment] om aan de slag te gaan met app-serviceomgevingen

Zie het artikel [Netwerkarchitectuuroverzicht][NetworkArchitectureOverview] voor een overzicht van de app-serviceomgeving.

Zie het volgende artikel over [Express Route- en App-serviceomgevingen][NetworkConfigDetailsForExpressRoute]voor meer informatie over het gebruik van een App-serviceomgeving met ExpressRoute.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: https://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->