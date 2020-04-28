---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ec3ecdafb8e475f5f13372789528612ccd7b8b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "66226015"
---
## <a name="using-rbac-to-share-images"></a>RBAC gebruiken om installatie kopieën te delen

U kunt afbeeldingen in abonnementen delen met behulp van op rollen gebaseerde Access Control (RBAC). Elke gebruiker met lees machtigingen voor een installatie kopie versie, zelfs via abonnementen, kan een virtuele machine implementeren met de versie van de installatie kopie.

Zie [toegang beheren met RBAC en Azure cli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)voor meer informatie over het delen van resources met RBAC.


## <a name="list-information"></a>Gegevens weer geven

De locatie, status en andere informatie over de beschik bare afbeeldings galerieën ophalen met de [lijst AZ sig](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

De afbeeldings definities in een galerie weer geven, met inbegrip van informatie over het type besturings systeem en de status, met behulp van de [lijst met AZ sig image-definition](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

De versies van de gedeelde installatie kopieën in een galerie weer geven met behulp van de [lijst AZ sig image-version](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

De ID van een afbeeldings versie ophalen met behulp van [AZ sig installatie kopie-versie weer geven](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```