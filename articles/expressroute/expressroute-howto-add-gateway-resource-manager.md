---
title: 'Azure ExpressRoute: een gateway toevoegen aan een VNet: Power shell'
description: Dit artikel helpt u bij het toevoegen van VNet-gateway naar een bestaande Resource Manager VNet voor ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6e6d79afa1126acb26cb8856c39ba486cce31a5e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037432"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Een virtuele netwerkgateway configureren voor ExpressRoute met behulp van PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassiek - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Dit artikel helpt u bij het toevoegen en verwijderen van de gateway van een virtueel netwerk (VNet) voor een bestaande VNet vergroten of verkleinen. De stappen voor deze configuratie zijn van toepassing op VNets die zijn gemaakt met behulp van het Resource Manager-implementatie model voor een ExpressRoute-configuratie. Zie [over virtuele netwerk gateways voor ExpressRoute](expressroute-about-virtual-network-gateways.md)voor meer informatie.

## <a name="before-beginning"></a>Voordat u begint

### <a name="working-with-powershell"></a>Werken met Power shell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Lijst met configuratie verwijzingen

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Volgende stappen
Nadat u de VNet-gateway hebt gemaakt, kunt u uw VNet koppelen aan een ExpressRoute-circuit. Zie [een Virtueelnetwerk koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md).
