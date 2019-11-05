---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495720"
---
U kunt de `Resize-AzVirtualNetworkGateway` Power shell-cmdlet gebruiken om een Generation1-of Generation2-SKU te upgraden of te downgradeen (alle VpnGw Sku's kunnen worden gewijzigd, behalve de Basic-Sku's). Als u de Basic gateway SKU gebruikt, moet u [deze instructies gebruiken](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) om de grootte van uw gateway te wijzigen.

In het volgende Power shell-voor beeld ziet u een gateway-SKU waarvan de grootte wordt gewijzigd in VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

U kunt ook het formaat van een gateway in de Azure Portal wijzigen door naar de **configuratie** pagina voor uw virtuele netwerk gateway te gaan en een andere SKU te selecteren in de vervolg keuzelijst.
