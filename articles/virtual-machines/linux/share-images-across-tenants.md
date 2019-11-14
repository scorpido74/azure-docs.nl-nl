---
title: Galerie-installatie kopieën delen via tenants in azure
description: Meer informatie over het delen van VM-installatie kopieën in azure-tenants met behulp van de galerie met gedeelde afbeeldingen.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 18337620a6f9506e402149909667026e4a8ba7eb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034975"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Galerie-VM-installatie kopieën delen in azure-tenants

Met de galerie met gedeelde afbeeldingen kunt u afbeeldingen delen met RBAC. U kunt RBAC gebruiken om installatie kopieën te delen binnen uw Tenant, en zelfs voor personen buiten uw Tenant. Zie de [Galerie delen](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery)voor meer informatie over deze eenvoudige optie voor delen.

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> U kunt de portal niet gebruiken om een virtuele machine te implementeren op basis van een installatie kopie in een andere Azure-Tenant. Als u een virtuele machine wilt maken op basis van een installatie kopie die tussen tenants wordt gedeeld, moet u de Azure CLI of [Power shell](../windows/share-images-across-tenants.md)gebruiken.

## <a name="create-a-vm-using-azure-cli"></a>Een virtuele machine maken met behulp van Azure CLI

Meld u aan bij de service-principal voor Tenant 1 met behulp van de appID, de app-sleutel en de ID van Tenant 1. U kunt `az account show --query "tenantId"` gebruiken om de Tenant-Id's, indien nodig, op te halen.

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

Maak de virtuele machine. Vervang de informatie in het voor beeld door uw eigen.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt, kunt u [problemen met gedeelde afbeeldings galerieën oplossen](troubleshooting-shared-images.md).