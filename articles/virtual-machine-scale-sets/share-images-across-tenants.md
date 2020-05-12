---
title: Galerie-installatie kopieën delen via tenants in azure
description: Meer informatie over het delen van VM-installatie kopieën in azure-tenants met behulp van de galerie met gedeelde afbeeldingen.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 5b86335ab8bcc3af75dbd8af39e3d913f2461e58
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119838"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Galerie-VM-installatie kopieën delen in azure-tenants

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Een schaalset maken met Azure CLI

Meld u aan bij de service-principal voor Tenant 1 met behulp van de appID, de app-sleutel en de ID van Tenant 1. U kunt gebruiken `az account show --query "tenantId"` voor het ophalen van de Tenant-id's, indien nodig.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Meld u aan bij de service-principal voor Tenant 2 met behulp van de appID, de app-sleutel en de ID van Tenant 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Maak de schaalset. Vervang de informatie in het voor beeld door uw eigen.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt, kunt u [problemen met gedeelde afbeeldings galerieën oplossen](troubleshooting-shared-images.md).
