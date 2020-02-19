---
title: Persoonlijke IP-adresbereiken van Azure Firewall SNAT
description: U kunt particuliere bereiken voor IP-adressen configureren, zodat de firewall geen verkeer naar die IP-adressen overbelasting.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/09/2020
ms.author: victorh
ms.openlocfilehash: b190d07ceadea43ca572f5eb5be3eeeafa616971
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444457"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Persoonlijke IP-adresbereiken van Azure Firewall SNAT

Azure Firewall geen SNAT wanneer het doel-IP-adres zich in een privé-IP-adres bereik bevindt op het niveau van de [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Als uw organisatie gebruikmaakt van een openbaar IP-adres bereik voor particuliere netwerken, verkrijgt Azure Firewall het verkeer naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet. U kunt Azure Firewall echter zodanig configureren dat uw open bare IP-adres bereik **niet** wordt gesnat.

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
$azfw.PrivateRange = @(“IANAPrivateRanges”,“IPRange1”, “IPRange2”)
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Sjablonen

U kunt het volgende toevoegen aan de sectie `additionalProperties`:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren en configureren van een Azure firewall](tutorial-firewall-deploy-portal.md).