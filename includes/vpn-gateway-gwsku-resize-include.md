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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73495720"
---
U `Resize-AzVirtualNetworkGateway` de PowerShell-cmdlet gebruiken om een Generation1 of Generation2 SKU te upgraden of te downgraden (alle VpnGw SKU's kunnen worden aangepast, behalve Basic SKU's). Als u de SKU voor basisgateway gebruikt, [gebruikt u deze instructies in plaats daarvan](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) om het formaat van uw gateway te wijzigen.

Het volgende PowerShell-voorbeeld toont een gateway SKU die wordt aangepast aan VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

U ook het formaat van een gateway in de Azure-portal wijzigen door naar de **configuratiepagina** voor uw virtuele netwerkgateway te gaan en een andere SKU te selecteren in de vervolgkeuzelijst.
