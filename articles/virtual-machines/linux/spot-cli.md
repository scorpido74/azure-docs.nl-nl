---
title: CLI gebruiken voor het implementeren van Azure spot-Vm's (preview-versie)
description: Meer informatie over hoe u de CLI kunt gebruiken om Azure spot-Vm's te implementeren om kosten te besparen.
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
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 0635be14937a3688792f65208dcb9d482b9e6d44
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781993"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>Voor beeld: spot-Vm's implementeren met behulp van Azure CLI

Met [Azure spot vm's](spot-vms.md) kunt u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kosten besparing. Op elk moment dat Azure de capaciteit nodig heeft, verwijdert de Azure-infra structuur spot Vm's. Daarom zijn de virtuele machines geschikt voor werk belastingen die onderbrekingen kunnen afhandelen, zoals batch verwerkings taken, ontwikkel-en test omgevingen, grootschalige werk belastingen en meer.

Prijzen voor spot Vm's zijn variabel, op basis van de regio en de SKU. Zie prijzen voor VM'S voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. 

U hebt de mogelijkheid om een maximum prijs voor de virtuele machine in te stellen die u wilt betalen, per uur. De maximale prijs voor een steun-VM kan worden ingesteld in Amerikaanse dollars (USD), met Maxi maal vijf decimalen. De waarde `0.98765`bijvoorbeeld een maximum prijs van $0,98765 USD per uur. Als u de maximum prijs instelt op `-1`, wordt de VM niet verwijderd op basis van de prijs. De prijs voor de virtuele machine is de huidige prijs voor steun of de prijs voor een standaard-VM, die ooit kleiner is, zolang er capaciteit en quota beschikbaar zijn. Zie [Spot vm's-prijzen](spot-vms.md#pricing)voor meer informatie over het instellen van de maximum prijs.

Het proces voor het maken van een virtuele machine met behulp van de Azure CLI is hetzelfde als in het Quick Start- [artikel](/azure/virtual-machines/linux/quick-create-cli). Voeg de para meter---Priority spot toe en geef een maximum prijs of `-1`op.

> [!IMPORTANT]
> Spot instanties zijn momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
>
> Voor het vroege deel van de open bare preview hebben spot instanties een vaste prijs, zodat er geen op prijzen gebaseerde verwijderingen zijn.


## <a name="install-azure-cli"></a>Azure CLI installeren

Als u een spot-Vm's wilt maken, moet u de Azure CLI-versie 2.0.74 of hoger uitvoeren. Voer **az --version** uit om de versie op te vragen. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). 

Meld u aan bij Azure met [AZ login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Een spot-VM maken

Dit voor beeld laat zien hoe u een Linux-spot-VM implementeert die niet wordt verwijderd op basis van de prijs. 

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

Nadat de VM is gemaakt, kunt u een query uitvoeren om de maximale facturerings prijs voor alle virtuele machines in de resource groep weer te geven.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Volgende stappen**

U kunt ook een spot-VM maken met behulp van [Azure PowerShell](../windows/spot-powershell.md) of een [sjabloon](spot-template.md).

Als er een fout optreedt, raadpleegt u [fout codes](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
