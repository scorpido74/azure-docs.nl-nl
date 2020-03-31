---
title: Software RAID configureren op een Linux VM
description: Meer informatie over het gebruik van mdadm om RAID op Linux in Azure te configureren.
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 122abda51b907491b322908c3c2c689bc1723e87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250255"
---
# <a name="configure-software-raid-on-linux"></a>Software RAID configureren op Linux
Het is een veelvoorkomend scenario om software RAID op virtuele Linux-machines in Azure te gebruiken om meerdere aangesloten gegevensschijven als één RAID-apparaat te presenteren. Meestal kan dit worden gebruikt om de prestaties te verbeteren en zorgen voor een betere doorvoer in vergelijking met het gebruik van slechts een enkele schijf.

## <a name="attaching-data-disks"></a>Gegevensschijven koppelen
Twee of meer lege gegevensschijven zijn nodig om een RAID-apparaat te configureren.  De belangrijkste reden voor het maken van een RAID-apparaat is om de prestaties van uw schijf IO te verbeteren.  Op basis van uw IO-behoeften u ervoor kiezen schijven te bevestigen die zijn opgeslagen in onze standaardopslag, met maximaal 500 IO/ps per schijf of onze Premium-opslag met maximaal 5000 IO/ps per schijf. Dit artikel gaat niet in detail over hoe u gegevensschijven inrichten en koppelen aan een Virtuele Linux-machine.  Zie het Microsoft Azure-artikel [bevestig een schijf](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) voor gedetailleerde instructies over het koppelen van een lege gegevensschijf aan een virtuele Linux-machine op Azure.

> [!IMPORTANT]
>Meng geen schijven van verschillende grootte, dit zou resulteren in de prestaties van de raidset worden beperkt tot die van de langzaamste schijf. 

## <a name="install-the-mdadm-utility"></a>Installeer het mdadm-hulpprogramma
* **Ubuntu**
  ```bash
  sudo apt-get update
  sudo apt-get install mdadm
  ```

* **CentOS & Oracle Linux**
  ```bash
  sudo yum install mdadm
  ```

* **SLES en openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>De schijfpartities maken
In dit voorbeeld maken we één schijfpartitie op /dev/sdc. De nieuwe schijfpartitie wordt /dev/sdc1 genoemd.

1. Beginnen `fdisk` met het maken van partities

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

1. Druk op 'n' bij de prompt om een **n ew-partitie**te maken:

    ```bash
    Command (m for help): n
    ```

1. Druk vervolgens op 'p' om een **p**rimary partitie te maken:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Druk op '1' om partitienummer 1 te selecteren:

    ```bash
    Partition number (1-4): 1
    ```

1. Selecteer het beginpunt van de `<enter>` nieuwe partitie of druk op de standaardinstelling om de partitie aan het begin van de vrije ruimte op het station te plaatsen:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Selecteer de grootte van de partitie, typ bijvoorbeeld '+10G' om een partitie van 10 gigabyte te maken. Of druk `<enter>` op één partitie maken die het hele station overspant:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Vervolgens wijzig je de ID en **t**ype van de partitie van de standaard ID '83' (Linux) naar ID 'fd' (Linux raid auto):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Tot slot, schrijf de partitietabel naar het station en sluit fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>De RAID-array maken
1. In het volgende voorbeeld wordt drie partities op drie afzonderlijke gegevensschijven (sdc1, sdd1, sde1) verwijderd (stripe" (RAID-niveau 0).  Na het uitvoeren van deze opdracht wordt een nieuw RAID-apparaat gemaakt met de naam **/dev/md127.** Houd er ook rekening mee dat als deze gegevensschijven we voorheen `--force` deel `mdadm` uitmaakten van een andere ter ziele gegane RAID-array, het mogelijk is om de parameter aan de opdracht toe te voegen:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Het bestandssysteem maken op het nieuwe RAID-apparaat
   
    **CentOS, Oracle Linux, SLES 12, openSUSE en Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    **SLES 11** - boot.md in staat stellen en mdadm.conf maken

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Een herstart kan nodig zijn na het aanbrengen van deze wijzigingen op SUSE-systemen. Deze stap is *niet* vereist op SLES 12.
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>Het nieuwe bestandssysteem toevoegen aan /etc/fstab
> [!IMPORTANT]
> Het onjuist bewerken van het /etc/fstab-bestand kan resulteren in een niet-opstartbaar systeem. Als u niet zeker weet wat u moet doen, raadpleegt u de documentatie van de distributie over het bewerken van dit bestand. Het wordt ook aanbevolen dat een back-up van het /etc/fstab-bestand wordt gemaakt voordat het wordt bewerkt.

1. Maak het gewenste bevestigingspunt voor uw nieuwe bestandssysteem, bijvoorbeeld:

    ```bash
    sudo mkdir /data
    ```
1. Bij het bewerken /etc/fstab moet de **UUID** worden gebruikt om naar het bestandssysteem te verwijzen in plaats van naar de naam van het apparaat.  Gebruik `blkid` het hulpprogramma om de UUID voor het nieuwe bestandssysteem te bepalen:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Open /etc/fstab in een teksteditor en voeg een vermelding toe voor het nieuwe bestandssysteem, bijvoorbeeld:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Of op **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Dan, opslaan en sluiten / etc / fstab.

1. Test of de /etc/fstab-vermelding juist is:

    ```bash  
    sudo mount -a
    ```

    Als deze opdracht resulteert in een foutbericht, raadpleegt u de syntaxis in het bestand /etc/fstab.
   
    Voer vervolgens `mount` de opdracht uit om ervoor te zorgen dat het bestandssysteem is gemonteerd:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (Optioneel) Failsafe-opstartparameters
   
    **fstab-configuratie**
   
    Veel distributies omvatten `nobootwait` `nofail` de of mount parameters die kunnen worden toegevoegd aan de / etc / fstab bestand. Deze parameters zorgen voor storingen bij het monteren van een bepaald bestandssysteem en zorgen ervoor dat het Linux-systeem kan blijven opstarten, zelfs als het niet in staat is om het RAID-bestandssysteem goed te monteren. Raadpleeg de documentatie van uw distributie voor meer informatie over deze parameters.
   
    Voorbeeld (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux-opstartparameters**
   
    Naast de bovenstaande parameters kan de`bootdegraded=true`kernelparameter " het systeem laten opstarten, zelfs als de RAID als beschadigd of gedegradeerd wordt ervaren, bijvoorbeeld als een gegevensstation per ongeluk uit de virtuele machine wordt verwijderd. Standaard kan dit ook resulteren in een niet-opstartbaar systeem.
   
    Raadpleeg de documentatie van uw distributie over het correct bewerken van kernelparameters. In veel distributies (CentOS, Oracle Linux, SLES 11) kunnen deze parameters`/boot/grub/menu.lst`bijvoorbeeld handmatig worden toegevoegd aan het " bestand"  Op Ubuntu kan deze parameter `GRUB_CMDLINE_LINUX_DEFAULT` worden toegevoegd aan de variabele op "/etc/default/grub".


## <a name="trimunmap-support"></a>ONDERSTEUNING VOOR TRIM/UNMAP
Sommige Linux-kernels ondersteunen TRIM/UNMAP-bewerkingen om ongebruikte blokken op de schijf te verwijderen. Deze bewerkingen zijn voornamelijk handig in standaardopslag om Azure te informeren dat verwijderde pagina's niet langer geldig zijn en kunnen worden verwijderd. Het verwijderen van pagina's kan kosten besparen als u grote bestanden maakt en deze vervolgens verwijdert.

> [!NOTE]
> RAID mag geen teruggooiopdrachten afgeven als de chunk-grootte voor de array is ingesteld op minder dan de standaardwaarde (512KB). Dit komt omdat de map granulariteit op de Host is ook 512KB. Als u de grootte van de array hebt `--chunk=` gewijzigd via de parameter mdadm, kunnen TRIM/unmap-aanvragen door de kernel worden genegeerd.

Er zijn twee manieren om TRIM-ondersteuning in te schakelen in uw Linux VM. Raadpleeg zoals gebruikelijk uw distributie voor de aanbevolen aanpak:

- Gebruik `discard` de optie `/etc/fstab`monteren in bijvoorbeeld:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- In sommige `discard` gevallen kan de optie gevolgen hebben voor de prestaties. U de `fstrim` opdracht ook handmatig uitvoeren vanaf de opdrachtregel of deze toevoegen aan uw crontab om regelmatig uit te voeren:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
