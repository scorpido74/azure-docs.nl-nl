---
title: CLI gebruiken om Azure Spot VM's te implementeren
description: Meer informatie over het gebruik van de CLI om Azure Spot VM's te implementeren om kosten te besparen.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 3f341271c208cc56a704c836433c33af0129a4ac
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758375"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>SpotVM's implementeren met de Azure CLI

Met Behulp van [Azure Spot VM's](spot-vms.md) u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kostenbesparing. Op elk moment waarop Azure de capaciteit terug nodig heeft, wordt spotvm's van Spot v.s. Daarom zijn Spot VM's ideaal voor workloads die onderbrekingen kunnen verwerken, zoals batchverwerkingstaken, dev/testomgevingen, grote compute workloads en meer.

De prijzen voor spotVM's zijn variabel, gebaseerd op regio en SKU. Zie VM-prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. 

Je hebt de mogelijkheid om een maximale prijs die u bereid bent te betalen, per uur, voor de VM. De maximale prijs voor een Spot VM kan worden ingesteld in Amerikaanse dollars (USD), met behulp van maximaal 5 decimalen. De waarde `0.98765`is bijvoorbeeld een maximale prijs van $ 0,98765 USD per uur. Als u de maximale `-1`prijs instelt, wordt de VM niet uitgezet op basis van de prijs. De prijs voor de VM is de huidige prijs voor Spot of de prijs voor een standaard VM, die ooit minder is, zolang er capaciteit en quotum beschikbaar is. Zie [Spot VM's - Prijzen](spot-vms.md#pricing)voor meer informatie over het instellen van de max.

Het proces om een VM met Spot te maken met de Azure CLI is hetzelfde als beschreven in het [quickstart-artikel.](/azure/virtual-machines/linux/quick-create-cli) Voeg gewoon de parameter '--priority Spot' `-1`toe en geef een max.


## <a name="install-azure-cli"></a>Azure CLI installeren

Als u Spot VM's wilt maken, moet u de Azure CLI-versie 2.0.74 of hoger uitvoeren. Voer **az --version** uit om de versie op te vragen. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). 

Meld u aan bij Azure met [az-aanmelding](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Een spot-vm maken

In dit voorbeeld ziet u hoe u een Linux Spot VM implementeert die niet wordt uitgezet op basis van de prijs. 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

Nadat de vm is gemaakt, u de maximale factureringsprijs voor alle VM's in de resourcegroep bekijken.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Volgende stappen**

U ook een spot-vm maken met [Azure PowerShell](../windows/spot-powershell.md) of een [sjabloon.](spot-template.md)

Zie [Foutcodes](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)als u een fout tegenkomt.
