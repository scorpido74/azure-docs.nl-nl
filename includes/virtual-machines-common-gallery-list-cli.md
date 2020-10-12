---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 0e8972b1b2bfaac12baee1ea823429749ed70461
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82792748"
---
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