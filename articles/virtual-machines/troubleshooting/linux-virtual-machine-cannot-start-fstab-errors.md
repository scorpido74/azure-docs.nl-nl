---
title: Problemen met het starten van Linux VM's oplossen als gevolg van fstab-fouten | Microsoft Documenten
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
ms.openlocfilehash: 7e16eabc4f9572591eabd37b93258fcd783cce7e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351156"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Problemen met het starten van Linux VM's oplossen als gevolg van fstab-fouten

U geen verbinding maken met een Vm (Azure Linux Virtual Machine) via een Secure Shell -verbinding (SSH). Wanneer u de functie [Opstartdiagnose](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) uitvoert op de [Azure-portal,](https://portal.azure.com/)ziet u logboekvermeldingen die lijken op de volgende voorbeelden:

## <a name="examples"></a>Voorbeelden

Hieronder volgen voorbeelden van mogelijke fouten.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Voorbeeld 1: Een schijf wordt gemonteerd door de SCSI-id in plaats van de universeel unieke id (UUID)

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Voorbeeld 2: Een apparaat zonder beslag ontbreekt op CentOS

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sdd1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sde1: nonexistent device ("nofail" fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Voorbeeld 3: Een VM kan niet worden gestart vanwege een foutieve configuratie of omdat de schijf niet meer is gekoppeld

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Voorbeeld 4: Een seriële logboekvermelding toont een onjuiste UUID

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run 'setenforce 1' to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

Dit probleem kan optreden als de syntaxis van de bestandssystementabel (fstab) onjuist is of als een vereiste gegevensschijf die is toegewezen aan een vermelding in het bestand "/etc/fstab" niet aan de VM is gekoppeld.

## <a name="resolution"></a>Oplossing

Als u dit probleem wilt oplossen, start u de VM in de noodmodus met behulp van de seriële console voor Azure Virtual Machines. Gebruik vervolgens het gereedschap om het bestandssysteem te repareren. Als de seriële console niet is ingeschakeld op uw vm, gaat u naar de sectie [Offline herstellen van de VM.](#repair-the-vm-offline)

## <a name="use-the-serial-console"></a>Via de seriële console

### <a name="using-single-user-mode"></a>Modus voor één gebruiker gebruiken

1. Maak verbinding met [de seriële console.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
2. Seriële console gebruiken om [de modus Één gebruiker in één](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode) gebruiker te gebruiken
3. Zodra de vm is opgestart in de modus voor één gebruiker. Gebruik uw favoriete teksteditor om het fstab-bestand te openen. 

   ```
   # nano /etc/fstab
   ```

4. Bekijk de vermelde bestandssystemen. Elke regel in het fstab-bestand geeft een bestandssysteem aan dat wordt gemonteerd wanneer de VM wordt gestart. Voer de opdracht man fstab uit voor meer informatie over de syntaxis van het fstab-bestand. Als u een beginfout wilt oplossen, controleert u elke regel om te controleren of deze correct is in zowel structuur als inhoud.

   > [!Note]
   > * Velden op elke regel worden gescheiden door tabbladen of spaties. Lege regels worden genegeerd. Regels met een getalteken (#) als het eerste teken zijn opmerkingen. Opmerkingen kunnen in het fstab-bestand blijven staan, maar worden niet verwerkt. We raden u aan om opmerkingen te maken over fstab-regels waarover u niet zeker bent in plaats van de regels te verwijderen.
   > * Als de VM kan worden hersteld en gestart, moeten de bestandssysteempartities de enige vereiste partities zijn. De VM kan toepassingsfouten ondervinden over extra opmerkingen. De VM moet echter starten zonder de extra partities. U later geen commentaar geven op opmerkingen.
   > * We raden u aan gegevensschijven op Azure VM's te monteren met behulp van de UUID van de bestandssysteempartitie. Voer bijvoorbeeld de volgende opdracht uit:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Voer de opdracht blkid uit om de UUID van het bestandssysteem te bepalen. Voer de opdracht man blkid uit voor meer informatie over de syntaxis.
   > * De optie nofail helpt ervoor te zorgen dat de VM wordt gestart, zelfs als het bestandssysteem beschadigd is of het bestandssysteem niet bestaat bij het opstarten. We raden u aan de optie nofail in het fstab-bestand te gebruiken om het opstarten in staat te stellen door te gaan nadat er fouten zijn opgetreden in partities die niet nodig zijn om de VM te starten.

5. Wijzig of reageer op onjuiste of onnodige regels in het fstab-bestand om de VM correct te laten starten.

6. Sla de wijzigingen op in het fstab-bestand.

7. Start de vm opnieuw op met de onderstaande opdracht.
   
   ```
   # reboot -f
   ```
> [!Note]
   > U ook de opdracht Ctrl+x gebruiken, die ook de vm opnieuw zou opstarten.


8. Als de opmerkingen opmerking of fix is geslaagd, moet het systeem een bash prompt in de portal te bereiken. Controleer of u verbinding maken met de VM.

### <a name="using-root-password"></a>Root-wachtwoord gebruiken

1. Maak verbinding met [de seriële console.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
2. Meld u aan bij het systeem met behulp van een lokale gebruiker en wachtwoord.

   > [!Note]
   > U geen SSH-sleutel gebruiken om u aan te melden bij het systeem in de seriële console.

3. Zoek naar de fout die aangeeft dat de schijf niet is gemonteerd. In het volgende voorbeeld probeerde het systeem een schijf te bevestigen die niet meer aanwezig was:

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. Maak verbinding met de VM met het hoofdwachtwoord (Red Hat-gebaseerde VM's).

5. Gebruik uw favoriete teksteditor om het fstab-bestand te openen. Nadat de schijf is gemonteerd, voert u de volgende opdracht uit voor Nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Bekijk de vermelde bestandssystemen. Elke regel in het fstab-bestand geeft een bestandssysteem aan dat wordt gemonteerd wanneer de VM wordt gestart. Voer de opdracht man fstab uit voor meer informatie over de syntaxis van het fstab-bestand. Als u een beginfout wilt oplossen, controleert u elke regel om te controleren of deze correct is in zowel structuur als inhoud.

   > [!Note]
   > * Velden op elke regel worden gescheiden door tabbladen of spaties. Lege regels worden genegeerd. Regels met een getalteken (#) als het eerste teken zijn opmerkingen. Opmerkingen kunnen in het fstab-bestand blijven staan, maar worden niet verwerkt. We raden u aan om opmerkingen te maken over fstab-regels waarover u niet zeker bent in plaats van de regels te verwijderen.
   > * Als de VM kan worden hersteld en gestart, moeten de bestandssysteempartities de enige vereiste partities zijn. De VM kan toepassingsfouten ondervinden over extra opmerkingen. De VM moet echter starten zonder de extra partities. U later geen commentaar geven op opmerkingen.
   > * We raden u aan gegevensschijven op Azure VM's te monteren met behulp van de UUID van de bestandssysteempartitie. Voer bijvoorbeeld de volgende opdracht uit:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Voer de opdracht blkid uit om de UUID van het bestandssysteem te bepalen. Voer de opdracht man blkid uit voor meer informatie over de syntaxis.
   > * De optie nofail helpt ervoor te zorgen dat de VM wordt gestart, zelfs als het bestandssysteem beschadigd is of het bestandssysteem niet bestaat bij het opstarten. We raden u aan de optie nofail in het fstab-bestand te gebruiken om het opstarten in staat te stellen door te gaan nadat er fouten zijn opgetreden in partities die niet nodig zijn om de VM te starten.

7. Wijzig of reageer op onjuiste of onnodige regels in het fstab-bestand om de VM correct te laten starten.

8. Sla de wijzigingen op in het fstab-bestand.

9. Start de virtuele machine opnieuw op.

10. Als de opmerkingen opmerking of fix is geslaagd, moet het systeem een bash prompt in de portal te bereiken. Controleer of u verbinding maken met de VM.

11. Controleer uw bevestigingspunten wanneer u een fstab-wijziging test door de houder uit te voeren : een opdracht. Als er geen fouten zijn, moeten uw bevestigingspunten goed zijn.

## <a name="repair-the-vm-offline"></a>De VM offline herstellen

1. Bevestig de systeemschijf van de VM als gegevensschijf aan een herstel-vm (elke werkende Linux-VM). Hiervoor u [CLI-opdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) gebruiken of de herstel-VM automatiseren met behulp van de [vm-reparatieopdrachten.](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

2. Nadat u de systeemschijf hebt gemonteerd als gegevensschijf op de herstel-VM, maakt u een back-up van het fstab-bestand voordat u wijzigingen aanbrengt en volgt u de volgende stappen om het fstab-bestand te corrigeren.

3.    Zoek naar de fout die aangeeft dat de schijf niet is gemonteerd. In het volgende voorbeeld probeerde het systeem een schijf te bevestigen die niet meer aanwezig was:

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. Maak verbinding met de VM met het hoofdwachtwoord (Red Hat-gebaseerde VM's).

5. Gebruik uw favoriete teksteditor om het fstab-bestand te openen. Nadat de schijf is gemonteerd, voert u de volgende opdracht voor Nano uit. Zorg ervoor dat u werkt aan het fstab-bestand dat zich op de gemonteerde schijf bevindt en niet het fstab-bestand dat op de reddings-VM staat.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Bekijk de vermelde bestandssystemen. Elke regel in het fstab-bestand geeft een bestandssysteem aan dat wordt gemonteerd wanneer de VM wordt gestart. Voer de opdracht man fstab uit voor meer informatie over de syntaxis van het fstab-bestand. Als u een beginfout wilt oplossen, controleert u elke regel om te controleren of deze correct is in zowel structuur als inhoud.

   > [!Note]
   > * Velden op elke regel worden gescheiden door tabbladen of spaties. Lege regels worden genegeerd. Regels met een getalteken (#) als het eerste teken zijn opmerkingen. Opmerkingen kunnen in het fstab-bestand blijven staan, maar worden niet verwerkt. We raden u aan om opmerkingen te maken over fstab-regels waarover u niet zeker bent in plaats van de regels te verwijderen.
   > * Als de VM kan worden hersteld en gestart, moeten de bestandssysteempartities de enige vereiste partities zijn. De VM kan toepassingsfouten ondervinden over extra opmerkingen. De VM moet echter starten zonder de extra partities. U later geen commentaar geven op opmerkingen.
   > * We raden u aan gegevensschijven op Azure VM's te monteren met behulp van de UUID van de bestandssysteempartitie. Voer bijvoorbeeld de volgende opdracht uit:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Voer de opdracht blkid uit om de UUID van het bestandssysteem te bepalen. Voer de opdracht man blkid uit voor meer informatie over de syntaxis. Merk op dat de schijf die u wilt herstellen nu is gemonteerd op een nieuwe VM. Hoewel de uuid's consistent moeten zijn, zijn de apparaatpartitie-id's (bijvoorbeeld "/dev/sda1") verschillend op deze VM. De bestandssysteempartities van de oorspronkelijke ontbrekende VM die zich op een niet-systeemVHD bevinden, zijn niet beschikbaar voor de herstel-VM [met CLI-opdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux).
   > * De optie nofail helpt ervoor te zorgen dat de VM wordt gestart, zelfs als het bestandssysteem beschadigd is of het bestandssysteem niet bestaat bij het opstarten. We raden u aan de optie nofail in het fstab-bestand te gebruiken om het opstarten in staat te stellen door te gaan nadat er fouten zijn opgetreden in partities die niet nodig zijn om de VM te starten.

7. Wijzig of reageer op onjuiste of onnodige regels in het fstab-bestand om de VM correct te laten starten.

8. Sla de wijzigingen op in het fstab-bestand.

9. Start de virtuele machine opnieuw op of bouw de oorspronkelijke vm opnieuw op.

10. Als de opmerkingen opmerking of fix is geslaagd, moet het systeem een bash prompt in de portal te bereiken. Controleer of u verbinding maken met de VM.

11. Controleer uw bevestigingspunten wanneer u een fstab-wijziging test door de houder uit te voeren : een opdracht. Als er geen fouten zijn, moeten uw bevestigingspunten goed zijn.

12. Maak de oorspronkelijke virtuele harde schijf los en maak vervolgens een vm van de oorspronkelijke systeemschijf. Hiervoor u [CLI-opdrachten](troubleshoot-recovery-disks-linux.md) of de [VM-reparatieopdrachten](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) gebruiken als u deze hebt gebruikt om de herstel-vm te maken.

13. Nadat u de VM opnieuw hebt gemaakt en u er verbinding mee maken via SSH, neemt u de volgende acties uit:
    * Bekijk een van de fstab-regels die tijdens het herstel zijn gewijzigd of becommentarieerd.
    * Zorg ervoor dat u UUID en de optie nofail op de juiste manier gebruikt.
    * Test eventuele fstab-wijzigingen voordat u de vm opnieuw start. Gebruik hiervoor de volgende opdracht:``$ sudo mount -a``
    * Maak een extra kopie van het gecorrigeerde fstab-bestand voor gebruik in toekomstige herstelscenario's.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met een Linux-vm oplossen door de OS-schijf aan een herstelvm te koppelen met de Azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Problemen met een Linux-vm oplossen door de OS-schijf aan een herstel-vm te koppelen met behulp van de Azure-portal](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

