---
title: Azure Virtual WAN en werkt op afstand
description: Op deze pagina wordt beschreven hoe u Azure Virtual WAN gebruiken om werken op afstand mogelijk te maken vanwege de COVID-19-pandemie.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: article
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: ce212b5da90906966025674b58884d0e2f5bb064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337130"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Azure Virtual WAN en ondersteuning van extern werk

>[!NOTE]
>In dit artikel wordt beschreven hoe u Azure Virtual WAN, Azure, Microsoft-netwerk en het Azure-partnerecosysteem gebruiken om op afstand te werken en netwerkproblemen te beperken waarmee u wordt geconfronteerd vanwege de COVID-19-crisis.
>

Bent u versluiering om connectiviteit te bieden voor externe gebruikers?
Ziet u plotseling een behoefte om een golf van gebruikers te ondersteunen dan wat u had gepland?
Heeft u de gebruiker nodig om verbinding te maken vanuit huis en niet alleen toegang te krijgen tot de cloud, maar ook on-premises te bereiken?
Heeft u uw externe gebruikers nodig om bronnen achter een privé-WAN te kunnen bereiken?
Heeft u behoefte aan gebruikers om toegang te krijgen tot intra cloud resources zonder dat u verbinding tussen regio's hoeft in te stellen?
Omdat deze wereldwijde pandemie ongekende veranderingen om ons heen creëert, staat het Azure Virtual WAN-team voor u klaar om aan uw connectiviteitsbehoeften te voldoen.

Azure Virtual WAN is een netwerkservice die veel netwerk-, beveiligings- en routeringsfuncties samenbrengt om één operationele interface te bieden. Deze functionaliteiten omvatten Branch-connectiviteit (via connectiviteitsautomatisering van Virtual WAN Partner-apparaten zoals SD-WAN of VPN CPE), Site-to-site VPN-connectiviteit, VPN-connectiviteit (Point-to-site) van externe gebruikers, privé (ExpressRoute)-connectiviteit, Intra cloud connectiviteit (Transitieve connectiviteit voor virtuele netwerken), VPN ExpressRoute Interconnectivity, Routing, Azure firewall, Encryptie voor private connectiviteit etc. U hoeft niet al deze use cases te hebben om Virtual WAN te gebruiken. U aan de slag met slechts één use case en uw netwerk aanpassen terwijl het evolueert.

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan1.png)

Nu we het hebben over externe gebruikers, laten we kijken naar wat u nodig hebt om uw netwerk operationeel te krijgen:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Externe gebruikersconnectiviteit instellen

U verbinding maken met uw resources in Azure via een IPsec/IKE (IKEv2) of OpenVPN-verbinding. Voor dit type verbinding moet een VPN-client worden geconfigureerd voor de externe gebruiker. Deze client kan de [Azure VPN-client](https://go.microsoft.com/fwlink/?linkid=2117554) of OpenVPN-client zijn of een client die IKEv2 ondersteunt. Zie [Een point-to-site-verbinding maken](virtual-wan-point-to-site-portal.md)voor meer informatie.

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Connectiviteit van de externe gebruiker naar on-premises

Je hebt twee opties hier:

* Site-to-site-connectiviteit instellen met elk bestaand VPN-apparaat. Wanneer u het IPsec VPN-apparaat aansluit op de Virtual WAN-hub van Azure, is de interconnectiviteit tussen de Point-to-site User VPN (Remote user) en Site-to-site VPN automatisch. Zie [Een site-to-site-verbinding](virtual-wan-site-to-site-portal.md)maken met Virtual WAN voor meer informatie over het instellen van site-to-site VPN van uw on-premise VPN-apparaat naar Azure Virtual WAN.

* Sluit uw ExpressRoute-circuit aan op de Virtual WAN-hub. Het aansluiten van een ExpressRoute-circuit vereist het implementeren van een ExpressRoute-gateway in Virtual WAN. Zodra u er een hebt geïmplementeerd, is de interconnectiviteit tussen de Point-to-site User VPN en ExpressRoute-gebruiker automatisch. Zie [Een ExpressRoute-verbinding maken met Virtual WAN](virtual-wan-expressroute-portal.md)als u de Verbinding ExpressRoute wilt maken. U een bestaand ExpressRoute-circuit gebruiken om verbinding te maken met Azure Virtual WAN.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Bestaande basis Virtual WAN-klant

Basic Virtual WAN biedt alleen site-to-site VPN. Om externe gebruikers verbinding te laten maken, moet u de virtuele WAN upgraden naar Standard Virtual WAN. Zie [Een virtueel WAN-upgrade uitvoeren van een virtueel WAN van Basic naar Standard](upgrade-virtual-wan.md) voor stappen om een virtuele WAN te upgraden

## <a name="additional-information"></a><a name="other considerations"></a>Aanvullende informatie

Virtueel WAN ondersteunt één hub per regio/locatie. Zie het artikel [Virtual WAN-partners en locaties](virtual-wan-locations-partners.md) voor locatiegegevens. Elke hub ondersteunt maximaal 10.000 externe gebruikersverbindingen, 1.000 branch-verbindingen, vier ExpressRoute-circuits en maximaal 500 virtuele netwerkverbindingen. Als u de externe gebruikers opschaalt, als u vragen heeft, aarzel azurevirtualwan@microsoft.comdan niet om hulp te zoeken door een e-mail te sturen naar . Als u technische ondersteuning nodig hebt, moet u een ondersteuningsticket openen vanaf de Azure-portal en is er onderweg hulp onderweg.

## <a name="faq"></a><a name="faq"></a>Veelgestelde vragen

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [Virtueel WAN-overzicht](virtual-wan-about.md) voor meer informatie over Virtual WAN