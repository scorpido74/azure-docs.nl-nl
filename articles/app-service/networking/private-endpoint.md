---
title: Privé verbinden met een web-app met behulp van een persoonlijk Azure-eind punt
description: Privé verbinden met een web-app met behulp van een persoonlijk Azure-eind punt
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ad7e04d611129766fe9fb72285fe35ccfbb17626
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136669"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Privé-eind punten gebruiken voor Azure-web-app (preview-versie)

U kunt een privé-eind punt voor uw Azure-web-app gebruiken om clients die zich in uw particuliere netwerk bevinden, veilig toegang te geven tot de app via een persoonlijke koppeling. Het persoonlijke eind punt gebruikt een IP-adres uit uw Azure VNet-adres ruimte. Netwerk verkeer tussen de client op uw particuliere netwerk en de web-app gaat over op het Vnet en een privé koppeling in het micro soft backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt voor komen.

Als u een persoonlijk eind punt gebruikt voor uw web-app, kunt u het volgende doen:

- Uw web-app beveiligen door het service-eind punt te configureren, open bare bloot stelling te elimineren
- Maak veilig verbinding met de web-app vanuit on-premises netwerken die verbinding maken met het Vnet met behulp van een VPN-of ExpressRoute privé-peering.

Als u alleen een beveiligde verbinding tussen uw Vnet en uw web-app nodig hebt, is service-eind punt de eenvoudigste oplossing. Als u de web-app ook van on-premises moet bereiken via een Azure-gateway, is een regionaal peered Vnet of een globaal peered Vnet, het persoonlijke eind punt is de oplossing.  

Voor meer informatie over [service-eind punten][serviceendpoint]

## <a name="conceptual-overview"></a>Conceptueel overzicht

Een persoonlijk eind punt is een speciale netwerk interface (NIC) voor uw Azure-web-app in uw subnet in uw Virtual Network (Vnet).
Wanneer u een persoonlijk eind punt voor uw web-app maakt, biedt het een beveiligde verbinding tussen clients in uw particuliere netwerk en uw web-app. Het persoonlijke eind punt krijgt een IP-adres uit het IP-adres bereik van uw Vnet.
De verbinding tussen het persoonlijke eind punt en de web-app maakt gebruik van een beveiligde [persoonlijke koppeling][privatelink]. Privé-eind punt wordt alleen gebruikt voor binnenkomende stromen naar uw web-app. Uitgaande stromen gebruiken dit persoonlijke eind punt niet, maar u kunt via de [Vnet-integratie functie][vnetintegrationfeature]uitgaande stromen naar uw netwerk injecteren in een ander subnet.

Het subnet waar u het persoonlijke eind punt aansluit, kan andere resources bevatten, u hebt geen toegewezen leeg subnet nodig.
U kunt een persoonlijk eind punt implementeren in een andere regio dan de web-app. 

> [!Note]
>De Vnet-integratie functie kan niet hetzelfde subnet gebruiken dan het persoonlijke eind punt. Dit is een beperking van de Vnet-integratie functie

Vanuit het beveiligings perspectief:

- Wanneer u service-eind punten voor uw web-app inschakelt, schakelt u alle open bare toegang uit
- U kunt meerdere privé-eind punten inschakelen in andere Vnets en subnetten, waaronder Vnets in andere regio's
- Het IP-adres van de NIC van het persoonlijke eind punt moet dynamisch zijn, maar blijft hetzelfde totdat u het persoonlijke eind punt verwijdert
- Aan de NIC van het persoonlijke eind punt kan geen NSG worden gekoppeld
- In het subnet waarop het persoonlijke eind punt wordt gehost, kan een NSG zijn gekoppeld, maar u moet het afdwingen van het netwerk beleid voor het privé-eind punt uitschakelen Zie [dit artikel][disablesecuritype]. Als gevolg hiervan kunt u niet filteren op alle NSG die toegang hebben tot uw persoonlijke eind punt
- Wanneer u een persoonlijk eind punt op uw web-app inschakelt, wordt de configuratie van de [toegangs beperkingen][accessrestrictions] van de web-app niet geëvalueerd.
- U kunt het risico op gegevens exfiltration van het vnet verminderen door alle NSG-regels te verwijderen waarbij het doel tag internet of Azure-Services is. Als u echter een Web App Service-eind punt in uw subnet toevoegt, krijgt u een web-app te bereiken die wordt gehost in hetzelfde stempel en beschikbaar te stellen aan Internet.

Het persoonlijke eind punt voor web-app is beschikbaar voor laag PremiumV2 en is geïsoleerd met een externe ASE.

In de web-http-logboeken van uw web-app vindt u het bron-IP-adres van de client. We hebben het TCP-proxy protocol geïmplementeerd en tot en met de Web-App de IP-eigenschap van de client doorgestuurd. Raadpleeg [dit artikel][tcpproxy] voor meer informatie.

![Globaal overzicht][1]


## <a name="dns"></a>DNS

Omdat deze functie in preview is, wordt de DNS-vermelding niet gewijzigd tijdens de preview-versie. U moet de DNS-vermelding in uw privé-DNS-server of Azure DNS privé zone beheren. Als u een aangepaste DNS-naam moet gebruiken, moet u de aangepaste naam toevoegen in uw web-app. Tijdens de preview moet de aangepaste naam worden gevalideerd, zoals een aangepaste naam, met behulp van een open bare DNS-omzetting. [technische Naslag informatie over aangepaste DNS-validatie][dnsvalidation]

## <a name="pricing"></a>Prijzen

Zie [prijzen voor persoonlijke Azure-koppelingen][pricing]voor prijs informatie.

## <a name="limitations"></a>Beperkingen

De functie voor persoonlijke koppelingen en het persoonlijke eind punt worden regel matig verbeterd. Raadpleeg [dit artikel][pllimitations] voor actuele informatie over beperkingen.

## <a name="next-steps"></a>Volgende stappen

Voor het implementeren van een persoonlijk eind punt voor uw web-app via de portal Zie [hoe u een persoonlijke verbinding maakt met een web-app][howtoguide]


<!--Image references-->
[1]: ./media/private-endpoint/schemaglobaloverview.png

<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
