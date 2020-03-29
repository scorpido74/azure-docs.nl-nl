---
title: Een VM voor het oplossen van problemen met Windows gebruiken met Azure PowerShell | Microsoft Documenten
description: Meer informatie over het oplossen van Windows VM-problemen in Azure door de OS-schijf te verbinden met een herstel-vm met Azure PowerShell
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 66cda98f272e7353b620059a731972714db585ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374129"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Problemen met een Windows-vm oplossen door de OS-schijf aan een herstel-vm te koppelen met Azure PowerShell
Als uw virtuele Windows-machine (VM) in Azure een opstart- of schijffout tegenkomt, moet u mogelijk stappen voor het oplossen van problemen uitvoeren op de schijf zelf. Een veelvoorkomend voorbeeld is een mislukte toepassingsupdate die voorkomt dat de VM kan opstarten. In dit artikel wordt beschreven hoe u Azure PowerShell gebruiken om de schijf aan te sluiten op een andere Windows-vm om eventuele fouten op te lossen en vervolgens uw oorspronkelijke vm te herstellen. 

> [!Important]
> De scripts in dit artikel zijn alleen van toepassing op de VM's die [Beheerde schijf](../windows/managed-disks-overview.md)gebruiken. 

 

## <a name="recovery-process-overview"></a>Overzicht van het herstelproces
We kunnen Azure PowerShell nu gebruiken om de OS-schijf voor een vm te wijzigen. We hoeven de VM niet langer te verwijderen en opnieuw te maken.

Het probleemoplossingsproces is als volgt:

1. Stop de betreffende VM.
2. Maak een momentopname op de OS-schijf van de vm.
3. Maak een schijf op basis van de momentopname van de OS-schijf.
4. Koppel de schijf als gegevensschijf aan een herstel-vm.
5. Maak verbinding met de herstel-vm. Bewerk bestanden of voer hulpprogramma's uit om problemen op de gekopieerde osschijf op te lossen.
6. De schijf loskoppelen en loskoppelen van herstel-VM.
7. Wijzig de OS-schijf voor de betreffende vm.

U de VM-reparatieopdrachten gebruiken om de stappen 1, 2, 3, 4, 6 en 7 te automatiseren. Zie [Een Windows-vm herstellen met de reparatieopdrachten van Azure Virtual Machine](repair-windows-vm-using-azure-virtual-machine-repair-commands.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer documentatie en instructies.

Zorg ervoor dat u [de nieuwste Azure PowerShell](/powershell/azure/overview) hebt geïnstalleerd en aangemeld bij uw abonnement:

```powershell
Connect-AzAccount
```

Vervang in de volgende voorbeelden de parameternamen door uw eigen waarden. 

## <a name="determine-boot-issues"></a>Opstartproblemen bepalen
U een schermafbeelding van uw VM in Azure bekijken om opstartproblemen op te lossen. Deze schermafbeelding kan helpen bepalen waarom een VM niet opstart. In het volgende voorbeeld wordt de `myVM` schermafbeelding van `myResourceGroup`de Windows-VM met de naam In de brongroep met de naam :

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Bekijk de schermafbeelding om te bepalen waarom de VM niet opstart. Let op eventuele specifieke foutmeldingen of foutcodes die worden verstrekt.

## <a name="stop-the-vm"></a>De virtuele machine stoppen

In het volgende voorbeeld `myVM` wordt de `myResourceGroup`vm met de naam van de resourcegroep met de naam :

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Wacht tot de VM klaar is met verwijderen voordat u naar de volgende stap gaat.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Een momentopname maken vanaf de OS-schijf van de vm

In het volgende voorbeeld `mySnapshot` wordt een momentopname gemaakt met de naam van de OS-schijf van de VM met de naam 'myVM'. 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

Een momentopname is een volledige, alleen-lezen kopie van een VHD. Het kan niet worden gekoppeld aan een VM. In de volgende stap maken we een schijf van deze momentopname.

## <a name="create-a-disk-from-the-snapshot"></a>Een schijf maken op basis van de momentopname

Met dit script wordt `newOSDisk` een beheerde `mysnapshot`schijf gemaakt met de naam van de momentopname met de naam .  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType = 'Standard_LRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Nu heb je een kopie van de originele OS schijf. U deze schijf op een andere Windows-VM monteren voor probleemoplossingsdoeleinden.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>De schijf aan een andere Windows-vm koppelen voor het oplossen van problemen

Nu koppelen we de kopie van de originele OS-schijf aan een VM als gegevensschijf. Met dit proces u configuratiefouten corrigeren of aanvullende toepassings- of systeemlogboekbestanden op de schijf controleren. In het volgende voorbeeld `newOSDisk` wordt de `RecoveryVM`schijf met de naam gekoppeld aan de vm met de naam .

> [!NOTE]
> Om de schijf te bevestigen, moeten de kopie van de oorspronkelijke osschijf en de herstel-vm zich op dezelfde locatie bevinden.

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Verbinding maken met de herstel-vm en problemen op de aangesloten schijf oplossen

1. RDP naar uw herstel-VM met behulp van de juiste referenties. In het volgende voorbeeld wordt het RDP-verbindingsbestand voor de VM met de naam `RecoveryVM` in de brongroep met de naam `myResourceGroup`' downloads en gedownload naar `C:\Users\ops\Documents`"

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. De gegevensschijf moet automatisch worden gedetecteerd en gekoppeld. Bekijk de lijst met bijgevoegde volumes om de stationsletter als volgt te bepalen:

    ```powershell
    Get-Disk
    ```

    In de volgende voorbeelduitvoer ziet u de schijf die is aangesloten op een schijf **2**. (U `Get-Volume` ook gebruiken om de stationsletter te bekijken):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Nadat de kopie van de originele osschijf is gemonteerd, u desgewenst alle onderhouds- en probleemoplossingsstappen uitvoeren. Zodra u de problemen hebt opgelost, kunt u doorgaan met de volgende stappen.

## <a name="unmount-and-detach-original-os-disk"></a>Originele osschijf loskoppelen en loskoppelen
Zodra uw fouten zijn opgelost, wordt de bestaande schijf losgekoppeld en losgekoppeld van uw herstel-vm. U uw schijf niet met een andere VM gebruiken totdat de lease die de schijf aan de herstel-VM koppelt, is vrijgegeven.

1. Vanuit uw RDP-sessie u de gegevensschijf op uw herstel-vm demonteren. U hebt het schijfnummer `Get-Disk` van de vorige cmdlet nodig. Gebruik vervolgens `Set-Disk` om de schijf offline in te stellen:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Controleer of de schijf nu `Get-Disk` opnieuw als offline is ingesteld. In de volgende voorbeelduitvoerwordt weergegeven dat de schijf nu als offline is ingesteld:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Sluit uw RDP-sessie af. Verwijder in uw Azure PowerShell-sessie de schijf met de naam `newOSDisk` 'RecoveryVM'.

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>De osschijf voor de betreffende vm wijzigen

U Azure PowerShell gebruiken om de OS-schijven te verwisselen. U hoeft de vm niet te verwijderen en opnieuw te maken.

In dit voorbeeld `myVM` wordt de met `newOSDisk` de naam VM gestopt en wordt de schijf met de naam de nieuwe osschijf toewijst. 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>Opstartdiagnose verifiëren en inschakelen

In het volgende voorbeeld wordt de `myVMDeployed` diagnostische extensie `myResourceGroup`op de VM ingeschakeld die is genoemd in de resourcegroep met de naam :

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Volgende stappen
Zie Problemen [met RDP-verbindingen met een Azure VM](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)als u problemen ondervindt bij het maken van verbinding met uw VM. Zie Problemen met de connectiviteit van [toepassingen op een Windows-vm oplossen voor](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)problemen met de toegang tot toepassingen op uw vm.

Zie overzicht van Azure Resource Manager voor meer informatie over het gebruik van Resource [Manager.](../../azure-resource-manager/management/overview.md)
