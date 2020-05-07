---
title: Een virtuele machine maken op basis van een gespecialiseerde installatie kopie versie met behulp van de Azure CLI
description: Maak een virtuele machine met behulp van een gespecialiseerde installatie kopie versie in een galerie met gedeelde afbeeldingen met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 1ccf03deee2a2f72c1eb2008e1acc5bf67d16447
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796770"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Een virtuele machine maken met behulp van een gespecialiseerde installatie kopie versie met de Azure CLI

Maak een virtuele machine op basis van een [gespecialiseerde afbeeldings versie](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) die is opgeslagen in een galerie met gedeelde afbeeldingen. Zie [een virtuele machine maken op basis van een gegeneraliseerde](vm-generalized-image-version-cli.md)installatie kopie, als u een virtuele machine wilt maken met behulp van een gegeneraliseerde installatie kopie versie.

Vervang resource namen naar behoefte in dit voor beeld. 

De afbeeldings definities in een galerie weer geven met behulp van [AZ sig Image List-definitie lijst](/cli/azure/sig/image-definition#az-sig-image-definition-list) om de naam en id van de definities weer te geven.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Maak de virtuele machine met [AZ VM Create](/cli/azure/vm#az-vm-create) met behulp van de--gespecialiseerde para meter om aan te geven dat de afbeelding een gespecialiseerde installatie kopie is. 

Gebruik de definitie-ID van `--image` de installatie kopie om de virtuele machine te maken op basis van de meest recente versie van de installatie kopie die beschikbaar is. U kunt de virtuele machine ook maken op basis van een specifieke versie door de versie-ID `--image`voor de installatie kopie op te geven voor. 

In dit voor beeld maken we een VM op basis van de nieuwste versie van de *myImageDefinition* -installatie kopie.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Volgende stappen
Met [Azure Image Builder (preview)](./linux/image-builder-overview.md) kunt u het maken van de installatie kopie versie automatiseren, maar u kunt deze zelfs gebruiken om [een nieuwe installatie kopie versie te maken op basis van een bestaande versie van de installatie kopie](./linux/image-builder-gallery-update-image-version.md). 

U kunt ook een resource voor de galerie met gedeelde afbeeldingen maken met behulp van sjablonen. Er zijn verschillende Azure Quick Start-sjablonen beschikbaar: 

- [Een gedeelde installatiekopiegalerie maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een installatiekopiedefinitie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een installatiekopieversie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken van een installatiekopieversie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


