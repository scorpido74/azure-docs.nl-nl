---
title: 'Azure ExpressRoute: een gateway toevoegen aan een VNet: PowerShell'
description: Met dit artikel u vnet-gateway toevoegen aan een reeds gemaakte Resource Manager VNet voor ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6e6d79afa1126acb26cb8856c39ba486cce31a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037432"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Een virtuele netwerkgateway configureren voor ExpressRoute met behulp van PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassiek - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Met dit artikel u een VNet-gateway (Virtual Network) toevoegen, wijzigen en verwijderen voor een reeds bestaand VNet. De stappen voor deze configuratie zijn van toepassing op VNets die zijn gemaakt met behulp van het implementatiemodel Resource Manager voor een ExpressRoute-configuratie. Zie [Over virtuele netwerkgateways voor ExpressRoute voor](expressroute-about-virtual-network-gateways.md)meer informatie.

## <a name="before-beginning"></a>Voordat u begint

### <a name="working-with-powershell"></a>Werken met PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Referentielijst voor configuratie

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Volgende stappen
Nadat u de VNet-gateway hebt gemaakt, u uw VNet koppelen aan een ExpressRoute-circuit. Zie [Een virtueel netwerk koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md).
