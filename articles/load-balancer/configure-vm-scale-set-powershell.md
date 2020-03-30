---
title: Virtuele machineschaalset configureren met een bestaande Azure Load Balancer - Azure PowerShell
description: Meer informatie over het configureren van een virtuele machineschaalset met een bestaande Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/26/2020
ms.openlocfilehash: 0db09083a2197ce72e6d6eed2381b0308239586e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349997"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Een virtuele machineschaalset configureren met een bestaande Azure Load Balancer met Azure PowerShell

In dit artikel leert u hoe u een virtuele machineschaalset configureert met een bestaande Azure Load Balancer. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement.
- Een bestaande standaard sku load balancer in het abonnement waar de virtuele machine schaal set zal worden ingezet.
- Een Azure Virtual Network voor de virtuele machineschaalset.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Aanmelden bij Azure CLI

Meld u aan bij Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Een virtuele machineschaalset implementeren met bestaande load balancer

Vervang de waarden tussen haakjes door de namen van de bronnen in uw configuratie.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

In het onderstaande voorbeeld wordt een virtuele machineschaalset geïmplementeerd met:

- Virtuele machineschaalset met de naam **myVMSS**
- Azure Load Balancer met de naam **myLoadBalancer**
- Load balancer backend pool named **myBackendPool**
- Azure Virtual Network met de naam **myVnet**
- Subnet met de naam **mySubnet**
- Resourcegroep met de naam **myResourceGroup**

```azureppowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> Nadat de schaalset is gemaakt, kan de backendpoort niet worden gewijzigd voor een regel voor het balanceren van de last die wordt gebruikt door een statussonde van de load balancer. Als u de poort wilt wijzigen, u de statussonde verwijderen door de azure-virtuele machineschaalset bij te werken, de poort bij te werken en vervolgens de statussonde opnieuw te configureren.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een virtuele machineschaalset geïmplementeerd met een bestaande Azure Load Balancer.  Zie voor meer informatie over virtuele machineschaalsets en load balancer:

- [Wat is Azure Load Balancer?](load-balancer-overview.md)
- [Wat zijn schaalsets voor virtuele machines?](../virtual-machine-scale-sets/overview.md)
                                