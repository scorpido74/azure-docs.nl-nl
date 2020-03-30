---
title: Azure Firewall SNAT privé-IP-adresbereiken
description: U privébereiken voor IP-adressen configureren, zodat de firewall geen SNAT-verkeer naar die IP-adressen heeft.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064816"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall SNAT privé-IP-adresbereiken

Azure Firewall is niet snat met netwerkregels wanneer het doel-IP-adres zich in een privé-IP-adresbereik bevindt per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Toepassingsregels worden altijd toegepast met behulp van een [transparante proxy,](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) ongeacht het doel-IP-adres.

Als uw organisatie een openbaar IP-adresbereik gebruikt voor privénetwerken, wordt het verkeer naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet gesind. U Azure Firewall echter configureren om **niet** uw openbare IP-adresbereik van SNAT te gebruiken.

## <a name="configure-snat-private-ip-address-ranges"></a>SNAT-privé-IP-adresbereiken configureren

U Azure PowerShell gebruiken om een IP-adresbereik op te geven dat de firewall niet snat.

### <a name="new-firewall"></a>Nieuwe firewall

Voor een nieuwe firewall is de opdracht Azure PowerShell:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges wordt uitgebreid naar de huidige standaardwaarden op Azure Firewall, terwijl de andere bereiken worden toegevoegd.

Zie [Nieuw-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0)voor meer informatie.

### <a name="existing-firewall"></a>Bestaande firewall

Als u een bestaande firewall wilt configureren, gebruikt u de volgende Azure PowerShell-opdrachten:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Sjablonen

U het volgende `additionalProperties` aan de sectie toevoegen:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren en configureren van een Azure Firewall](tutorial-firewall-deploy-portal.md).