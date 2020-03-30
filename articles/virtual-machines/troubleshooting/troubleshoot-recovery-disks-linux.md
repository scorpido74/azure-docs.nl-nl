---
title: Gebruik een VM voor het oplossen van problemen met Linux met de Azure CLI | Microsoft Documenten
description: Meer informatie over het oplossen van Linux VM-problemen door de OS-schijf te verbinden met een herstel-vm met de Azure CLI
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 1b91a39e1297d8952da67a4f8d3b8568cefe04ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73620564"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Problemen met een Linux-vm oplossen door de OS-schijf aan een herstel-vm te koppelen aan de Azure CLI
Als uw Virtuele Linux-machine (VM) een opstart- of schijffout tegenkomt, moet u mogelijk stappen voor het oplossen van problemen uitvoeren op de virtuele harde schijf zelf. Een veelvoorkomend voorbeeld is `/etc/fstab` een ongeldige vermelding die voorkomt dat de VM met succes kan opstarten. In dit artikel wordt beschreven hoe u de Azure CLI gebruiken om uw virtuele harde schijf aan te sluiten op een andere Linux-vm om eventuele fouten op te lossen en vervolgens uw oorspronkelijke VM opnieuw te maken. 

## <a name="recovery-process-overview"></a>Overzicht van het herstelproces
Het probleemoplossingsproces is als volgt:

1. Stop de betreffende VM.
1. Maak een momentopname van de OS-schijf van de VM.
1. Maak een schijf op basis van de momentopname van de OS-schijf.
1. Bevestig en monteer de nieuwe OS-schijf op een andere Linux-vm voor probleemoplossingsdoeleinden.
1. Maak verbinding met de VM voor probleemoplossing. Bewerk bestanden of voer hulpprogramma's uit om problemen op de nieuwe osschijf op te lossen.
1. Maak de nieuwe OS-schijf los en los van de VM voor het oplossen van problemen.
1. Wijzig de OS-schijf voor de betreffende vm.

Als u deze stappen voor het oplossen van problemen wilt uitvoeren, moet u de nieuwste [Azure CLI-installatie](/cli/azure/install-az-cli2) en aangemeld bij een Azure-account gebruiken met [az-aanmelding.](/cli/azure/reference-index)

> [!Important]
> De scripts in dit artikel zijn alleen van toepassing op de VM's die [Beheerde schijf](../linux/managed-disks-overview.md)gebruiken. 

Vervang in de volgende voorbeelden parameternamen door uw `myResourceGroup` `myVM`eigen waarden, zoals en .

## <a name="determine-boot-issues"></a>Opstartproblemen bepalen
Controleer de seriële uitvoer om te bepalen waarom uw VM niet correct kan opstarten. Een veelvoorkomend voorbeeld is `/etc/fstab`een ongeldige vermelding in of de onderliggende virtuele harde schijf wordt verwijderd of verplaatst.

Download de boot logs met [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics). In het volgende voorbeeld wordt de `myVM` seriële `myResourceGroup`uitvoer van de VM met de naam in de resourcegroep met de naam :

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Controleer de seriële uitvoer om te bepalen waarom de VM niet wordt opgestart. Als de seriële uitvoer geen indicatie geeft, moet `/var/log` u mogelijk logbestanden controleren zodra u de virtuele harde schijf hebt aangesloten op een vm voor het oplossen van problemen.

## <a name="stop-the-vm"></a>De virtuele machine stoppen

In het volgende voorbeeld `myVM` wordt de `myResourceGroup`vm met de naam van de resourcegroep met de naam :

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Maak een momentopname van de OS-schijf van de getroffen VM

Een momentopname is een volledige, alleen-lezen kopie van een VHD. Het kan niet worden gekoppeld aan een VM. In de volgende stap maken we een schijf van deze momentopname. In het volgende voorbeeld `mySnapshot` wordt een momentopname gemaakt met de naam van de OS-schijf van de VM met de naam 'myVM'. 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Een schijf maken op basis van de momentopname

Met dit script wordt `myOSDisk` een beheerde `mySnapshot`schijf gemaakt met de naam van de momentopname met de naam .  

```azurecli
#Provide the name of your resource group
$resourceGroup=myResourceGroup

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot=mySnapshot

#Provide the name of the Managed Disk
$osDisk=myNewOSDisk

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType=Premium_LRS

#Provide the OS type
$osType=linux

#Provide the name of the virtual machine
$virtualMachine=myVM

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query [id] -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

Als de resourcegroep en de bronmomentopname zich niet in hetzelfde gebied bevinden, ontvangt `az disk create`u de fout 'Bron wordt niet gevonden' wanneer u uitvoert . In dit geval moet `--location <region>` u opgeven om de schijf in hetzelfde gebied als de bronmomentopname te maken.

Nu heb je een kopie van de originele OS schijf. U deze nieuwe schijf op een andere Windows-vm monteren voor probleemoplossingsdoeleinden.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>De nieuwe virtuele harde schijf koppelen aan een andere virtuele schijf
Voor de volgende stappen gebruikt u een andere vm voor probleemoplossingsdoeleinden. U koppelt de schijf aan deze VM voor het oplossen van problemen om de inhoud van de schijf te bladeren en te bewerken. Met dit proces u eventuele configuratiefouten corrigeren of aanvullende toepassings- of systeemlogboekbestanden controleren.

Dit script koppelt `myNewOSDisk` de `MyTroubleshootVM`schijf aan de VM:

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $diskId --resource-group MyResourceGroup --size-gb 128 --sku Standard_LRS --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>De gekoppelde gegevensschijf monteren

> [!NOTE]
> In de volgende voorbeelden worden de stappen beschreven die vereist zijn voor een Ubuntu-vm. Als u een andere Linux distro gebruikt, zoals Red Hat Enterprise Linux `mount` of SUSE, kunnen de logbestandslocaties en -opdrachten een beetje anders zijn. Raadpleeg de documentatie voor uw specifieke distro voor de juiste wijzigingen in opdrachten.

1. SSH aan uw vm voor het oplossen van problemen met behulp van de juiste referenties. Als deze schijf de eerste gegevensschijf is die is gekoppeld `/dev/sdc`aan de vm voor het oplossen van problemen, is de schijf waarschijnlijk verbonden met . Met `dmesg` gebruiken om gekoppelde schijven weer te geven:

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

    In het voorgaande voorbeeld staat `/dev/sda` de OS-schijf op en `/dev/sdb`is de tijdelijke schijf voor elke vm op . Als u meerdere gegevensschijven had, `/dev/sdd` `/dev/sde`zouden zij bij, , enzovoort moeten zijn.

2. Maak een map om uw bestaande virtuele harde schijf te monteren. In het volgende voorbeeld `troubleshootingdisk`wordt een map gemaakt met de naam:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Als u meerdere partities op uw bestaande virtuele harde schijf hebt, monteert u de vereiste partitie. In het volgende voorbeeld wordt `/dev/sdc1`de eerste primaire partitie gemonteerd op :

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Aanbevolen is het monteren van gegevensschijven op VM's in Azure met behulp van de universeel unieke id (UUID) van de virtuele harde schijf. Voor dit korte scenario voor het oplossen van problemen is het niet nodig om de virtuele harde schijf te monteren met behulp van de UUID. Bij normaal gebruik kan het `/etc/fstab` bewerken om virtuele harde schijven te monteren met de naam van het apparaat in plaats van UUID er echter toe leiden dat de VM niet wordt opgestart.


## <a name="fix-issues-on-the-new-os-disk"></a>Problemen op de nieuwe osschijf oplossen
Met de bestaande virtuele harde schijf gemonteerd, u nu alle onderhouds- en probleemoplossingsstappen uitvoeren als dat nodig is. Zodra u de problemen hebt opgelost, kunt u doorgaan met de volgende stappen.


## <a name="unmount-and-detach-the-new-os-disk"></a>De nieuwe OS-schijf loskoppelen en loskoppelen
Zodra uw fouten zijn opgelost, verwijdert u de bestaande virtuele harde schijf en ontkoppelt u deze van uw VM voor het oplossen van problemen. U uw virtuele harde schijf niet gebruiken met een andere VM totdat de lease die de virtuele harde schijf aan de VM voor het oplossen van problemen koppelt, is vrijgegeven.

1. Van de SSH-sessie tot de VM voor het oplossen van problemen, de bestaande virtuele harde schijf wordt losgekoppeld. Wijzig eerst de bovenliggende map voor uw bevestigingspunt:

    ```bash
    cd /
    ```

    Maak nu de bestaande virtuele harde schijf los. In het volgende voorbeeld wordt `/dev/sdc1`het apparaat ontkoppeld op :

    ```bash
    sudo umount /dev/sdc1
    ```

2. Maak nu de virtuele harde schijf los van de VM. Sluit de SSH-sessie af op uw VM voor het oplossen van problemen:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>De osschijf voor de betreffende vm wijzigen

U Azure CLI gebruiken om de OS-schijven te verwisselen. U hoeft de vm niet te verwijderen en opnieuw te maken.

In dit voorbeeld `myVM` wordt de met `myNewOSDisk` de naam VM gestopt en wordt de schijf met de naam de nieuwe osschijf toewijst.

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen
Zie [SSH-verbindingen met een Azure VM oplossen](troubleshoot-ssh-connection.md)als u problemen ondervindt bij het maken van verbinding met uw vm. Zie Problemen met de connectiviteit van [toepassingen op een Linux-vm oplossen voor](troubleshoot-app-connection.md)problemen met de toegang tot toepassingen op uw vm.

