---
title: Galerijafbeeldingen delen tussen tenants in Azure
description: Meer informatie over het delen van VM-afbeeldingen in Azure-tenants met gedeelde galerieën.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 4259ca01dbe45463b73cf1ec1c620c3921ab9459
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758464"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>GalerieVM-afbeeldingen delen in Azure-tenants

Met Gedeelde galerijen u afbeeldingen delen met RBAC. U RBAC gebruiken om afbeeldingen te delen binnen uw tenant, en zelfs met personen buiten uw huurder. Zie [de galerie Delen](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery)voor meer informatie over deze eenvoudige optie voor delen.

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> U de portal niet gebruiken om een VM te implementeren vanuit een afbeelding in een andere azure-tenant. Als u een VM wilt maken op basis van een afbeelding die wordt gedeeld tussen tenants, moet u azure CLI of [Powershell](../windows/share-images-across-tenants.md)gebruiken.

## <a name="create-a-vm-using-azure-cli"></a>Een VM maken met Azure CLI

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

Maak de VM. Vervang de informatie in het voorbeeld door die van u.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt, u [problemen oplossen met gedeelde afbeeldingsgalerieën.](troubleshooting-shared-images.md)