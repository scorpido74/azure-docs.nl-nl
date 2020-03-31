---
title: Problemen met het starten van Linux-vm's oplossen als gevolg van fouten in het bestandssysteem | Microsoft Documenten
description: Legt uit waarom Linux VM niet kan starten en hoe het probleem op te lossen.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: 455cb1e0067217be6edcf665e8c07e8fcd684ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842398"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Problemen met het starten van Linux VM's oplossen als gevolg van fouten in het bestandssysteem

U geen verbinding maken met een Virtuele Azure Linux-machine (VM) met Behulp van Secure Shell (SSH). Wanneer u de functie Diagnostische gegevens opstart uitvoert op [azure-portal,](https://portal.azure.com/)ziet u logboekvermeldingen die lijken op de volgende voorbeelden.

## <a name="examples"></a>Voorbeelden

Hieronder volgen voorbeelden van mogelijke fouten.

### <a name="example-1"></a>Voorbeeld 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>Voorbeeld 2

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>Voorbeeld 3

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>Voorbeeld 4 

Dit voorbeeld wordt veroorzaakt door een schone fsck. In dit geval zijn er ook extra gegevensschijven gekoppeld aan de VM (/dev/sdc1 en /dev/sde1).

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

Dit probleem kan optreden als het bestandssysteem niet netjes is afgesloten of opslaggerelateerde problemen. De problemen omvatten hardware- of softwarefouten, problemen met stuurprogramma's of programma's, schrijffouten, enz. Het is altijd belangrijk om een back-up van kritieke gegevens te hebben. De hulpprogramma's die in dit artikel worden beschreven, kunnen helpen bij het herstellen van bestandssystemen, maar het is dat gegevensverlies nog steeds kan optreden.

Linux heeft verschillende bestandssysteemcheckers beschikbaar. De meest voorkomende voor de distributies in Azure zijn: [FSCK,](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific) [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific), en [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Oplossing

Als u dit probleem wilt oplossen, start u de VM op in de noodmodus met behulp van de [seriële console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) en gebruikt u dat gereedschap om het bestandssysteem te herstellen. Zie het gedeelte [Vm offline herstellen](#repair-the-vm-offline) van dit artikel als de seriële console niet is ingeschakeld op uw virtuele machine of niet werkt.

## <a name="use-the-serial-console"></a>Via de seriële console

1. Maak verbinding met de seriële console.

   > [!Note]
   > Zie voor meer informatie over het gebruik van seriële console voor Linux:
   > * [Seriële console gebruiken om toegang te krijgen tot GRUB en de modus voor één gebruiker](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [Seriële console gebruiken voor SysRq- en NMI-gesprekken](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. Selecteer de knop Aan/uit-pictogram en selecteer VM opnieuw starten. (Als de seriële console niet is ingeschakeld of niet is verbonden, ziet u de knop niet.)

   ![AFBEELDING](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Start de VM in de noodmodus.

4. Voer het wachtwoord van uw root-account in om u aan te melden bij de noodmodus.

5. Gebruik xfs_repair met de optie -n om de fouten in het bestandssysteem te detecteren. In het volgende voorbeeld gaan we ervan uit dat de systeempartitie /dev/sda1 is. Vervang deze door de juiste waarde voor uw vm:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Voer de volgende opdracht uit om het bestandssysteem te herstellen:

   ```
   xfs_repair /dev/sda1
   ```

7. Als u het foutbericht "FOUT: het bestandssysteem heeft waardevolle metadata wijzigingen in een logboek dat moet worden afgespeeld", maak een tijdelijke directory en monteer het bestandssysteem:

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. Als de schijf niet wordt gemonteerd, voert u de opdracht xfs_repair uit met de optie -L (force log zeroing):

   ```
   xfs_repair /dev/sda1 -L
   ```

9. Probeer vervolgens het bestandssysteem te monteren. Als de schijf is gemonteerd, ontvangt u de volgende uitvoer:
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. Start de VM opnieuw en controleer of het probleem is opgelost.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>De VM offline herstellen

1. Bevestig de systeemschijf van de VM als gegevensschijf aan een herstel-vm (elke werkende Linux-VM). Hiervoor u [CLI-opdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) gebruiken of de herstel-VM automatiseren met behulp van de [vm-reparatieopdrachten.](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

2. Zoek het schijflabel van de systeemschijf die u hebt aangesloten. In dit geval gaan we ervan uit dat het label van de systeemschijf die u hebt aangesloten /dev/sdc1 is. Vervang deze door de juiste waarde voor uw vm.

3. Gebruik xfs_repair met de optie -n om de fouten in het bestandssysteem te detecteren.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Voer de volgende opdracht uit om het bestandssysteem te herstellen:

   ```
   xfs_repair /dev/sdc1
   ```

5. Als u het foutbericht "FOUT: het bestandssysteem heeft waardevolle metadata wijzigingen in een logboek dat moet worden afgespeeld", maak een tijdelijke directory en monteer het bestandssysteem:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Als de schijf niet wordt gemonteerd, voert u de opdracht xfs_repair uit met de optie -L (force log zeroing):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Probeer vervolgens het bestandssysteem te monteren. Als de schijf is gemonteerd, ontvangt u de volgende uitvoer:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Maak de oorspronkelijke virtuele harde schijf los en maak vervolgens een vm van de oorspronkelijke systeemschijf. Hiervoor u [CLI-opdrachten](troubleshoot-recovery-disks-linux.md) of de [VM-reparatieopdrachten](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) gebruiken als u deze hebt gebruikt om de herstel-vm te maken.

8. Controleer of het probleem is opgelost.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met een Linux-vm oplossen door de OS-schijf aan een herstelvm te koppelen met de Azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [De portal gebruiken om een gegevensschijf aan een Linux-vm te koppelen](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)

