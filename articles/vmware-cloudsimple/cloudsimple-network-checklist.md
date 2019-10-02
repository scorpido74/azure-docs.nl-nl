---
title: Azure VMware-oplossing per CloudSimple-netwerk controlelijst
description: Controle lijst voor het toewijzen van netwerk-CIDR op Azure VMware-oplossing door CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c31942b198697ddc913f3732fd41409334ff0ef6
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817473"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Netwerk vereisten voor de Azure VMware-oplossing op CloudSimple

De Azure VMware-oplossing van CloudSimple biedt een VMware-Privécloud die toegankelijk is voor gebruikers en toepassingen van on-premises omgevingen, van door ondernemingen beheerde apparaten en van Azure-bronnen. De connectiviteit wordt geleverd met behulp van netwerk services, zoals Vpn's en ExpressRoute-verbindingen.  Voor sommige van de Network Services moet u netwerk adresbereiken opgeven voor het inschakelen van de services.  In de tabellen in dit artikel wordt de set adresbereiken en de bijbehorende services beschreven die gebruikmaken van de opgegeven adressen.  Sommige van de verholpen zijn verplicht en sommige zijn afhankelijk van de services die u wilt implementeren.  Deze adres ruimten mogen niet overlappen met een van uw on-premises subnetten, Azure Virtual Network subnetten of geplande CloudSimple.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Netwerk adresbereiken vereist voor het maken van een Privécloud

Tijdens het maken van de CloudSimple-service en een Privécloud is het volgende vereist voor het netwerk CIDR-bereik.

| Naam/gebruikt voor     | Description                                                                                                                            | Adresbereik            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Gateway-CIDR      | Vereist voor Edge-Services (VPN-gateways).  Deze CIDR is vereist tijdens het maken van de CloudSimple-service en moet zich bevindt in RFC 1918-ruimte. | /28                      |
| vSphere/vSAN CIDR | Vereist voor VMware-beheer netwerken. Deze CIDR moet worden opgegeven tijdens het maken van een Privécloud.                                    | /24 of/23 of/22 of/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-on-premises-network"></a>Het netwerk adres bereik dat is vereist voor de Azure-netwerk verbinding met het on-premises netwerk

Verbinding maken vanaf een [on-premises netwerk met het particuliere cloud netwerk met behulp van ExpressRoute](on-premises-connection.md) brengt een Global Reach verbinding tot stand.  Via de verbinding worden routes via BGP tussen uw on-premises netwerk, het particuliere cloud netwerk en uw Azure-netwerken uitgewisseld.

| Naam/gebruikt voor             | Description                                                                                                                                                                             | Adresbereik |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute-peering CIDR | Vereist wanneer het gebruik van ExpressRoute Global Reach wordt gebruikt voor on-premises connectiviteit. Deze CIDR moet worden opgegeven wanneer een Global Reach verbindings aanvraag wordt gedaan via een ondersteunings ticket. | /29           |

## <a name="network-address-range-required-for-using-site-to-site-vpn-connection-to-on-premises-network"></a>Het netwerk adres bereik dat is vereist voor het gebruik van een site-naar-site-VPN-verbinding met een on-premises netwerk

Verbinding maken vanaf een [on-premises netwerk met het particuliere cloud netwerk met behulp van site-naar-site-VPN](vpn-gateway.md) zijn de volgende IP-adressen, het on-premises netwerk en de id's vereist. 

| Adres/adres bereik | Description                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Peer-IP-adres               | Openbaar IP-adres van on-premises VPN-gateway. Vereist om een site-naar-site-VPN-verbinding tot stand te brengen tussen on-premises Data Center en de CloudSimple-service regio. Dit IP-adres is vereist tijdens het maken van de site-naar-site-VPN-gateway.                                         |
| Peer-id       | De peer-id van de on-premises VPN-gateway. Dit is meestal hetzelfde als het **IP-adres**van de peer.  Als er een unieke id is opgegeven op uw on-premises VPN-gateway, moet de id worden opgegeven.  Peer-ID is vereist tijdens het maken van site-naar-site-VPN Gateway.   |
| On-premises netwerken   | On-premises voor voegsels waarvoor toegang is vereist tot CloudSimple netwerken in de regio.  Neem alle voor voegsels van on-premises netwerk op die toegang hebben tot het CloudSimple-netwerk, met inbegrip van het client netwerk van waaruit gebruikers toegang hebben.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Het netwerk adres bereik dat is vereist voor het gebruik van punt-naar-site-VPN-verbindingen

Punt-naar-site-VPN-verbinding maakt toegang tot het CloudSimple-netwerk vanaf een client computer mogelijk.  Voor het [instellen van punt-naar-site-VPN](vpn-gateway.md) moet het volgende netwerk adres bereik worden opgegeven.

| Adres/adres bereik | Description                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Client subnet         | Wanneer u verbinding maakt met behulp van punt-naar-site-VPN, worden DHCP-adressen verleend vanuit het client-subnet. Dit subnet is vereist tijdens het maken van een punt-naar-site-VPN-gateway in de CloudSimple-Portal.  Het netwerk wordt onderverdeeld in twee subnetten en één wordt gebruikt voor UDP-verbinding en andere voor TCP-verbindingen. |

## <a name="next-steps"></a>Volgende stappen

* [Quick Start-CloudSimple-service maken](quickstart-create-cloudsimple-service.md)
* [Quick start-maken-Private-Cloud](quickstart-create-private-cloud.md)
* Meer informatie over [Azure-netwerk verbindingen](cloudsimple-azure-network-connection.md)
* Meer informatie over [VPN-gateways](cloudsimple-vpn-gateways.md)
