---
title: 'Een op route gebaseerde VPN-gateway maken: portal'
titleSuffix: Azure VPN Gateway
description: Een vpn-gateway op basis van route maken met behulp van de Azure-portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6156d689a29ee348f9b1974d1520eb7d186a8d8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331346"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Een op route gebaseerde VPN-gateway maken met behulp van de Azure-portal

Met dit artikel u snel een op route gebaseerde Azure VPN-gateway maken met behulp van de Azure-portal.  Een VPN-gateway wordt gebruikt bij het maken van een VPN-verbinding met uw on-premises netwerk. U ook een VPN-gateway gebruiken om VNets met elkaar te verbinden. 

De stappen in dit artikel maken een VNet, een subnet, een gateway subnet en een route-gebaseerde VPN-gateway (virtual network gateway). Zodra de gatewaycreatie is voltooid, u verbindingen maken. Voor deze stappen is een Azure-abonnement vereist. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Een virtueel netwerk maken

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>De gateway configureren en maken

In deze stap maakt u de virtuele netwerkgateway VNet. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde gateway-SKU.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>De Basic gateway SKU ondersteunt geen IKEv2- of RADIUS-verificatie. Als u van plan bent Mac-clients verbinding te laten maken met uw virtuele netwerk, gebruikt u de Basic SKU niet.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Bekijk de VPN-gateway

1. Nadat de gateway is gemaakt, navigeert u naar VNet1 in de portal. De VPN-gateway wordt op de pagina Overzicht weergegeven als een verbonden apparaat.

   ![Verbonden apparaten](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Verbonden apparaten")

2. Klik in de lijst met apparaten op **VNet1GW** om meer informatie te bekijken.

   ![VPN-gateway bekijken](./media/create-routebased-vpn-gateway-portal/view-gateway.png "VPN-gateway bekijken")

## <a name="next-steps"></a>Volgende stappen

Zodra de gateway is voltooid, u een verbinding maken tussen uw virtuele netwerk en een andere VNet. Of maak een verbinding tussen uw virtuele netwerk en een on-premises locatie.

> [!div class="nextstepaction"]
> [Een site-to-site-verbinding maken](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Een punt-naar-site-verbinding maken](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Een verbinding maken met een andere VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
