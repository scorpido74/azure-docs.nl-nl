---
title: Problemen met het starten van Linux VM oplossen vanwege bestandssysteem fouten | Microsoft Docs
description: Meer informatie over het oplossen van problemen bij het starten van Linux-VM'S door bestandssysteem fouten
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
ms.openlocfilehash: 8f12b88a0ddbc6ae31f40ab31b0126e4fd66b1a5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325943"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Problemen met het starten van een Linux-VM oplossen vanwege bestandssysteem fouten

U kunt geen verbinding maken met een virtuele machine van Azure Linux met behulp van Secure Shell (SSH). Wanneer u de functie diagnostische gegevens over opstarten uitvoert op [Azure Portal](https://portal.azure.com/), worden logboek vermeldingen weer gegeven die lijken op de volgende voor beelden.

## <a name="examples"></a>Voorbeelden

Hier volgen enkele voor beelden van mogelijke fouten.

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

Dit voor beeld wordt veroorzaakt door een schone fsck. In dit geval zijn er ook extra gegevens schijven gekoppeld aan de VM (/dev/sdc1 en/dev/sde1).

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

Dit probleem kan optreden als het bestands systeem niet op de juiste wijze is afgesloten of problemen met betrekking tot opslag. De problemen zijn onder andere hardware-of software fouten, problemen met stuur Programma's of Program ma's, schrijf fouten enz. Het is altijd belang rijk om een back-up te maken van belang rijke gegevens. De hulpprogram ma's die in dit artikel worden beschreven, kunnen helpen bij het herstellen van bestands systemen, maar het kan ook gebeuren dat er gegevens verloren gaan.

Linux heeft verschillende bestandssysteem controles beschikbaar. De meest voorkomende distributies in azure zijn: [FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific), [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)en [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Oplossing

Om dit probleem op te lossen, start u de virtuele machine op in de nood herstel modus met behulp van de [seriële console](./serial-console-linux.md) en gebruikt u het hulp programma om het bestands systeem te herstellen. Als de seriële console niet is ingeschakeld op uw virtuele machine of niet werkt, raadpleegt u de sectie [de virtuele machine offline herstellen](#repair-the-vm-offline) in dit artikel.

## <a name="use-the-serial-console"></a>Via de seriële console

1. Maak verbinding met de seriële console.

   > [!Note]
   > Zie voor meer informatie over het gebruik van seriële console voor Linux:
   > * [Seriële console gebruiken voor toegang tot de GRUB en de modus voor één gebruiker](serial-console-grub-single-user-mode.md)
   > * [Seriële console gebruiken voor SysRq-en NMI-aanroepen](./serial-console-nmi-sysrq.md)

2. Selecteer de knop aan/uit en selecteer vervolgens VM opnieuw opstarten. (Als de seriële console niet is ingeschakeld of niet is verbonden, ziet u de knop niet.)

   ![AFBEELDING](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Start de virtuele machine op in de nood herstel modus.

4. Voer het wacht woord van uw hoofd account in om u aan te melden bij de nood herstel modus.

5. Gebruik xfs_repair met de-n optie om de fouten in het bestands systeem te detecteren. In het volgende voor beeld wordt ervan uitgegaan dat de systeem partitie/dev/sda1. is Vervang deze door de juiste waarde voor uw virtuele machine:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Voer de volgende opdracht uit om het bestands systeem te herstellen:

   ```
   xfs_repair /dev/sda1
   ```

7. Als het fout bericht ' fout: het bestands systeem heeft waardevolle wijzigingen in de meta gegevens in een logboek dat opnieuw moet worden afgespeeld ' wordt weer gegeven, maakt u een tijdelijke map en koppelt u het bestands systeem:

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. Als de schijf niet kan worden gekoppeld, voert u de xfs_repair opdracht uit met de optie-L (geforceerde logboek registratie toestaan):

   ```
   xfs_repair /dev/sda1 -L
   ```

9. Probeer vervolgens het bestands systeem te koppelen. Als de schijf is gekoppeld, wordt de volgende uitvoer weer gegeven:
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. Start de virtuele machine opnieuw op en controleer of het probleem is opgelost.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>De virtuele machine offline herstellen

1. Koppel de systeem schijf van de virtuele machine als een gegevens schijf aan een herstel-VM (wille keurige werkende Linux-machine). U kunt hiervoor [cli-opdrachten](./troubleshoot-recovery-disks-linux.md) gebruiken of u kunt de herstel-VM instellen met behulp van de opdrachten voor het herstellen van de [virtuele machine](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Zoek het label station van de systeem schijf die u hebt gekoppeld. In dit geval gaan we ervan uit dat het label van de systeem schijf die u hebt gekoppeld,/dev/sdc1. is Vervang deze door de juiste waarde voor uw virtuele machine.

3. Gebruik xfs_repair met de-n optie om de fouten in het bestands systeem te detecteren.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Voer de volgende opdracht uit om het bestands systeem te herstellen:

   ```
   xfs_repair /dev/sdc1
   ```

5. Als het fout bericht ' fout: het bestands systeem heeft waardevolle wijzigingen in de meta gegevens in een logboek dat opnieuw moet worden afgespeeld ' wordt weer gegeven, maakt u een tijdelijke map en koppelt u het bestands systeem:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Als de schijf niet kan worden gekoppeld, voert u de xfs_repair opdracht uit met de optie-L (geforceerde logboek registratie toestaan):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Probeer vervolgens het bestands systeem te koppelen. Als de schijf is gekoppeld, wordt de volgende uitvoer weer gegeven:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Ontkoppel de oorspronkelijke virtuele harde schijf en maak vervolgens een virtuele machine van de oorspronkelijke systeem schijf. Hiervoor kunt u [cli-opdrachten](troubleshoot-recovery-disks-linux.md) of de opdrachten voor het [herstellen](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) van de VM gebruiken als u deze hebt gebruikt om de herstel-VM te maken.

8. Controleer of het probleem is opgelost.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met een virtuele Linux-machine oplossen door de besturingssysteem schijf te koppelen aan een herstel-VM met de Azure CLI 2,0](./troubleshoot-recovery-disks-linux.md)
* [Een gegevens schijf koppelen aan een virtuele Linux-machine met behulp van de portal](../linux/attach-disk-portal.md)
