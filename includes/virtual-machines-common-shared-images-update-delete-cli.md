---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8d0f9866864ca4b02ca6238be2ac44537a586c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176047"
---
## <a name="update-resources"></a>Resources bijwerken

Er zijn een aantal beperkingen op wat kan worden bijgewerkt. De volgende items kunnen worden bijgewerkt: 

Gedeelde afbeeldingsgalerie:
- Beschrijving

Afbeeldingsdefinitie:
- Aanbevolen vCPU's
- Aanbevolen geheugen
- Beschrijving
- Einddatum van het einde van de levensduur

Afbeeldingsversie:
- Aantal regionale replica's
- Doelregio's
- Uitsluiting van de laatste
- Einddatum van het einde van de levensduur

Als u replicagebieden wilt toevoegen, verwijdert u de door de bron beheerde afbeelding niet. De bronbeheerde afbeelding is nodig voor het repliceren van de afbeeldingsversie naar extra regio's. 

Update de beschrijving van een galerij met behulp van ([az sig update](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Werk de beschrijving van een beelddefinitie bij met behulp van [de az sig image-definition update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Werk een afbeeldingsversie bij om een regio toe te voegen om te repliceren met het gebruik van [de update van de AZ SIG-afbeeldingsversie](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Deze wijziging zal een tijdje duren als de afbeelding wordt gerepliceerd naar de nieuwe regio.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Resources verwijderen

U moet resources in omgekeerde volgorde verwijderen door eerst de afbeeldingsversie te verwijderen. Nadat u alle afbeeldingsversies hebt verwijderd, u de afbeeldingsdefinitie verwijderen. Nadat u alle afbeeldingsdefinities hebt verwijderd, u de galerie verwijderen. 

Verwijder een afbeeldingversie met behulp van [az sig image-versie verwijderen](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Een afbeeldingsdefinitie verwijderen met behulp van [het verwijderen van az sig-afbeeldingsdefinitie](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Een afbeeldingsgalerie verwijderen met [az sig delete](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
