---
title: Een gegevensschijf toevoegen aan Linux VM met de Azure CLI
description: Leer een permanente gegevensschijf toevoegen aan uw Linux-vm met de Azure CLI
author: roygara
manager: twooley
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/13/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: a80a1fe21ba0b40aebf9e426e3d49f499c2d2a21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250411"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Een schijf toevoegen aan een virtuele Linux-machine
In dit artikel ziet u hoe u een permanente schijf aan uw vm koppelen, zodat u uw gegevens behouden, zelfs als uw virtuele machine opnieuw is ingericht vanwege onderhoud of het formaat.


## <a name="attach-a-new-disk-to-a-vm"></a>Een nieuwe schijf aan een virtuele machine koppelen

Als u een nieuwe, lege gegevensschijf op uw VM wilt toevoegen, `--new` gebruikt u de opdracht [az vm-schijfbijvoegen](/cli/azure/vm/disk?view=azure-cli-latest) met de parameter. Als uw vm zich in een beschikbaarheidszone bevindt, wordt de schijf automatisch gemaakt in dezelfde zone als de VM. Zie [Overzicht van beschikbaarheidszones voor](../../availability-zones/az-overview.md)meer informatie. In het volgende voorbeeld wordt een schijf met de naam *myDataDisk* gemaakt met een grootte van 50 Gb:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Een bestaande schijf koppelen

Als u een bestaande schijf wilt koppelen, zoekt u de schijf-id en geeft u de ID door aan de opdracht voor het koppelen van de [AZ-schijf.](/cli/azure/vm/disk?view=azure-cli-latest) In het volgende voorbeeld query's voor een schijf met de naam *myDataDisk* in *myResourceGroup,* wordt deze vervolgens gekoppeld aan de VM met de naam *myVM:*

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Maak verbinding met de Linux-VM om de nieuwe schijf te monteren

Om uw nieuwe schijf te partitioneren, op te maken en te monteren zodat uw Linux-vm deze kan gebruiken, ssh in uw VM. Zie voor meer informatie [SSH gebruiken met Linux op Azure](mac-create-ssh-keys.md). In het volgende voorbeeld wordt verbinding gemaakt met een VM met de openbare DNS-vermelding van *mypublicdns.westus.cloudapp.azure.com* met de *azureuser*met gebruikersnaam:

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Zodra u verbinding hebt gemaakt met uw vm, u een schijf aansluiten. Zoek eerst de `dmesg` schijf met behulp van (de methode die u gebruikt om uw nieuwe schijf te ontdekken kan variëren). In het volgende voorbeeld wordt dmesg gebruikt om te filteren op *SCSI-schijven:*

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
> Het wordt aanbevolen dat u de nieuwste versies van fdisk of parted die beschikbaar zijn voor uw distro te gebruiken.

Hier, *sdc* is de schijf die we willen. Partitie van `parted`de schijf met, als de schijfgrootte is 2 tebibytes (TiB) of groter dan moet u GPT partitioneren gebruiken, als het onder 2TiB, dan u gebruik maken van MBR of GPT partitionering. Als u MBR-partitionering gebruikt, `fdisk`u. Maak er een primaire schijf van op partitie 1 en accepteer de andere standaardinstellingen. In het volgende `fdisk` voorbeeld wordt het proces gestart op */dev/sdc:*

```bash
sudo fdisk /dev/sdc
```

Gebruik de opdracht `n` om een nieuwe partitie toe te voegen. In dit voorbeeld kiezen `p` we ook voor een primaire partitie en accepteren we de rest van de standaardwaarden. De uitvoer ziet er ongeveer als volgt uit:

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

Druk de partitietabel `p` af `w` door te typen en vervolgens de tabel naar schijf en afsluiten te schrijven. De uitvoer moet op het volgende voorbeeld lijken:

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

Schrijf nu een bestandssysteem naar `mkfs` de partitie met de opdracht. Geef het bestandssysteemtype en de apparaatnaam op. In het volgende voorbeeld wordt een *ext4-bestandssysteem* gemaakt op de */dev/sdc1-partitie* die in de voorgaande stappen is gemaakt:

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

Maak nu een map om het `mkdir`bestandssysteem te monteren met behulp van . In het volgende voorbeeld wordt een map gemaakt op */datadrive:*

```bash
sudo mkdir /datadrive
```

Gebruik `mount` om vervolgens het bestandssysteem te monteren. In het volgende voorbeeld wordt de */dev/sdc1-partitie* op het mountpunt */datadrive* gemonteerd:

```bash
sudo mount /dev/sdc1 /datadrive
```

Om ervoor te zorgen dat het station automatisch wordt gemonteerd na een herstart, moet het worden toegevoegd aan het */etc/fstab-bestand.* Het is ook sterk aanbevolen dat de UUID (Universally Unique IDentifier) wordt gebruikt in */ etc / fstab* te verwijzen naar de drive in plaats van alleen de naam van het apparaat (zoals, */ dev / sdc1*). Als het besturingssysteem een schijffout ontdekt tijdens het opstarten, kunt u door de UUID te gebruiken voorkomen dat de verkeerde schijf wordt gekoppeld aan een bepaalde locatie. Resterende gegevensschijven worden dan toegewezen aan dezelfde apparaat-id's. Als u de UUID van het nieuwe station wilt zoeken, gebruikt u het hulpprogramma `blkid`:

```bash
sudo blkid
```

De uitvoer lijkt op het volgende voorbeeld:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Het onjuist bewerken van het **/etc/fstab-bestand** kan resulteren in een niet-opstartbaar systeem. Als u niet zeker weet wat u moet doen, raadpleegt u de documentatie van de distributie over het bewerken van dit bestand. Het wordt ook aanbevolen dat een back-up van het /etc/fstab-bestand wordt gemaakt voordat het wordt bewerkt.

Open vervolgens het */etc/fstab-bestand* in een teksteditor als volgt:

```bash
sudo vi /etc/fstab
```

Gebruik in dit voorbeeld de UUID-waarde voor het */dev/sdc1-apparaat* dat in de vorige stappen is gemaakt en het bevestigingspunt van */datadrive*. Voeg de volgende regel toe aan het einde van het */etc/fstab-bestand:*

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Als u later een gegevensschijf verwijdert zonder fstab te bewerken, kan de VM niet worden opgestart. De meeste distributies bieden de *nofail* en / of *nobootwait* fstab opties. Met deze opties kan een systeem worden opgestart, zelfs als de schijf niet kan worden gemonteerd bij het opstarten. Raadpleeg de documentatie van uw distributie voor meer informatie over deze parameters.
>
> De *optie nofail* zorgt ervoor dat de VM wordt gestart, zelfs als het bestandssysteem beschadigd is of als de schijf niet bestaat op het opstarten. Zonder deze optie u gedrag tegenkomen zoals beschreven in [Kan SSH naar Linux VM als gevolg van FSTAB fouten](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)
>
> De Azure VM Serial Console kan worden gebruikt voor consoletoegang tot uw VM als het wijzigen van fstab heeft geleid tot een opstartfout. Meer details zijn beschikbaar in de [documentatie van de seriële console.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

### <a name="trimunmap-support-for-linux-in-azure"></a>TRIM/UNMAP-ondersteuning voor Linux in Azure
Sommige Linux-kernels ondersteunen TRIM/UNMAP-bewerkingen om ongebruikte blokken op de schijf te verwijderen. Deze functie is voornamelijk handig in standaardopslag om Azure te informeren dat verwijderde pagina's niet langer geldig zijn en kunnen worden verwijderd, en kan geld besparen als u grote bestanden maakt en deze vervolgens verwijdert.

Er zijn twee manieren om TRIM-ondersteuning in te schakelen in uw Linux VM. Raadpleeg zoals gebruikelijk uw distributie voor de aanbevolen aanpak:

* Gebruik `discard` de mount optie in */etc/fstab,* bijvoorbeeld:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* In sommige gevallen `discard` kan de optie gevolgen hebben voor de prestaties. U de `fstrim` opdracht ook handmatig uitvoeren vanaf de opdrachtregel of deze toevoegen aan uw crontab om regelmatig uit te voeren:

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

* Als u wilt weten of uw Linux-VM correct is geconfigureerd, bekijkt u de [prestatieaanbevelingen voor uw Linux-machine optimaliseren.](optimization.md)
* Breid uw opslagcapaciteit uit door extra schijven toe te voegen en [RAID te configureren](configure-raid.md) voor extra prestaties.
