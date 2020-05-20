---
title: Het formaat wijzigen van een besturingssysteem schijf met een GPT-partitie | Microsoft Docs
description: Dit artikel bevat instructies voor het wijzigen van de grootte van een besturingssysteem schijf met een GPT-partitie.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 7c408e8e29b3f9ac423a6104c40242f11f93a171
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651090"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Het formaat van een besturingssysteem schijf met een GPT-partitie wijzigen

> [!NOTE]
> Dit scenario geldt alleen voor besturingssysteem schijven met een GPT-partitie (GUID-partitie tabel).

In dit artikel wordt beschreven hoe u de grootte van een besturingssysteem schijf met een GPT-partitie in Linux kunt verg Roten. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Vaststellen of de besturingssysteem schijf een MBR-of GPT-partitie heeft

Gebruik de **geparteerde** opdracht om te bepalen of de schijf partitie is gemaakt met een master boot record partitie (MBR) of een GPT-partitie.

### <a name="mbr-partition"></a>MBR-partitie

In de volgende uitvoer toont de **partitie tabel** de waarde van **MSDOS**. Met deze waarde wordt een MBR-partitie aangeduid.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>GPT-partitie

In de volgende uitvoer toont de **partitie tabel** de waarde **GPT**. Met deze waarde wordt een GPT-partitie aangeduid.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Als uw virtuele machine (VM) een GPT-partitie op de besturingssysteem schijf heeft, verg root u de besturingssysteem schijf.

## <a name="increase-the-size-of-the-os-disk"></a>De grootte van de besturingssysteem schijf verg Roten

De volgende instructies zijn van toepassing op door Linux goedgekeurde distributies.

> [!NOTE]
> Voordat u doorgaat, maakt u een back-up van uw VM of neemt u een moment opname van de besturingssysteem schijf op.

### <a name="ubuntu"></a>Ubuntu

Verhoog de grootte van de besturingssysteem schijf in Ubuntu 16. x en 18. x:

1. Stop de virtuele machine.
1. Verhoog de grootte van de besturingssysteem schijf van de portal.
1. Start de virtuele machine opnieuw op en meld u aan bij de VM als een **hoofd** gebruiker.
1. Controleer of op de besturingssysteem schijf nu een groter formaat van het bestands systeem wordt weer gegeven.

Zoals in het volgende voor beeld wordt weer gegeven, is het formaat van de besturingssysteem schijf gewijzigd van de portal naar 100 GB. Op het **/dev/sda1** -bestands systeem dat is gekoppeld, **/** wordt 97 GB weer gegeven.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

De grootte van de besturingssysteem schijf in SUSE 12 SP4, SUSE SLES 12 voor SAP, SUSE SLES 15 en SUSE SLES 15 voor SAP verg Roten:

1. Stop de virtuele machine.
1. Verhoog de grootte van de besturingssysteem schijf van de portal.
1. Start de VM opnieuw.

Wanneer de VM opnieuw is opgestart, voert u de volgende stappen uit:

   1. Gebruik de volgende opdracht om toegang te krijgen tot uw VM als een **hoofd** gebruiker:
   
      `#sudo su`

   1. Gebruik de volgende opdracht om het **gptfdisk** -pakket te installeren. Dit is vereist voor het verg Roten van de grootte van de besturingssysteem schijf:

      `#zypper install gptfdisk -y`

   1. Als u de grootste beschik bare sector op de schijf wilt weer geven, voert u de volgende opdracht uit:

      `#sgdisk -e /dev/sda`

   1. Wijzig de grootte van de partitie zonder deze te verwijderen met de volgende opdracht. De gegroepeerde opdracht heeft een optie **met de naam** **resizepart** om de partitie te verg Roten of verkleinen zonder deze te verwijderen. Het getal 4 na **resizepart** geeft aan dat het formaat van de vierde partitie wordt gewijzigd.

      `#parted -s /dev/sda "resizepart 4 -1" quit`

   1. Voer de **#lsblk** opdracht uit om te controleren of de partitie is verhoogd.

      In de volgende uitvoer ziet u dat de grootte van de **/dev/sda4** -partitie is gewijzigd in 98,5 GB.

      ```
      user@myvm:~ # lsblk
      NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda      8:0    0  100G  0 disk
      ├─sda1   8:1    0    2M  0 part
      ├─sda2   8:2    0  512M  0 part /boot/efi
      └─sda4   8:4    0 98.5G  0 part /
      sdb      8:16   0   20G  0 disk
      └─sdb1   8:17   0   20G  0 part /mnt/resource
      ```
      
   1. Bepaal het type bestands systeem op de besturingssysteem schijf met behulp van de volgende opdracht:

      `blkid`

      Voorbeelduitvoer:

      ```
      #blkid

      user@myvm:~ # blkid
      /dev/sda1: PARTLABEL="p.legacy" PARTUUID="0122fd4c-0069-4a45-bfd4-98b97ccb6e8c"
      /dev/sda2: SEC_TYPE="msdos" LABEL_FATBOOT="EFI" LABEL="EFI" UUID="00A9-D170" TYPE="vfat" PARTLABEL="p.UEFI" PARTUUID="abac3cd8-949b-4e83-81b1-9636493388c7"
      /dev/sda3: LABEL="BOOT" UUID="aa2492db-f9ed-4f5a-822a-1233c06d57cc" TYPE="xfs" PARTLABEL="p.lxboot" PARTUUID="dfb36c61-b15f-4505-8e06-552cf1589cf7"
      /dev/sda4: LABEL="ROOT" UUID="26104965-251c-4e8d-b069-5f5323d2a9ba" TYPE="xfs" PARTLABEL="p.lxroot" PARTUUID="50fecee0-f22b-4406-94c3-622507e2dbce"
      /dev/sdb1: UUID="95239fce-ca97-4f03-a077-4e291588afc9" TYPE="ext4" PARTUUID="953afef3-01"
      ```

   1. Gebruik op basis van het bestandssysteem type de juiste opdrachten om de grootte van het bestands systeem te wijzigen.

      Voor **xfs**, gebruikt u de volgende opdracht:

      ` #xfs_growfs /`

      Voorbeelduitvoer:

      ```
      user@myvm:~ # xfs_growfs /
      meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
               =                       sectsz=512   attr=2, projid32bit=1
               =                       crc=1        finobt=1 spinodes=0 rmapbt=0
               =                       reflink=0
      data     =                       bsize=4096   blocks=7470331, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=3647, version=2
               =                       sectsz=512   sunit=0 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 7470331 to 25820172
      ```

      Voor **ext4**, gebruikt u de volgende opdracht:

      ```#resize2fs /dev/sda4```

   1. Controleer de verhoogde grootte van het bestands systeem voor **VG-th**, met behulp van de volgende opdracht:

      `#df -Th`

      Voorbeelduitvoer:

      ```
      user@myvm:~ # df -Th
      Filesystem     Type      Size  Used Avail Use% Mounted on
      devtmpfs       devtmpfs  306M  4.0K  306M   1% /dev
      tmpfs          tmpfs     320M     0  320M   0% /dev/shm
      tmpfs          tmpfs     320M  8.8M  311M   3% /run
      tmpfs          tmpfs     320M     0  320M   0% /sys/fs/cgroup
      /dev/sda4      xfs        99G  1.8G   97G   2% /
      /dev/sda3      xfs      1014M   88M  927M   9% /boot
      /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
      /dev/sdb1      ext4       20G   45M   19G   1% /mnt/resource
      tmpfs          tmpfs      64M     0   64M   0% /run/user/1000
      user@myvm:~ #
      ```

In het vorige voor beeld ziet u dat de bestandssysteem grootte van de besturingssysteem schijf is verhoogd.

### <a name="rhel"></a>RHEL

Verhoog de grootte van de besturingssysteem schijf in RHEL 7. x met LVM:

1. Stop de virtuele machine.
1. Verhoog de grootte van de besturingssysteem schijf van de portal.
1. Start de virtuele machine.

Wanneer de VM opnieuw is opgestart, voert u de volgende stappen uit:

   1. Gebruik de volgende opdracht om toegang te krijgen tot uw VM als een **hoofd** gebruiker:
   
      `#sudo su`

   1. Installeer het **gptfdisk** -pakket, dat vereist is voor het verg Roten van de besturingssysteem schijf.

      `#yum install gdisk -y`

   1. Als u de grootste beschik bare sector op de schijf wilt zien, voert u de volgende opdracht uit:

      `#sgdisk -e /dev/sda`

   1. Wijzig de grootte van de partitie zonder deze te verwijderen met de volgende opdracht. De gegroepeerde opdracht heeft een optie **met de naam** **resizepart** om de partitie te verg Roten of verkleinen zonder deze te verwijderen. Het getal 4 na **resizepart** geeft aan dat het formaat van de vierde partitie wordt gewijzigd.

      `#parted -s /dev/sda "resizepart 4 -1" quit`
    
   1. Voer de volgende opdracht uit om te controleren of de partitie is verhoogd:

      `#lsblk`

      In de volgende uitvoer ziet u dat de grootte van de **/dev/sda4** -partitie is gewijzigd in 99 GB.

      ```
      [user@myvm ~]# lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      fd0                 2:0    1    4K  0 disk
      sda                 8:0    0  100G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot/efi
      ├─sda2              8:2    0  500M  0 part /boot
      ├─sda3              8:3    0    2M  0 part
      └─sda4              8:4    0   99G  0 part
      ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
      ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
      ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
      ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
      ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
      └─rootvg-rootlv   253:5    0    2G  0 lvm  /
      sdb                 8:16   0   50G  0 disk
      └─sdb1              8:17   0   50G  0 part /mnt/resource
      ```

   1. Gebruik de volgende opdracht om het formaat van het fysieke volume (HW) te wijzigen:

      `#pvresize /dev/sda4`

      In de volgende uitvoer ziet u dat de PV is gewijzigd in 99,02 GB.

      ```
      [user@myvm ~]# pvresize /dev/sda4
      Physical volume "/dev/sda4" changed
      1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      [user@myvm ~]# pvs
      PV         VG     Fmt  Attr PSize   PFree
      /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
      ```

   1. In het volgende voor beeld wordt **/dev/mapper/rootvg-rootlv** gewijzigd van 2 GB in 12 GB (een toename van 10 GB) met de volgende opdracht. Met deze opdracht wordt ook de grootte van het bestands systeem gewijzigd.

      `#lvresize -r -L +10G /dev/mapper/rootvg-rootlv`

      Voorbeelduitvoer:

      ```
      [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
      Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
      Logical volume rootvg/rootlv successfully resized.
      meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
               =                       sectsz=4096  attr=2, projid32bit=1
               =                       crc=1        finobt=0 spinodes=0
      data     =                       bsize=4096   blocks=524288, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=2560, version=2
               =                       sectsz=4096  sunit=1 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 524288 to 3145728
      ```
         
   1. Controleer of **/dev/mapper/rootvg-rootlv** een verhoogde bestandssysteem grootte heeft met behulp van de volgende opdracht:

      `#df -Th /`

      Voorbeelduitvoer:

      ```
      [user@myvm ~]# df -Th /
      Filesystem                Type  Size  Used Avail Use% Mounted on
      /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
      [user@myvm ~]#
      ```

   > [!NOTE]
   > Als u dezelfde procedure wilt gebruiken om de grootte van een ander logisch volume te wijzigen, wijzigt u de **LV** -naam in stap 7.

## <a name="next-steps"></a>Volgende stappen

- [Grootte van schijf wijzigen](expand-disks.md)