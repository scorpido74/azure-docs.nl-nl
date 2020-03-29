---
title: Een VM voor het oplossen van problemen met Linux gebruiken in de Azure-portal | Microsoft Documenten
description: Meer informatie over het oplossen van problemen met de virtuele machine van Linux door de OS-schijf te verbinden met een herstel-vm via de Azure-portal
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: e45de5c12f0d93645a0b1253acf8300527cafdbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374638"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Problemen met een Linux-vm oplossen door de OS-schijf aan een herstel-vm te koppelen met behulp van de Azure-portal
Als uw Virtuele Linux-machine (VM) een opstart- of schijffout tegenkomt, moet u mogelijk stappen voor het oplossen van problemen uitvoeren op de virtuele harde schijf zelf. Een veelvoorkomend voorbeeld is `/etc/fstab` een ongeldige vermelding die voorkomt dat de VM met succes kan opstarten. In dit artikel wordt beschreven hoe u de Azure-portal gebruiken om uw virtuele harde schijf aan te sluiten op een andere Linux-vm om eventuele fouten op te lossen en vervolgens uw oorspronkelijke VM opnieuw te maken.

## <a name="recovery-process-overview"></a>Overzicht van het herstelproces
Het probleemoplossingsproces is als volgt:

1. Stop de betreffende VM.
1. Maak een momentopname voor de OS-schijf van de VM.
1. Maak een virtuele harde schijf van de momentopname.
1. Voeg de virtuele harde schijf vast aan een andere Windows-vm voor probleemoplossingsdoeleinden.
1. Maak verbinding met de VM voor probleemoplossing. Bewerk bestanden of voer hulpprogramma's uit om problemen op de oorspronkelijke virtuele harde schijf op te lossen.
1. Koppel de virtuele harde schijf van de VM voor probleemoplossing los.
1. Wissel de OS-schijf in voor de VM.

> [!NOTE]
> Dit artikel is niet van toepassing op de VM met onbeheerde schijf.

## <a name="determine-boot-issues"></a>Opstartproblemen bepalen
Bekijk de opstartdiagnostiek en vm-schermafbeelding om te bepalen waarom uw vm niet correct kan worden opgestart. Een veelvoorkomend voorbeeld is `/etc/fstab`een ongeldige vermelding in of een onderliggende virtuele harde schijf die wordt verwijderd of verplaatst.

Selecteer uw VM in de portal en blader naar beneden naar de sectie **Ondersteuning + Probleemoplossing.** Klik **op Diagnostische gegevens opstarten** om de consoleberichten weer te geven die vanaf uw vm worden gestreamd. Bekijk de consolelogboeken om te zien of u bepalen waarom de virtuele machine een probleem tegenkomt. In het volgende voorbeeld wordt een VM weergegeven die vastzit in de onderhoudsmodus waarvoor handmatige interactie vereist is:

![Consolelogboeken voor vm-opstartdiagnostiek weergeven](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

U ook klikken op **Screenshot** over de bovenkant van de boot diagnostics log om een opname van de VM screenshot te downloaden.

## <a name="take-a-snapshot-of-the-os-disk"></a>Maak een momentopname van de OS Disk
Een momentopname is een volledige, alleen-lezen kopie van een virtuele harde schijf (VHD). We raden u aan de VM netjes af te sluiten voordat u een momentopname maakt, zodat alle processen die worden uitgevoerd, worden gewist. Voer de volgende stappen uit om een momentopname van een os-schijf te maken:

1. Ga naar [Azure-portal](https://portal.azure.com). Selecteer **Virtuele machines** op de zijbalk en selecteer de vm die een probleem heeft.
1. Selecteer **schijven**in het linkerdeelvenster en selecteer vervolgens de naam van de osschijf.
    ![Afbeelding over de naam van de OS-schijf](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Selecteer **op** de pagina Overzicht van de osschijf en selecteer **Momentopname maken**.
1. Maak een momentopname op dezelfde locatie als de OS-schijf.

## <a name="create-a-disk-from-the-snapshot"></a>Een schijf maken op basis van de momentopname
Voer de volgende stappen uit om een schijf te maken op basis van de momentopname:

1. Selecteer **Cloud Shell** in de Azure-portal.

    ![Afbeelding over Open Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Voer de volgende PowerShell-opdrachten uit om een beheerde schijf van de momentopname te maken. U moet deze voorbeeldnamen vervangen door de juiste namen.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Als de opdrachten zijn uitgevoerd, ziet u de nieuwe schijf in de door u opgegeven brongroep.

## <a name="attach-disk-to-another-vm"></a>Schijf aan een andere virtuele machine koppelen
Voor de volgende stappen gebruikt u een andere vm voor probleemoplossingsdoeleinden. Nadat u de schijf aan de VM voor het oplossen van problemen hebt gekoppeld, u de inhoud van de schijf bekijken en bewerken. Met dit proces u eventuele configuratiefouten corrigeren of aanvullende toepassings- of systeemlogboekbestanden controleren. Voer de volgende stappen uit om de schijf aan een andere virtuele machine te koppelen:

1. Selecteer uw brongroep in de portal en selecteer vervolgens de VM voor het oplossen van problemen. Selecteer **Schijven,** selecteer **Bewerken**en klik op **Gegevensschijf toevoegen:**

    ![Bestaande schijf in de portal koppelen](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Selecteer in de lijst **Gegevensschijven** de osschijf van de vm die u hebt geïdentificeerd. Als u de osschijf niet ziet, controleert u of vm en de OS-schijf probleemoplossing zich in dezelfde regio (locatie) bevinden. 
3. Selecteer **Opslaan** om de wijzigingen toe te passen.

## <a name="mount-the-attached-data-disk"></a>De gekoppelde gegevensschijf monteren

> [!NOTE]
> In de volgende voorbeelden worden de stappen beschreven die vereist zijn voor een Ubuntu-vm. Als u een andere Linux distro gebruikt, zoals Red Hat Enterprise Linux `mount` of SUSE, kunnen de logbestandslocaties en -opdrachten een beetje anders zijn. Raadpleeg de documentatie voor uw specifieke distro voor de juiste wijzigingen in opdrachten.

1. SSH aan uw vm voor het oplossen van problemen met behulp van de juiste referenties. Als deze schijf de eerste gegevensschijf is die is gekoppeld `/dev/sdc`aan de VM voor het oplossen van problemen, is deze waarschijnlijk verbonden met . Met `dmseg` gebruiken om bijgevoegde schijven weer te geven:

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


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Problemen op de originele virtuele harde schijf oplossen
Met de bestaande virtuele harde schijf gemonteerd, u nu alle onderhouds- en probleemoplossingsstappen uitvoeren als dat nodig is. Zodra u de problemen hebt opgelost, kunt u doorgaan met de volgende stappen.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Originele virtuele harde schijf loskoppelen en loskoppelen
Zodra uw fouten zijn opgelost, u de bestaande virtuele harde schijf loskoppelen van uw vm voor het oplossen van problemen. U uw virtuele harde schijf niet gebruiken met een andere VM totdat de lease die de virtuele harde schijf aan de VM voor het oplossen van problemen koppelt, is vrijgegeven.

1. Van de SSH-sessie tot de VM voor het oplossen van problemen, de bestaande virtuele harde schijf wordt losgekoppeld. Wijzig eerst de bovenliggende map voor uw bevestigingspunt:

    ```bash
    cd /
    ```

    Maak nu de bestaande virtuele harde schijf los. In het volgende voorbeeld wordt `/dev/sdc1`het apparaat ontkoppeld op :

    ```bash
    sudo umount /dev/sdc1
    ```

2. Maak nu de virtuele harde schijf los van de VM. Selecteer uw VM in de portal en klik op **Schijven**. Selecteer uw bestaande virtuele harde schijf en klik op **Losmaken:**

    ![Bestaande virtuele harde schijf loskoppelen](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Wacht totdat de VM de gegevensschijf heeft losgemaakt voordat u verdergaat.

## <a name="swap-the-os-disk-for-the-vm"></a>De OS-schijf ruilen voor de VM

Azure-portal ondersteunt nu het wijzigen van de OS-schijf van de VM. Voer de volgende stappen uit om dit te doen:

1. Ga naar [Azure-portal](https://portal.azure.com). Selecteer **Virtuele machines** op de zijbalk en selecteer de vm die een probleem heeft.
1. Selecteer **schijven**in het linkerdeelvenster en selecteer **vervolgens De schijf van Het besturingssysteem wisselen**.
        ![De afbeelding over Swap OS-schijf in Azure-portal](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Kies de nieuwe schijf die u hebt gerepareerd en typ de naam van de virtuele machine om de wijziging te bevestigen. Als u de schijf niet in de lijst ziet, wacht u 10 ~ 15 minuten nadat u de schijf loskoppelt van de VM voor het oplossen van problemen. Zorg er ook voor dat de schijf zich op dezelfde locatie bevindt als de VM.
1. Selecteer OK.

## <a name="next-steps"></a>Volgende stappen
Zie [SSH-verbindingen met een Azure VM oplossen](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)als u problemen ondervindt bij het maken van verbinding met uw vm. Zie Problemen met de connectiviteit van [toepassingen op een Linux-vm oplossen voor](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)problemen met de toegang tot toepassingen op uw vm.

Zie overzicht van Azure Resource Manager voor meer informatie over het gebruik van Resource [Manager.](../../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
