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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67175856"
---
De veelgestelde vragen over VNet-naar-VNet zijn van toepassing op VPN gateway-verbindingen. Zie [peering op virtueel netwerk](../articles/virtual-network/virtual-network-peering-overview.md)voor meer informatie over VNet-peering.

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Worden er door Azure kosten in rekening gebracht voor verkeer tussen VNets?

VNet-naar-VNet-verkeer binnen dezelfde regio is gratis voor beide richtingen wanneer u een VPN-gateway verbinding gebruikt. Verkeer voor meerdere regio's VNet-naar-VNet wordt in rekening gebracht met de uitgaande gegevens overdrachts tarieven op basis van de voor waarden die zijn gebaseerd op de bron regio's. Zie [VPN gateway pagina met prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway/)voor meer informatie. Zie [prijzen van virtueel netwerk](https://azure.microsoft.com/pricing/details/virtual-network/)als u uw VNets verbindt met behulp van VNet-peering in plaats van een VPN-gateway.

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>Reist het VNet-naar-VNet-verkeer via Internet?

Nee. VNet-naar-VNet-verkeer wordt verplaatst over de Microsoft Azure backbone, niet via internet.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Kan ik een VNet-naar-VNet-verbinding tot stand brengen tussen de tenants van Azure Active Directory (AAD)?

Ja, VNet-naar-VNet-verbindingen die gebruikmaken van Azure VPN-gateways, werken via AAD-tenants.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Is het VNet-naar-VNet-verkeer beveiligd?

Ja, het wordt beveiligd door IPsec/IKE-versleuteling.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Heb ik een VPN-apparaat nodig om VNets met elkaar te verbinden?

Nee. Om meerdere virtuele netwerken van Azure met elkaar te verbinden, hebt u geen VPN-apparaat nodig, tenzij cross-premises connectiviteit is vereist.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Moeten mijn VNets zich in dezelfde regio bevinden?

Nee. De virtuele netwerken kunnen zich in dezelfde of verschillende Azure-regio's (locaties) bevinden.

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Als de VNets zich niet in hetzelfde abonnement bevinden, moeten de abonnementen worden gekoppeld aan dezelfde Active Directory Tenant?

Nee.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Kan ik VNet-naar-VNet-verbindingen gebruiken tussen virtuele netwerken in verschillende Azure-exemplaren? 

Nee. VNet-naar-VNet ondersteunt het verbinden van virtuele netwerken binnen hetzelfde Azure-exemplaar. U kunt bijvoorbeeld geen verbinding maken tussen wereld wijde Azure-en Chinese/Duitse/Amerikaanse overheids instanties. Overweeg het gebruik van een site-naar-site-VPN-verbinding voor deze scenario's.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Kan ik VNet-naar-VNet- én multi-site-verbindingen gebruiken?

Ja. U kunt virtuele-netwerkverbindingen tegelijk gebruiken met multi-site-VPN’s.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Met hoeveel on-premises sites en virtuele netwerken kan één virtueel netwerk verbinding maken?

Zie de tabel met [Gateway vereisten](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements) .

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Kan ik VNet-naar-VNet gebruiken om virtuele machines of cloudservices met elkaar te verbinden buiten een VNet?

Nee. VNet naar VNet ondersteunt het verbinden van virtuele netwerken. Het is niet mogelijk om verbinding te maken met virtuele machines of Cloud Services die zich niet in een virtueel netwerk bevinden.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Kan een Cloud service of een eind punt voor taak verdeling VNets?

Nee. Een Cloud service of een taakverdelings eindpunt kan geen virtuele netwerken omvatten, zelfs niet als ze met elkaar zijn verbonden.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Kan ik een PolicyBased VPN-type gebruiken voor VNet-naar-VNet-en multi-site-verbindingen?

Nee. Voor VNet-naar-VNet-en multi-site-verbindingen zijn Azure VPN-gateways met RouteBased (voorheen dynamische route ring genoemd) VPN-typen vereist.

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Kan ik een VNet met een op route gebaseerd VPN-type verbinden met een op beleid gebaseerd VPN-type?

Nee, beide virtuele netwerken moeten gebruikmaken van op route gebaseerde Vpn's (voorheen dynamische route ring genoemd).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Delen VPN-tunnels bandbreedte?

Ja. Alle VPN-tunnels van het virtuele netwerk delen de beschikbare bandbreedte op de Azure VPN-gateway en dezelfde SLA voor VPN-gatewaybedrijfstijd in Azure.

### <a name="are-redundant-tunnels-supported"></a>Worden redundante tunnels ondersteund?

Redundante tunnels tussen twee virtuele netwerken worden ondersteund, mits één virtuele-netwerkgateway is geconfigureerd als actief-actief.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Mogen er overlappende adresruimten zijn voor VNet-naar-VNet-configuraties?

Nee. Er mag geen sprake zijn van overlappende IP-adresbereiken.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Mogen er overlappende adresruimten zijn tussen de verbonden virtuele netwerken en on-premises lokale sites?

Nee. Er mag geen sprake zijn van overlappende IP-adresbereiken.



