---
title: Een gegevensschijf koppelen aan een Linux-vm
description: Gebruik de portal om nieuwe of bestaande gegevensschijf aan een Linux-vm te koppelen.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 746cef8dfe026c731a677cbf77f729d36342f007
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969355"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>De portal gebruiken om een gegevensschijf aan een Linux-vm te koppelen 
In dit artikel ziet u hoe u zowel nieuwe als bestaande schijven aan een virtuele Linux-machine koppelen via de Azure-portal. U ook [een gegevensschijf koppelen aan een Windows-vm in de Azure-portal.](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 

Voordat u schijven aan uw vm koppelt, controleert u de volgende tips:

* De grootte van de virtuele machine bepaalt hoeveel gegevensschijven u koppelen. Zie Grootte [voor virtuele machines voor](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)meer informatie.
* Schijven die zijn gekoppeld aan virtuele machines zijn eigenlijk .vhd-bestanden die zijn opgeslagen in Azure. Zie voor meer informatie onze [inleiding tot beheerde schijven.](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Nadat u de schijf hebt aangesloten, moet u [verbinding maken met de Linux-VM om de nieuwe schijf te monteren.](#connect-to-the-linux-vm-to-mount-the-new-disk)


## <a name="find-the-virtual-machine"></a>Zoek de virtuele machine
1. Ga naar de [Azure-portal](https://portal.azure.com/) om de VM te vinden. Zoeken naar virtuele machines en selecteer **deze**.
2. Kies de VM in de lijst.
3. Kies **schijven**op de zijbalk van de pagina **Virtuele machines** onder **Instellingen**.
   
    ![Instellingen voor geopende schijven](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Een nieuwe schijf koppelen

1. Klik **in** het deelvenster Schijven op **+ Gegevensschijf toevoegen**.
2. Klik op de vervolgkeuzelijst voor **Naam** en selecteer **Schijf maken:**

    ![Azure-beheerde schijf maken](./media/attach-disk-portal/create-new-md.png)

3. Voer een naam in voor uw beheerde schijf. Controleer de standaardinstellingen, werk indien nodig bij en klik op **Maken**.
   
   ![Schijfinstellingen controleren](./media/attach-disk-portal/create-new-md-settings.png)

4. Klik **op Opslaan** om de beheerde schijf te maken en de VM-configuratie bij te werken:

   ![Nieuwe Azure Managed Disk opslaan](./media/attach-disk-portal/confirm-create-new-md.png)

5. Nadat Azure de schijf heeft gemaakt en aan de virtuele machine heeft bevestigd, wordt de nieuwe schijf weergegeven in de schijfinstellingen van de virtuele machine onder **Gegevensschijven**. Aangezien beheerde schijven een bron op het hoogste niveau zijn, wordt de schijf weergegeven aan de hoofdmap van de brongroep:

   ![Azure Managed Disk in resourcegroep](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Een bestaande schijf koppelen
1. Klik **in** het deelvenster Schijven op **+ Gegevensschijf toevoegen**.
2. Klik op de vervolgkeuzelijst voor **Naam** om een lijst weer te geven van bestaande beheerde schijven die toegankelijk zijn voor uw Azure-abonnement. Selecteer de beheerde schijf die u wilt koppelen:

   ![Bestaande Azure Managed Disk koppelen](./media/attach-disk-portal/select-existing-md.png)

3. Klik **op Opslaan** om de bestaande beheerde schijf te koppelen en de VM-configuratie bij te werken:
   
   ![Azure Managed Disk-updates opslaan](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Nadat Azure de schijf aan de virtuele machine heeft bevestigd, wordt deze weergegeven in de schijfinstellingen van de virtuele machine onder **Gegevensschijven**.

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

Hier, *sdc* is de schijf die we willen. 

### <a name="partition-a-new-disk"></a>Een nieuwe schijf partitioneren
Als u een bestaande schijf gebruikt die gegevens bevat, gaat u naar de montage van de schijf. Als u een nieuwe schijf koppelt, moet u de schijf partitioneren.

> [!NOTE]
> Het wordt aanbevolen dat u de nieuwste versies van fdisk of parted die beschikbaar zijn voor uw distro te gebruiken.

Partitioneer de schijf met `fdisk`. Als de schijfgrootte 2 tebibytes (TiB) of groter is, moet `parted` u GPT-partitionering gebruiken, u gpt-partitionering gebruiken. Als de schijfgrootte onder 2TiB valt, u MBR- of GPT-partitionering gebruiken. Maak er een primaire schijf van op partitie 1 en accepteer de andere standaardinstellingen. In het volgende `fdisk` voorbeeld wordt het proces gestart op */dev/sdc:*

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

#### <a name="alternate-method-using-parted"></a>Alternatieve methode met gescheiden
De fdisk utility heeft interactieve input nodig en is daarom niet ideaal voor gebruik binnen automatiseringsscripts. Echter, de [gescheiden](https://www.gnu.org/software/parted/) nut kan worden gescript en dus leent zich beter in automatisering scenario's. Het gescheiden hulpprogramma kan worden gebruikt om een gegevensschijf te partitioneren en op te maken. Voor de onderstaande walkthrough gebruiken we een nieuwe dataschijf /dev/sdc en maken we deze op met behulp van het [XFS-bestandssysteem.](https://xfs.wiki.kernel.org/)
```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
partprobe /dev/sdc1
```
Zoals hierboven te zien, gebruiken we het [partprobe-hulpprogramma](https://linux.die.net/man/8/partprobe) om ervoor te zorgen dat de kernel onmiddellijk op de hoogte is van de nieuwe partitie en bestandssysteem. Het niet gebruiken van partprobe kan ertoe leiden dat de blkid- of lslbk-opdrachten de UUID niet onmiddellijk voor het nieuwe bestandssysteem retourneren.

### <a name="mount-the-disk"></a>De schijf monteren
Maak een map om het `mkdir`bestandssysteem te monteren met behulp van . In het volgende voorbeeld wordt een map gemaakt op */datadrive:*

```bash
sudo mkdir /datadrive
```

Gebruik `mount` om vervolgens het bestandssysteem te monteren. In het volgende voorbeeld wordt de */dev/sdc1-partitie* op het mountpunt */datadrive* gemonteerd:

```bash
sudo mount /dev/sdc1 /datadrive
```

Om ervoor te zorgen dat het station automatisch wordt gemonteerd na een herstart, moet het worden toegevoegd aan het */etc/fstab-bestand.* Het is ook sterk aanbevolen dat de UUID (Universally Unique IDentifier) wordt gebruikt in */ etc / fstab* te verwijzen naar de drive in plaats van alleen de naam van het apparaat (zoals, */ dev / sdc1*). Als het besturingssysteem een schijffout ontdekt tijdens het opstarten, kunt u door de UUID te gebruiken voorkomen dat de verkeerde schijf wordt gekoppeld aan een bepaalde locatie. Resterende gegevensschijven worden dan toegewezen aan dezelfde apparaat-id's. Als u de UUID van het nieuwe station wilt zoeken, gebruikt u het hulpprogramma `blkid`:

```bash
sudo -i blkid
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
Wanneer u klaar bent, slaat u het */etc/fstab-bestand* op en start het systeem opnieuw op.
> [!NOTE]
> Als u later een gegevensschijf verwijdert zonder fstab te bewerken, kan de VM niet worden opgestart. De meeste distributies bieden de *nofail* en / of *nobootwait* fstab opties. Met deze opties kan een systeem worden opgestart, zelfs als de schijf niet kan worden gemonteerd bij het opstarten. Raadpleeg de documentatie van uw distributie voor meer informatie over deze parameters.
> 
> De *optie nofail* zorgt ervoor dat de VM wordt gestart, zelfs als het bestandssysteem beschadigd is of als de schijf niet bestaat op het opstarten. Zonder deze optie u gedrag tegenkomen zoals beschreven in [Kan SSH naar Linux VM als gevolg van FSTAB fouten](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

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

## <a name="next-steps"></a>Volgende stappen
U ook [een gegevensschijf koppelen](add-disk.md) met de Azure CLI.
