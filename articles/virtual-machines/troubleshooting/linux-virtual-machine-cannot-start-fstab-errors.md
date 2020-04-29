---
title: Problemen met het starten van Linux VM oplossen vanwege fstab-fouten | Microsoft Docs
description: In dit artikel wordt uitgelegd waarom Linux VM niet kan worden gestart en hoe het probleem kan worden opgelost.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80351156"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Problemen met het starten van Linux VM oplossen vanwege fstab-fouten

U kunt geen verbinding maken met een virtuele machine van Azure Linux met behulp van een SSH-verbinding (Secure Shell). Wanneer u de functie [Diagnostische gegevens over opstarten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) uitvoert op de [Azure Portal](https://portal.azure.com/), worden logboek vermeldingen weer gegeven die lijken op de volgende voor beelden:

## <a name="examples"></a>Voorbeelden

Hier volgen enkele voor beelden van mogelijke fouten.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Voor beeld 1: een schijf wordt gekoppeld door de SCSI-ID in plaats van de Universally Unique Identifier (UUID)

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Voor beeld 2: een niet-gekoppeld apparaat ontbreekt op CentOS

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

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Voor beeld 3: een virtuele machine kan niet worden gestart vanwege een onjuiste fstab of omdat de schijf niet meer is gekoppeld

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Voor beeld 4: een serie logboek vermelding bevat een onjuiste UUID

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

Dit probleem kan optreden als de syntaxis van de bestandssysteem tabel (fstab) onjuist is of als een vereiste gegevens schijf die is toegewezen aan een vermelding in het bestand/etc/fstab-bestand, niet is gekoppeld aan de virtuele machine.

## <a name="resolution"></a>Oplossing

Om dit probleem op te lossen, start u de virtuele machine in de nood herstel modus met behulp van de seriële console voor Azure Virtual Machines. Gebruik vervolgens het hulp programma om het bestands systeem te herstellen. Als de seriële console niet is ingeschakeld op uw virtuele machine, gaat u naar de sectie [de virtuele machine offline herstellen](#repair-the-vm-offline) .

## <a name="use-the-serial-console"></a>Via de seriële console

### <a name="using-single-user-mode"></a>Modus voor één gebruiker gebruiken

1. Maak verbinding met [de seriële console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Seriële console gebruiken om de [modus voor één](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode) gebruiker in de modus voor één gebruiker in te voeren
3. Zodra de VM is opgestart in de modus voor één gebruiker. Gebruik uw favoriete tekst editor om het fstab-bestand te openen. 

   ```
   # nano /etc/fstab
   ```

4. Bekijk de vermelde bestands systemen. Elke regel in het fstab-bestand geeft aan dat een bestands systeem wordt gekoppeld wanneer de virtuele machine wordt gestart. Voer de man fstab-opdracht uit voor meer informatie over de syntaxis van het fstab-bestand. Als u een start fout wilt oplossen, controleert u elke regel om er zeker van te zijn dat deze correct is in zowel de structuur als de inhoud.

   > [!Note]
   > * De velden op elke regel worden gescheiden door tabs of spaties. Lege regels worden genegeerd. Regels met een hekje (#) als het eerste teken zijn opmerkingen. Commentaar regels kunnen in het fstab-bestand blijven, maar ze worden niet verwerkt. We raden u aan om fstab regels te controleren die u niet weet in plaats van de regels te verwijderen.
   > * Als de virtuele machine moet worden hersteld en gestart, moeten de bestandssysteem partities de enige vereiste partities zijn. De virtuele machine kan toepassings fouten ondervinden over extra opmerkingen met een opmerking. De virtuele machine moet echter worden gestart zonder de extra partities. U kunt later opmerkingen toevoegen aan alle opmerkingen regels.
   > * We raden aan dat u gegevens schijven op virtuele Azure-machines koppelt met behulp van de UUID van de bestandssysteem partitie. Voer bijvoorbeeld de volgende opdracht uit:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Als u de UUID van het bestands systeem wilt bepalen, voert u de opdracht blkid uit. Voer de man blkid opdracht uit voor meer informatie over de syntaxis.
   > * De niet-werkende optie zorgt ervoor dat de VM wordt gestart, zelfs als het bestands systeem beschadigd is of wanneer het bestands systeem niet aanwezig is bij het opstarten. We raden u aan de optie niet in het fstab-bestand te gebruiken om het opstarten van de computer te laten door gaan nadat er fouten zijn opgetreden in partities die niet vereist zijn voor het starten van de VM.

5. U kunt onjuiste of overbodige regels in het fstab-bestand wijzigen of opmerkingen maken, zodat de virtuele machine correct kan worden gestart.

6. Sla de wijzigingen in het fstab-bestand op.

7. Start de VM opnieuw op met behulp van de onderstaande opdracht.
   
   ```
   # reboot -f
   ```
> [!Note]
   > U kunt ook de opdracht ' Ctrl + x ' gebruiken, waarmee ook de virtuele machine opnieuw wordt opgestart.


8. Als de vermeldingen opmerkingen of correctie geslaagd zijn, moet het systeem een bash-prompt in de portal bereiken. Controleer of u verbinding kunt maken met de virtuele machine.

### <a name="using-root-password"></a>Basis wachtwoord gebruiken

1. Maak verbinding met [de seriële console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Meld u aan bij het systeem met behulp van een lokale gebruiker en een wacht woord.

   > [!Note]
   > U kunt geen SSH-sleutel gebruiken om u aan te melden bij het systeem in de seriële console.

3. Zoek naar de fout die aangeeft dat de schijf niet is gekoppeld. In het volgende voor beeld probeert het systeem een schijf toe te voegen die niet meer aanwezig is:

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

4. Maak verbinding met de virtuele machine met behulp van het hoofd wachtwoord (op Red Hat gebaseerde Vm's).

5. Gebruik uw favoriete tekst editor om het fstab-bestand te openen. Nadat de schijf is gekoppeld, voert u de volgende opdracht uit voor nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Bekijk de vermelde bestands systemen. Elke regel in het fstab-bestand geeft aan dat een bestands systeem wordt gekoppeld wanneer de virtuele machine wordt gestart. Voer de man fstab-opdracht uit voor meer informatie over de syntaxis van het fstab-bestand. Als u een start fout wilt oplossen, controleert u elke regel om er zeker van te zijn dat deze correct is in zowel de structuur als de inhoud.

   > [!Note]
   > * De velden op elke regel worden gescheiden door tabs of spaties. Lege regels worden genegeerd. Regels met een hekje (#) als het eerste teken zijn opmerkingen. Commentaar regels kunnen in het fstab-bestand blijven, maar ze worden niet verwerkt. We raden u aan om fstab regels te controleren die u niet weet in plaats van de regels te verwijderen.
   > * Als de virtuele machine moet worden hersteld en gestart, moeten de bestandssysteem partities de enige vereiste partities zijn. De virtuele machine kan toepassings fouten ondervinden over extra opmerkingen met een opmerking. De virtuele machine moet echter worden gestart zonder de extra partities. U kunt later opmerkingen toevoegen aan alle opmerkingen regels.
   > * We raden aan dat u gegevens schijven op virtuele Azure-machines koppelt met behulp van de UUID van de bestandssysteem partitie. Voer bijvoorbeeld de volgende opdracht uit:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Als u de UUID van het bestands systeem wilt bepalen, voert u de opdracht blkid uit. Voer de man blkid opdracht uit voor meer informatie over de syntaxis.
   > * De niet-werkende optie zorgt ervoor dat de VM wordt gestart, zelfs als het bestands systeem beschadigd is of wanneer het bestands systeem niet aanwezig is bij het opstarten. We raden u aan de optie niet in het fstab-bestand te gebruiken om het opstarten van de computer te laten door gaan nadat er fouten zijn opgetreden in partities die niet vereist zijn voor het starten van de VM.

7. U kunt onjuiste of overbodige regels in het fstab-bestand wijzigen of opmerkingen maken, zodat de virtuele machine correct kan worden gestart.

8. Sla de wijzigingen in het fstab-bestand op.

9. Start de virtuele machine opnieuw op.

10. Als de vermeldingen opmerkingen of correctie geslaagd zijn, moet het systeem een bash-prompt in de portal bereiken. Controleer of u verbinding kunt maken met de virtuele machine.

11. Controleer uw koppel punten wanneer u de wijziging van de fstab test door het koppelen – een opdracht uit te voeren. Als er geen fouten zijn, moeten uw koppel punten goed zijn.

## <a name="repair-the-vm-offline"></a>De virtuele machine offline herstellen

1. Koppel de systeem schijf van de virtuele machine als een gegevens schijf aan een herstel-VM (wille keurige werkende Linux-machine). U kunt hiervoor [cli-opdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) gebruiken of u kunt de herstel-VM instellen met behulp van de opdrachten voor het herstellen van de [virtuele machine](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Nadat u de systeem schijf als een gegevens schijf op de herstel-VM hebt gekoppeld, maakt u een back-up van het fstab-bestand voordat u wijzigingen aanbrengt en voert u de volgende stappen uit om het fstab-bestand te corrigeren.

3.    Zoek naar de fout die aangeeft dat de schijf niet is gekoppeld. In het volgende voor beeld probeert het systeem een schijf toe te voegen die niet meer aanwezig is:

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. Maak verbinding met de virtuele machine met behulp van het hoofd wachtwoord (op Red Hat gebaseerde Vm's).

5. Gebruik uw favoriete tekst editor om het fstab-bestand te openen. Nadat de schijf is gekoppeld, voert u de volgende opdracht uit voor nano. Zorg ervoor dat u werkt aan het fstab-bestand dat zich op de gekoppelde schijf bevindt en niet het fstab-bestand dat zich op de virtuele machine voor de werk ruimte bevond.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Bekijk de vermelde bestands systemen. Elke regel in het fstab-bestand geeft aan dat een bestands systeem wordt gekoppeld wanneer de virtuele machine wordt gestart. Voer de man fstab-opdracht uit voor meer informatie over de syntaxis van het fstab-bestand. Als u een start fout wilt oplossen, controleert u elke regel om er zeker van te zijn dat deze correct is in zowel de structuur als de inhoud.

   > [!Note]
   > * De velden op elke regel worden gescheiden door tabs of spaties. Lege regels worden genegeerd. Regels met een hekje (#) als het eerste teken zijn opmerkingen. Commentaar regels kunnen in het fstab-bestand blijven, maar ze worden niet verwerkt. We raden u aan om fstab regels te controleren die u niet weet in plaats van de regels te verwijderen.
   > * Als de virtuele machine moet worden hersteld en gestart, moeten de bestandssysteem partities de enige vereiste partities zijn. De virtuele machine kan toepassings fouten ondervinden over extra opmerkingen met een opmerking. De virtuele machine moet echter worden gestart zonder de extra partities. U kunt later opmerkingen toevoegen aan alle opmerkingen regels.
   > * We raden aan dat u gegevens schijven op virtuele Azure-machines koppelt met behulp van de UUID van de bestandssysteem partitie. Voer bijvoorbeeld de volgende opdracht uit:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Als u de UUID van het bestands systeem wilt bepalen, voert u de opdracht blkid uit. Voer de man blkid opdracht uit voor meer informatie over de syntaxis. U ziet dat de schijf die u wilt herstellen, nu is gekoppeld op een nieuwe virtuele machine. Hoewel de UUID consistent moeten zijn, verschillen de partitie-Id's van de apparaten (bijvoorbeeld '/dev/sda1 ') op deze VM. De bestandssysteem partities van de oorspronkelijke beschadigde virtuele machine die zich op een niet-systeem-VHD bevinden, zijn niet beschikbaar voor de Recovery-VM [met behulp van CLI-opdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux).
   > * De niet-werkende optie zorgt ervoor dat de VM wordt gestart, zelfs als het bestands systeem beschadigd is of wanneer het bestands systeem niet aanwezig is bij het opstarten. We raden u aan de optie niet in het fstab-bestand te gebruiken om het opstarten van de computer te laten door gaan nadat er fouten zijn opgetreden in partities die niet vereist zijn voor het starten van de VM.

7. U kunt onjuiste of overbodige regels in het fstab-bestand wijzigen of opmerkingen maken, zodat de virtuele machine correct kan worden gestart.

8. Sla de wijzigingen in het fstab-bestand op.

9. Start de virtuele machine opnieuw op of bouw de oorspronkelijke VM opnieuw op.

10. Als de vermeldingen opmerkingen of correctie geslaagd zijn, moet het systeem een bash-prompt in de portal bereiken. Controleer of u verbinding kunt maken met de virtuele machine.

11. Controleer uw koppel punten wanneer u de wijziging van de fstab test door het koppelen – een opdracht uit te voeren. Als er geen fouten zijn, moeten uw koppel punten goed zijn.

12. Ontkoppel de oorspronkelijke virtuele harde schijf en maak vervolgens een virtuele machine van de oorspronkelijke systeem schijf. Hiervoor kunt u [cli-opdrachten](troubleshoot-recovery-disks-linux.md) of de opdrachten voor het [herstellen](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) van de VM gebruiken als u deze hebt gebruikt om de herstel-VM te maken.

13. Nadat u de virtuele machine opnieuw hebt gemaakt en u er verbinding mee kunt maken via SSH, voert u de volgende acties uit:
    * Bekijk de fstab-regels die tijdens het herstel zijn gewijzigd of waarvoor een opmerking is opgetreden.
    * Zorg ervoor dat u de UUID gebruikt en dat u de optie geen juiste hebt.
    * Test eventuele wijzigingen in fstab voordat u de virtuele machine opnieuw opstart. Gebruik hiervoor de volgende opdracht:``$ sudo mount -a``
    * Maak een extra kopie van het gecorrigeerde fstab-bestand voor gebruik in toekomstige herstel scenario's.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met een virtuele Linux-machine oplossen door de besturingssysteem schijf te koppelen aan een herstel-VM met de Azure CLI 2,0](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Problemen met een virtuele Linux-machine oplossen door de besturingssysteem schijf te koppelen aan een herstel-VM met behulp van de Azure Portal](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

