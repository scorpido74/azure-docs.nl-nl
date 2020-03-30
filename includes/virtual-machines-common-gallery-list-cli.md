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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66226015"
---
## <a name="using-rbac-to-share-images"></a>RBAC gebruiken om afbeeldingen te delen

U afbeeldingen delen tussen abonnementen met RBAC (Role-Based Access Control). Elke gebruiker die leesmachtigingen voor een afbeeldingsversie heeft, zelfs voor verschillende abonnementen, kan een virtuele machine implementeren met behulp van de afbeeldingsversie.

Zie [Toegang beheren met RBAC en Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)voor meer informatie over het delen van resources met RBAC.


## <a name="list-information"></a>Lijstgegevens

Krijg de locatie, status en andere informatie over de beschikbare beeldgalerijen met behulp van [az sig lijst](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Vermeld de afbeeldingsdefinities in een galerie, inclusief informatie over het type en de status van het besturingssysteem, met behulp van [de lijst met az sig-afbeeldingsdefinitie](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Vermeld de versies van gedeelde afbeeldingen in een galerie met de [lijst met az sig-afbeeldingen.](/cli/azure/sig/image-version#az-sig-image-version-list)

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Krijg de ID van een afbeeldingversie met behulp van [az sig image-versie laten zien](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```