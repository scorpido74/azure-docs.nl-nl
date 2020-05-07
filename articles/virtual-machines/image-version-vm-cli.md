---
title: Een installatiekopie van een VM maken
description: Meer informatie over het maken van een installatie kopie in een galerie met gedeelde afbeeldingen van een virtuele machine in Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: f53a6b63c744b0e3e41f7ad22270cd842da57674
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796575"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Een installatie kopie versie maken op basis van een virtuele machine in azure met behulp van Azure CLI

Als u een bestaande VM hebt die u wilt gebruiken om meerdere, identieke Vm's te maken, kunt u die VM gebruiken om een installatie kopie te maken in een galerie met gedeelde afbeeldingen met behulp van de Azure CLI. U kunt ook een installatie kopie maken op basis van een virtuele machine met behulp van [Azure PowerShell](image-version-vm-powershell.md).

Een **installatie kopie versie** is wat u gebruikt om een virtuele machine te maken wanneer u een galerie met gedeelde afbeeldingen gebruikt. U kunt meerdere versies van een installatie kopie naar behoefte hebben voor uw omgeving. Wanneer u een installatie kopie-versie gebruikt om een virtuele machine te maken, wordt de versie van de installatie kopie gebruikt voor het maken van schijven voor de nieuwe virtuele machine. Installatie kopie versies kunnen meerdere keren worden gebruikt.


## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt volt ooien, moet u beschikken over een bestaande galerie met gedeelde installatie kopieën. 

U moet ook een bestaande virtuele machine in azure hebben, in dezelfde regio als uw galerie. 

Als er een gegevens schijf aan de virtuele machine is gekoppeld, kan de grootte van de gegevens schijf niet groter zijn dan 1 TB.

Wanneer u dit artikel doorwerkt, vervangt u de resource namen waar nodig.

## <a name="get-information-about-the-vm"></a>Informatie over de virtuele machine ophalen

U ziet een lijst met virtuele machines die beschikbaar zijn via de [AZ VM-lijst](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Wanneer u de naam van de virtuele machine kent en de resource groep waarin deze zich bevindt, haalt u de ID van de virtuele machine op met [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Een definitie van een installatie kopie maken

Afbeeldings definities maken een logische groepering voor installatie kopieën. Ze worden gebruikt voor het beheren van informatie over de installatie kopieën die in deze versies worden gemaakt. 

Definitie namen van afbeeldingen kunnen bestaan uit hoofd letters, kleine letters, cijfers, punten, streepjes en punten. 

Zorg ervoor dat de definitie van de afbeelding het juiste type is. Als u de VM hebt gegeneraliseerd (met behulp van Sysprep voor Windows of waagent-deprovisioning voor Linux), moet u een `--os-state generalized`algemene definitie voor de installatie kopie maken met behulp van. Als u de virtuele machine wilt gebruiken zonder bestaande gebruikers accounts te verwijderen, maakt u een gespecialiseerde definitie `--os-state specialized`van de installatie kopie met behulp van.

Zie [afbeeldings definities](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)voor meer informatie over de waarden die u kunt opgeven voor de definitie van een installatie kopie.

Maak een definitie van een installatie kopie in de galerie met behulp van [AZ sig image-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

In dit voor beeld heeft de definitie van de installatie kopie de naam *myImageDefinition*en is voor een [gespecialiseerde](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) Linux-installatie kopie van het besturings systeem. Als u een definitie voor installatie kopieën wilt maken met een Windows `--os-type Windows`-besturings systeem, gebruikt u. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>De versie van de installatie kopie maken

Maak een installatie kopie versie van de virtuele machine met behulp van [AZ Image Gallery Create-Image galerie-version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Toegestane tekens voor de versie van de installatie kopie zijn getallen en punten. Getallen moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*. *MinorVersion*. *Patch*.

In dit voor beeld is de versie van onze installatie kopie *1.0.0* en we gaan twee replica's maken in de regio *West-Centraal VS* , 1 replica in de regio *Zuid-Centraal VS* en 1 replica in de regio *VS-Oost 2* met zone-redundante opslag. De replicatie regio's moeten de regio bevatten waarin de bron-VM zich bevindt.

Vervang de waarde van `--managed-image` in dit voor beeld door de id van uw virtuele machine uit de vorige stap.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> U moet wachten tot de versie van de installatie kopie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatie kopie kunt gebruiken om een andere versie van de installatie kopie te maken.
>
> U kunt uw installatie kopie ook opslaan in Premiun-opslag door `--storage-account-type  premium_lrs`een toevoeg-of [zone-redundante opslag](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) door toe te voegen `--storage-account-type  standard_zrs` wanneer u de versie van de installatie kopie maakt.
>

## <a name="next-steps"></a>Volgende stappen

Maak een virtuele machine op basis van de [gegeneraliseerde installatie kopie](vm-generalized-image-version-cli.md) met behulp van de Azure cli.
