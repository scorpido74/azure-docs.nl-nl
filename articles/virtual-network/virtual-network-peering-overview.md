---
title: Peering van virtuele Azure-netwerken
titlesuffix: Azure Virtual Network
description: Meer informatie over peering in virtuele netwerken in Azure.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: anavin
ms.openlocfilehash: 5fb54e812e72b9393ffdf632085d0f32ab8b1988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279544"
---
# <a name="virtual-network-peering"></a>Peering op virtueel netwerk

Met virtuele netwerkpeering u netwerken naadloos verbinden in [Azure Virtual Network.](virtual-networks-overview.md) De virtuele netwerken worden weergegeven als één voor connectiviteitsdoeleinden. Het verkeer tussen virtuele machines maakt gebruik van de Microsoft-backbone-infrastructuur. Net als verkeer tussen virtuele machines in hetzelfde netwerk, wordt het verkeer alleen via het *privénetwerk* van Microsoft doorgestuurd.

Azure ondersteunt de volgende typen peering:

* Virtueel netwerkpeeren: sluit virtuele netwerken binnen dezelfde Azure-regio.
* Wereldwijd virtueel netwerkpeeren: virtuele netwerken verbinden in Azure-regio's.

Enkele voordelen van het gebruik van peering van virtuele netwerken - lokaal of globaal - zijn:

* Een verbinding met lage latentie en hoge bandbreedte tussen resources in verschillende virtuele netwerken.
* De mogelijkheid voor resources in één virtueel netwerk om te communiceren met bronnen in een ander virtueel netwerk.
* De mogelijkheid om gegevens over te dragen tussen virtuele netwerken tussen Azure-abonnementen, Azure Active Directory-tenants, implementatiemodellen en Azure-regio's.
* De mogelijkheid om virtuele netwerken te peeren die zijn gemaakt via Azure Resource Manager.
* De mogelijkheid om een virtueel netwerk dat via Resource Manager is gemaakt, te peeren met een netwerk dat is gemaakt via het klassieke implementatiemodel. Zie [Over Azure-implementatiemodellen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over Azure-implementatiemodellen.
* Geen downtime tot bronnen in een virtueel netwerk bij het maken van de peering of nadat de peering is gemaakt.

Netwerkverkeer tussen gekoppelde virtuele netwerken is privé. Verkeer tussen de virtuele netwerken wordt opgeslagen in het Microsoft-backbone-netwerk. Er zijn geen openbare internetverbinding, gateways of versleuteling vereist in de communicatie tussen de virtuele netwerken.

## <a name="connectivity"></a>Connectiviteit

Voor peered virtuele netwerken kunnen bronnen in een virtueel netwerk rechtstreeks verbinding maken met bronnen in het peered virtuele netwerk.

De netwerklatentie tussen virtuele machines in gekoppelde virtuele netwerken in dezelfde regio is gelijk aan de latentie binnen één virtueel netwerk. De netwerkdoorvoer is gebaseerd op de bandbreedte die is toegestaan voor de virtuele machine, evenredig aan de grootte. Er gelden verder geen extra beperkingen voor bandbreedte binnen de peering.

Het verkeer tussen virtuele machines in gekoppelde virtuele netwerken wordt rechtstreeks doorgestuurd via de Microsoft-backbone-infrastructuur, niet via een gateway of via het openbare internet.

U netwerkbeveiligingsgroepen toepassen in een virtueel netwerk om de toegang tot andere virtuele netwerken of subnetten te blokkeren.
Wanneer u virtuele netwerkpeering configureert, opent of sluit u de regels voor netwerkbeveiliging tussen de virtuele netwerken. Als u volledige verbinding tussen virtuele netwerken met peered opent, u netwerkbeveiligingsgroepen toepassen om specifieke toegang te blokkeren of te weigeren. Volledige connectiviteit is de standaardoptie. Zie [Beveiligingsgroepen](security-overview.md)voor meer informatie over netwerkbeveiligingsgroepen.

## <a name="service-chaining"></a>Servicechaining

Met servicechaining u verkeer van het ene virtuele netwerk naar een virtueel toestel of gateway in een peered-netwerk door gebruikers gedefinieerde routes leiden.

Als u servicechaining wilt inschakelen, configureert u door de gebruiker gedefinieerde routes die naar virtuele machines in gepeerde virtuele netwerken als het volgende hop-IP-adres wijzen. *next hop* Door de gebruiker gedefinieerde routes kunnen ook wijzen op virtuele netwerkgateways om servicechaining mogelijk te maken.

U hub-and-spoke-netwerken implementeren, waarbij het virtuele netwerk van de hub infrastructuurcomponenten host, zoals een virtueel netwerktoestel of VPN-gateway. Alle virtuele spoke-netwerken kunnen vervolgens worden gekoppeld aan het virtuele hub-netwerk. Verkeer stroomt door netwerk virtuele apparaten of VPN-gateways in de hub virtuele netwerk.

Met peering van virtuele netwerken kan de volgende hop op de door de gebruiker gedefinieerde route het IP-adres zijn van een virtuele machine in het gekoppelde virtuele netwerk of VPN-gateway. U niet routeren tussen virtuele netwerken met een door de gebruiker gedefinieerde route die een Azure ExpressRoute-gateway opgeeft als het volgende hoptype. Zie [Door de gebruiker gedefinieerde routes](virtual-networks-udr-overview.md#user-defined) voor meer informatie over door de gebruiker gedefinieerde routes. Zie [Hub-spoke network topology in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het maken van een hub en spoke network topology.

## <a name="gateways-and-on-premises-connectivity"></a>Gateways en on-premises connectiviteit

Elk virtueel netwerk, inclusief een peered virtueel netwerk, kan zijn eigen gateway hebben. Een virtueel netwerk kan de gateway gebruiken om verbinding te maken met een on-premises netwerk. U ook [virtuele netwerk-naar-virtuele netwerkverbindingen](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) configureren met behulp van gateways, zelfs voor peered virtuele netwerken.

Wanneer u beide opties voor interconnectiviteit van virtuele netwerken configureert, stroomt het verkeer tussen de virtuele netwerken door de peeringconfiguratie. Het verkeer maakt gebruik van de Azure-backbone.

U de gateway in het virtuele netwerk met peered ook configureren als doorvoerpunt naar een on-premises netwerk. In dit geval kan het virtuele netwerk dat een externe gateway gebruikt, geen eigen gateway hebben. Een virtueel netwerk heeft slechts één gateway. De gateway is een lokale of externe gateway in het virtuele netwerk met peered, zoals in het volgende diagram wordt weergegeven:

![doorvoer bij peering van virtuele netwerken](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Zowel virtuele netwerk peering en wereldwijde virtuele netwerk peering ondersteuning gateway transit.

Gateway-doorvoer tussen virtuele netwerken die via verschillende implementatiemodellen zijn gemaakt, wordt ondersteund. De gateway moet zich in het virtuele netwerk in het Resource Manager-model bevinden. Zie voor meer informatie over het gebruik van een gateway voor de doorvoer, [Een VPN-gateway configureren voor de doorvoer in een virtueel netwerkpeering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Wanneer u virtuele netwerken peert die één Azure ExpressRoute-verbinding delen, gaat het verkeer tussen deze netwerken door de peering-relatie. Dat verkeer maakt gebruik van het Azure-backbone-netwerk. U kunt nog steeds in elk virtueel netwerk lokale gateways gebruiken om verbinding te maken met het on-premises circuit. Anders u een gedeelde gateway gebruiken en doorvoer configureren voor on-premises connectiviteit.

## <a name="troubleshoot"></a>Problemen oplossen

Als u wilt bevestigen dat virtuele netwerken zijn peered, u effectieve routes controleren. Controleer routes voor een netwerkinterface in een subnet in een virtueel netwerk. Als een peering op een virtueel netwerk bestaat, hebben alle subnets binnen het virtuele netwerk routes met het volgende hoptype *VNet-peering* voor elke adresruimte in elk gekoppeld virtueel netwerk. Zie [Een probleem met de routering van een virtuele machine diagnosticeren](diagnose-network-routing-problem.md)voor meer informatie.

U ook problemen oplossen met de connectiviteit met een virtuele machine in een virtueel netwerk met peered met Azure Network Watcher. Met een connectiviteitscontrole u zien hoe het verkeer wordt doorgestuurd van de netwerkinterface van een virtuele computer naar de netwerkinterface van een virtuele bestemming. Zie [Problemen met verbindingen met Azure Network Watcher oplossen met azure network watcher met behulp van de Azure-portal](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine)voor meer informatie.

U ook proberen [problemen met virtuele netwerkpeering oplossen.](virtual-network-troubleshoot-peering-issues.md)

## <a name="constraints-for-peered-virtual-networks"></a>Beperkingen voor virtuele netwerken met peered<a name="requirements-and-constraints"></a>

De volgende beperkingen zijn alleen van toepassing wanneer virtuele netwerken wereldwijd zijn gekoppeld:

* Bronnen in één virtueel netwerk kunnen niet communiceren met het front-end IP-adres van een Basic Internal Load Balancer (ILB) in een wereldwijd getouwerd virtueel netwerk.
* Sommige services die een Basic load balancer gebruiken, werken niet via wereldwijde virtuele netwerkpeering. Zie [Wat zijn de beperkingen met betrekking tot Global VNet Peering and Load Balancers voor](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)meer informatie? .

Zie [Vereisten en beperkingen voor](virtual-network-manage-peering.md#requirements-and-constraints)meer informatie. Zie [Netwerklimieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie over het ondersteunde aantal peerings.

## <a name="permissions"></a>Machtigingen

Zie [Machtigingen](virtual-network-manage-peering.md#permissions)voor meer informatie over machtigingen die nodig zijn voor het maken van een virtueel netwerk peering.

## <a name="pricing"></a>Prijzen

Er is een nominale vergoeding voor binnendringen en uitgaande verkeer dat gebruik maakt van een virtuele netwerk peering verbinding. Zie [Prijzen van virtuele netwerken](https://azure.microsoft.com/pricing/details/virtual-network)voor meer informatie.

Gateway Transit is een peering-eigenschap waarmee een virtueel netwerk een VPN/ExpressRoute-gateway kan gebruiken in een peered virtueel netwerk. Gateway transit werkt voor zowel cross premises als network-to-network connectiviteit. Verkeer naar de gateway (binnendringen of uitgang) in het peered virtuele netwerk maakt virtuele netwerk peering kosten op de spaak VNet (of niet-gateway VNet). Zie [VPN Gateway-prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway/) voor VPN-gatewaykosten en ExpressRoute Gateway-prijzen voor ExpressRoute-gatewaykosten voor meer informatie.

>[!NOTE]
> In een eerdere versie van dit document stond dat kosten voor virtuele netwerkpeering niet van toepassing zouden zijn op de spaakvnet (of niet-gateway VNet) met Gateway Transit. Het weerspiegelt nu nauwkeurige prijzen per de prijspagina.

## <a name="next-steps"></a>Volgende stappen

* U een peering maken tussen twee virtuele netwerken. De netwerken kunnen tot hetzelfde abonnement, verschillende implementatiemodellen in hetzelfde abonnement of verschillende abonnementen behoren. Volg een zelfstudie voor een van de volgende scenario's:

    |Azure-implementatiemodel             | Abonnement  |
    |---------                          |---------|
    |Beide in Resource Manager              |[Hetzelfde](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Verschillend](create-peering-different-subscriptions.md)|
    |Eén in Resource Manager, één klassiek  |[Hetzelfde](create-peering-different-deployment-models.md)|
    |                                   |[Verschillend](create-peering-different-deployment-models-subscriptions.md)|

* Zie [Hub-spoke network topology in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het maken van een hub en spoke network topology.
* Zie [Een virtueel netwerk peering maken, wijzigen of verwijderen](virtual-network-manage-peering.md)voor meer informatie over alle instellingen voor virtuele netwerkpeering.
* Zie [VNet Peering](virtual-networks-faq.md#vnet-peering)voor antwoorden op veelvoorkomende virtuele netwerkpeering- en wereldwijde virtuele netwerkpeering-vragen.
