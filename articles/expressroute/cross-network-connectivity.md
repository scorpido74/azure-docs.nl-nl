---
title: Azure-connectiviteit met verschillende netwerken
description: Op deze pagina wordt een toepassingsscenario beschreven voor cross-network-connectiviteit en -oplossingen op basis van Azure-netwerkfuncties.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644253"
---
# <a name="cross-network-connectivity"></a>Onderlinge netwerkconnectiviteit

Fabrikam Inc. heeft een grote fysieke aanwezigheid en Azure-implementatie in Oost-VS. Fabrikam heeft back-endconnectiviteit tussen de on-premises en Azure-implementaties via ExpressRoute. Op dezelfde manier heeft Contoso Ltd. een aanwezigheid en Azure-implementatie in West-VS. Contoso heeft back-endconnectiviteit tussen de on-premises en Azure-implementaties via ExpressRoute.  

Fabrikam Inc. neemt Contoso Ltd. over. Na de fusie wil Fabrikam de netwerken met elkaar verbinden. Het volgende cijfer illustreert het scenario:

 [![1]][1 1.]

De gestippelde pijlen in het midden van de bovenstaande figuur geven de gewenste netwerkverbindingen aan. Concreet zijn er drie soorten cross-verbindingen gewenst: 1) Fabrikam en Contoso VNets cross connect, 2) Cross regional on-premises en VNets cross connects (dat wil zeggen, het aansluiten van Fabrikam on-premises netwerk met Contoso VNet en het aansluiten van Contoso on-premises netwerk naar Fabrikam VNet), en 3) Fabrikam en Contoso on-premises netwerk cross connect. 

De volgende tabel toont de routetabel van de private peering van de ExpressRoute van Contoso Ltd., vóór de fusie.

[![2]][2 2.]

De volgende tabel toont de effectieve routes van een VM in het Contoso-abonnement, vóór de fusie. Volgens de tabel is de VM op de VNet op de hoogte van de VNet-adresruimte en het Contoso on-premises netwerk, afgezien van de standaard- en 

[![4]][4.]

De volgende tabel toont de routetabel van de private peering van de ExpressRoute van Fabrikam Inc., vóór de fusie.

[![3.]][3]

De volgende tabel toont de effectieve routes van een VM in het Fabrikam-abonnement, vóór de fusie. Volgens de tabel is de VM op de VNet op de hoogte van de VNet-adresruimte en het on-premises netwerk van Fabrikam, afgezien van de standaard- en

[![5]][5.]

Laten we in dit artikel stap voor stap doornemen en bespreken hoe u de gewenste kruisverbindingen bereiken met behulp van de volgende Azure-netwerkfuncties:

* [Virtueel netwerk peering][Virtual network peering] 
* [Verbinding met virtuele netwerkExpressRoute][connection]
* [Globaal bereik][Global Reach] 

## <a name="cross-connecting-vnets"></a>Cross connecting VNets

Virtual network peering (VNet peering) biedt de meest optimale en de beste netwerkprestaties bij het aansluiten van twee virtuele netwerken. VNet-peering ondersteunt peering tussen twee VNets, zowel binnen dezelfde Azure-regio (ook wel VNet-peering genoemd) als in twee verschillende Azure-regio's (algemeen Global VNet-peering genoemd). 

Globale VNet-peering configureren tussen de VNets in Contoso- en Fabrikam Azure-abonnementen. Zie [Een virtueel netwerkpeering-artikel maken][Configure VNet peering] voor het maken van het virtuele netwerk peering tussen twee virtuele netwerken.

De volgende afbeelding toont de netwerkarchitectuur na het configureren van Global VNet-peering.

[![6]][6]

In de volgende tabel worden de routes weergegeven die bekend zijn bij de VM van het Contoso-abonnement. Let op de laatste vermelding van de tafel. Dit item is het resultaat van cross connecting the virtual networks.

[![7]][7]

In de volgende tabel worden de routes weergegeven die bekend zijn bij de VM van het Fabrikam-abonnement. Let op de laatste vermelding van de tafel. Dit item is het resultaat van cross connecting the virtual networks.

[![8]][8]

VNet peering direct links twee virtuele netwerken (zie er zijn geen volgende hop voor *VNetGlobalPeering* vermelding in de bovenstaande twee tabellen)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Cross connecting VNets to the on-premises networks Cross connecting VNets to the on-premises networks Cross connecting VNets to the on-premises networks Cross connecting

We kunnen een ExpressRoute-circuit aansluiten op meerdere virtuele netwerken. Zie [Abonnements- en servicelimieten][Subscription limits] voor het maximum aantal virtuele netwerken dat kan worden aangesloten op een ExpressRoute-circuit. 

Laten we Fabrikam ExpressRoute-circuit verbinden met Contoso-abonnement VNet en ook het Contoso ExpressRoute-circuit met Fabrikam-abonnement VNet om cross-connectiviteit tussen virtuele netwerken en de on-premises netwerken mogelijk te maken. Om een virtueel netwerk aan te sluiten op een ExpressRoute-circuit in een ander abonnement, moeten we een autorisatie maken en gebruiken.  Zie het artikel: [Sluit een virtueel netwerk aan op een ExpressRoute-circuit.][Connect-ER-VNet]

De volgende afbeelding toont de netwerkarchitectuur na het configureren van de ExpressRoute-kruisverbinding met de virtuele netwerken.

[![9]][9]

De volgende tabel toont de routetabel van de private peering van de ExpressRoute van Contoso Ltd., na het kruisen van virtuele netwerken met de on-premises netwerken via ExpressRoute. Zorg ervoor dat de routetabel routes heeft die tot beide virtuele netwerken behoren.

[![10]][10]

De volgende tabel toont de routetabel van de private peering van de ExpressRoute van Fabrikam Inc., na het kruisen van virtuele netwerken met de on-premises netwerken via ExpressRoute. Zorg ervoor dat de routetabel routes heeft die tot beide virtuele netwerken behoren.

[![11]][11]

In de volgende tabel worden de routes weergegeven die bekend zijn bij de VM van het Contoso-abonnement. Besteed aandacht aan *virtuele netwerkgateway-vermeldingen* van de tabel. De VM ziet routes voor beide on-premises netwerken.

[![12]][12]

In de volgende tabel worden de routes weergegeven die bekend zijn bij de VM van het Fabrikam-abonnement. Besteed aandacht aan *virtuele netwerkgateway-vermeldingen* van de tabel. De VM ziet routes voor beide on-premises netwerken.

[![13]][13]

>[!NOTE]
>In de Fabrikam- en/of Contoso-abonnementen u ook VNets hebben gesproken met de respectievelijke hub VNet (een hub- en spaakontwerp wordt niet geïllustreerd in de architectuurdiagrammen in dit artikel). De kruisverbindingen tussen de hub VNet gateways naar ExpressRoute maken ook communicatie mogelijk tussen Oost- en West-hubs en spaken.
>

## <a name="cross-connecting-on-premises-networks"></a>Cross connecting on-premises netwerken

ExpressRoute Global Reach biedt connectiviteit tussen on-premises netwerken die zijn aangesloten op verschillende ExpressRoute-circuits. Laten we Global Reach configureren tussen de circuits Contoso en Fabrikam ExpressRoute. Omdat de ExpressRoute-circuits in verschillende abonnementen zijn, moeten we een autorisatie maken en gebruiken. Zie [ExpressRoute Global Reach-artikel configureren][Configure Global Reach] voor stapsgewijze richtlijnen.

De volgende afbeelding toont de netwerkarchitectuur na het configureren van Global Reach.

[![14]][14]

De volgende tabel toont de routetabel van het privé-peering van de ExpressRoute van Contoso Ltd., na het configureren van Global Reach. Zorg ervoor dat de routetabel routes heeft die tot beide on-premises netwerken behoren. 

[![15]][15]

De volgende tabel toont de routetabel van het privé-peering van de ExpressRoute van Fabrikam Inc., na het configureren van Global Reach. Zorg ervoor dat de routetabel routes heeft die tot beide on-premises netwerken behoren.

[![16]][16]

## <a name="next-steps"></a>Volgende stappen

Zie [veelgestelde vragen][VNet-FAQ]over virtueel netwerk voor verdere vragen over VNet en VNet-peering. Zie [Veelgestelde vragen][ER-FAQ] over ExpressRoute voor verdere vragen over ExpressRoute en virtuele netwerkconnectiviteit.

Global Reach wordt per land/regio per land/regio uitgerold. Zie [ExpressRoute Global Reach][Global Reach]als u wilt weten of Global Reach beschikbaar is in de gewenste landen/regio's.

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "Het toepassingsscenario"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Contoso ExpressRoute routetabel voor fusie"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Fabrikam ExpressRoute routetabel voor fusie"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Contoso VM routes voor fusie"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "Fabrikam VM routes voor fusie"
[6]: ./media/cross-network-connectivity/vnet-peering.png "De architectuur na VNet-peering"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Contoso VM-routes na VNet-peering"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "Fabrikam VM routes na VNet peering"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "De architectuur na ExpressRoutes dwarsverbinding"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "Contoso ExpressRoute routetabel na kruisverbinding ExR en VNets"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Fabrikam ExpressRoute route tabel na cross aansluiten ExR en VNets"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "Contoso VM-routes na cross connecting ExR en VNets"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "Fabrikam VM routes na cross connecting ExR en VNets"
[14]: ./media/cross-network-connectivity/globalreach.png "De architectuur na het configureren van Global Reach"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Contoso ExpressRoute routetabel na Global Reach"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Fabrikam ExpressRoute routetabel na Global Reach"

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