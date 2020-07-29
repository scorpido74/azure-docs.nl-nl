---
title: Chroot-omgeving in een Linux-hulpverlenings-VM.
description: In dit artikel wordt beschreven hoe u problemen met de chroot-omgeving kunt oplossen in de virtuele machine voor herstel in Linux.
mservices: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.date: 05/05/2020
ms.author: kaib
ms.openlocfilehash: d6605aa77cfa746f60fc3b23e120267a2614f7b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82864680"
---
# <a name="chroot-environment-in-a-linux-rescue-vm"></a>Chroot-omgeving in een Linux-hulpverlenings-VM

In dit artikel wordt beschreven hoe u problemen met de chroot-omgeving kunt oplossen in de virtuele machine voor herstel in Linux.

## <a name="ubuntu-16x--ubuntu-18x"></a>Ubuntu 16. x && Ubuntu 18. x

1. De betreffende virtuele machine stoppen of de toewijzing ervan ongedaan maken.
1. Maak een herstel-VM-installatie kopie van dezelfde versie van het besturings systeem, in dezelfde resource groep (herstel) en locatie met beheerde schijf.
1. Gebruik de Azure Portal om een moment opname te maken van de besturingssysteem schijf van de virtuele machine.
1. Maak een schijf van de moment opname van de besturingssysteem schijf en koppel deze aan de virtuele machine voor herstel.
1. Wanneer de schijf is gemaakt, lost u de chroot-omgeving op in de virtuele machine voor herstel.

   1. Open uw VM als de hoofd gebruiker met behulp van de volgende opdracht:

      `#sudo su -`

   1. Zoek de schijf met behulp van `dmesg` (de methode die u gebruikt om de nieuwe schijf te detecteren) kan verschillen. In het volgende voor beeld wordt **dmesg** gebruikt om te filteren op **SCSI** -schijven:

      `dmesg | grep SCSI`

      De uitvoer is vergelijkbaar met het volgende voor beeld. In dit voor beeld willen we de **Dit SDC** -schijf:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Gebruik de volgende opdrachten om toegang te krijgen tot de chroot-omgeving:

      ```
      #mkdir /rescue
      #mount /dev/sdc1 /rescue
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Problemen met de chroot-omgeving oplossen.

   1. Gebruik de volgende opdrachten om de chroot-omgeving af te sluiten:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue
      ```

      > [!NOTE]
      > Als het fout bericht `unable to unmount /rescue` , voegt u de optie-l toe aan de umount-opdracht.
      >
      > Voorbeeld: `umount -l /rescue`

1. Ontkoppel de schijf van de virtuele machine voor herstel en voer een schijf wisseling uit met de oorspronkelijke VM.
1. Start de oorspronkelijke VM en controleer de connectiviteit.

## <a name="rhelcentosoracle-6x--oracle-8x--rhelcentos-7x-with-raw-partitions"></a>RHEL/CentOS/Oracle 6. x && Oracle 8. x && RHEL/CentOS 7. x met onbewerkte partities

1. De betreffende virtuele machine stoppen of de toewijzing ervan ongedaan maken.
1. Maak een herstel-VM-installatie kopie van dezelfde versie van het besturings systeem, in dezelfde resource groep (herstel) en locatie met beheerde schijf.
1. Gebruik de Azure Portal om een moment opname te maken van de besturingssysteem schijf van de virtuele machine.
1. Maak een schijf van de moment opname van de besturingssysteem schijf en koppel deze aan de virtuele machine voor herstel.
1. Wanneer de schijf is gemaakt, lost u de chroot-omgeving op in de virtuele machine voor herstel.

   1. Open uw VM als de hoofd gebruiker met behulp van de volgende opdracht:

      `#sudo su -`

   1. Zoek de schijf met behulp van `dmesg` (de methode die u gebruikt om de nieuwe schijf te detecteren) kan verschillen. In het volgende voor beeld wordt **dmesg** gebruikt om te filteren op **SCSI** -schijven:

      `dmesg | grep SCSI`

      De uitvoer is vergelijkbaar met het volgende voor beeld. In dit voor beeld willen we de **Dit SDC** -schijf:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Gebruik de volgende opdrachten om toegang te krijgen tot de chroot-omgeving:

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc2 /rescue
      #mount -o nouuid /dev/sdc1 /rescue/boot/
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Problemen met de chroot-omgeving oplossen.

   1. Gebruik de volgende opdrachten om de chroot-omgeving af te sluiten:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue
      ```

      > [!NOTE]
      > Als het fout bericht `unable to unmount /rescue` , voegt u de optie-l toe aan de umount-opdracht.
      >
      > Voorbeeld: `umount -l /rescue`

1. Ontkoppel de schijf van de virtuele machine voor herstel en voer een schijf wisseling uit met de oorspronkelijke VM.
1. Start de oorspronkelijke VM en controleer de connectiviteit.

## <a name="rhelcentos-7x-with-lvm"></a>RHEL/CentOS 7. x met LVM

   > [!NOTE]
   > Als uw oorspronkelijke virtuele machine Logical Volume Manager (LVM) op de besturingssysteem schijf bevat, maakt u de herstel-VM met behulp van de installatie kopie met onbewerkte partities op de besturingssysteem schijf.

1. De betreffende virtuele machine stoppen of de toewijzing ervan ongedaan maken.
1. Maak een herstel-VM-installatie kopie van dezelfde versie van het besturings systeem, in dezelfde resource groep (herstel) en locatie met beheerde schijf.
1. Gebruik de Azure Portal om een moment opname te maken van de besturingssysteem schijf van de virtuele machine.
1. Maak een schijf van de moment opname van de besturingssysteem schijf en koppel deze aan de virtuele machine voor herstel.
1. Wanneer de schijf is gemaakt, lost u de chroot-omgeving op in de virtuele machine voor herstel.

   1. Open uw VM als de hoofd gebruiker met behulp van de volgende opdracht:

      `#sudo su -`

   1. Zoek de schijf met behulp van `dmesg` (de methode die u gebruikt om de nieuwe schijf te detecteren) kan verschillen. In het volgende voor beeld wordt **dmesg** gebruikt om te filteren op **SCSI** -schijven:

      `dmesg | grep SCSI`

      De uitvoer is vergelijkbaar met het volgende voor beeld. In dit voor beeld willen we de **Dit SDC** -schijf:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Gebruik de volgende opdracht om de logische volume groep te activeren:

      ```
      #vgscan --mknodes
      #vgchange -ay
      #lvscan
      ```

   1. Gebruik de `lsblk` opdracht om de namen van de LVM op te halen:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      ├─sdc3              8:3    0    2M  0 part
      ├─sdc4              8:4    0   63G  0 part
        ├─rootvg-tmplv  253:0    0    2G  0 lvm  
        ├─rootvg-usrlv  253:1    0   10G  0 lvm  
        ├─rootvg-optlv  253:2    0    2G  0 lvm  
        ├─rootvg-homelv 253:3    0    1G  0 lvm  
        ├─rootvg-varlv  253:4    0    8G  0 lvm  
        └─rootvg-rootlv 253:5    0    2G  0 lvm
      ```

   1. Gebruik de volgende opdrachten om toegang te krijgen tot de chroot-omgeving:

      ```
      #mkdir /rescue
      #mount /dev/mapper/rootvg-rootlv /rescue
      #mount /dev/mapper/rootvg-varlv /rescue/var
      #mount /dev/mapper/rootvg-homelv /rescue/home
      #mount /dev/mapper/rootvg-usrlv /rescue/usr
      #mount /dev/mapper/rootvg-tmplv /rescue/tmp
      #mount /dev/mapper/rootvg-optlv /rescue/opt
      #mount /dev/sdc2 /rescue/boot/
      #mount /dev/sdc1 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Problemen met de chroot-omgeving oplossen.

   1. Gebruik de volgende opdrachten om de chroot-omgeving af te sluiten:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue/home
      #umount /rescue/var
      #umount /rescue/usr
      #umount /rescue/tmp
      #umount /rescue/opt
      #umount /rescue
      ```

      > [!NOTE]
      > Als het fout bericht `unable to unmount /rescue` , voegt u de optie-l toe aan de umount-opdracht.
      >
      > Voorbeeld: `umount -l /rescue`

1. Ontkoppel de schijf van de virtuele machine voor herstel en voer een schijf wisseling uit met de oorspronkelijke VM.
1. Start de oorspronkelijke VM en controleer de connectiviteit.

## <a name="rhel-8x-with-lvm"></a>RHEL 8. x met LVM

   > [!NOTE]
   > Als uw oorspronkelijke virtuele machine Logical Volume Manager (LVM) op de besturingssysteem schijf bevat, maakt u de herstel-VM met behulp van de installatie kopie met onbewerkte partities op de besturingssysteem schijf.

1. De betreffende virtuele machine stoppen of de toewijzing ervan ongedaan maken.
1. Maak een herstel-VM-installatie kopie van dezelfde versie van het besturings systeem, in dezelfde resource groep (herstel) en locatie met beheerde schijf.
1. Gebruik de Azure Portal om een moment opname te maken van de besturingssysteem schijf van de virtuele machine.
1. Maak een schijf van de moment opname van de besturingssysteem schijf en koppel deze aan de virtuele machine voor herstel.
1. Wanneer de schijf is gemaakt, lost u de chroot-omgeving op in de virtuele machine voor herstel.

   1. Open uw VM als de hoofd gebruiker met behulp van de volgende opdracht:

      `#sudo su -`

   1. Zoek de schijf met behulp van `dmesg` (de methode die u gebruikt om de nieuwe schijf te detecteren) kan verschillen. In het volgende voor beeld wordt **dmesg** gebruikt om te filteren op **SCSI** -schijven:

      `dmesg | grep SCSI`

      De uitvoer is vergelijkbaar met het volgende voor beeld. In dit voor beeld willen we de **Dit SDC** -schijf:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Gebruik de volgende opdracht om de logische volume groep te activeren:

      ```
      #vgscan --mknodes
      #vgchange -ay
      #lvscan
      ```

   1. Gebruik de `lsblk` opdracht om de namen van de LVM op te halen:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      │ ├─rootvg-tmplv  253:0    0    2G  0 lvm  
      │ ├─rootvg-usrlv  253:1    0   10G  0 lvm  
      │ ├─rootvg-homelv 253:2    0    1G  0 lvm  
      │ ├─rootvg-varlv  253:3    0    8G  0 lvm  
      │ └─rootvg-rootlv 253:4    0    2G  0 lvm  
      ├─sdc14             8:14   0    4M  0 part
      └─sdc15             8:15   0  495M  0 part
      ```

   1. Gebruik de volgende opdrachten om toegang te krijgen tot de chroot-omgeving:

      ```
      #mkdir /rescue
      #mount /dev/mapper/rootvg-rootlv /rescue
      #mount /dev/mapper/rootvg-varlv /rescue/var
      #mount /dev/mapper/rootvg-homelv /rescue/home
      #mount /dev/mapper/rootvg-usrlv /rescue/usr
      #mount /dev/mapper/rootvg-tmplv /rescue/tmp
      #mount /dev/sdc1 /rescue/boot/
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Problemen met de chroot-omgeving oplossen.

   1. Gebruik de volgende opdrachten om de chroot-omgeving af te sluiten:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue/home
      #umount /rescue/var
      #umount /rescue/usr
      #umount /rescue/tmp
      #umount /rescue
      ```

      > [!NOTE]
      > Als het fout bericht `unable to unmount /rescue` , voegt u de optie-l toe aan de umount-opdracht.
      >
      > Voorbeeld: `umount -l /rescue`

1. Ontkoppel de schijf van de virtuele machine voor herstel en voer een schijf wisseling uit met de oorspronkelijke VM.
1. Start de oorspronkelijke VM en controleer de connectiviteit.

## <a name="oracle-7x"></a>Oracle 7. x

1. De betreffende virtuele machine stoppen of de toewijzing ervan ongedaan maken.
1. Maak een herstel-VM-installatie kopie van dezelfde versie van het besturings systeem, in dezelfde resource groep (herstel) en locatie met beheerde schijf.
1. Gebruik de Azure Portal om een moment opname te maken van de besturingssysteem schijf van de virtuele machine.
1. Maak een schijf van de moment opname van de besturingssysteem schijf en koppel deze aan de virtuele machine voor herstel.
1. Wanneer de schijf is gemaakt, lost u de chroot-omgeving op in de virtuele machine voor herstel.

   1. Open uw VM als de hoofd gebruiker met behulp van de volgende opdracht:

      `#sudo su -`

   1. Zoek de schijf met behulp van `dmesg` (de methode die u gebruikt om de nieuwe schijf te detecteren) kan verschillen. In het volgende voor beeld wordt **dmesg** gebruikt om te filteren op **SCSI** -schijven:

      `dmesg | grep SCSI`

      De uitvoer is vergelijkbaar met het volgende voor beeld. In dit voor beeld willen we de **Dit SDC** -schijf:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Gebruik de volgende opdrachten om toegang te krijgen tot de chroot-omgeving:

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc2 /rescue
      #mount -o nouuid /dev/sdc1 /rescue/boot/
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      ##chroot /rescue
      ```

   1. Problemen met de chroot-omgeving oplossen.

   1. Gebruik de volgende opdrachten om de chroot-omgeving af te sluiten:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue
      ```

      > [!NOTE]
      > Als het fout bericht `unable to unmount /rescue` , voegt u de optie-l toe aan de umount-opdracht.
      >
      > Voorbeeld: `umount -l /rescue`

1. Ontkoppel de schijf van de virtuele machine voor herstel en voer een schijf wisseling uit met de oorspronkelijke VM.
1. Start de oorspronkelijke VM en controleer de connectiviteit.

## <a name="suse-sles-12-sp4-suse-sles-12-sp4-for-sap---suse-sles-15-sp1-suse-sles-15-sp1-for-sap"></a>SUSE-SLES 12 SP4, SUSE-SLES 12 SP4 voor SAP &&  # # SUSE-SLES 15 SP1, SUSE-SLES 15 SP1 voor SAP

1. De betreffende virtuele machine stoppen of de toewijzing ervan ongedaan maken.
1. Maak een herstel-VM-installatie kopie van dezelfde versie van het besturings systeem, in dezelfde resource groep (herstel) en locatie met beheerde schijf.
1. Gebruik de Azure Portal om een moment opname te maken van de besturingssysteem schijf van de virtuele machine.
1. Maak een schijf van de moment opname van de besturingssysteem schijf en koppel deze aan de virtuele machine voor herstel.
1. Wanneer de schijf is gemaakt, lost u de chroot-omgeving op in de virtuele machine voor herstel.

   1. Open uw VM als de hoofd gebruiker met behulp van de volgende opdracht:

      `#sudo su -`

   1. Zoek de schijf met behulp van `dmesg` (de methode die u gebruikt om de nieuwe schijf te detecteren) kan verschillen. In het volgende voor beeld wordt **dmesg** gebruikt om te filteren op **SCSI** -schijven:

      `dmesg | grep SCSI`

      De uitvoer is vergelijkbaar met het volgende voor beeld. In dit voor beeld willen we de **Dit SDC** -schijf:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Gebruik de volgende opdrachten om toegang te krijgen tot de chroot-omgeving:

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc4 /rescue
      #mount -o nouuid /dev/sdc3 /rescue/boot/
      #mount /dev/sdc2 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Problemen met de chroot-omgeving oplossen.

   1. Gebruik de volgende opdrachten om de chroot-omgeving af te sluiten:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue
      ```

      > [!NOTE]
      > Als het fout bericht `unable to unmount /rescue` , voegt u de optie-l toe aan de umount-opdracht.
      >
      > Voorbeeld: `umount -l /rescue`

1. Ontkoppel de schijf van de virtuele machine voor herstel en voer een schijf wisseling uit met de oorspronkelijke VM.
1. Start de oorspronkelijke VM en controleer de connectiviteit.

## <a name="next-steps"></a>Volgende stappen

- [Problemen met SSH-verbinding oplossen](troubleshoot-ssh-connection.md)