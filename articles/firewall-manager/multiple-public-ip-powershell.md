---
title: Azure Firewall met meerdere open bare IP-adressen implementeren met behulp van Azure PowerShell
description: Een firewall met meerdere open bare IP-adressen implementeren om een virtuele hub te beveiligen
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 87af7f0f9b446fb3a54a600f61409c2cfc1a2494
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86189433"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses"></a>Een Azure Firewall met meerdere open bare IP-adressen implementeren

Als u een virtuele hub wilt beveiligen met Azure Firewall, kunt u de firewall met meerdere open bare IP-adressen implementeren met behulp van Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-firewall"></a>De firewall implementeren

Gebruik het volgende Azure PowerShell voor beeld om een Azure Firewall te implementeren met meerdere open bare IP-adressen om een virtuele hub te beveiligen.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID> 

$rgName = <resource group name> 
$vHub = Get-AzVirtualHub -Name <hub name> 
$vHubId = $vHub.Id 
$fwpips = New-AzFirewallHubPublicIpAddress -Count 3
$hubIpAddresses = New-AzFirewallHubIpAddress -PublicIPs $fwpips 
$fw = New-AzFirewall -Name <firewall name> -ResourceGroupName $rgName `
     -Location <location> -Sku AZFW_Hub -HubIPAddresses $hubIpAddresses `
     -VirtualHubId $vHubId 
```

### <a name="delete-a-public-ip-address"></a>Een openbaar IP-adres verwijderen

U kunt Azure PowerShell gebruiken om een openbaar IP-adres te verwijderen uit een Azure Firewall. In het volgende voor beeld wordt één openbaar IP-adres uit een firewall verwijderd. Het begint met drie open bare IP-adressen.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID>

$azfw = get-azfirewall -Name <firewall name> -ResourceGroupName <resource group name>
$ip1 = New-AzFirewallPublicIpAddress -Address <first ip address to keep>
$ip2 = New-AzFirewallPublicIpAddress -Address <second ip address to keep>
$ipAddresses = $ip1,$ip2
$azfw.HubIPAddresses.publicIPs.Addresses = $ipAddresses
$azfw.HubIPAddresses.publicIPs.count = 2
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="next-steps"></a>Volgende stappen

[Wat is een beveiligde virtuele hub?](secured-virtual-hub.md)