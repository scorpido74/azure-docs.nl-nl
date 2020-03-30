---
title: Het besturingssysteem van een Windows-vm in een Azure uitvouwen
description: Vergroot de grootte van de os-schijf van een virtuele machine met Azure Powershell in het implementatiemodel Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: c76f57d15cd4cbdad5ded3b7545aab4d57272a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033504"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>De besturingssysteemschijf van een virtuele machine uitbreiden

Wanneer u een nieuwe virtuele machine (VM) maakt in een resourcegroep door een afbeelding te implementeren vanuit [Azure Marketplace,](https://azure.microsoft.com/marketplace/)is het standaardstation vaak 127 GB (sommige afbeeldingen hebben standaard kleinere schijfformaten voor het besturingssysteem). Hoewel het mogelijk is om gegevensschijven toe te voegen aan de VM (het aantal is afhankelijk van de SKU die u hebt gekozen) en het bovendien wordt aangeraden om toepassingen en CPU-intensieve werkbelastingen te installeren op deze aanvullende schijven, moeten klanten vaak ook de besturingssysteemschijf uitbreiden om bepaalde scenario's te ondersteunen, zoals de volgende:

- Ondersteuning voor oudere toepassingen die onderdelen op de besturingssysteemschijf installeren.
- Een fysieke computer of virtuele machine migreren van on-premises met een grotere besturingssysteemschijf.


> [!IMPORTANT]
> Voor het aanpassen van het formaat van de OS-schijf van een Azure Virtual Machine moet de virtuele machine worden toegewezen.
>
> Nadat u de schijven hebt uitgebreid, moet u [het volume in het besturingssysteem uitbreiden](#expand-the-volume-within-the-os) om te profiteren van de grotere schijf.
> 


 


## <a name="resize-a-managed-disk"></a>Het formaat van een beheerde schijf wijzigen

Open de Powershell ISE of het Powershell-venster in de beheerdersmodus en voer de volgende stappen uit:

1. Meld u aan bij uw Microsoft Azure-account in de modus resourcebeheer en selecteer uw abonnement als volgt:
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Stel de naam van de resourcegroep en de VM als volgt in:
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Vraag als volgt een verwijzing op naar uw VM:
   
   ```powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Ga als volgt te werk om de VM te stoppen voordat u de grootte van de schijf aanpast:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Een verwijzing naar de beheerde osschijf verkrijgen. Stel de grootte van de beheerde osschijf in op de gewenste waarde en werk de schijf als volgt bij:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > De nieuwe grootte moet groter zijn dan de bestaande schijfgrootte. Het maximaal toegestane is 2048 GB voor OS-schijven. (Het is mogelijk om de VHD-blob uit te breiden buiten die grootte, maar het OS zal alleen kunnen werken met de eerste 2048 GB aan ruimte.)
   > 
   > 
6. Het bijwerken van de VM kan een paar seconden duren. Zodra de opdracht is voltooid, start u de VM als volgt opnieuw op:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

Dat is alles. Ga nu met RDP naar de VM, open Computerbeheer (of Schijfbeheer) en vouw het station met de zojuist toegewezen ruimte uit.

## <a name="resize-an-unmanaged-disk"></a>Het formaat van een niet-beheerde schijf wijzigen

Open de Powershell ISE of het Powershell-venster in de beheerdersmodus en voer de volgende stappen uit:

1. Meld u aan bij uw Microsoft Azure-account in de modus resourcebeheer en selecteer uw abonnement als volgt:
   
   ```Powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Stel de naam van de resourcegroep en de VM als volgt in:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Vraag als volgt een verwijzing op naar uw VM:
   
   ```Powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Ga als volgt te werk om de VM te stoppen voordat u de grootte van de schijf aanpast:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Stel de grootte van de niet-beheerde osschijf in op de gewenste waarde en werk de VM als volgt bij:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > De nieuwe grootte moet groter zijn dan de bestaande schijfgrootte. Het maximaal toegestane is 2048 GB voor OS-schijven. (Het is mogelijk om de VHD-blob uit te breiden buiten die grootte, maar het OS zal alleen kunnen werken met de eerste 2048 GB aan ruimte.)
   > 
   > 
   
6. Het bijwerken van de VM kan een paar seconden duren. Zodra de opdracht is voltooid, start u de VM als volgt opnieuw op:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Scripts voor osschijf

Hieronder vindt u het volledige script voor uw referentie voor zowel beheerde als onbeheerde schijven:


**Managed Disks**

```Powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Niet-beheerde schijven**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Het formaat van gegevensschijven wijzigen

Dit artikel is voornamelijk gericht op het uitbreiden van de OS-schijf van de VM, maar het script kan ook worden gebruikt voor het uitbreiden van de gegevensschijven die aan de VM zijn gekoppeld. Als u bijvoorbeeld de eerste gegevensschijf die is gekoppeld aan de VM wilt uitbreiden, vervangt u het object `OSDisk` van `StorageProfile` door de matrix `DataDisks` en gebruikt u een numerieke index om een verwijzing naar de eerste gekoppelde schijf te verkrijgen, zoals hieronder wordt weergegeven:

**Beheerde schijf**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Onbeheerde schijf**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Op dezelfde manier u verwijzen naar andere gegevensschijven die aan de VM zijn gekoppeld, met behulp van een index zoals hierboven weergegeven, of de eigenschap **Naam** van de schijf:


**Beheerde schijf**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Onbeheerde schijf**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Het volume binnen het besturingssysteem uitbreiden

Zodra u de schijf voor de VM hebt uitgebreid, moet u naar het besturingssysteem gaan en het volume uitbreiden naar de nieuwe ruimte. Er zijn verschillende methoden voor het uitbreiden van een partitie. In deze sectie wordt het verbinden van de VM via een RDP-verbinding gebruikt om de partitie uit te vouwen met **DiskPart**.

1. Open een RDP-verbinding met uw VM.

2.  Open een opdrachtprompt en typ **schijfonderdeel**.

2.  Typ bij de prompt `list volume`VAN **SCHIJFONDERDEEL** . Noteer het volume dat u wilt uitbreiden.

3.  Typ bij de prompt `select volume <volumenumber>`VAN **SCHIJFONDERDEEL** . Hiermee selecteert u het *volumenummer* dat u wilt uitbreiden naar aaneengesloten, lege ruimte op dezelfde schijf.

4.  Typ bij de prompt `extend [size=<size>]`VAN **SCHIJFONDERDEEL** . Hiermee wordt het geselecteerde volume in *megabytes* (MB) uitgebreid.


## <a name="next-steps"></a>Volgende stappen

U ook schijven koppelen via de [Azure-portal.](attach-managed-disk-portal.md)
