---
title: Gedeelde VM-installatie kopieën gebruiken om een schaalset in azure te maken
description: Meer informatie over hoe u de Azure CLI gebruikt om gedeelde VM-installatie kopieën te maken die u kunt gebruiken voor het implementeren van schaal sets voor virtuele machines in Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 83b3d5c904a65b28482acf8b685c939493c8c03b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276269"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Gedeelde installatie kopieën maken en gebruiken voor schaal sets voor virtuele machines met Azure CLI 2,0

Wanneer u een schaalset maakt, geeft u een installatiekopie op die moet worden gebruikt wanneer de VM-exemplaren zijn geïmplementeerd. Met de [galerieën met gedeelde afbeeldingen](shared-image-galleries.md) wordt het delen van aangepaste afbeeldingen in uw organisatie aanzienlijk vereenvoudigd. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. Met de galerie voor gedeelde afbeeldingen kunt u uw aangepaste VM-installatie kopieën delen met anderen in uw organisatie, binnen of tussen verschillende regio's binnen een AAD-Tenant. Kies welke installatie kopieën u wilt delen, in welke regio's u ze beschikbaar wilt maken en met wie u wilt delen. U kunt meerdere galerieën maken zodat u gedeelde installatie kopieën logisch kunt groeperen. De galerie is een resource op het hoogste niveau die volledige op rollen gebaseerd toegangs beheer (RBAC) biedt. Installatie kopieën kunnen versie nummer hebben en u kunt ervoor kiezen om elke installatie kopie versie te repliceren naar een andere set Azure-regio's. De galerie werkt alleen met beheerde installatie kopieën. 

>[!NOTE]
> In dit artikel wordt stapsgewijs uitgelegd hoe u een gegeneraliseerde beheerde installatie kopie gebruikt. Het maken van een schaalset op basis van een gespecialiseerde VM-installatiekopie wordt niet ondersteund.


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Een schaalset maken op basis van de aangepaste VM-installatiekopie
Een schaalset maken met [`az vmss create`](/cli/azure/vmss#az-vmss-create). In plaats van een platforminstallatiekopie, zoals *UbuntuLTS* of *CentOS*, geeft u de naam van uw aangepaste VM-installatiekopie op. In het volgende voorbeeld wordt een schaalset gemaakt met de naam *myScaleSet*, die gebruikmaakt van de aangepaste installatiekopie met de naam *myImage* uit de vorige stap:

```azurecli-interactive
az vmss create \
  -g myGalleryRG \
  -n myScaleSet \
  --image "/subscriptions/<subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
  --admin-username azureuser \
  --generate-ssh-keys
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.


[!INCLUDE [virtual-machines-common-gallery-list-cli](../../includes/virtual-machines-common-gallery-list-cli.md)]


## <a name="clean-up-resources"></a>Resources opschonen
Als u de schaalset en aanvullende resources wilt verwijderen, verwijdert u de resourcegroep en alle bijbehorende resources met [az group delete](/cli/azure/group). De parameter `--no-wait` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid. De parameter `--yes` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Volgende stappen

U kunt ook een resource voor de galerie met gedeelde afbeeldingen maken met behulp van sjablonen. Er zijn verschillende Azure Quick Start-sjablonen beschikbaar: 

- [Een galerie met gedeelde afbeeldingen maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een definitie van een installatie kopie maken in een galerie met gedeelde afbeeldingen](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een installatie kopie versie maken in een galerie met gedeelde afbeeldingen](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken op basis van de installatie kopie versie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Als u problemen ondervindt, kunt u [problemen met gedeelde afbeeldings galerieën oplossen](troubleshooting-shared-images.md).
