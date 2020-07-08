---
title: Een gegevens schijf toevoegen aan een Linux-VM met behulp van Azure CLI
description: Meer informatie over het toevoegen van een permanente gegevens schijf aan uw virtuele Linux-machine met Azure CLI
author: roygara
manager: twooley
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 06/13/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: eb18207c15007820bf93254886ab38a43bc5b48f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84658330"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Een schijf toevoegen aan een virtuele Linux-machine
In dit artikel wordt beschreven hoe u een permanente schijf aan uw virtuele machine koppelt, zodat u uw gegevens kunt bewaren, zelfs als uw virtuele machine opnieuw wordt ingericht door onderhoud of het wijzigen van de grootte.


## <a name="attach-a-new-disk-to-a-vm"></a>Een nieuwe schijf koppelen aan een VM

Als u een nieuwe, lege gegevens schijf wilt toevoegen aan uw VM, gebruikt u de opdracht [AZ VM Disk attach](/cli/azure/vm/disk?view=azure-cli-latest) met de `--new` para meter. Als uw virtuele machine zich in een beschikbaarheids zone bevindt, wordt de schijf automatisch gemaakt in dezelfde zone als de virtuele machine. Zie [overzicht van Beschikbaarheidszones](../../availability-zones/az-overview.md)voor meer informatie. In het volgende voor beeld wordt een schijf gemaakt met de naam *myDataDisk* met een grootte van 50 GB:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Een bestaande schijf koppelen

Als u een bestaande schijf wilt koppelen, zoekt u de schijf-ID en geeft u de ID door aan de opdracht [AZ VM Disk attach](/cli/azure/vm/disk?view=azure-cli-latest) . In het volgende voor beeld wordt een query uitgevoerd voor een schijf met de naam *myDataDisk* in *myResourceGroup*, waarna deze wordt gekoppeld aan de virtuele machine met de naam *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Verbinding maken met de Linux-VM om de nieuwe schijf te koppelen

Om uw nieuwe schijf te partitioneren, te Format teren en te koppelen, zodat uw virtuele Linux-machine deze kan gebruiken, SSH in uw VM. Zie voor meer informatie [SSH gebruiken met Linux op Azure](mac-create-ssh-keys.md). In het volgende voor beeld wordt verbinding gemaakt met een virtuele machine met de open bare DNS-vermelding van *mypublicdns.westus.cloudapp.Azure.com* met de gebruikers naam *azureuser*:

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Wanneer u bent verbonden met uw virtuele machine, kunt u een schijf koppelen. Zoek eerst de schijf met `dmesg` (de methode die u gebruikt om de nieuwe schijf te detecteren). In het volgende voor beeld wordt dmesg gebruikt om te filteren op *SCSI* -schijven:

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

> [!NOTE]
> Het is raadzaam om de meest recente versies van fdisk of een deel daarvan te gebruiken die beschikbaar zijn voor uw distributie.

Hier is *Dit SDC* de schijf die u wilt. De schijf partitioneren met `parted` , als de schijf grootte 2 tebibytes (Tib) of groter is, moet u GPT-partitionering gebruiken, als deze zich onder 2TiB bevindt, dan kunt u gebruikmaken van MBR-of GPT-partitionering. Als u gebruik maakt van MBR-partities, kunt u gebruiken `fdisk` . Maak een primaire schijf op partitie 1 en accepteer de andere standaard waarden. In het volgende voor beeld wordt het `fdisk` proces op */dev/SDC*gestart:

```bash
sudo fdisk /dev/sdc
```

Gebruik de opdracht `n` om een nieuwe partitie toe te voegen. In dit voor beeld kiezen we ook `p` voor een primaire partitie en accepteren we de rest van de standaard waarden. De uitvoer ziet er ongeveer als volgt uit:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Druk de partitie tabel af door te typen `p` en vervolgens `w` te gebruiken om de tabel naar schijf te schrijven en af te sluiten. De uitvoer moet er ongeveer uitzien als in het volgende voor beeld:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```
Gebruik de onderstaande opdracht om de kernel bij te werken:
```
partprobe 
```

Schrijf nu een bestands systeem naar de partitie met de `mkfs` opdracht. Geef het type bestands systeem en de naam van het apparaat op. In het volgende voor beeld wordt een *ext4* -bestands systeem gemaakt op de */dev/sdc1* -partitie die in de voor gaande stappen is gemaakt:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

De uitvoer lijkt op die in het volgende voorbeeld:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Maak nu een directory voor het koppelen van het bestands systeem met behulp van `mkdir` . In het volgende voor beeld maakt u een map op */datadrive*:

```bash
sudo mkdir /datadrive
```

Gebruik `mount` om het bestands systeem vervolgens te koppelen. In het volgende voor beeld wordt de */dev/sdc1* -partitie gekoppeld aan het */datadrive* -koppel punt:

```bash
sudo mount /dev/sdc1 /datadrive
```

Om ervoor te zorgen dat het station automatisch na het opnieuw opstarten opnieuw wordt gekoppeld, moet het worden toegevoegd aan het *bestand/etc/fstab* -bestand. Het wordt ook ten zeerste aanbevolen om de UUID (Universally Unique IDentifier) in *bestand/etc/fstab* te gebruiken om te verwijzen naar het station in plaats van alleen de naam van het apparaat (zoals */dev/sdc1*). Als het besturingssysteem een schijffout ontdekt tijdens het opstarten, kunt u door de UUID te gebruiken voorkomen dat de verkeerde schijf wordt gekoppeld aan een bepaalde locatie. Resterende gegevensschijven worden dan toegewezen aan dezelfde apparaat-id's. Als u de UUID van het nieuwe station wilt zoeken, gebruikt u het hulpprogramma `blkid`:

```bash
sudo blkid
```

De uitvoer ziet er ongeveer uit als in het volgende voor beeld:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Het onjuist bewerken van het **bestand/etc/fstab** -bestand kan resulteren in een systeem dat niet kan worden opgestart. Als u niet zeker weet wat u moet doen, raadpleegt u de documentatie van de distributie over het bewerken van dit bestand. U wordt ook aangeraden een back-up van het bestand/etc/fstab-bestand te maken voordat u het bewerkt.

Open vervolgens het *bestand/etc/fstab* -bestand in een tekst editor als volgt:

```bash
sudo vi /etc/fstab
```

In dit voor beeld gebruikt u de UUID-waarde voor het */dev/sdc1* -apparaat dat is gemaakt in de vorige stappen en de koppel punt van */datadrive*. Voeg de volgende regel toe aan het einde van het *bestand/etc/fstab* -bestand:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Later het verwijderen van een gegevens schijf zonder het bewerken van fstab kan ertoe leiden dat de virtuele machine niet kan worden opgestart. De meeste distributies bieden de opties *nobootwait* *en/* of fstab. Met deze opties kan een systeem worden opgestart zelfs als de schijf niet kan worden gekoppeld tijdens het opstarten. Raadpleeg de documentatie van uw distributie voor meer informatie over deze para meters.
>
> De *niet-werkende* optie zorgt ervoor dat de VM wordt gestart, zelfs als het bestands systeem beschadigd is of wanneer de schijf niet aanwezig is tijdens het opstarten. Zonder deze optie kunt u zich voordoen als beschreven in [kan niet SSH-naar-Linux-VM wegens FSTAB-fouten](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)
>
> De seriële Azure VM-console kan worden gebruikt voor toegang tot de console van uw virtuele machine als het wijzigen van fstab een opstart fout heeft veroorzaakt. Meer informatie vindt u in de [documentatie van de seriële console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).

### <a name="trimunmap-support-for-linux-in-azure"></a>Ondersteuning voor Linux in azure bijsnijden/ontkoppelen
Sommige Linux-kernels ondersteunen bewerkingen voor het verwijderen/ontkoppelen van ongebruikte blokken op de schijf. Deze functie is vooral nuttig in de standaard opslag om Azure te informeren dat verwijderde pagina's niet meer geldig zijn en kunnen worden verwijderd en geld kunnen besparen als u grote bestanden maakt en deze vervolgens verwijdert.

Er zijn twee manieren om ondersteuning voor het verkleinen van de virtuele Linux-machine in te scha kelen. Zoals gebruikelijk, raadpleegt u de distributie voor de aanbevolen benadering:

* Gebruik de `discard` koppelings optie in *bestand/etc/fstab*, bijvoorbeeld:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* In sommige gevallen kan de `discard` optie invloed hebben op de prestaties. U kunt de `fstrim` opdracht ook hand matig uitvoeren vanaf de opdracht regel of deze toevoegen aan uw crontab om regel matig uit te voeren:

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Problemen oplossen

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Volgende stappen

* Bekijk de aanbevelingen voor het [optimaliseren van uw Linux-machine](optimization.md) om ervoor te zorgen dat uw Linux-VM correct is geconfigureerd.
* Breid uw opslag capaciteit uit door extra schijven toe te voegen en [RAID te configureren](configure-raid.md) voor extra prestaties.
