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
ms.openlocfilehash: d2a85f3947e9993e5d1853e45c6d03586a074cf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175998"
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

Als u de beschrijving van een galerie wilt bijwerken, gebruikt u [Update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

In dit voorbeeld ziet u hoe [u Update-AzGalleryImageDefinition kunt](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) gebruiken om de einddatum voor onze afbeeldingsdefinitie bij te werken.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

In dit voorbeeld ziet u hoe [u Update-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) gebruiken om uit te sluiten dat deze afbeeldingsversie als *de nieuwste* afbeelding wordt gebruikt.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u resources verwijderde, moet u beginnen met het laatste item in de geneste bronnen - de afbeeldingsversie. Zodra versies zijn verwijderd, u de afbeeldingsdefinitie verwijderen. U de galerie pas verwijderen als alle bronnen eronder zijn verwijderd.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

