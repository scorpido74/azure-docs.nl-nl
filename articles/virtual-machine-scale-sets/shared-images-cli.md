---
title: Gedeelde VM-afbeeldingen gebruiken om een schaalset in Azure te maken
description: Meer informatie over het gebruik van de Azure CLI om gedeelde VM-afbeeldingen te maken die u gebruiken voor het implementeren van virtuele machineschaalsets in Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 83b3d5c904a65b28482acf8b685c939493c8c03b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276269"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Gedeelde afbeeldingen maken en gebruiken voor virtuele machineschaalsets met de Azure CLI 2.0

Wanneer u een schaalset maakt, geeft u een installatiekopie op die moet worden gebruikt wanneer de VM-exemplaren zijn geïmplementeerd. [Gedeelde afbeeldingsgalerieën](shared-image-galleries.md) vereenvoudigt het delen van aangepaste afbeeldingen in uw hele organisatie sterk. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. Met de gedeelde afbeeldingsgalerie u uw aangepaste VM-afbeeldingen delen met anderen in uw organisatie, binnen of tussen regio's, binnen een AAD-tenant. Kies in welke afbeeldingen u wilt delen, in welke regio's u ze beschikbaar wilt maken en met wie u ze wilt delen. U meerdere galerieën maken, zodat u op logischmogelijke wijze gedeelde afbeeldingen groeperen. De galerie is een bron op het hoogste niveau die volledige role-based access control (RBAC) biedt. Afbeeldingen kunnen worden geversioneerd en u ervoor kiezen om elke afbeeldingsversie te repliceren naar een andere set Azure-regio's. De galerie werkt alleen met Beheerde afbeeldingen. 

>[!NOTE]
> Dit artikel loopt door het proces van het gebruik van een algemeen beheerde afbeelding. Het maken van een schaalset op basis van een gespecialiseerde VM-installatiekopie wordt niet ondersteund.


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Een schaalset maken op basis van de aangepaste VM-installatiekopie
Maak een schaalset met [`az vmss create`](/cli/azure/vmss#az-vmss-create). In plaats van een platforminstallatiekopie, zoals *UbuntuLTS* of *CentOS*, geeft u de naam van uw aangepaste VM-installatiekopie op. In het volgende voorbeeld wordt een schaalset gemaakt met de naam *myScaleSet*, die gebruikmaakt van de aangepaste installatiekopie met de naam *myImage* uit de vorige stap:

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
Als u uw schaalset en extra resources wilt verwijderen, verwijdert u de brongroep en al de bronnen met [de AZ-groep verwijderen](/cli/azure/group). De parameter `--no-wait` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid. De parameter `--yes` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Volgende stappen

U ook bronnen voor Shared Image Gallery maken met sjablonen. Er zijn verschillende Azure Quickstart-sjablonen beschikbaar: 

- [Een gedeelde afbeeldingsgalerie maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een afbeeldingsdefinitie maken in een gedeelde afbeeldingsgalerie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een afbeeldingsversie maken in een gedeelde afbeeldingsgalerie](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken op basis van afbeeldingsversie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Als u problemen ondervindt, u [problemen oplossen met gedeelde afbeeldingsgalerieën.](troubleshooting-shared-images.md)
