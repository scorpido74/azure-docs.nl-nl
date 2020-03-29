---
title: Galerijafbeeldingen delen tussen tenants in Azure
description: Meer informatie over het delen van VM-afbeeldingen in Azure-tenants met gedeelde galerieën.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: a29999102ad8a10d8965145b31a7d804675e0e57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276342"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>GalerieVM-afbeeldingen delen in Azure-tenants

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Een schaalset maken met Azure CLI

Meld u aan bij de serviceprincipal voor tenant 1 met behulp van de appID, de app-sleutel en de id van tenant 1. U kunt `az account show --query "tenantId"` gebruiken om de huurder ID's te krijgen indien nodig.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Meld u aan bij de serviceprincipal voor tenant 2 met behulp van de appID, de app-sleutel en de id van tenant 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Maak de schaalset. Vervang de informatie in het voorbeeld door die van u.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt, u [problemen oplossen met gedeelde afbeeldingsgalerieën.](troubleshooting-shared-images.md)
