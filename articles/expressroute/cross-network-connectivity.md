---
title: Connectiviteit tussen meerdere netwerken van Azure
description: Op deze pagina wordt een toepassings scenario beschreven voor cross-netwerk connectiviteit en oplossingen op basis van Azure-netwerk functies.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644253"
---
# <a name="cross-network-connectivity"></a>Onderlinge netwerkconnectiviteit

Fabrikam Inc. heeft een grote fysieke aanwezigheid en Azure-implementatie in VS-Oost. Fabrikam heeft back-end-connectiviteit tussen de on-premises en Azure-implementaties via ExpressRoute. Op dezelfde manier heeft Contoso Ltd. een aanwezigheids-en Azure-implementatie in VS West. Contoso heeft een back-end-verbinding tussen de on-premises en Azure-implementaties via ExpressRoute.  

Fabrikam Inc. verwerft Contoso Ltd. Na de fusie wil Fabrikam de netwerken verbinden. In de volgende afbeelding ziet u het scenario:

 [![1]][1]

De onderbroken pijlen in het midden van de bovenstaande afbeelding geven de gewenste netwerk verbindingen aan. Met name zijn er drie typen cross-Connections gewenst: 1) Fabrikam en contoso VNets Cross Connect, 2) Cross-premises lokale en VNets cross connects (dat wil zeggen fabrikam on-premises netwerk verbinden met Contoso VNet en contoso verbinding maken) on-premises netwerk naar fabrikam VNet) en 3) Fabrikam en on-premises netwerk Kruis verbinding van contoso. 

In de volgende tabel ziet u de route tabel van de persoonlijke peering van de ExpressRoute van Contoso Ltd. vóór de fusie.

[![2]][2]

De volgende tabel bevat de efficiënte routes van een virtuele machine in het contoso-abonnement, vóór de fusie. De virtuele machine op het VNet is in de tabel op de hoogte van de VNet-adres ruimte en het on-premises netwerk van contoso, onafhankelijk van de standaard waarden. 

[![4]][4]

De volgende tabel toont de route tabel van de privé-peering van de ExpressRoute van Fabrikam Inc. vóór de fusie.

[![3]][3]

De volgende tabel toont de efficiënte routes van een virtuele machine in het fabrikam-abonnement, vóór de fusie. De virtuele machine op het VNet is in de tabel op de hoogte van de VNet-adres ruimte en het on-premises netwerk van Fabrikam, onafhankelijk van de standaard waarden.

[![5]][5]

In dit artikel gaan we stapsgewijs door en bespreken hoe u de gewenste cross-verbindingen kunt maken met behulp van de volgende Azure-netwerk functies:

* [Peering op virtueel netwerk][Virtual network peering] 
* [Verbinding met virtuele netwerk ExpressRoute][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>VNets Kruis verbinding maken

Virtual Network-peering (VNet-peering) biedt de meest optimale en beste netwerk prestaties wanneer u twee virtuele netwerken verbindt. VNet-peering ondersteunt peering van twee VNets in dezelfde Azure-regio (ook wel VNet-peering genoemd) en in twee verschillende Azure-regio's (ook wel wereld wijde VNet-peering genoemd). 

Laten we globaal VNet-peering configureren tussen de VNets in contoso en fabrikam Azure-abonnementen. Zie het artikel [peering van een virtueel netwerk maken][Configure VNet peering] voor informatie over het maken van de peering van het virtuele netwerk tussen twee virtuele netwerken.

In de volgende afbeelding ziet u de netwerk architectuur na het configureren van globale VNet-peering.

[![6]][6]

De volgende tabel bevat de routes die bekend zijn bij de VM van het contoso-abonnement. Let op de laatste vermelding van de tabel. Dit item is het resultaat van het cross verbinden van de virtuele netwerken.

[![7]][7]

In de volgende tabel ziet u de routes die bekend zijn bij de werk-VM van Fabrikam-abonnement. Let op de laatste vermelding van de tabel. Dit item is het resultaat van het cross verbinden van de virtuele netwerken.

[![8]][8]

VNet-peering koppelt rechtstreeks twee virtuele netwerken (zie er is geen volgende hop voor *VNetGlobalPeering* -vermelding in de bovenstaande twee tabellen)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Verbinding maken tussen VNets en de on-premises netwerken

We kunnen een ExpressRoute-circuit verbinden met meerdere virtuele netwerken. Zie de [abonnements-en service limieten][Subscription limits] voor het maximum aantal virtuele netwerken dat kan worden verbonden met een ExpressRoute-circuit. 

We gaan verbinding maken met het ExpressRoute-circuit van Fabrikam met het VNet-abonnement van Contoso en een soortgelijk ExpressRoute-circuit van Contoso naar het VNet-abonnement van Fabrikam om tussen virtuele netwerken en de on-premises netwerken over te scha kelen. Als u een virtueel netwerk wilt verbinden met een ExpressRoute-circuit in een ander abonnement, moeten we een autorisatie maken en gebruiken.  Zie het artikel: [een virtueel netwerk verbinden met een ExpressRoute-circuit][Connect-ER-VNet].

In de volgende afbeelding ziet u de netwerk architectuur na het configureren van de ExpressRoute-Kruis verbinding met de virtuele netwerken.

[![9]][9]

In de volgende tabel ziet u de route tabel van de persoonlijke peering van de ExpressRoute van Contoso Ltd., nadat u virtuele netwerken met elkaar hebt verbonden met de on-premises netwerken via ExpressRoute. Zie dat de route tabel routes bevat die deel uitmaken van de virtuele netwerken.

[![10]][10]

In de volgende tabel ziet u de route tabel van de persoonlijke peering van de ExpressRoute van Fabrikam Inc., nadat u virtuele netwerken met elkaar hebt verbonden met de on-premises netwerken via ExpressRoute. Zie dat de route tabel routes bevat die deel uitmaken van de virtuele netwerken.

[![9]][11]

De volgende tabel bevat de routes die bekend zijn bij de VM van het contoso-abonnement. Let op de gateway vermeldingen van het *virtuele netwerk* van de tabel. De virtuele machine ziet routes voor de on-premises netwerken.

[![12]][12]

In de volgende tabel ziet u de routes die bekend zijn bij de werk-VM van Fabrikam-abonnement. Let op de gateway vermeldingen van het *virtuele netwerk* van de tabel. De virtuele machine ziet routes voor de on-premises netwerken.

[![13]][13]

>[!NOTE]
>In de abonnementen Fabrikam en/of Contoso kunt u ook spoke VNets naar het respectieve hub VNet (een hub-en spoke-ontwerp wordt niet geïllustreerd in de architectuur diagrammen in dit artikel). Door de Kruis verbindingen tussen de hub VNet-gateways naar ExpressRoute wordt ook communicatie tussen Oost-en West-hubs en spokes toegestaan.
>

## <a name="cross-connecting-on-premises-networks"></a>Cross-premises verbinding maken met netwerk

ExpressRoute Global Reach biedt connectiviteit tussen on-premises netwerken die zijn verbonden met verschillende ExpressRoute-circuits. Laten we Global Reach configureren tussen Contoso en fabrikam ExpressRoute-circuits. Omdat de ExpressRoute-circuits zich in verschillende abonnementen bevinden, moeten we een autorisatie maken en gebruiken. Zie [Configure ExpressRoute Global Reach][Configure Global Reach] article (Engelstalig) voor stapsgewijze instructies.

In de volgende afbeelding ziet u de netwerk architectuur na het configureren van Global Reach.

[![15]][14]

In de volgende tabel ziet u de route tabel van de persoonlijke peering van de ExpressRoute van Contoso Ltd., na het configureren van Global Reach. Zie dat de route tabel routes bevat die deel uitmaken van de on-premises netwerken. 

[![15]][15]

In de volgende tabel ziet u de route tabel van de persoonlijke peering van de ExpressRoute van Fabrikam Inc. na het configureren van Global Reach. Zie dat de route tabel routes bevat die deel uitmaken van de on-premises netwerken.

[![18]][16]

## <a name="next-steps"></a>Volgende stappen

Zie [Veelgestelde vragen over virtuele netwerken][VNet-FAQ]voor verdere vragen over VNet en vnet-peering. Zie [Veelgestelde vragen over ExpressRoute][ER-FAQ] voor meer vragen over de connectiviteit van ExpressRoute en het virtuele netwerk.

Global Reach wordt op basis van land/regio in een land/regio uitgerold. Zie [ExpressRoute Global Reach][Global Reach]als u wilt weten of Global Reach beschikbaar is in de gewenste landen/regio's.

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "het toepassings scenario"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Contoso ExpressRoute-route tabel vóór fusie"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "fabrikam ExpressRoute-route tabel vóór fusie"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Contoso VM-routes vóór fusie"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "routes voor de fabrikam-VM vóór fusie"
[6]: ./media/cross-network-connectivity/vnet-peering.png "de architectuur na VNet-peering"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Contoso-VM-routes na VNet-peering"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "fabrikam-VM-routes na VNet-peering"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "de architectuur na expressroutes waaraan-Kruis verbinding"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "Contoso ExpressRoute-route tabel na cross-verbinding maken met ExR en VNets"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "fabrikam ExpressRoute-route tabel na cross-verbinding maken met ExR en VNets"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "Contoso-VM-routes na Kruis verbinding maken met ExR en VNets"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "fabrikam-VM-routes na Kruis verbinding ExR en VNets"
[14]: ./media/cross-network-connectivity/globalreach.png "de architectuur na het configureren van Global Reach"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Contoso ExpressRoute-route tabel na Global Reach"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "fabrikam ExpressRoute-route tabel na Global Reach"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq