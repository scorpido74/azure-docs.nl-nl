---
title: Verouderde Azure virtual network VPN gateway SKU's
description: Hoe te werken met de oude virtuele netwerk gateway SKU's; Basic, Standard en HighPerformance.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279388"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Werken met sku's voor virtuele netwerkgateways (oudere SKU's)

Dit artikel bevat informatie over de verouderde (oude) virtuele netwerkgateway SKU's. De oude SKU's werken nog steeds in beide implementatiemodellen voor VPN-gateways die al zijn gemaakt. Klassieke VPN-gateways blijven de verouderde SKU's gebruiken, zowel voor bestaande gateways als voor nieuwe gateways. Gebruik bij het maken van nieuwe VPN-gateways van Resource Manager de nieuwe gateway-SKU's. Zie [Over VPN Gateway](vpn-gateway-about-vpngateways.md)voor informatie over de nieuwe SKU's.

## <a name="gateway-skus"></a><a name="gwsku"></a>Gateway-SKU's

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

U de prijzen van oudere gateways bekijken in de sectie **Virtual Network Gateways,** die zich bevindt op de [prijspagina ExpressRoute.](https://azure.microsoft.com/pricing/details/expressroute)

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>Geschatte geaggregeerde doorvoer per SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>Ondersteunde configuraties door SKU- en VPN-type

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>Het formaat van een gateway wijzigen

U het formaat van uw gateway naar een gateway SKU binnen dezelfde SKU-familie wijzigen. Als u bijvoorbeeld een Standaard SKU hebt, u het formaat wijzigen in een HighPerformance SKU. U uw VPN-gateway echter niet wijzigen tussen de oude SKU's en de nieuwe SKU-families. Je bijvoorbeeld niet van een Standaard SKU naar een VpnGw2 SKU gaan, of een Basic SKU naar VpnGw1.

### <a name="resource-manager"></a>Resource Manager

Als u het formaat van een gateway voor het implementatiemodel resourcebeheer met PowerShell wilt wijzigen, gebruikt u de volgende opdracht:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

U ook het formaat van een gateway wijzigen in de Azure-portal.

### <a name="classic"></a><a name="classicresize"></a>Klassiek

Als u het formaat van een gateway voor het klassieke implementatiemodel wilt wijzigen, moet u de PowerShell-cmdlets servicebeheer gebruiken. Gebruik de volgende opdracht:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>Wijzigen in de nieuwe gateway SKU's

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Volgende stappen

Zie Gateway SKU's voor meer informatie over de nieuwe Gateway [SKU's.](vpn-gateway-about-vpngateways.md#gwsku)

Zie [Configuratie-instellingen voor VPN-gateway' voor](vpn-gateway-about-vpn-gateway-settings.md)meer informatie over configuratie-instellingen.
