---
title: Virtuele-machine schaal sets configureren met een bestaande Azure Load Balancer-Azure CLI
description: Meer informatie over het configureren van een schaalset voor virtuele machines met een bestaande Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: a7f44a21dd404c556d6f3d8444fa70583cd71c57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80349736"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Een schaalset voor virtuele machines configureren met een bestaande Azure Load Balancer met behulp van de Azure CLI

In dit artikel leert u hoe u een schaalset voor virtuele machines kunt configureren met een bestaande Azure Load Balancer. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement.
- Een bestaande standaard SKU load balancer in het abonnement waarin de schaalset van de virtuele machine wordt geïmplementeerd.
- Een Azure-Virtual Network voor de schaalset van de virtuele machine.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Als u ervoor kiest om de CLI lokaal te gebruiken, moet u voor dit artikel een versie van de Azure CLI-versie 2.0.28 of hoger hebben geïnstalleerd. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Aanmelden bij Azure CLI

Meld u aan bij Azure.

```azurecli-interactive
az login
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Een schaalset voor virtuele machines implementeren met bestaande load balancer

Vervang de waarden tussen vier Kante haken door de namen van de resources in uw configuratie.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

In het onderstaande voor beeld wordt een schaalset voor virtuele machines geïmplementeerd met:

- Virtuele-machine schaalset met de naam **myVMSS**
- Azure Load Balancer met de naam **myLoadBalancer**
- Back-end van Load Balancer-groep met de naam **myBackendPool**
- Azure Virtual Network met de naam **myVnet**
- Subnet met de naam **mySubnet**
- Resource groep met de naam **myResourceGroup**
- Ubuntu-Server installatie kopie voor de schaalset voor virtuele machines

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> Nadat de schaalset is gemaakt, kan de backend-poort niet worden gewijzigd voor een taakverdelings regel die wordt gebruikt door een status test van de load balancer. Als u de poort wilt wijzigen, kunt u de status test verwijderen door de schaalset voor virtuele Azure-machines bij te werken, de poort bij te werken en de status test vervolgens opnieuw te configureren.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een schaalset voor virtuele machines geïmplementeerd met een bestaande Azure Load Balancer.  Zie voor meer informatie over virtuele-machine schaal sets en load balancer:

- [Wat is Azure Load Balancer?](load-balancer-overview.md)
- [Wat zijn schaalsets voor virtuele machines?](../virtual-machine-scale-sets/overview.md)
                                