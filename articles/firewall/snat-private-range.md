---
title: Persoonlijke IP-adresbereiken van Azure Firewall SNAT
description: U kunt particuliere bereiken voor IP-adressen configureren, zodat de firewall geen verkeer naar die IP-adressen overbelasting.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064816"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Persoonlijke IP-adresbereiken van Azure Firewall SNAT

Azure Firewall niet wordt gesnat met netwerk regels wanneer het doel-IP-adres zich in een privé-IP-adres bereik per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918)bevindt. Toepassings regels worden altijd toegepast met behulp van een [transparante proxy](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) , ongeacht het doel-IP-adres.

Als uw organisatie gebruikmaakt van een openbaar IP-adres bereik voor particuliere netwerken, Azure Firewall SNATs het verkeer naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet. U kunt Azure Firewall echter zodanig configureren dat uw open bare IP-adres bereik **niet** wordt gesnat.

## <a name="configure-snat-private-ip-address-ranges"></a>Persoonlijke IP-adresbereiken voor het SNAT configureren

U kunt Azure PowerShell gebruiken om een IP-adres bereik op te geven dat niet door de firewall kan worden gesnat.

### <a name="new-firewall"></a>Nieuwe firewall

Voor een nieuwe firewall is de Azure PowerShell opdracht:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges wordt uitgebreid naar de huidige standaard waarden op Azure Firewall terwijl de andere bereiken hieraan worden toegevoegd.

Zie [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0)voor meer informatie.

### <a name="existing-firewall"></a>Bestaande firewall

Als u een bestaande firewall wilt configureren, gebruikt u de volgende Azure PowerShell-opdrachten:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Sjablonen

U kunt het volgende toevoegen aan de `additionalProperties` sectie:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren en configureren van een Azure firewall](tutorial-firewall-deploy-portal.md).