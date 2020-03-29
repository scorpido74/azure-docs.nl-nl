---
title: 'Azure ExpressRoute: een gateway toevoegen aan een VNet: klassiek'
description: Configureer een VNet-gateway voor een klassiek implementatiemodel VNet met PowerShell voor een ExpressRoute-configuratie.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: d7927af5b831dbe3294e1abc3a878e323bf1867e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928047"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Een virtuele netwerkgateway voor ExpressRoute configureren met PowerShell (klassiek)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassiek - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

In dit artikel vindt u de stappen om een VNet-gateway (Virtual Network) toe te voegen, te vergroten en te verwijderen voor een reeds bestaand VNet. De stappen voor deze configuratie zijn specifiek voor VNets die zijn gemaakt met behulp van het **klassieke implementatiemodel** en die worden gebruikt in een ExpressRoute-configuratie. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Over Azure-implementatiemodellen**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Voordat u begint
Controleer of u de Azure PowerShell-cmdlets hebt geïnstalleerd die nodig zijn voor deze configuratie.

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Volgende stappen
Nadat u de VNet-gateway hebt gemaakt, u uw VNet koppelen aan een ExpressRoute-circuit. Zie [Een virtueel netwerk koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-classic.md).

