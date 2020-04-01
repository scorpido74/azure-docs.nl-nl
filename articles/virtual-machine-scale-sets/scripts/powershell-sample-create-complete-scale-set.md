---
title: Azure PowerShell-voorbeelden - Maak een complete virtuele machineschaalset
description: Met dit script wordt een virtuele machineschaalset gemaakt met Windows Server 2016, waarbij afzonderlijke resources zijn geconfigureerd en gemaakt.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: sample
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 522dc1e24b8d3c4abd7b24dd3e0901e02b78992d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76278710"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Een volledige virtuele-machineschaalset maken met PowerShell

Met dit script maakt u een virtuele-machineschaalset waarop Windows Server 2016 wordt uitgevoerd. Afzonderlijke resources worden geconfigureerd en gemaakt, in plaats van de [ingebouwde opties voor het maken van resources die hier beschikbaar zijn in Nieuw-AzVmss.](powershell-sample-create-simple-scale-set.md) Nadat het script is uitgevoerd, hebt u via RDP toegang tot de virtuele machine.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep, de schaalset en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script
In dit script worden de volgende opdrachten gebruikt om de implementatie te maken. Elk item in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Hiermee maakt u een subnetconfiguratie. Deze configuratie wordt gebruikt bij het maken van het virtueel netwerk. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Hiermee maakt u een front-endconfiguratie voor een IP-adres voor een load balancer. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Hiermee maakt u een back-endconfiguratie voor een adresgroep voor een load balancer. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Hiermee maakt u binnenkomende NAT-regelconfiguratie voor een load balancer. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Hiermee maakt u een load balancer. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Hiermee maakt u een testconfiguratie voor een load balancer. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Hiermee maakt u een regelconfiguratie voor een load balancer. |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | Werk de load balancer bij met de opgegeven gegevens. |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | Maak een IP-configuratie voor de VM-exemplaren van de schaalset. De VM-exemplaren zijn verbonden met de back-endpool van de load balancer, de NAT-pool en het virtueel-netwerksubnet. |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | Hiermee wordt een schaalsetconfiguratie gemaakt. Deze configuratie bevat informatie zoals het aantal VM-exemplaren dat moet worden gemaakt, de VM-SKU (grootte) en de upgradebeleidsmodus. De configuratie wordt toegevoegd aan andere cmdlets en wordt gebruikt tijdens het maken van de schaalset. |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Definieer de installatiekopie die moet worden gebruikt voor de VM-exemplaren en voeg deze toe aan de schaalsetconfiguratie. |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Definieer de gebruikersnaam- en wachtwoordreferenties van de beheerder en het VM-naamgevingsvoorvoegsel. Voeg deze waarden toe aan de schaalsetconfiguratie. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | Voeg de virtuele-netwerkinterface toe aan de VM-exemplaren op basis van de IP-configuratie. Voeg deze waarden toe aan de schaalsetconfiguratie. |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | Maak de schaalaanpassingsset op basis van de informatie in de schaalsetconfiguratie. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor virtuele-machineschaalsets](../powershell-samples.md).
