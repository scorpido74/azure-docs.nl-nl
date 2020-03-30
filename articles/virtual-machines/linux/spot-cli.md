---
title: CLI gebruiken om Azure Spot VM's (Preview) te implementeren
description: Meer informatie over het gebruik van de CLI om Azure Spot VM's te implementeren om kosten te besparen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 110e935671ab1d640b2ff3dc26c203b262e999fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77163089"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>Voorbeeld: SpotVM's implementeren met de Azure CLI

Met Behulp van [Azure Spot VM's](spot-vms.md) u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kostenbesparing. Op elk moment waarop Azure de capaciteit terug nodig heeft, wordt spotvm's van Spot v.s. Daarom zijn Spot VM's ideaal voor workloads die onderbrekingen kunnen verwerken, zoals batchverwerkingstaken, dev/testomgevingen, grote compute workloads en meer.

De prijzen voor spotVM's zijn variabel, gebaseerd op regio en SKU. Zie VM-prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. 

Je hebt de mogelijkheid om een maximale prijs die u bereid bent te betalen, per uur, voor de VM. De maximale prijs voor een Spot VM kan worden ingesteld in Amerikaanse dollars (USD), met behulp van maximaal 5 decimalen. De waarde `0.98765`is bijvoorbeeld een maximale prijs van $ 0,98765 USD per uur. Als u de maximale `-1`prijs instelt, wordt de VM niet uitgezet op basis van de prijs. De prijs voor de VM is de huidige prijs voor Spot of de prijs voor een standaard VM, die ooit minder is, zolang er capaciteit en quotum beschikbaar is. Zie [Spot VM's - Prijzen](spot-vms.md#pricing)voor meer informatie over het instellen van de max.

Het proces om een VM met Spot te maken met de Azure CLI is hetzelfde als beschreven in het [quickstart-artikel.](/azure/virtual-machines/linux/quick-create-cli) Voeg gewoon de parameter '--priority Spot' `-1`toe en geef een max.

> [!IMPORTANT]
> Spotexemplaren staan momenteel in openbare preview.
> Deze preview-versie wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.
>



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
