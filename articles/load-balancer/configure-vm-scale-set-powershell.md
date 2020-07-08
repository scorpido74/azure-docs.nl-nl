---
title: Virtuele-machine schaal sets configureren met een bestaande Azure Load Balancer-Azure PowerShell
description: Meer informatie over het configureren van een schaalset voor virtuele machines met een bestaande Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 858315008434f511e3adc0a91d591d924634fc39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809488"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Een schaalset voor virtuele machines configureren met een bestaande Azure Load Balancer met behulp van Azure PowerShell

In dit artikel leert u hoe u een schaalset voor virtuele machines kunt configureren met een bestaande Azure Load Balancer. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement.
- Een bestaande standaard SKU load balancer in het abonnement waarin de schaalset van de virtuele machine wordt geïmplementeerd.
- Een Azure-Virtual Network voor de schaalset van de virtuele machine.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Aanmelden bij Azure CLI

Meld u aan bij Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Een schaalset voor virtuele machines implementeren met bestaande load balancer

Vervang de waarden tussen vier Kante haken door de namen van de resources in uw configuratie.

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

In het onderstaande voor beeld wordt een schaalset voor virtuele machines geïmplementeerd met:

- Virtuele-machine schaalset met de naam **myVMSS**
- Azure Load Balancer met de naam **myLoadBalancer**
- Back-end van Load Balancer-groep met de naam **myBackendPool**
- Azure Virtual Network met de naam **myVnet**
- Subnet met de naam **mySubnet**
- Resource groep met de naam **myResourceGroup**

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
> Nadat de schaalset is gemaakt, kan de backend-poort niet worden gewijzigd voor een taakverdelings regel die wordt gebruikt door een status test van de load balancer. Als u de poort wilt wijzigen, kunt u de status test verwijderen door de schaalset voor virtuele Azure-machines bij te werken, de poort bij te werken en de status test vervolgens opnieuw te configureren.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een schaalset voor virtuele machines geïmplementeerd met een bestaande Azure Load Balancer.  Zie voor meer informatie over virtuele-machine schaal sets en load balancer:

- [Wat is Azure Load Balancer?](load-balancer-overview.md)
- [Wat zijn schaalsets voor virtuele machines?](../virtual-machine-scale-sets/overview.md)
                                