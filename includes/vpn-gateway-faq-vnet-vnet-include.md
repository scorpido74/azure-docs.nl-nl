---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/03/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72ddd0b6cd6c3e12417d3698c403f89312b531f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175856"
---
De VNet-to-VNet FAQ is van toepassing op VPN-gatewayverbindingen. Zie Virtueel netwerkpeeren voor informatie over [VNet-peering.](../articles/virtual-network/virtual-network-peering-overview.md)

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Worden er door Azure kosten in rekening gebracht voor verkeer tussen VNets?

VNet-naar-VNet-verkeer binnen dezelfde regio is gratis voor beide richtingen wanneer u een VPN-gatewayverbinding gebruikt. Cross-region VNet-to-VNet uitgangverkeer wordt belast met de uitgaande inter-VNet gegevensoverdracht tarieven op basis van de bronregio's. Zie [prijspagina VPN Gateway voor](https://azure.microsoft.com/pricing/details/vpn-gateway/)meer informatie. Zie [Virtuele netwerkprijzen](https://azure.microsoft.com/pricing/details/virtual-network/)als u uw VNets verbindt met VNet-peering in plaats van een VPN-gateway.

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>Is VNet-to-VNet verkeer reizen over het internet?

Nee. VNet-naar-VNet-verkeer reist over de Microsoft Azure-backbone, niet via het internet.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Kan ik een VNet-naar-VNet-verbinding tot stand brengen voor Azure Active Directory -tenants (AAD)?

Ja, VNet-naar-VNet-verbindingen die azure VPN-gateways gebruiken, werken voor AAD-tenants.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Is het VNet-naar-VNet-verkeer beveiligd?

Ja, het wordt beschermd door IPsec/IKE encryptie.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Heb ik een VPN-apparaat nodig om VNets met elkaar te verbinden?

Nee. Om meerdere virtuele netwerken van Azure met elkaar te verbinden, hebt u geen VPN-apparaat nodig, tenzij cross-premises connectiviteit is vereist.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Moeten mijn VNets zich in dezelfde regio bevinden?

Nee. De virtuele netwerken kunnen zich in dezelfde of verschillende Azure-regio's (locaties) bevinden.

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Als de VNets niet in hetzelfde abonnement zitten, moeten de abonnementen dan aan dezelfde Active Directory-tenant worden gekoppeld?

Nee.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Kan ik VNet-naar-VNet-verbindingen gebruiken tussen virtuele netwerken in verschillende Azure-exemplaren? 

Nee. VNet-naar-VNet ondersteunt het verbinden van virtuele netwerken binnen hetzelfde Azure-exemplaar. U bijvoorbeeld geen verbinding maken tussen azure-globale en Azure-exemplaren van de Chinese/Duitse/Amerikaanse overheid. Overweeg een Site-to-Site VPN-verbinding te gebruiken voor deze scenario's.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Kan ik VNet-naar-VNet- én multi-site-verbindingen gebruiken?

Ja. U kunt virtuele-netwerkverbindingen tegelijk gebruiken met multi-site-VPN’s.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Met hoeveel on-premises sites en virtuele netwerken kan één virtueel netwerk verbinding maken?

Zie de tabel [Gateway-vereisten.](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements)

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Kan ik VNet-naar-VNet gebruiken om virtuele machines of cloudservices met elkaar te verbinden buiten een VNet?

Nee. VNet naar VNet ondersteunt het verbinden van virtuele netwerken. Het ondersteunt geen verbinding tussen virtuele machines of cloudservices die zich niet in een virtueel netwerk bevinden.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Kan een cloudservice of een load-balancing endpoint-overspanning VNets?

Nee. Een cloudservice of een endpoint voor het balanceren van de load-balancing kan niet over virtuele netwerken heen, zelfs niet als ze met elkaar zijn verbonden.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Kan ik een PolicyBased VPN-type gebruiken voor VNet-to-VNet- of Multi-Site-verbindingen?

Nee. VNet-naar-VNet- en Multi-Site-verbindingen vereisen Azure VPN-gateways met RouteBased -VPN-typen (voorheen dynamic routing genoemd).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Kan ik een VNet met een op route gebaseerd VPN-type verbinden met een op beleid gebaseerd VPN-type?

Nee, beide virtuele netwerken moeten routegebaseerde (voorheen dynamische routering) VPN's gebruiken.

### <a name="do-vpn-tunnels-share-bandwidth"></a>Delen VPN-tunnels bandbreedte?

Ja. Alle VPN-tunnels van het virtuele netwerk delen de beschikbare bandbreedte op de Azure VPN-gateway en dezelfde SLA voor VPN-gatewaybedrijfstijd in Azure.

### <a name="are-redundant-tunnels-supported"></a>Worden redundante tunnels ondersteund?

Redundante tunnels tussen twee virtuele netwerken worden ondersteund, mits één virtuele-netwerkgateway is geconfigureerd als actief-actief.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Mogen er overlappende adresruimten zijn voor VNet-naar-VNet-configuraties?

Nee. Er mag geen sprake zijn van overlappende IP-adresbereiken.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Mogen er overlappende adresruimten zijn tussen de verbonden virtuele netwerken en on-premises lokale sites?

Nee. Er mag geen sprake zijn van overlappende IP-adresbereiken.



