---
title: Azure VMware Solution by CloudSimple - Checklist netwerk
description: Checklist voor het toewijzen van netwerk-CIDR aan Azure VMware-oplossing door CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025007"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Netwerkvereisten voor Azure VMware-oplossing door CloudSimple

Azure VMware Solution by CloudSimple biedt een VMware private cloudomgeving die toegankelijk is voor gebruikers en toepassingen vanuit on-premises omgevingen, door bedrijven beheerde apparaten en Azure-resources. De connectiviteit wordt geleverd via netwerkservices zoals VPN's en Azure ExpressRoute-verbindingen. Voor sommige van deze netwerkservices moet u netwerkadresbereiken opgeven om de services in te schakelen. 

Tabellen in dit artikel beschrijven de set adresbereiken en bijbehorende services die de opgegeven adressen gebruiken. Sommige adressen zijn verplicht en sommige zijn afhankelijk van de services die u wilt implementeren. Deze adresruimten mogen niet overlappen met een van uw on-premises subnetten, Subnetten van Azure Virtual Network of geplande Subnetten voor CloudSimple-workloads.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Netwerkadresbereiken die nodig zijn voor het maken van een private cloud

Tijdens het maken van een CloudSimple-service en een private cloud moet u als volgt voldoen aan de opgegeven CIDR-bereiken (Network Classless Inter-Domain Routing).

| Naam/gebruikt voor     | Beschrijving                                                                                                                            | Adresbereik            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Gateway CIDR      | Vereist voor edge-services (VPN-gateways).  Deze CIDR is vereist tijdens het maken van CloudSimple Service en moet afkomstig zijn uit de RFC 1918-ruimte. | /28                      |
| vSphere/vSAN CIDR | Vereist voor VMware-beheernetwerken. Deze CIDR moet worden opgegeven tijdens het maken van private cloud.                                    | /24 of /23 of /22 of /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Netwerkadresbereik vereist voor Azure-netwerkverbinding met een on-premises netwerk

Door verbinding te maken van een [on-premises netwerk met het private cloudnetwerk via ExpressRoute](on-premises-connection.md) ontstaat een Global Reach-verbinding.  De verbinding maakt gebruik van Border Gateway Protocol (BGP) om routes uit te wisselen tussen uw on-premises netwerk, uw privécloudnetwerk en uw Azure-netwerken.

| Naam/gebruikt voor             | Beschrijving                                                                                                                                                                             | Adresbereik |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute Peering CIDR | Vereist wanneer u ExpressRoute Global Reach gebruikt voor on-premises connectiviteit. Deze CIDR moet worden verstrekt wanneer een Global Reach-verbindingsaanvraag wordt gedaan via een ondersteuningsticket. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Netwerkadresbereik vereist voor het gebruik van een site-to-site VPN-verbinding met een on-premises netwerk

Verbinding maken van een [on-premises netwerk met het private cloudnetwerk via site-to-site VPN](vpn-gateway.md) vereist de volgende IP-adressen, on-premises netwerk en id's. 

| Adres-/adresbereik | Beschrijving                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Peer IP               | On-premises VPN-gateway openbaar IP-adres. Vereist om een site-to-site VPN-verbinding tot stand te brengen tussen een on-premises datacenter en de CloudSimple Service-regio. Dit IP-adres is vereist tijdens het maken van de VPN-gateway van site-to-site.                                         |
| Peer-id       | Peer-id van de on-premises VPN-gateway. Dit is meestal hetzelfde als **peer IP**.  Als een unieke id is opgegeven op uw on-premises VPN-gateway, moet de id worden opgegeven.  Peer ID is vereist tijdens het maken van de VPN-gateway van site-to-site.   |
| On-premises netwerken   | On-premises voorvoegsels die toegang nodig hebben tot CloudSimple-netwerken in de regio.  Neem alle voorvoegsels op van een on-premises netwerk dat toegang heeft tot het CloudSimple-netwerk, inclusief het clientnetwerk van waaruit gebruikers toegang krijgen tot het netwerk.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Netwerkadresbereik vereist voor het gebruik van point-to-site VPN-verbindingen

Een point-to-site VPN-verbinding maakt toegang tot het CloudSimple-netwerk mogelijk vanaf een clientmachine.  [Als u point-to-site VPN](vpn-gateway.md)wilt instellen, moet u het volgende netwerkadresbereik opgeven.

| Adres-/adresbereik | Beschrijving                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subnet client         | DHCP-adressen worden door het subnet van de client geleverd wanneer u verbinding maakt met een point-to-site VPN. Dit subnet is vereist terwijl u een point-to-site VPN-gateway maakt op een CloudSimple-portal.  Het netwerk is verdeeld in twee subnetten; een voor de UDP-verbinding en de andere voor TCP-verbindingen. |

## <a name="next-steps"></a>Volgende stappen

* [On-premises firewall-setup voor toegang tot uw private cloud](on-premises-firewall-configuration.md)
* [Quickstart - Een CloudSimple-service maken](quickstart-create-cloudsimple-service.md)
* [Snelstart- Een private cloud configureren](quickstart-create-private-cloud.md)
* Meer informatie over [Azure-netwerkverbindingen](cloudsimple-azure-network-connection.md)
* Meer informatie over [VPN-gateways](cloudsimple-vpn-gateways.md)
