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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73495720"
---
U kunt de `Resize-AzVirtualNetworkGateway`PowerShell-cmdlet gebruiken om een Generation1- of Generation2-SKU te upgraden of te downgraden (alle VpnGw-SKU's kunnen worden gewijzigd, behalve de Basic-SKU's). Als u de basis-gateway-SKU gebruikt, [kunt u deze instructies gebruiken in plaats](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) het formaat van uw gateway te wijzigen.

In het volgende Powershell-voor beeld ziet u een gateway-SKU waarvan de grootte wordt gewijzigd in VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

U kunt ook het formaat van een gateway in de Azure Portal wijzigen door naar de pagina **Configuratie** voor uw virtueel-netwerkgateway te gaan en een andere SKU te selecteren in de vervolgkeuzelijst.
