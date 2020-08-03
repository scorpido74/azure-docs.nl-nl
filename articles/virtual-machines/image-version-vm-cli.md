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
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0a1083c1889fbd83a1b8bd119a71013b738d7f74
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499531"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Een installatie kopie versie maken op basis van een virtuele machine in azure met behulp van Azure CLI

Als u een bestaande VM hebt die u wilt gebruiken om meerdere, identieke Vm's te maken, kunt u die VM gebruiken om een installatie kopie te maken in een galerie met gedeelde afbeeldingen met behulp van de Azure CLI. U kunt ook een installatie kopie maken op basis van een virtuele machine met behulp van [Azure PowerShell](image-version-vm-powershell.md).

Een **installatie kopie versie** is wat u gebruikt om een virtuele machine te maken wanneer u een galerie met gedeelde afbeeldingen gebruikt. U kunt meerdere versies van een installatie kopie naar behoefte hebben voor uw omgeving. Wanneer u een installatie kopie-versie gebruikt om een virtuele machine te maken, wordt de versie van de installatie kopie gebruikt voor het maken van schijven voor de nieuwe virtuele machine. Installatie kopie versies kunnen meerdere keren worden gebruikt.


## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt volt ooien, moet u beschikken over een bestaande galerie met gedeelde installatie kopieën. 

U moet ook een bestaande virtuele machine in azure hebben, in dezelfde regio als uw galerie. 

Als er een gegevens schijf aan de virtuele machine is gekoppeld, kan de grootte van de gegevens schijf niet groter zijn dan 1 TB.

Wanneer u dit artikel doorwerkt, vervangt u de resource namen waar nodig.

## <a name="get-information-about-the-vm"></a>Informatie over de VM ophalen

U kunt een lijst weergeven met virtuele machines die beschikbaar zijn met [az vm list](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Zodra u de naam van de virtuele machine weet en in welke resourcegroep die zich bevindt kunt u de id van de virtuele machine ophalen met [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Een definitie voor de installatiekopie maken

Definities van installatiekopieën maken een logische groepering voor installatiekopieën. Die worden gebruikt voor het beheren van informatie over de installatiekopieversies die daarbinnen worden gemaakt. 

Namen van installatiekopiedefinities kunnen bestaan uit hoofdletters, kleine letters, cijfers, streepjes en punten. 

Zorg ervoor dat de definitie van de afbeelding het juiste type is. Als u de VM hebt gegeneraliseerd (met behulp van Sysprep voor Windows of waagent-deprovisioning voor Linux), moet u een algemene definitie voor de installatie kopie maken met behulp van `--os-state generalized` . Als u de virtuele machine wilt gebruiken zonder bestaande gebruikers accounts te verwijderen, maakt u een gespecialiseerde definitie van de installatie kopie met behulp van `--os-state specialized` .

Zie [Installatiekopiedefinities](./linux/shared-image-galleries.md#image-definitions) voor meer informatie over de waarden die u kunt specificeren voor een installatiekopiedefinitie.

Een installatiekopiedefinitie in de galerie maken met [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

In dit voorbeeld heeft de definitie van de installatiekopie de naam *myImageDefinition* en is deze voor een [gespecialiseerde](./linux/shared-image-galleries.md#generalized-and-specialized-images) installatiekopie van een Linux-besturingssysteem. Als u een definitie voor installatie kopieën wilt maken met een Windows-besturings systeem, gebruikt u `--os-type Windows` . 

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


## <a name="create-the-image-version"></a>De installatiekopieversie maken

Maak een installatiekopieversie van de virtuele machine met [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

In dit voorbeeld is de installatiekopieversie *1.0.0* en gaan we 2 replica's maken in de regio *VS - west-centraal*, 1 replica in de regio *VS - zuid-centraal* regio en 1 replica in de regio *US - oost 2* met zone-redundante opslag. De replicatieregio’s moeten de regio omvatten waarin de bron-VM zich bevindt.

Vervang de waarde van `--managed-image` in dit voorbeeld door de id van uw virtuele machine uit de vorige stap.

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
> U moet wachten tot de installatiekopieversie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatiekopie kunt gebruiken om een andere versie van de installatiekopie te maken.
>
> U kunt uw installatiekopie ook opslaan in Premium Storage door een `--storage-account-type  premium_lrs` toe te voegen, of in [Zone-redundante opslag](../storage/common/storage-redundancy.md) door `--storage-account-type  standard_zrs` toe te voegen wanneer u de installatiekopieversie maakt.
>

## <a name="next-steps"></a>Volgende stappen

Maak een virtuele machine op basis van de [gegeneraliseerde installatie kopie](vm-generalized-image-version-cli.md) met behulp van de Azure cli.

Zie voor meer informatie over het verstrekken van informatie over het aankoop plan [Azure Marketplace-informatie over het aankoop plan bij het maken van installatie kopieën](marketplace-images.md).
