---
title: Richtlijnen voor netwerkplanning azure NetApp Files | Microsoft Documenten
description: Beschrijft richtlijnen waarmee u een effectieve netwerkarchitectuur ontwerpen met Azure NetApp-bestanden.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: 8e6a1c3472c6b20b27cf181edbeeb96ab71eb58d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242476"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Richtlijnen voor Azure NetApp Files-netwerkplanning

Netwerkarchitectuurplanning is een belangrijk element van het ontwerpen van een toepassingsinfrastructuur. Met dit artikel u een effectieve netwerkarchitectuur ontwerpen voor uw workloads om te profiteren van de uitgebreide mogelijkheden van Azure NetApp-bestanden.

Azure NetApp-bestandenvolumes zijn ontworpen om te worden opgenomen in een subnet voor speciale doeleinden, een [gedelegeerd subnet,](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) binnen uw Azure Virtual Network. Daarom hebt u rechtstreeks toegang tot de volumes vanaf uw VNet, vanaf peered VNets in dezelfde regio of indien nodig via een Virtual Network Gateway (ExpressRoute of VPN Gateway). Het subnet is gewijd aan Azure NetApp Files en er is geen verbinding met andere Azure-services of internet.

## <a name="considerations"></a>Overwegingen  

U moet een paar overwegingen begrijpen wanneer u een abonnement plant voor het Azure NetApp Files-netwerk.

### <a name="constraints"></a>Beperkingen

De onderstaande functies worden momenteel niet ondersteund voor Azure NetApp-bestanden: 

* Netwerkbeveiligingsgroepen (NSG's) toegepast op het gedelegeerde subnet
* Door de gebruiker gedefinieerde routes (UDR's) met adresvoorvoegsel als subnet Azure NetApp-bestanden
* Azure-beleid (bijvoorbeeld aangepaste naamgevingsbeleidsregels) op de Azure NetApp-bestandeninterface
* Load balancers voor Azure NetApp Files-verkeer

De volgende netwerkbeperkingen zijn van toepassing op Azure NetApp-bestanden:

* Het aantal IP's dat wordt gebruikt in een VNet met Azure NetApp-bestanden (inclusief peered VNets) mag niet hoger zijn dan 1000. We werken aan het verhogen van deze limiet om te voldoen aan de vraag van klantenschaal. Als u in de tussentijd meer IP's nodig hebt, neem dan contact op met ons ondersteuningsteam met uw use case en vereiste limiet.
* In elk virtueel Azure-netwerk (VNet) kan er slechts één subnet aan Azure NetApp Files worden gedelegeerd.


### <a name="supported-network-topologies"></a>Ondersteunde netwerktopologieën

In de volgende tabel worden de netwerktopologieën beschreven die worden ondersteund door Azure NetApp-bestanden.  Het beschrijft ook de oplossingen voor de niet-ondersteunde topologieën. 

|    Topologieën    |    Wordt ondersteund    |     Tijdelijke oplossing    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Verbinding met volume in een lokaal VNet    |    Ja    |         |
|    Verbinding met volume in een peered VNet (Zelfde regio)    |    Ja    |         |
|    Connectiviteit met volume in een peered VNet (Cross region of global peering)    |    Nee    |    Geen    |
|    Connectiviteit met een volume via ExpressRoute-gateway    |    Ja    |         |
|    Connectiviteit van on-premises naar een volume in een spaak VNet over ExpressRoute gateway en VNet peering met gateway transit    |    Ja    |        |
|    Connectiviteit van on-premises naar een volume in een gesproken VNet via VPN-gateway    |    Ja    |         |
|    Connectiviteit van on-premises naar een volume in een gesproken VNet via VPN-gateway en VNet-peering met gatewaytransit    |    Ja    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Virtueel netwerk voor Azure NetApp-bestandenvolumes

In dit gedeelte worden concepten uitgelegd die u helpen bij de planning van virtuele netwerken.

### <a name="azure-virtual-networks"></a>Virtuele netwerken van Azure

Voordat u een Azure NetApp Files-volume indient, moet u een Virtueel Azure-netwerk (VNet) maken of een netwerk gebruiken dat al in uw abonnement bestaat. De VNet definieert de netwerkgrens van het volume.  Zie de documentatie van het [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)voor meer informatie over het maken van virtuele netwerken.

### <a name="subnets"></a>Subnetten

Subnetten segmenteren het virtuele netwerk in afzonderlijke adresruimten die bruikbaar zijn voor de Azure-bronnen in deze netwerken.  Azure NetApp-bestandenvolumes zijn opgenomen in een subnet voor speciale doeleinden dat een [gedelegeerd subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)wordt genoemd. 

Subnetdelegatie geeft expliciete machtigingen aan de Azure NetApp Files-service om servicespecifieke bronnen in het subnet te maken.  Het maakt gebruik van een unieke id bij het implementeren van de service. In dit geval wordt een netwerkinterface gemaakt om connectiviteit met Azure NetApp-bestanden mogelijk te maken.

Als u een nieuw VNet gebruikt, u een subnet maken en het subnet delegeren aan Azure NetApp-bestanden door instructies te volgen in [Een subnet delegeren aan Azure NetApp-bestanden](azure-netapp-files-delegate-subnet.md). U ook een bestaand leeg subnet delegeren dat nog niet is gedelegeerd aan andere services.

Als de VNet is peered met een andere VNet, u de VNet-adresruimte niet uitbreiden. Daarom moet het nieuwe gedelegeerde subnet worden gemaakt binnen de VNet-adresruimte. Als u de adresruimte wilt uitbreiden, moet u de VNet-peering verwijderen voordat u de adresruimte uitbreidt.

### <a name="udrs-and-nsgs"></a>UDR's en NSGs

Door de gebruiker gedefinieerde routes (UDR's) en Netwerkbeveiligingsgroepen (NSG's) worden niet ondersteund op gedelegeerde subnetten voor Azure NetApp-bestanden.

Als tijdelijke oplossing u NSG's toepassen op andere subnetten die het verkeer van en naar het gedelegeerde subnet van Azure NetApp Files toestaan of weigeren.  

## <a name="azure-native-environments"></a>Azure-native omgevingen

In het volgende diagram wordt een Azure-native omgeving geïllustreerd:

![Azure-native netwerkomgeving](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Lokale VNet

Een basisscenario is het maken of verbinden met een Azure NetApp-bestandenvolume vanaf een virtuele machine (VM) in hetzelfde VNet. Voor VNet 2 in het bovenstaande diagram wordt volume 1 gemaakt in een gedelegeerd subnet en kan deze op VM 1 in het standaardsubnet worden gemonteerd.

### <a name="vnet-peering"></a>VNet-peering

Als u extra VNets in dezelfde regio hebt die toegang tot elkaars resources nodig hebben, kunnen de VNets worden verbonden met [VNet-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) om veilige connectiviteit via de Azure-infrastructuur mogelijk te maken. 

Denk aan VNet 2 en VNet 3 in het bovenstaande diagram. Als VM 1 verbinding moet maken met VM 2 of Volume 2, of als VM 2 verbinding moet maken met VM 1 of Volume 1, moet u VNet-peering tussen VNet 2 en VNet 3 inschakelen. 

Overweeg bovendien een scenario waarin VNet 1 is gekoppeld aan VNet 2 en VNet 2 wordt peered met VNet 3 in dezelfde regio. De bronnen van VNet 1 kunnen verbinding maken met resources in VNet 2, maar het kan geen verbinding maken met resources in VNet 3, tenzij VNet 1 en VNet 3 zijn peered. 

In het bovenstaande diagram kan VM 3 weliswaar verbinding maken met volume 1, maar kan VM 4 geen verbinding maken met Volume 2.  De reden hiervoor is dat de spaak VNets niet worden peered, en _transit routing wordt niet ondersteund via VNet peering_.

## <a name="hybrid-environments"></a>Hybride omgevingen

Het volgende diagram illustreert een hybride omgeving: 

![Hybride netwerkomgeving](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

In het hybride scenario hebben toepassingen uit on-premises datacenters toegang nodig tot de bronnen in Azure.  Dit is het geval of u uw datacenter wilt uitbreiden naar Azure, of dat u Azure Native Services wilt gebruiken of voor noodherstel. Zie [VPN-gatewayplanningsopties](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) voor informatie over het on-premises verbinden van meerdere bronnen met resources in Azure via een site-to-site VPN of een ExpressRoute.

In een hybride hub-spoke topologie fungeert de hub VNet in Azure als een centraal verbindingspunt voor uw on-premises netwerk. De spaken zijn VNets peered met de hub, en ze kunnen worden gebruikt om workloads te isoleren.

Afhankelijk van de configuratie u on-premises resources verbinden met resources in de hub en de spaken.

In de hierboven geïllustreerde topologie is het on-premises netwerk verbonden met een hub VNet in Azure en zijn er 2 spaakvNets in dezelfde regio die zijn peered met de hub VNet.  In dit scenario zijn de connectiviteitsopties die worden ondersteund voor Azure NetApp Files-volumes als volgt:

* On-premises bronnen VM 1 en VM 2 kunnen verbinding maken met Volume 1 in de hub via een site-to-site VPN- of ExpressRoute-circuit. 
* On-premises bronnen VM 1 en VM 2 kunnen verbinding maken met Deel 2 of Deel 3 via een site-to-site VPN en regionale Vnet-peering.
* VM 3 in de hub VNet kan verbinding maken met Volume 2 in spaak VNet 1 en Volume 3 in spaak VNet 2.
* VM 4 van spaakVNet 1 en VM 5 van spaak VNet 2 kunnen verbinding maken met Volume 1 in de hub VNet.

VM 4 in spaak VNet 1 kan geen verbinding maken met Volume 3 in spaak VNet 2. Ook VM 5 in spaak VNet2 kan geen verbinding maken met Volume 2 in spaak VNet 1. Dit is het geval omdat de spaak VNets niet worden peered en _transit routing wordt niet ondersteund via VNet peering_.

## <a name="next-steps"></a>Volgende stappen

[Een subnet delegeren aan Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
