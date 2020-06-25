---
title: Het station met het besturings systeem van een virtuele Windows-machine in een Azure uitbreiden
description: Breid de grootte van het OS-station van een virtuele machine uit met Azure Power shell in het Resource Manager-implementatie model.
author: mimckitt
manager: vashan
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: mimckitt
ms.subservice: disks
ms.openlocfilehash: 5044993e04dabc363a7a4ee49abb66285bcd7521
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85338241"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>De besturingssysteemschijf van een virtuele machine uitbreiden

Wanneer u een nieuwe virtuele machine (VM) maakt in een resource groep door een installatie kopie te implementeren vanuit [Azure Marketplace](https://azure.microsoft.com/marketplace/), is het standaard station van het besturings systeem vaak 127 GB (sommige installatie kopieën hebben standaard kleinere besturingssysteem schijf grootten). Hoewel het mogelijk is om gegevensschijven toe te voegen aan de VM (het aantal is afhankelijk van de SKU die u hebt gekozen) en het bovendien wordt aangeraden om toepassingen en CPU-intensieve werkbelastingen te installeren op deze aanvullende schijven, moeten klanten vaak ook de besturingssysteemschijf uitbreiden om bepaalde scenario's te ondersteunen, zoals de volgende:

- Ondersteuning voor oudere toepassingen die onderdelen op de besturingssysteemschijf installeren.
- Een fysieke computer of virtuele machine migreren van on-premises met een grotere besturingssysteemschijf.


> [!IMPORTANT]
> Voor het wijzigen van de grootte van de besturingssysteem schijf van een virtuele machine van Azure moet de toewijzing van de virtuele machine ongedaan worden gemaakt.
>
> Nadat u de schijven hebt uitgebreid, moet u [het volume in het besturings systeem uitbreiden](#expand-the-volume-within-the-os) om te kunnen profiteren van de grotere schijf.
> 


 


## <a name="resize-a-managed-disk"></a>Het formaat van een beheerde schijf wijzigen

Open de Powershell ISE of het Powershell-venster in de beheerdersmodus en voer de volgende stappen uit:

1. Meld u aan bij uw Microsoft Azure-account in de modus resource beheer en selecteer uw abonnement als volgt:
   
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
5. Verkrijg een verwijzing naar de beheerde besturingssysteem schijf. Stel de grootte van de beheerde besturingssysteem schijf in op de gewenste waarde en werk de schijf als volgt bij:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > De nieuwe grootte moet groter zijn dan de bestaande schijfgrootte. Het Maxi maal toegestane aantal is 2048 GB voor besturingssysteem schijven. (Het is mogelijk om de VHD-BLOB buiten die grootte uit te breiden, maar het besturings systeem kan alleen met de eerste 2048 GB aan ruimte werken.)
   > 
   > 
6. Het bijwerken van de VM kan een paar seconden duren. Zodra de opdracht is voltooid, start u de VM als volgt opnieuw op:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

Dat is alles. Ga nu met RDP naar de VM, open Computerbeheer (of Schijfbeheer) en vouw het station met de zojuist toegewezen ruimte uit.

## <a name="resize-an-unmanaged-disk"></a>Het formaat van een onbeheerde schijf wijzigen

Open de Powershell ISE of het Powershell-venster in de beheerdersmodus en voer de volgende stappen uit:

1. Meld u aan bij uw Microsoft Azure-account in de modus resource beheer en selecteer uw abonnement als volgt:
   
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
5. Stel de grootte van de niet-beheerde besturingssysteem schijf in op de gewenste waarde en werk de virtuele machine als volgt bij:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > De nieuwe grootte moet groter zijn dan de bestaande schijfgrootte. Het Maxi maal toegestane aantal is 2048 GB voor besturingssysteem schijven. (Het is mogelijk om de VHD-BLOB buiten die grootte uit te breiden, maar het besturings systeem kan alleen met de eerste 2048 GB aan ruimte werken.)
   > 
   > 
   
6. Het bijwerken van de VM kan een paar seconden duren. Zodra de opdracht is voltooid, start u de VM als volgt opnieuw op:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Scripts voor besturingssysteem schijf

Hieronder ziet u het volledige script voor uw referentie voor zowel beheerde als onbeheerde schijven:


**Beheerde schijven**

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

## <a name="resizing-data-disks"></a>Grootte van gegevens schijven wijzigen

Dit artikel is voornamelijk gericht op het uitbreiden van de besturingssysteem schijf van de virtuele machine, maar het script kan ook worden gebruikt voor het uitbreiden van de gegevens schijven die zijn gekoppeld aan de virtuele machine. Als u alleen een gegevens schijf uitbreidt, hoeft u de toewijzing van de VM **niet** ongedaan te maken. Als u bijvoorbeeld de eerste gegevensschijf die is gekoppeld aan de VM wilt uitbreiden, vervangt u het object `OSDisk` van `StorageProfile` door de matrix `DataDisks` en gebruikt u een numerieke index om een verwijzing naar de eerste gekoppelde schijf te verkrijgen, zoals hieronder wordt weergegeven:

**Beheerde schijf**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Niet-beheerde schijf**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Op dezelfde manier kunt u verwijzen naar andere gegevens schijven die zijn gekoppeld aan de virtuele machine, hetzij door gebruik te maken van een index zoals hierboven wordt weer gegeven, of de eigenschap **name** van de schijf:


**Beheerde schijf**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Niet-beheerde schijf**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Het volume binnen het besturings systeem uitbreiden

Wanneer u de schijf voor de virtuele machine hebt uitgebreid, moet u het besturings systeem openen en het volume uitbreiden om de nieuwe ruimte te maken. Er zijn verschillende methoden voor het uitbreiden van een partitie. In deze sectie wordt beschreven hoe u de virtuele machine verbindt met een RDP-verbinding om de partitie uit te breiden met **Disk Part**.

1. Open een RDP-verbinding met uw VM.

2.  Open een opdracht prompt en typ **Disk Part**.

2.  Typ bij de **Disk Part** -prompt `list volume` . Noteer het volume dat u wilt uitbreiden.

3.  Typ bij de **Disk Part** -prompt `select volume <volumenumber>` . Hiermee wordt het volume *volumenumber* geselecteerd dat u wilt uitbreiden naar aaneengesloten lege ruimte op dezelfde schijf.

4.  Typ bij de **Disk Part** -prompt `extend [size=<size>]` . Hiermee wordt het geselecteerde volume uitgebreid naar *grootte* in mega bytes (MB).


## <a name="next-steps"></a>Volgende stappen

U kunt ook schijven koppelen met behulp van de [Azure Portal](attach-managed-disk-portal.md).
