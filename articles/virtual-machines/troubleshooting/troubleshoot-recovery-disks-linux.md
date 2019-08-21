---
title: Een Linux-probleemoplossings-VM gebruiken met de Azure CLI | Microsoft Docs
description: Meer informatie over het oplossen van problemen met Linux-VM'S door de besturingssysteem schijf te koppelen aan een herstel-VM met behulp van de Azure CLI
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 49ee83e451e9d555a7fe5fca57bc58d6616334da
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641053"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Problemen met een virtuele Linux-machine oplossen door de besturingssysteem schijf te koppelen aan een herstel-VM met de Azure CLI
Als op de virtuele Linux-machine (VM) een opstart-of schijf fout optreedt, moet u mogelijk de stappen voor probleem oplossing uitvoeren op de virtuele harde schijf zelf. Een voor beeld hiervan is een ongeldige vermelding in `/etc/fstab` die verhindert dat de virtuele machine kan worden opgestart. In dit artikel wordt beschreven hoe u de Azure CLI gebruikt om de virtuele harde schijf te verbinden met een andere Linux-VM om eventuele fouten op te lossen en vervolgens de oorspronkelijke VM opnieuw te maken. 

## <a name="recovery-process-overview"></a>Overzicht van het herstelproces
Het probleemoplossingsproces is als volgt:

1. Stop de betrokken VM.
1. Maak een moment opname van de besturingssysteem schijf van de virtuele machine.
1. Maak een schijf van de moment opname van de besturingssysteem schijf.
1. Koppel en koppel de nieuwe besturingssysteem schijf aan een andere virtuele Linux-machine voor het oplossen van problemen.
1. Maak verbinding met de VM voor probleemoplossing. Bewerk bestanden of voer hulpprogram ma's uit om problemen op de nieuwe besturingssysteem schijf op te lossen.
1. Ontkoppel de nieuwe besturingssysteem schijf en ontkoppel deze van de virtuele machine voor probleem oplossing.
1. Wijzig de besturingssysteem schijf voor de betrokken VM.

Als u deze probleemoplossings stappen wilt uitvoeren, moet u de nieuwste [Azure cli](/cli/azure/install-az-cli2) installeren en u aanmelden bij een Azure-account met [AZ login](/cli/azure/reference-index).

> [!Important]
> De scripts in dit artikel zijn alleen van toepassing op de virtuele machines die gebruikmaken van [beheerde schijven](../linux/managed-disks-overview.md). 

Vervang in de volgende voor beelden parameter namen door uw eigen waarden. Voor beelden van parameter `myResourceGroup` namen `myVM`zijn en.

## <a name="determine-boot-issues"></a>Opstart problemen vaststellen
Controleer de seriële uitvoer om te bepalen waarom de virtuele machine niet correct kan worden opgestart. Een veelvoorkomend voor beeld is een ongeldige vermelding `/etc/fstab`in, of de onderliggende virtuele harde schijf die wordt verwijderd of verplaatst.

De opstart logboeken ophalen met [AZ VM boot-Diagnostics Get-boot-log](/cli/azure/vm/boot-diagnostics). `myVM` In het volgende voor beeld wordt de seriële uitvoer opgehaald van de virtuele machine met de `myResourceGroup`naam in de resource groep met de naam:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Bekijk de seriële uitvoer om te bepalen waarom de virtuele machine niet kan worden opgestart. Als de seriële uitvoer geen indicatie geeft, moet u mogelijk logboek bestanden `/var/log` weer geven zodra u de virtuele harde schijf hebt verbonden met een VM voor probleem oplossing.

## <a name="stop-the-vm"></a>De virtuele machine stoppen

In het volgende voor beeld wordt de `myVM` VM gestopt met de naam `myResourceGroup`van de resource groep met de naam:

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Wacht totdat de virtuele machine is verwijderd voordat u de volgende stap verwerkt.

## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Een moment opname maken van de besturingssysteem schijf van de virtuele machine

Een moment opname is een volledige, alleen-lezen kopie van een VHD. Deze kan niet worden gekoppeld aan een virtuele machine. In de volgende stap maakt u een schijf van deze moment opname. In het volgende voor beeld wordt een moment `mySnapshot` opname gemaakt met de naam van de besturingssysteem schijf van de virtuele machine met de naam ' myVM '. 

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
## <a name="create-a-disk-from-the-snapshot"></a>Een schijf maken op basis van de moment opname

Met dit script maakt u een beheerde schijf `newOSDisk` met een naam uit `mysnapshot`de moment opname met de naam.  

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

#Provide the storage type for Managed Disk. PremiumLRS or StandardLRS.
$storageType = 'StandardLRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
U hebt nu een kopie van de oorspronkelijke besturingssysteem schijf. U kunt deze nieuwe schijf koppelen aan een andere Windows-VM voor het oplossen van problemen.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>De nieuwe virtuele harde schijf koppelen aan een andere VM
Voor de volgende stappen gebruikt u een andere virtuele machine voor het oplossen van problemen. U koppelt de schijf aan deze virtuele machine voor probleem oplossing om de schijf inhoud te zoeken en te bewerken. Met dit proces kunt u eventuele configuratie fouten corrigeren of aanvullende toepassings-of systeem logboek bestanden controleren, bijvoorbeeld. Kies of maak een andere virtuele machine die moet worden gebruikt voor het oplossen van problemen.

Koppel de bestaande virtuele harde schijf met [AZ VM unmanaged-Disk attach](/cli/azure/vm/unmanaged-disk). Wanneer u de bestaande virtuele harde schijf koppelt, geeft u de URI op naar de schijf die `az vm show` u hebt verkregen in de voor gaande opdracht. In het volgende voor beeld wordt een bestaande virtuele harde schijf gekoppeld aan de VM `myVMRecovery` voor probleem oplossing met de `myResourceGroup`naam in de resource groep met de naam:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```
## <a name="mount-the-attached-data-disk"></a>De gekoppelde gegevens schijf koppelen

> [!NOTE]
> In de volgende voor beelden worden de stappen beschreven die nodig zijn voor een Ubuntu-VM. Als u een andere Linux-distributie gebruikt, zoals Red Hat Enterprise Linux of Suse, zijn de locaties en `mount` opdrachten van het logboek bestand mogelijk iets anders. Raadpleeg de documentatie voor uw specifieke distributie voor de desbetreffende wijzigingen in de opdrachten.

1. SSH naar uw virtuele machine voor probleem oplossing met de juiste referenties. Als deze schijf de eerste gegevens schijf is die aan uw virtuele machine voor probleem oplossing is gekoppeld, is `/dev/sdc`de schijf waarschijnlijk verbonden met. Gebruiken `dmseg` om gekoppelde schijven weer te geven:

    ```bash
    dmesg | grep SCSI
    ```

    De uitvoer lijkt op die in het volgende voorbeeld:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    In het vorige voor beeld bevindt de besturingssysteem `/dev/sda` schijf zich op en de tijdelijke schijf die voor elke `/dev/sdb`VM is ingesteld, bevindt zich op. Als u meerdere gegevens schijven had, moeten ze zich op `/dev/sdd`, `/dev/sde`,,,, enzovoort.

2. Maak een directory voor het koppelen van uw bestaande virtuele harde schijf. In het volgende voor beeld wordt een `troubleshootingdisk`map gemaakt met de naam:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Als u meerdere partities op de bestaande virtuele harde schijf hebt, koppelt u de vereiste partitie. In het volgende voor beeld wordt de eerste primaire partitie `/dev/sdc1`gekoppeld aan:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Best Practice is het koppelen van gegevens schijven op virtuele machines in azure met behulp van de Universally Unique Identifier (UUID) van de virtuele harde schijf. Voor dit korte probleemoplossings scenario is het niet nodig om de virtuele harde schijf te koppelen met behulp van de UUID. Het bewerken `/etc/fstab` van virtuele harde schijven met de apparaatnaam in plaats van de UUID kan er echter toe leiden dat de VM niet kan worden opgestart onder normaal gebruik.


## <a name="fix-issues-on-the-new-os-disk"></a>Problemen op de nieuwe besturingssysteem schijf oplossen
Als de bestaande virtuele harde schijf is gekoppeld, kunt u nu eventuele onderhouds-en probleemoplossings stappen uitvoeren. Zodra u de problemen hebt opgelost, kunt u doorgaan met de volgende stappen.


## <a name="unmount-and-detach-the-new-os-disk"></a>De nieuwe besturingssysteem schijf ontkoppelen en loskoppelen
Wanneer de fouten zijn opgelost, ontkoppelt u de bestaande virtuele harde schijf en koppelt u deze los van de VM voor probleem oplossing. U kunt de virtuele harde schijf niet met andere virtuele machines gebruiken totdat de lease die de virtuele harde schijf koppelt aan de VM voor probleem oplossing is vrijgegeven.

1. Ontkoppel de bestaande virtuele harde schijf van de SSH-sessie naar uw VM voor probleem oplossing. Wijzig eerst de bovenliggende map voor het koppel punt:

    ```bash
    cd /
    ```

    Ontkoppel nu de bestaande virtuele harde schijf. In het volgende voor beeld wordt het apparaat ontkoppeld op `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Ontkoppel nu de virtuele harde schijf van de VM. Sluit de SSH-sessie af op uw virtuele machine voor probleem oplossing. Vermeld de gekoppelde gegevens schijven aan uw virtuele machine voor probleem oplossing met [AZ VM unmanaged-Disk List](/cli/azure/vm/unmanaged-disk). `myVMRecovery` In het volgende voor beeld ziet u de gegevens schijven die zijn gekoppeld aan de virtuele machine `myResourceGroup`met de naam in de resource groep met de naam:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Noteer de naam van de bestaande virtuele harde schijf. De naam van een schijf met de URI van **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** is bijvoorbeeld **myVHD**. 

    De gegevens schijf loskoppelen van uw VM [AZ VM unmanaged-Disk Detach](/cli/azure/vm/unmanaged-disk). In het volgende voor beeld wordt de schijf `myVHD` losgekoppeld van de virtuele `myVMRecovery` machine met `myResourceGroup` de naam in de resource groep:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>De besturingssysteem schijf voor de betrokken VM wijzigen

U kunt Azure PowerShell gebruiken om de schijven van het besturings systeem te wisselen. U hoeft de virtuele machine niet te verwijderen en opnieuw te maken.

In dit voor beeld wordt de `myVM` virtuele machine met de naam gestopt en wordt de schijf toegewezen als de nieuwe besturingssysteem schijf. `newOSDisk` 

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

## <a name="next-steps"></a>Volgende stappen
Zie [problemen met ssh-verbindingen met een Azure VM oplossen](troubleshoot-ssh-connection.md)als u problemen ondervindt bij het maken van verbinding met uw virtuele machine. Zie problemen met [toepassings connectiviteit oplossen op een Linux-VM](troubleshoot-app-connection.md)voor problemen met het openen van toepassingen die op uw virtuele machine worden uitgevoerd.

