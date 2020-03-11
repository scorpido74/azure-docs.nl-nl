---
title: Een gegevens schijf koppelen aan een virtuele Linux-machine
description: Gebruik de portal om een nieuwe of bestaande gegevens schijf te koppelen aan een virtuele Linux-machine.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 746cef8dfe026c731a677cbf77f729d36342f007
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969355"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Een gegevens schijf koppelen aan een virtuele Linux-machine met behulp van de portal 
Dit artikel laat u zien hoe u met de Azure Portal zowel nieuwe als bestaande schijven kunt koppelen aan een virtuele Linux-machine. U kunt ook [een gegevens schijf koppelen aan een virtuele Windows-machine in de Azure Portal](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Lees de volgende tips voordat u schijven aan uw virtuele machine koppelt:

* De grootte van de virtuele machine bepaalt hoeveel gegevens schijven u kunt bijvoegen. Zie [grootten voor virtuele machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie.
* Schijven die zijn gekoppeld aan virtuele machines zijn eigenlijk VHD-bestanden die zijn opgeslagen in Azure. Zie onze [Inleiding tot Managed disks](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie.
* Nadat u de schijf hebt gekoppeld, moet u [verbinding maken met de Linux-VM om de nieuwe schijf te koppelen](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>De virtuele machine zoeken
1. Ga naar het [Azure Portal](https://portal.azure.com/) om de virtuele machine te vinden. Zoek en selecteer **virtuele machines**.
2. Kies de virtuele machine in de lijst.
3. Kies op de pagina **virtuele machines** de zijbalk onder **instellingen**de optie **schijven**.
   
    ![Schijf instellingen openen](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Een nieuwe schijf koppelen

1. Klik in het deel venster **schijven** op **+ gegevens schijf toevoegen**.
2. Klik op de vervolg keuzelijst voor **naam** en selecteer **schijf maken**:

    ![Een Azure Managed Disk maken](./media/attach-disk-portal/create-new-md.png)

3. Voer een naam in voor uw beheerde schijf. Controleer de standaard instellingen, werk indien nodig bij en klik vervolgens op **maken**.
   
   ![Schijf instellingen controleren](./media/attach-disk-portal/create-new-md-settings.png)

4. Klik op **Opslaan** om de beheerde schijf te maken en de VM-configuratie bij te werken:

   ![Nieuwe Azure Managed Disk opslaan](./media/attach-disk-portal/confirm-create-new-md.png)

5. Nadat Azure de schijf heeft gemaakt en deze aan de virtuele machine is gekoppeld, wordt de nieuwe schijf weer gegeven in de schijf instellingen van de virtuele machine onder **gegevens schijven**. Aangezien Managed disks een resource op het hoogste niveau zijn, wordt de schijf weer gegeven in de hoofdmap van de resource groep:

   ![Azure Managed disk in resource groep](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Een bestaande schijf koppelen
1. Klik in het deel venster **schijven** op **+ gegevens schijf toevoegen**.
2. Klik op de vervolg keuzelijst voor **naam** om een lijst weer te geven met de bestaande beheerde schijven die toegankelijk zijn voor uw Azure-abonnement. Selecteer de beheerde schijf die u wilt koppelen:

   ![Bestaande Azure Managed Disk koppelen](./media/attach-disk-portal/select-existing-md.png)

3. Klik op **Opslaan** om de bestaande beheerde schijf te koppelen en de VM-configuratie bij te werken:
   
   ![Azure Managed Disk-updates opslaan](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Nadat Azure de schijf aan de virtuele machine heeft gekoppeld, wordt deze weer gegeven in de schijf instellingen van de virtuele machine onder **gegevens schijven**.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Verbinding maken met de Linux-VM om de nieuwe schijf te koppelen
Om uw nieuwe schijf te partitioneren, te Format teren en te koppelen, zodat uw virtuele Linux-machine deze kan gebruiken, SSH in uw VM. Zie voor meer informatie [SSH gebruiken met Linux op Azure](mac-create-ssh-keys.md). In het volgende voor beeld wordt verbinding gemaakt met een virtuele machine met de open bare DNS-vermelding van *mypublicdns.westus.cloudapp.Azure.com* met de gebruikers naam *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Wanneer u bent verbonden met uw virtuele machine, kunt u een schijf koppelen. Zoek eerst de schijf met behulp van `dmesg` (de methode die u gebruikt om uw nieuwe schijf te detecteren) kan verschillen. In het volgende voor beeld wordt dmesg gebruikt om te filteren op *SCSI* -schijven:

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

Hier is *Dit SDC* de schijf die u wilt. 

### <a name="partition-a-new-disk"></a>Een nieuwe schijf partitioneren
Als u een bestaande schijf gebruikt die gegevens bevat, gaat u door met het koppelen van de schijf. Als u een nieuwe schijf koppelt, moet u de schijf partitioneren.

> [!NOTE]
> Het is raadzaam om de meest recente versies van fdisk of een deel daarvan te gebruiken die beschikbaar zijn voor uw distributie.

Partitioneer de schijf met `fdisk`. Als de schijf grootte 2 tebibytes (TiB) of groter is, moet u GPT-partitionering gebruiken. u kunt `parted` gebruiken om GPT-partitionering uit te voeren. Als de grootte van de schijf onder 2TiB is, kunt u MBR-of GPT-partitionering gebruiken. Maak een primaire schijf op partitie 1 en accepteer de andere standaard waarden. In het volgende voor beeld wordt het `fdisk` proces op */dev/SDC*gestart:

```bash
sudo fdisk /dev/sdc
```

Gebruik de opdracht `n` om een nieuwe partitie toe te voegen. In dit voor beeld kiezen we ook `p` voor een primaire partitie en de rest van de standaard waarden te accepteren. De uitvoer ziet er ongeveer uit als in het volgende voor beeld:

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

Druk de partitie tabel af door `p` te typen en vervolgens `w` te gebruiken om de tabel naar schijf te schrijven en af te sluiten. De uitvoer moet er ongeveer uitzien als in het volgende voor beeld:

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

Schrijf nu een bestands systeem naar de partitie met de opdracht `mkfs`. Geef het type bestands systeem en de naam van het apparaat op. In het volgende voor beeld wordt een *ext4* -bestands systeem gemaakt op de */dev/sdc1* -partitie die in de voor gaande stappen is gemaakt:

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

#### <a name="alternate-method-using-parted"></a>Alternatieve methode met behulp van opgenomen
Het hulp programma fdisk heeft interactieve invoer nodig en is daarom niet ideaal voor gebruik in Automation-scripts. [Het hulp](https://www.gnu.org/software/parted/) programma met functies kan echter worden gescripteerd en is daarom beter in automatiserings scenario's. Het hulp programma parted kan worden gebruikt voor het partitioneren en Format teren van een gegevens schijf. Voor de onderstaande procedure gebruiken we een nieuwe/dev/SDC voor de gegevens schijf en Format teer deze met het bestands systeem [xfs](https://xfs.wiki.kernel.org/) .
```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
partprobe /dev/sdc1
```
Zoals hierboven weer gegeven, gebruiken we het [partprobe](https://linux.die.net/man/8/partprobe) -hulp programma om ervoor te zorgen dat de kernel onmiddellijk op de hoogte is van de nieuwe partitie en het bestands systeem. Het gebruik van partprobe kan ertoe leiden dat de blkid-of lslbk-opdrachten de UUID voor het nieuwe bestands systeem niet direct retour neren.

### <a name="mount-the-disk"></a>De schijf koppelen
Maak een map om het bestands systeem met behulp van `mkdir`te koppelen. In het volgende voor beeld maakt u een map op */datadrive*:

```bash
sudo mkdir /datadrive
```

Gebruik `mount` om het bestands systeem vervolgens te koppelen. In het volgende voor beeld wordt de */dev/sdc1* -partitie gekoppeld aan het */datadrive* -koppel punt:

```bash
sudo mount /dev/sdc1 /datadrive
```

Om ervoor te zorgen dat het station automatisch na het opnieuw opstarten opnieuw wordt gekoppeld, moet het worden toegevoegd aan het *bestand/etc/fstab* -bestand. Het wordt ook ten zeerste aanbevolen om de UUID (Universally Unique IDentifier) in *bestand/etc/fstab* te gebruiken om te verwijzen naar het station in plaats van alleen de naam van het apparaat (zoals */dev/sdc1*). Als het besturings systeem tijdens het opstarten een schijf fout detecteert, voor komt u dat de onjuiste schijf wordt gekoppeld aan een bepaalde locatie. De resterende gegevens schijven worden vervolgens toegewezen aan dezelfde apparaat-Id's. Als u de UUID van het nieuwe station wilt zoeken, gebruikt u het hulp programma `blkid`:

```bash
sudo -i blkid
```

De uitvoer ziet er ongeveer uit als in het volgende voor beeld:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Het onjuist bewerken van het **bestand/etc/fstab** -bestand kan resulteren in een systeem dat niet kan worden opgestart. Als dat niet het geval is, raadpleegt u de documentatie van de distributie voor informatie over het correct bewerken van dit bestand. U wordt ook aangeraden een back-up van het bestand/etc/fstab-bestand te maken voordat u het bewerkt.

Open vervolgens het *bestand/etc/fstab* -bestand in een tekst editor als volgt:

```bash
sudo vi /etc/fstab
```

In dit voor beeld gebruikt u de UUID-waarde voor het */dev/sdc1* -apparaat dat is gemaakt in de vorige stappen en de koppel punt van */datadrive*. Voeg de volgende regel toe aan het einde van het *bestand/etc/fstab* -bestand:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```
Wanneer u klaar bent, slaat u het *bestand/etc/fstab* -bestand op en start u het systeem opnieuw op.
> [!NOTE]
> Later het verwijderen van een gegevens schijf zonder het bewerken van fstab kan ertoe leiden dat de virtuele machine niet kan worden opgestart. De meeste distributies bieden de opties *nobootwait* *en/* of fstab. Met deze opties kan een systeem worden opgestart zelfs als de schijf niet kan worden gekoppeld tijdens het opstarten. Raadpleeg de documentatie van uw distributie voor meer informatie over deze para meters.
> 
> De *niet-werkende* optie zorgt ervoor dat de VM wordt gestart, zelfs als het bestands systeem beschadigd is of wanneer de schijf niet aanwezig is tijdens het opstarten. Zonder deze optie kunt u zich voordoen als beschreven in [kan niet SSH-naar-Linux-VM wegens FSTAB-fouten](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>Ondersteuning voor Linux in azure bijsnijden/ontkoppelen
Sommige Linux-kernels ondersteunen bewerkingen voor het verwijderen/ontkoppelen van ongebruikte blokken op de schijf. Deze functie is vooral nuttig in de standaard opslag om Azure te informeren dat verwijderde pagina's niet meer geldig zijn en kunnen worden verwijderd en geld kunnen besparen als u grote bestanden maakt en deze vervolgens verwijdert.

Er zijn twee manieren om ondersteuning voor het verkleinen van de virtuele Linux-machine in te scha kelen. Zoals gebruikelijk, raadpleegt u de distributie voor de aanbevolen benadering:

* Gebruik de optie voor het koppelen van `discard` in *bestand/etc/fstab*, bijvoorbeeld:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* In sommige gevallen heeft de optie `discard` mogelijk gevolgen voor de prestaties. U kunt ook de `fstrim` opdracht hand matig uitvoeren vanaf de opdracht regel of deze toevoegen aan uw crontab om regel matig uit te voeren:
  
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
U kunt ook [een gegevens schijf koppelen](add-disk.md) met behulp van de Azure cli.
