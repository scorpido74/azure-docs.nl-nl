---
title: Een beheerde schijf maken op basis van een installatie kopie versie
description: Een beheerde schijf maken op basis van een installatie kopie-versie in een galerie met gedeelde afbeeldingen.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/06/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 35edcfb4bdb0715245f4a3190fb22638b1162429
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370981"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Een beheerde schijf maken op basis van een installatie kopie versie

Als dat het geval is, kunt u een beheerde schijf maken op basis van een installatie kopie-versie die is opgeslagen in een galerie met gedeelde afbeeldingen.


## <a name="cli"></a>CLI

Stel de `source` variabele in op de id van de installatie kopie en gebruik [AZ Disk Create](/cli/azure/disk#az_disk_create) om de beheerde schijf te maken. 


U kunt een lijst met installatie kopieën weer geven met behulp van de [lijst met AZ sig-installatie kopieën](/cli/azure/sig/image-version#az_sig_image_version_list). In dit voor beeld zoeken we naar alle installatie kopieën die deel uitmaken van de definitie van de *myImageDefinition* -installatie kopie in de galerie met installatie kopieën van *myGallery* .

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```


In dit voor beeld maken we een beheerde schijf met de naam *myManagedDisk*, in de regio *Oost* , in een resource groep met de naam *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDisk --gallery-image-reference $source 
```

Als de schijf een gegevens schijf is, voegt `--gallery-image-reference-lun` u deze toe om het LUN op te geven.

## <a name="powershell"></a>PowerShell

U kunt alle versies van de installatie kopie weer geven met behulp van [Get-AzResource](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```



Wanneer u alle informatie hebt die u nodig hebt, kunt u [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) gebruiken om de versie van de bron installatie kopie die u wilt gebruiken, op te halen en toe te wijzen aan een variabele. In dit voor beeld wordt de `1.0.0` installatie kopie versie van de `myImageDefinition` definitie in de `myGallery` bron galerie in de `myResourceGroup` resource groep opgehaald.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Stel enkele variabelen voor de schijf informatie in.

```azurepowershell-interactive
$location = "East US"
$resourceGroup = "myResourceGroup"
$diskName = "myDisk"
```

Maak een schijf configuratie en vervolgens de schijf met behulp van de versie-ID van de bron installatie kopie. Voor de is `-GalleryImageReference` LUN alleen vereist als de bron een gegevens schijf is.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location $location `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=1}
```

Maak de schijf.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName $resourceGroup `
   -DiskName $diskName
```

## <a name="next-steps"></a>Volgende stappen

U kunt ook een installatie kopie versie maken op basis van een beheerde schijf met behulp van de [Azure cli](image-version-managed-image-cli.md) of [Power shell](image-version-managed-image-powershell.md).


