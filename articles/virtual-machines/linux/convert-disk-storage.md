---
title: Beheerde schijven opslag converteren tussen standaard en premium SSD
description: Azure-opslag voor beheerde schijven converteren van standaard naar premium of premium naar standaard met behulp van de Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cd9bb92b3ed86c3a57b5fc70411a4593335acedb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431494"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Azure managed disks storage converteren van Standard naar Premium of Premium naar Standard

Er zijn vier schijftypen azure-beheerde schijven: Azure ultra SSD's (preview), premium SSD, standaard SSD en standaard HDD. U schakelen tussen de drie GA-schijftypen (premium SSD, standaard SSD en standaard HDD) op basis van uw prestatiebehoeften. U bent nog niet in staat om over te schakelen van of naar een ultra SSD, moet u een nieuwe implementeren.

Deze functionaliteit wordt niet ondersteund voor niet-beheerde schijven. Maar u [een onbeheerde schijf](convert-unmanaged-to-managed-disks.md) eenvoudig converteren naar een beheerde schijf om te kunnen schakelen tussen schijftypen.

In dit artikel ziet u hoe beheerde schijven van Standard naar Premium of Premium naar Standard kunnen worden geconverteerd met behulp van de Azure CLI. Zie [Azure CLI installeren](/cli/azure/install-azure-cli)als u het hulpprogramma wilt installeren of upgraden.

## <a name="before-you-begin"></a>Voordat u begint

* Voor schijfconversie is een herstart van de virtuele machine (VM) vereist, zodat u de migratie van uw schijfopslag plant tijdens een reeds bestaand onderhoudsvenster.
* Voor niet-beheerde schijven moet u [eerst converteren naar beheerde schijven,](convert-unmanaged-to-managed-disks.md) zodat u schakelen tussen opslagopties.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Schakelen tussen alle beheerde schijven van een virtuele machine tussen Premium en Standard

In dit voorbeeld ziet u hoe u alle schijven van een VM converteert van Standaard naar Premium-opslag of van Premium naar Standaardopslag. Als u premium beheerde schijven wilt gebruiken, moet uw vm een [VM-formaat](sizes.md) gebruiken dat Premium-opslag ondersteunt. In dit voorbeeld wordt ook overgeschakelt naar een grootte die Premium-opslag ondersteunt.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Schakelen tussen afzonderlijke beheerde schijven tussen Standaard en Premium

Voor uw dev/testworkload wilt u misschien een mix van Standaard- en Premium-schijven hebben om uw kosten te verlagen. U ervoor kiezen om alleen die schijven te upgraden die betere prestaties nodig hebben. In dit voorbeeld ziet u hoe u één VM-schijf converteert van Standaard naar Premium-opslag of van Premium naar Standaardopslag. Als u premium beheerde schijven wilt gebruiken, moet uw vm een [VM-formaat](sizes.md) gebruiken dat Premium-opslag ondersteunt. In dit voorbeeld wordt ook overgeschakelt naar een grootte die Premium-opslag ondersteunt.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Managed disks schakelen tussen Standard HDD en Standard SSD

In dit voorbeeld ziet u hoe u één VM-schijf converteert van Standard HDD naar Standard SSD of van Standard SSD naar Standard HDD.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Beheerde schijven schakelen tussen Standard en Premium in Azure-portal

Volg deze stappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer de VM in de lijst **met virtuele machines**.
3. Als de vm niet is gestopt, selecteert u **Stoppen** boven aan het deelvenster **VM-overzicht** en wacht u tot de VM is gestopt.
4. Selecteer **Schijven** in het menu in het deelvenster voor de virtuele machine.
5. Selecteer de schijf die u wilt converteren.
6. Selecteer **Configuratie** in het menu.
7. Wijzig het **accounttype** van **Standard HDD** naar **Premium SSD** of van **Premium SSD** naar **Standard HDD.**
8. Selecteer **Opslaan**en sluit het schijfvenster.

De update van het schijftype is onmiddellijk. U uw vm opnieuw starten na de conversie.

## <a name="next-steps"></a>Volgende stappen

Maak een alleen-lezen kopie van een vm met behulp van [snapshots](snapshot-copy-managed-disk.md).
