---
title: Concepten-API Management
description: Meer informatie over hoe API Management Api's beschermt die worden uitgevoerd op virtuele machines van Azure VMware (AVS) (Vm's)
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 62112bf3c0bf551232e09e5910e3eaae228dc202
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85306890"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-avs-based-vms"></a>API Management voor het publiceren en beveiligen van Api's die worden uitgevoerd op op AVS gebaseerde Vm's

Met Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) kunnen ontwikkel aars en DevOps teams veilig publiceren naar interne of externe consumenten.

Hoewel deze in verschillende Sku's worden aangeboden, bieden Azure Virtual Network-integratie alleen gebruik van de ontwikkelaar en Premium-Sku's voor het publiceren van Api's die worden uitgevoerd op de Azure VMware-oplossing (AVS)-workloads. Met deze twee Sku's wordt de verbinding tussen API Management service en de back-end veilig ingeschakeld. De SDK voor ontwikkel aars is bedoeld voor ontwikkelings-en test doeleinden terwijl de Premium-SKU voor productie-implementaties is.

Voor back-end-services die worden uitgevoerd op de virtuele AVS-machines (Vm's), is de configuratie in API Management standaard hetzelfde als on-premises back-upservices. Voor interne en externe implementaties API Management de virtuele IP (VIP) van de load balancer configureren als het back-end-eind punt wanneer de back-end-server achter een NSX-Load Balancer op de AVS-zijde wordt geplaatst.

## <a name="external-deployment"></a>Externe implementatie

Een externe implementatie publiceert Api's die worden gebruikt door externe gebruikers met behulp van een openbaar eind punt. Ontwikkel aars en DevOps-technici kunnen Api's beheren via Azure Portal of Power shell en de API Management ontwikkelaars Portal.

Het externe implementatie diagram toont het hele proces en de betrokken actoren (bovenaan weer gegeven). De Actors zijn:

- **Beheerder (s):** Vertegenwoordigt het team van de beheerder of het DevOps, dat AVS beheert via de Azure Portal en automatiserings mechanismen zoals Power shell of Azure DevOps.

- **Gebruikers:**  Vertegenwoordigt de consumenten van de beschik bare Api's en vertegenwoordigen zowel gebruikers als services die de Api's gebruiken.

De verkeers stroom loopt API Management exemplaar, waarmee de back-end-services worden abstract, die zijn aangesloten op het virtuele hub-netwerk. De ExpressRoute-gateway stuurt het verkeer naar ExpressRoute Global Reach kanaal en bereikt een NSX Load Balancer het binnenkomende verkeer naar de verschillende exemplaren van de back-end-services te distribueren.

API Management heeft een open bare Azure-API en wordt het activeren van de Azure DDOS Protection-service aanbevolen. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Externe implementatie-API Management voor AVS":::


## <a name="internal-deployment"></a>Interne implementatie

Een interne implementatie publiceert Api's die worden gebruikt door interne gebruikers of systemen. DevOps-team en API-Ontwikkel aars gebruiken dezelfde beheer hulpprogramma's en ontwikkelaars portal als in de externe implementatie.

Interne implementaties kunnen Azure-toepassing gateway zijn om een openbaar en beveiligd eind punt te maken voor de API met [behulp](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) van de mogelijkheden en het maken van een hybride implementatie die verschillende scenario's mogelijk maakt.  De API maakt gebruik van de mogelijkheden en creëert een hybride implementatie die verschillende scenario's mogelijk maakt.

* Gebruik dezelfde API Management resource voor verbruik door zowel interne als externe consumenten.

* Beschikken over een enkele API Management resource met een subset van Api's die zijn gedefinieerd en beschikbaar zijn voor externe consumenten.

* Bieden een eenvoudige manier om de toegang tot API Management van het open bare Internet te wijzigen in en uit te scha kelen.

In het onderstaande implementatie diagram worden gebruikers weer gegeven die intern of extern kunnen zijn, waarbij elk type toegang tot dezelfde of verschillende Api's heeft.

In een interne implementatie worden Api's blootgesteld aan hetzelfde API Management-exemplaar. Application Gateway wordt voor API Management geïmplementeerd met de functionaliteit van Azure Web Application firewall (WAF) geactiveerd en een set HTTP-listener (s) en regels waarmee het verkeer kan worden gefilterd, waardoor slechts een subset van de backend-services wordt weer gegeven die op AVS worden uitgevoerd.

* Intern verkeer wordt doorgestuurd via de ExpressRoute-gateway naar Azure Firewall en vervolgens API Management als er verkeers regels tot stand worden gebracht of rechtstreeks aan API Management.  

* Extern verkeer voert Azure in via Application Gateway, dat gebruikmaakt van de externe beveiligingslaag voor API Management.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Interne implementatie-API Management voor AVS":::