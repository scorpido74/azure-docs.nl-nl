---
title: Virtuele machineschaalset configureren met een bestaande Azure Load Balancer - Azure CLI
description: Meer informatie over het configureren van een virtuele machineschaalset met een bestaande Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: a7f44a21dd404c556d6f3d8444fa70583cd71c57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349736"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Een virtuele machineschaalset configureren met een bestaande Azure Load Balancer met behulp van de Azure CLI

In dit artikel leert u hoe u een virtuele machineschaalset configureert met een bestaande Azure Load Balancer. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement.
- Een bestaande standaard sku load balancer in het abonnement waar de virtuele machine schaal set zal worden ingezet.
- Een Azure Virtual Network voor de virtuele machineschaalset.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Als u ervoor kiest de CLI lokaal te gebruiken, moet in dit artikel een versie van de Azure CLI-versie 2.0.28 of hoger zijn geïnstalleerd. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Aanmelden bij Azure CLI

Meld u aan bij Azure.

```azurecli-interactive
az login
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Een virtuele machineschaalset implementeren met bestaande load balancer

Vervang de waarden tussen haakjes door de namen van de bronnen in uw configuratie.

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

In het onderstaande voorbeeld wordt een virtuele machineschaalset geïmplementeerd met:

- Virtuele machineschaalset met de naam **myVMSS**
- Azure Load Balancer met de naam **myLoadBalancer**
- Load balancer backend pool named **myBackendPool**
- Azure Virtual Network met de naam **myVnet**
- Subnet met de naam **mySubnet**
- Resourcegroep met de naam **myResourceGroup**
- Ubuntu Server-afbeelding voor de virtuele machineschaalset

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
> Nadat de schaalset is gemaakt, kan de backendpoort niet worden gewijzigd voor een regel voor het balanceren van de last die wordt gebruikt door een statussonde van de load balancer. Als u de poort wilt wijzigen, u de statussonde verwijderen door de azure-virtuele machineschaalset bij te werken, de poort bij te werken en vervolgens de statussonde opnieuw te configureren.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een virtuele machineschaalset geïmplementeerd met een bestaande Azure Load Balancer.  Zie voor meer informatie over virtuele machineschaalsets en load balancer:

- [Wat is Azure Load Balancer?](load-balancer-overview.md)
- [Wat zijn schaalsets voor virtuele machines?](../virtual-machine-scale-sets/overview.md)
                                