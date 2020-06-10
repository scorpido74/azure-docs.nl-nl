---
title: Software-RAID configureren op een virtuele Linux-machine
description: Meer informatie over het gebruik van mdadm voor het configureren van RAID op Linux in Azure.
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 3471ccfa0899f73969c511dea283c2d0d7051af8
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659787"
---
# <a name="configure-software-raid-on-linux"></a>Software-RAID op Linux configureren
Het is een veelvoorkomend scenario voor het gebruik van software-RAID op virtuele Linux-machines in azure om meerdere gekoppelde gegevens schijven als één RAID-apparaat te presen teren. Dit kan meestal worden gebruikt om de prestaties te verbeteren en een betere door voer te bieden ten opzichte van het gebruik van slechts één schijf.

## <a name="attaching-data-disks"></a>Gegevens schijven koppelen
Er zijn twee of meer lege gegevens schijven nodig voor het configureren van een RAID-apparaat.  De belangrijkste reden voor het maken van een RAID-apparaat is het verbeteren van de prestaties van de schijf-i/o.  Op basis van uw IO-behoeften kunt u ervoor kiezen om schijven die zijn opgeslagen in onze standaard opslag te koppelen, met Maxi maal 500 IO/PS per schijf of onze Premium-opslag, met Maxi maal 5000 IO/PS per schijf. In dit artikel vindt u geen gedetailleerde informatie over het inrichten en koppelen van gegevens schijven aan een virtuele Linux-machine.  Raadpleeg het Microsoft Azure artikel [een schijf koppelen](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) voor gedetailleerde instructies over het koppelen van een lege gegevens schijf aan een virtuele Linux-machine in Azure.

> [!IMPORTANT]
>Combi neer geen schijven van verschillende grootten. Dit leidt ertoe dat de prestaties van de RAID-set beperkt zijn tot die van de langzaamste schijf. 

## <a name="install-the-mdadm-utility"></a>Het hulp programma mdadm installeren
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

## <a name="create-the-disk-partitions"></a>De schijf partities maken
In dit voor beeld maken we een enkele schijf partitie op/dev/SDC. De nieuwe schijf partitie krijgt de naam/dev/sdc1.

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

1. Druk op ' n ' bij de prompt om een **n**ieuwe partitie te maken:

    ```bash
    Command (m for help): n
    ```

1. Druk vervolgens op ' p ' om een **p**rimaire-partitie te maken:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Druk op 1 om partitie nummer 1 te selecteren:

    ```bash
    Partition number (1-4): 1
    ```

1. Selecteer het begin punt van de nieuwe partitie of druk op `<enter>` de standaard waarde om de partitie aan het begin van de beschik bare ruimte op het station te plaatsen:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Selecteer de grootte van de partitie, bijvoorbeeld type + 10G, om een 10 GB-partitie te maken. Of druk op `<enter>` een enkele partitie maken die het hele station omvat:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Wijzig vervolgens de **id en het**ype van de partitie van de standaard-id ' 83 ' (Linux) naar de id ' FD ' (Linux RAID auto):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Ten slotte schrijft u de partitie tabel naar het station en sluit u Fdisk af:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>De RAID-matrix maken
1. In het volgende voor beeld wordt ' Stripe ' (RAID level 0) drie partities op drie afzonderlijke gegevens schijven (sdc1, sdd1, sde1).  Na het uitvoeren van deze opdracht wordt een nieuw RAID-apparaat met de naam **/dev/md127** gemaakt. Als deze gegevens schijven eerder deel uitmaken van een andere verouderde RAID-matrix, kan het nodig zijn om de `--force` para meter toe te voegen aan de `mdadm` opdracht:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Het bestands systeem maken op het nieuwe RAID-apparaat
   
    **CentOS, Oracle Linux, SLES 12, openSUSE en Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    **SLES 11** : boot.MD inschakelen en mdadm. conf maken

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Mogelijk moet u de computer opnieuw opstarten nadat u deze wijzigingen hebt aangebracht op SUSE-systemen. Deze stap is *niet* vereist voor SLES 12.
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>Het nieuwe bestands systeem toevoegen aan bestand/etc/fstab
> [!IMPORTANT]
> Het onjuist bewerken van het bestand/etc/fstab-bestand kan resulteren in een systeem dat niet kan worden opgestart. Als u niet zeker weet wat u moet doen, raadpleegt u de documentatie van de distributie over het bewerken van dit bestand. U wordt ook aangeraden een back-up van het bestand/etc/fstab-bestand te maken voordat u het bewerkt.

1. Maak het gewenste koppel punt voor het nieuwe bestands systeem, bijvoorbeeld:

    ```bash
    sudo mkdir /data
    ```
1. Bij het bewerken van bestand/etc/fstab moet de **uuid** worden gebruikt om te verwijzen naar het bestands systeem in plaats van de naam van het apparaat.  Gebruik het `blkid` hulp programma om de UUID voor het nieuwe bestands systeem te bepalen:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Open bestand/etc/fstab in een tekst editor en voeg een vermelding toe voor het nieuwe bestands systeem, bijvoorbeeld:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Of op **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Sla vervolgens op en sluit/etc/fstab.

1. Test of de bestand/etc/fstab-vermelding juist is:

    ```bash  
    sudo mount -a
    ```

    Als met deze opdracht een fout bericht wordt weer gegeven, controleert u de syntaxis in het bestand/etc/fstab-bestand.
   
    Voer de volgende `mount` opdracht uit om te controleren of het bestands systeem is gekoppeld:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. Beschrijving Failsafe-opstart parameters
   
    **fstab-configuratie**
   
    Veel distributies bevatten de `nobootwait` `nofail` para meters of Mount die kunnen worden toegevoegd aan het bestand/etc/fstab-bestand. Met deze para meters kunnen fouten optreden bij het koppelen van een bepaald bestands systeem, waardoor het Linux-systeem kan blijven opstarten, zelfs als het RAID-bestands systeem niet goed kan worden gekoppeld. Raadpleeg de documentatie van uw distributie voor meer informatie over deze para meters.
   
    Voor beeld (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux-opstart parameters**
   
    Naast de bovenstaande para meters, kan de kernel-para meter `bootdegraded=true` het systeem laten opstarten, zelfs als de RAID wordt waargenomen als beschadigd of gedegradeerd, bijvoorbeeld als een gegevens station per ongeluk van de virtuele machine wordt verwijderd. Dit kan standaard ook leiden tot een niet-opstartbaar systeem.
   
    Raadpleeg de documentatie van uw distributie voor het correct bewerken van kernel-para meters. In veel distributies (CentOS, Oracle Linux, SLES 11) kunnen deze para meters bijvoorbeeld hand matig worden toegevoegd aan het `/boot/grub/menu.lst` bestand.  Op Ubuntu kunt u deze para meter toevoegen aan de `GRUB_CMDLINE_LINUX_DEFAULT` variabele op '/etc/default/grub '.


## <a name="trimunmap-support"></a>Ondersteuning voor knippen/ontkoppelen
Sommige Linux-kernels ondersteunen bewerkingen voor het verwijderen/ontkoppelen van ongebruikte blokken op de schijf. Deze bewerkingen zijn voornamelijk handig in de standaard opslag om Azure te informeren dat verwijderde pagina's niet meer geldig zijn en kunnen worden verwijderd. Bij het verwijderen van pagina's kunnen kosten worden bespaard als u grote bestanden maakt en deze vervolgens verwijdert.

> [!NOTE]
> RAID kan geen opdrachten voor negeren geven als de segment grootte voor de matrix is ingesteld op kleiner dan de standaard waarde (512 KB). Dit komt doordat de granulariteit van de toewijzing op de host ook 512 KB is. Als u de segment grootte van de matrix hebt gewijzigd via de para meter van de mdadm `--chunk=` , kunnen aanvragen voor knippen/opheffen door de kernel worden genegeerd.

Er zijn twee manieren om ondersteuning voor het verkleinen van de virtuele Linux-machine in te scha kelen. Zoals gebruikelijk, raadpleegt u de distributie voor de aanbevolen benadering:

- Gebruik de `discard` optie koppelen in `/etc/fstab` , bijvoorbeeld:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- In sommige gevallen `discard` kan de optie invloed hebben op de prestaties. U kunt de `fstrim` opdracht ook hand matig uitvoeren vanaf de opdracht regel of deze toevoegen aan uw crontab om regel matig uit te voeren:

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
