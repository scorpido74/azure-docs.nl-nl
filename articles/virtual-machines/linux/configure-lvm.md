---
title: LVM configureren op een virtuele machine met Linux
description: Meer informatie over het configureren van LVM op Linux in Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/27/2018
ms.author: guybo
ms.subservice: disks
ms.openlocfilehash: 7f560a1e6266b5f2452bf9442d2d4c983de1236e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066800"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>LVM configureren op een Linux-vm in Azure
In dit document wordt besproken hoe u Logical Volume Manager (LVM) configureert in uw virtuele Azure-machine. LVM kan worden gebruikt op de osschijf of gegevensschijven in Azure VM's, maar standaard hebben de meeste cloudafbeeldingen geen LVM geconfigureerd op de OS-schijf. De onderstaande stappen zullen zich richten op het configureren van LVM voor uw gegevensschijven.

## <a name="linear-vs-striped-logical-volumes"></a>Lineaire versus gestreepte logische volumes
LVM kan worden gebruikt om een aantal fysieke schijven te combineren tot één opslagvolume. Standaard creëert LVM meestal lineaire logische volumes, wat betekent dat de fysieke opslag samen wordt samengevoegd. In dit geval worden lees-/schrijfbewerkingen meestal alleen naar één schijf verzonden. In tegenstelling, kunnen we ook gestreepte logische volumes waar leest en schrijft worden gedistribueerd naar meerdere schijven in de volumegroep (vergelijkbaar met RAID0). Om prestatieredenen, is het waarschijnlijk dat u wilt strepen uw logische volumes, zodat leest en schrijft gebruik maken van al uw bijgevoegde gegevens schijven.

In dit document wordt beschreven hoe u meerdere gegevensschijven in één volumegroep combineren en vervolgens een gestreept logisch volume maken. De onderstaande stappen worden gegeneraliseerd om met de meeste distributies te werken. In de meeste gevallen zijn de hulpprogramma's en werkstromen voor het beheren van LVM op Azure niet fundamenteel anders dan andere omgevingen. Raadpleeg zoals gebruikelijk ook uw Linux-leverancier voor documentatie en best practices voor het gebruik van LVM met uw specifieke distributie.

## <a name="attaching-data-disks"></a>Gegevensschijven koppelen
Men zal meestal willen beginnen met twee of meer lege gegevens schijven bij het gebruik van LVM. Op basis van uw IO-behoeften u ervoor kiezen schijven te bevestigen die zijn opgeslagen in onze standaardopslag, met maximaal 500 IO/ps per schijf of onze Premium-opslag met maximaal 5000 IO/ps per schijf. Dit artikel zal niet in detail treden over hoe u gegevensschijven inrichten en koppelen aan een Virtuele Linux-machine. Zie het Microsoft Azure-artikel [bevestig een schijf](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) voor gedetailleerde instructies over het koppelen van een lege gegevensschijf aan een virtuele Linux-machine op Azure.

## <a name="install-the-lvm-utilities"></a>De LVM-hulpprogramma's installeren
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS & Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 en openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    Op SLES11 moet u `/etc/sysconfig/lvm` ook `LVM_ACTIVATED_ON_DISCOVERED` bewerken en instellen op "inschakelen":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>LVM configureren
In deze gids gaan we ervan uit dat u drie gegevensschijven `/dev/sdd` `/dev/sde`hebt bijgevoegd, die we zullen `/dev/sdc`noemen, en . Deze paden komen mogelijk niet overeen met de namen van het schijfpad in uw vm. U`sudo fdisk -l`de opdracht ' ' of vergelijkbaar uitvoeren om uw beschikbare schijven weer te geven.

1. Bereid de fysieke volumes voor:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Maak een volumegroep. In dit voorbeeld noemen we `data-vg01`de volumegroep:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Het logische volume(en) maken. De onderstaande opdracht maken we `data-lv01` één logisch volume dat wordt aangeroepen om de hele volumegroep te bestrijken, maar houd er rekening mee dat het ook haalbaar is om meerdere logische volumes in de volumegroep te maken.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Het logische volume opmaken

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Met SLES11 `-t ext3` gebruik in plaats van ext4. SLES11 ondersteunt alleen-lezen toegang tot ext4-bestandssystemen.

## <a name="add-the-new-file-system-to-etcfstab"></a>Het nieuwe bestandssysteem toevoegen aan /etc/fstab
> [!IMPORTANT]
> Als u het bestand `/etc/fstab` onjuist bewerkt, kan dat erin resulteren dat het systeem niet meer kan worden opgestart. Als u niet zeker weet wat u moet doen, raadpleegt u de documentatie van de distributie over het bewerken van dit bestand. Het wordt ook aanbevolen dat `/etc/fstab` een back-up van het bestand wordt gemaakt voordat het wordt bewerkt.

1. Maak het gewenste bevestigingspunt voor uw nieuwe bestandssysteem, bijvoorbeeld:

    ```bash  
    sudo mkdir /data
    ```

2. Het logische volumepad zoeken

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Open `/etc/fstab` in een teksteditor en voeg een vermelding toe voor het nieuwe bestandssysteem, bijvoorbeeld:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Sla op en `/etc/fstab`sluit.

4. Test of `/etc/fstab` de vermelding juist is:

    ```bash    
    sudo mount -a
    ```

    Als deze opdracht resulteert in een foutbericht, controleert u de syntaxis in het `/etc/fstab` bestand.
   
    Voer vervolgens `mount` de opdracht uit om ervoor te zorgen dat het bestandssysteem is gemonteerd:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Optioneel) Failsafe-opstartparameters in`/etc/fstab`
   
    Veel distributies bevatten `nobootwait` `nofail` de parameters of mount `/etc/fstab` parameters die aan het bestand kunnen worden toegevoegd. Deze parameters zorgen voor storingen bij het monteren van een bepaald bestandssysteem en zorgen ervoor dat het Linux-systeem kan blijven opstarten, zelfs als het niet in staat is om het RAID-bestandssysteem goed te monteren. Raadpleeg de documentatie van uw distributie voor meer informatie over deze parameters.
   
    Voorbeeld (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>ONDERSTEUNING VOOR TRIM/UNMAP
Sommige Linux-kernels ondersteunen TRIM/UNMAP-bewerkingen om ongebruikte blokken op de schijf te verwijderen. Deze bewerkingen zijn voornamelijk handig in standaardopslag om Azure te informeren dat verwijderde pagina's niet langer geldig zijn en kunnen worden verwijderd. Het verwijderen van pagina's kan kosten besparen als u grote bestanden maakt en deze vervolgens verwijdert.

Er zijn twee manieren om TRIM-ondersteuning in te schakelen in uw Linux VM. Raadpleeg zoals gebruikelijk uw distributie voor de aanbevolen aanpak:

- Gebruik `discard` de optie `/etc/fstab`monteren in bijvoorbeeld:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- In sommige `discard` gevallen kan de optie gevolgen hebben voor de prestaties. U de `fstrim` opdracht ook handmatig uitvoeren vanaf de opdrachtregel of deze toevoegen aan uw crontab om regelmatig uit te voeren:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL, CentOS & Oracle Linux**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
