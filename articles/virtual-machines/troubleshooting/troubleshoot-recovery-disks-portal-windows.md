---
title: Gebruik een Windows-probleemoplossings-VM in de Azure Portal | Microsoft Docs
description: Informatie over het oplossen van problemen met virtuele Windows-machines in azure door de besturingssysteem schijf te koppelen aan een herstel-VM met behulp van de Azure Portal
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: e76fc2da8da2325a8bb0cda47c4405c9eb03c8f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79249995"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Problemen met een Windows-VM oplossen door de besturingssysteem schijf te koppelen aan een herstel-VM met behulp van de Azure Portal
Als op uw virtuele Windows-machine (VM) in azure een opstart-of schijf fout optreedt, moet u mogelijk de stappen voor probleem oplossing uitvoeren op de virtuele harde schijf zelf. Een veelvoorkomend voor beeld hiervan is een mislukte toepassings update waarmee wordt voor komen dat de virtuele machine kan worden opgestart. In dit artikel wordt beschreven hoe u met behulp van de Azure Portal de virtuele harde schijf verbindt met een andere Windows-VM om eventuele fouten op te lossen en vervolgens de oorspronkelijke VM opnieuw te maken. 

## <a name="recovery-process-overview"></a>Overzicht van het herstelproces
Het probleemoplossingsproces is als volgt:

1. Stop de betrokken VM.
1. Maak een moment opname voor de besturingssysteem schijf van de virtuele machine.
1. Maak een virtuele harde schijf van de moment opname.
1. Koppel en koppel de virtuele harde schijf aan een andere Windows-VM voor het oplossen van problemen.
1. Maak verbinding met de VM voor probleemoplossing. Bewerk bestanden of voer hulpprogram ma's uit om problemen op de oorspronkelijke virtuele harde schijf op te lossen.
1. Koppel de virtuele harde schijf van de VM voor probleemoplossing los.
1. Wissel de besturingssysteem schijf voor de virtuele machine uit.

> [!NOTE]
> Dit artikel is niet van toepassing op de virtuele machine met een niet-beheerde schijf.

## <a name="take-a-snapshot-of-the-os-disk"></a>Een moment opname maken van de besturingssysteem schijf
Een moment opname is een volledige, alleen-lezen kopie van een virtuele harde schijf (VHD). U wordt aangeraden de virtuele machine op een schone manier af te sluiten voordat u een moment opname maakt, zodat alle processen die worden uitgevoerd, worden gewist. Voer de volgende stappen uit om een moment opname te maken van een besturingssysteem schijf:

1. Ga naar [Azure Portal](https://portal.azure.com). Selecteer **virtuele machines** in de zijbalk en selecteer vervolgens de VM met een probleem.
1. Selecteer **schijven**in het linkerdeel venster en selecteer vervolgens de naam van de besturingssysteem schijf.
    ![Afbeelding over de naam van de besturingssysteem schijf](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Op de pagina **overzicht** van de besturingssysteem schijf en selecteert u **moment opname maken**.
1. Maak een moment opname op dezelfde locatie als de besturingssysteem schijf.

## <a name="create-a-disk-from-the-snapshot"></a>Een schijf maken op basis van de moment opname
Voer de volgende stappen uit om een schijf te maken op basis van de moment opname:

1. Selecteer **Cloud shell** in het Azure Portal.

    ![Afbeelding over open Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Voer de volgende Power shell-opdrachten uit om een beheerde schijf te maken op basis van de moment opname. Vervang deze voorbeeld namen door de juiste namen.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk.  Premium_LRS or Standard_LRS.
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
3. Als de opdrachten correct worden uitgevoerd, ziet u de nieuwe schijf in de resource groep die u hebt ingevoerd.

## <a name="attach-the-disk-to-another-vm"></a>De schijf koppelen aan een andere virtuele machine
Voor de volgende stappen gebruikt u een andere virtuele machine voor het oplossen van problemen. Nadat u de schijf aan de virtuele machine voor probleem oplossing hebt gekoppeld, kunt u de inhoud van de schijf bekijken en bewerken. Met dit proces kunt u eventuele configuratie fouten corrigeren of aanvullende toepassings-of systeem logboek bestanden bekijken. Voer de volgende stappen uit om de schijf aan een andere virtuele machine te koppelen:

1. Selecteer uw resource groep in de portal en selecteer vervolgens de virtuele machine voor probleem oplossing. Selecteer **schijven**, selecteer **bewerken**en klik vervolgens op **gegevens schijf toevoegen**:

    ![Een bestaande schijf koppelen in de portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Selecteer in de lijst **gegevens schijven** de besturingssysteem schijf van de virtuele machine die u hebt geïdentificeerd. Als u de besturingssysteem schijf niet ziet, zorg er dan voor dat u problemen met de virtuele machine oplost en de besturingssysteem schijf zich in dezelfde regio (locatie) bevindt. 
3. Selecteer **Opslaan** om de wijzigingen toe te passen.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>De gekoppelde gegevens schijf aan de virtuele machine koppelen

1. Open een Extern bureaublad verbinding met de virtuele machine voor probleem oplossing. 
2. Open **Serverbeheer**op de virtuele machine voor problemen oplossen en selecteer vervolgens **Bestands-en opslag Services**. 

    ![Bestands-en opslag Services in Serverbeheer selecteren](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. De gegevens schijf wordt automatisch gedetecteerd en gekoppeld. Selecteer **schijven**als u een lijst met de verbonden schijven wilt weer geven. U kunt uw gegevens schijf selecteren om volume gegevens weer te geven, inclusief de stationsletter. In het volgende voor beeld ziet u de gegevens schijf die is gekoppeld aan en met **F:**:

    ![De schijf is gekoppeld en de volume gegevens in Serverbeheer](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Problemen op de oorspronkelijke virtuele harde schijf oplossen
Als de bestaande virtuele harde schijf is gekoppeld, kunt u nu eventuele onderhouds-en probleemoplossings stappen uitvoeren. Zodra u de problemen hebt opgelost, kunt u doorgaan met de volgende stappen.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>De oorspronkelijke virtuele harde schijf ontkoppelen en loskoppelen
Wanneer de fouten zijn opgelost, koppelt u de bestaande virtuele harde schijf los van uw probleemoplossings-VM. U kunt de virtuele harde schijf niet met andere virtuele machines gebruiken totdat de lease die de virtuele harde schijf koppelt aan de VM voor probleem oplossing is vrijgegeven.

1. Open **Serverbeheer**van de RDP-sessie naar uw VM en selecteer vervolgens **Bestands-en opslag Services**:

    ![Selecteer Bestands-en opslag Services in Serverbeheer](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Selecteer **schijven** en selecteer vervolgens uw gegevens schijf. Klik met de rechter muisknop op uw gegevens schijf en selecteer **offline halen**:

    ![De gegevens schijf als offline instellen in Serverbeheer](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Ontkoppel nu de virtuele harde schijf van de VM. Selecteer uw virtuele machine in de Azure Portal en klik op **schijven**. 
4. Selecteer **bewerken**, selecteer de besturingssysteem schijf die u hebt toegevoegd en klik vervolgens op **ontkoppelen**:

    ![Bestaande virtuele harde schijf ontkoppelen](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Wacht tot de virtuele machine de gegevens schijf heeft losgekoppeld voordat u doorgaat.

## <a name="swap-the-os-disk-for-the-vm"></a>De besturingssysteem schijf voor de virtuele machine wisselen

Azure Portal ondersteunt nu het wijzigen van de besturingssysteem schijf van de virtuele machine. Voer de volgende stappen uit om dit te doen:

1. Ga naar [Azure Portal](https://portal.azure.com). Selecteer **virtuele machines** in de zijbalk en selecteer vervolgens de VM met een probleem.
1. Selecteer **schijven**in het linkerdeel venster en selecteer vervolgens **besturingssysteem schijf wisselen**.
        ![De afbeelding over het wisselen van de besturingssysteem schijf in Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Kies de nieuwe schijf die u hebt gerepareerd en typ de naam van de virtuele machine om de wijziging te bevestigen. Als de schijf niet in de lijst wordt weer geven, wacht u 10 ~ 15 minuten nadat u de schijf hebt losgekoppeld van de virtuele machine voor probleem oplossing. Zorg er ook voor dat de schijf zich op dezelfde locatie bevindt als de virtuele machine.
1. Selecteer OK.

## <a name="next-steps"></a>Volgende stappen
Zie [problemen met RDP-verbindingen met een Azure VM oplossen](troubleshoot-rdp-connection.md)als u problemen ondervindt bij het maken van verbinding met uw virtuele machine. Zie problemen met [toepassings connectiviteit oplossen op een Windows-VM](troubleshoot-app-connection.md)voor problemen met het openen van toepassingen die op uw virtuele machine worden uitgevoerd.

Zie [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md)voor meer informatie over het gebruik van Resource Manager.


