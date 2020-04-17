---
title: Een Red Hat Enterprise Linux VHD maken en uploaden voor gebruik in Azure
description: Leer een Virtual Hard Disk (Azure Virtual Hard Disk) maken en uploaden die een Red Hat Linux-besturingssysteem bevat.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/17/2019
ms.author: guybo
ms.openlocfilehash: cc806fe0c3894174835c99493ebf2ba19a11ca28
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460457"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Een op Red Hat gebaseerde virtuele machine voor Azure voorbereiden
In dit artikel leert u hoe u een Virtual Machine (Red Hat Enterprise Linux) voorbereidt voor gebruik in Azure. De versies van RHEL die in dit artikel worden behandeld zijn 6.7+ en 7.1+. De hypervisors ter voorbereiding die in dit artikel worden behandeld zijn Hyper-V, kernel-based virtual machine (KVM) en VMware. Zie de [Cloud Access-website](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) van Red Hat en [RHEL uitvoeren op Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure)voor meer informatie over geschiktheidsvereisten voor deelname aan het Cloud Access-programma van Red Hat. Zie de [Azure Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview)voor manieren om RHEL-afbeeldingen te automatiseren.

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Bereid een op Red Hat gebaseerde virtuele machine van Hyper-V Manager

### <a name="prerequisites"></a>Vereisten
In deze sectie wordt ervan uitgegaan dat u al een ISO-bestand van de Red Hat-website hebt verkregen en de RHEL-afbeelding hebt geïnstalleerd op een virtuele harde schijf (VHD). Zie [Hyper-V-rol installeren en een virtuele machine configureren](https://technet.microsoft.com/library/hh846766.aspx)voor meer informatie over het gebruik van Hyper-V Manager om een afbeelding van het besturingssysteem te installeren.

**RHEL-installatienotities**

* Azure biedt geen ondersteuning voor de VHDX-indeling. Azure ondersteunt alleen vaste VHD. U Hyper-V Manager gebruiken om de schijf om te zetten naar VHD-formaat, of u de convert-vhd-cmdlet gebruiken. Als u VirtualBox gebruikt, selecteert u **Vaste grootte** in tegenstelling tot de standaardoptie die dynamisch is toegewezen wanneer u de schijf maakt.
* Azure ondersteunt Gen1 (BIOS boot) & Gen2 (UEFI boot) Virtuele machines.
* De maximale grootte die is toegestaan voor de VHD is 1.023 GB.
* Logical Volume Manager (LVM) wordt ondersteund en kan worden gebruikt op de OS-schijf of gegevensschijven in virtuele Azure-machines. In het algemeen wordt het echter aanbevolen om standaardpartities op de OS-schijf te gebruiken in plaats van LVM. Deze praktijk voorkomt dat LVM-naam conflicten met gekloonde virtuele machines, vooral als je ooit nodig hebt om een besturingssysteem schijf hechten aan een andere identieke virtuele machine voor het oplossen van problemen. Zie ook [LVM-](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) en [RAID-documentatie.](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Kernel ondersteuning voor het monteren van Universal Disk Format (UDF) bestandssystemen is vereist. Bij het eerste opstarten op Azure, de UDF-geformatteerde media die is gekoppeld aan de gast passeert de inrichting configuratie aan de Linux virtuele machine. De Azure Linux Agent moet het UDF-bestandssysteem kunnen monteren om de configuratie en inrichting van de virtuele machine te lezen.
* Configureer geen swappartitie op de schijf van het besturingssysteem. De Linux-agent kan worden geconfigureerd om een swapbestand op de tijdelijke resourceschijf te maken.  Meer informatie hierover vindt u in de volgende stappen.
* Alle VHD's op Azure moeten een virtuele grootte hebben die is uitgelijnd op 1 MB. Bij het converteren van een ruwe schijf naar VHD moet u ervoor zorgen dat de ruwe schijfgrootte een veelvoud van 1 MB is voor conversie. Meer details zijn te vinden in de onderstaande stappen. Zie ook [Linux Installatienotities](create-upload-generic.md#general-linux-installation-notes) voor meer informatie.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Bereid een RHEL 6 virtuele machine van Hyper-V Manager

1. Selecteer in Hyper-V Manager de virtuele machine.

1. Klik **op Verbinding maken** om een consolevenster voor de virtuele machine te openen.

1. In RHEL 6 kan NetworkManager de Azure Linux-agent verstoren. Verwijder dit pakket door de volgende opdracht uit te voeren:
   
        # sudo rpm -e --nodeps NetworkManager

1. Maak of bewerk het `/etc/sysconfig/network` bestand en voeg de volgende tekst toe:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Maak of bewerk het `/etc/sysconfig/network-scripts/ifcfg-eth0` bestand en voeg de volgende tekst toe:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Verplaats (of verwijder) de udev-regels om te voorkomen dat statische regels voor de Ethernet-interface worden genereren. Deze regels veroorzaken problemen wanneer u een virtuele machine kloont in Microsoft Azure of Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Controleer of de netwerkservice op de opstarttijd wordt gestart door de volgende opdracht uit te voeren:

        # sudo chkconfig network on

1. Registreer uw Red Hat-abonnement om de installatie van pakketten uit de RHEL-opslagplaats in te schakelen door de volgende opdracht uit te voeren:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. De WALinuxAgent `WALinuxAgent-<version>`pakket, , is geduwd om de Red Hat extras repository. Schakel de extra's repository in door de volgende opdracht uit te voeren:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Wijzig de kernelbootlijn in uw grubconfiguratie om extra kernelparameters voor Azure op te nemen. Als u deze `/boot/grub/menu.lst` wijziging wilt doen, opent u deze teksteditor en zorgt u ervoor dat de standaardkernel de volgende parameters bevat:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Dit zorgt er ook voor dat alle consoleberichten naar de eerste seriële poort worden verzonden, die Azure-ondersteuning kan helpen bij foutopsporingsproblemen.
    
    Daarnaast raden we u aan de volgende parameters te verwijderen:
    
        rhgb quiet crashkernel=auto
    
    Grafische en stille boot zijn niet nuttig in een cloud-omgeving waar we willen dat alle logs worden verzonden naar de seriële poort.  U `crashkernel` de optie indien gewenst geconfigureerd laten. Houd er rekening mee dat deze parameter de hoeveelheid beschikbaar geheugen in de virtuele machine met 128 MB of meer vermindert. Deze configuratie kan problematisch zijn bij kleinere virtuele machineformaten.


1. Zorg ervoor dat de Secure Shell (SSH)-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten, wat meestal de standaard is. Wijzig /etc/ssh/sshd_config om de volgende regel op te nemen:

        ClientAliveInterval 180

1. Installeer de Azure Linux Agent door de volgende opdracht uit te voeren:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    Als u het WALinuxAgent-pakket installeert, worden de NetworkManager- en NetworkManager-gnome-pakketten verwijderd als ze nog niet in stap 3 zijn verwijderd.

1. Maak geen wisselruimte op de schijf van het besturingssysteem.

    De Azure Linux Agent kan de swapruimte automatisch configureren met behulp van de lokale resourceschijf die aan de virtuele machine is gekoppeld nadat de virtuele machine is ingericht op Azure. Houd er rekening mee dat de lokale resourceschijf een tijdelijke schijf is en dat deze kan worden geleegd als de virtuele machine is gedeprovisioneerd. Nadat u de Azure Linux Agent in de vorige stap hebt geïnstalleerd, wijzigt u de volgende parameters in /etc/waagent.conf op de juiste manier:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Meld u het abonnement (indien nodig) uit door de volgende opdracht uit te voeren:

        # sudo subscription-manager unregister

1. Voer de volgende opdrachten uit om de virtuele machine te deprovisioneren en voor te bereiden op het inrichten op Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Klik op **Action** > **Shut Down** in Hyper-V Manager. Uw Linux VHD is nu klaar om te worden geüpload naar Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Bereid een RHEL 7 virtuele machine van Hyper-V Manager

1. Selecteer in Hyper-V Manager de virtuele machine.

1. Klik **op Verbinding maken** om een consolevenster voor de virtuele machine te openen.

1. Maak of bewerk het `/etc/sysconfig/network` bestand en voeg de volgende tekst toe:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Maak of bewerk het `/etc/sysconfig/network-scripts/ifcfg-eth0` bestand en voeg de volgende tekst toe:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=ja NM_CONTROLLED=ja

1. Controleer of de netwerkservice op de opstarttijd wordt gestart door de volgende opdracht uit te voeren:

        # sudo systemctl enable network

1. Registreer uw Red Hat-abonnement om de installatie van pakketten uit de RHEL-opslagplaats in te schakelen door de volgende opdracht uit te voeren:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Wijzig de kernelbootlijn in uw grubconfiguratie om extra kernelparameters voor Azure op te nemen. Als u deze `/etc/default/grub` wijziging wilt doen, opent `GRUB_CMDLINE_LINUX` u een teksteditor en bewerkt u de parameter. Bijvoorbeeld:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Dit zorgt er ook voor dat alle consoleberichten naar de eerste seriële poort worden verzonden, die Azure-ondersteuning kan helpen bij foutopsporingsproblemen. Deze configuratie schakelt ook de nieuwe RHEL 7-naamgevingsconventies voor NIC's uit. Daarnaast raden we u aan de volgende parameters te verwijderen:
   
        rhgb quiet crashkernel=auto
   
    Grafische en stille boot zijn niet nuttig in een cloud-omgeving waar we willen dat alle logs worden verzonden naar de seriële poort. U `crashkernel` de optie indien gewenst geconfigureerd laten. Houd er rekening mee dat deze parameter de hoeveelheid beschikbaar geheugen in de virtuele machine met 128 MB of meer vermindert, wat problematisch kan zijn bij kleinere virtuele machineformaten.

1. Nadat u klaar bent `/etc/default/grub`met bewerken, voert u de volgende opdracht uit om de grubconfiguratie opnieuw op te bouwen:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Zorg ervoor dat de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten, wat meestal de standaard is. Wijzigen `/etc/ssh/sshd_config` om de volgende regel op te nemen:

        ClientAliveInterval 180

1. De WALinuxAgent `WALinuxAgent-<version>`pakket, , is geduwd om de Red Hat extras repository. Schakel de extra's repository in door de volgende opdracht uit te voeren:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Installeer de Azure Linux Agent door de volgende opdracht uit te voeren:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Maak geen wisselruimte op de schijf van het besturingssysteem.

    De Azure Linux Agent kan de swapruimte automatisch configureren met behulp van de lokale resourceschijf die aan de virtuele machine is gekoppeld nadat de virtuele machine is ingericht op Azure. Houd er rekening mee dat de lokale resourceschijf een tijdelijke schijf is en deze mogelijk wordt geleegd als de virtuele machine is gedeprovisioneerd. Nadat u de Azure Linux Agent in de vorige `/etc/waagent.conf` stap hebt geïnstalleerd, wijzigt u de volgende parameters op de juiste manier:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Als u het abonnement wilt uitschrijven, voert u de volgende opdracht uit:

        # sudo subscription-manager unregister

1. Voer de volgende opdrachten uit om de virtuele machine te deprovisioneren en voor te bereiden op het inrichten op Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Klik op **Action** > **Shut Down** in Hyper-V Manager. Uw Linux VHD is nu klaar om te worden geüpload naar Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Bereid een op Red Hat gebaseerde virtuele machine van KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Bereid een RHEL 6 virtuele machine van KVM

1. Download de KVM-afbeelding van RHEL 6 van de Red Hat website.

1. Stel een hoofdwachtwoord in.

    Een versleuteld wachtwoord genereren en de uitvoer van de opdracht kopiëren:

        # openssl passwd -1 changeme

    Stel een hoofdwachtwoord in met guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Het tweede veld van de hoofdgebruiker wijzigen van "!!" naar het versleutelde wachtwoord.

1. Maak een virtuele machine in KVM op basis van de qcow2-afbeelding. Stel het schijftype in op **qcow2**en stel het apparaatmodel voor virtuele netwerkinterface in op **virtio**. Start vervolgens de virtuele machine en meld u aan als root.

1. Maak of bewerk het `/etc/sysconfig/network` bestand en voeg de volgende tekst toe:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Maak of bewerk het `/etc/sysconfig/network-scripts/ifcfg-eth0` bestand en voeg de volgende tekst toe:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Verplaats (of verwijder) de udev-regels om te voorkomen dat statische regels voor de Ethernet-interface worden genereren. Deze regels veroorzaken problemen wanneer u een virtuele machine kloont in Azure of Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Controleer of de netwerkservice op de opstarttijd wordt gestart door de volgende opdracht uit te voeren:

        # chkconfig network on

1. Registreer uw Red Hat-abonnement om de installatie van pakketten uit de RHEL-opslagplaats in te schakelen door de volgende opdracht uit te voeren:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Wijzig de kernelbootlijn in uw grubconfiguratie om extra kernelparameters voor Azure op te nemen. Als u deze `/boot/grub/menu.lst` configuratie wilt doen, opent u deze in een teksteditor en zorgt u ervoor dat de standaardkernel de volgende parameters bevat:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Dit zorgt er ook voor dat alle consoleberichten naar de eerste seriële poort worden verzonden, die Azure-ondersteuning kan helpen bij foutopsporingsproblemen.
    
    Daarnaast raden we u aan de volgende parameters te verwijderen:
    
        rhgb quiet crashkernel=auto
    
    Grafische en stille boot zijn niet nuttig in een cloud-omgeving waar we willen dat alle logs worden verzonden naar de seriële poort. U `crashkernel` de optie indien gewenst geconfigureerd laten. Houd er rekening mee dat deze parameter de hoeveelheid beschikbaar geheugen in de virtuele machine met 128 MB of meer vermindert, wat problematisch kan zijn bij kleinere virtuele machineformaten.


1. Voeg Hyper-V modules toe aan initramfs:  

    Bewerk `/etc/dracut.conf`en voeg de volgende inhoud toe:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Reconstructie van initramfs:

        # dracut -f -v

1. Verwijder cloud-init:

        # yum remove cloud-init

1. Controleer of de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten:

        # chkconfig sshd on

    Wijzig /etc/ssh/sshd_config om de volgende regels op te nemen:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. De WALinuxAgent `WALinuxAgent-<version>`pakket, , is geduwd om de Red Hat extras repository. Schakel de extra's repository in door de volgende opdracht uit te voeren:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Installeer de Azure Linux Agent door de volgende opdracht uit te voeren:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. De Azure Linux Agent kan de swapruimte automatisch configureren met behulp van de lokale resourceschijf die aan de virtuele machine is gekoppeld nadat de virtuele machine is ingericht op Azure. Houd er rekening mee dat de lokale resourceschijf een tijdelijke schijf is en deze mogelijk wordt geleegd als de virtuele machine is gedeprovisioneerd. Nadat u de Azure Linux Agent in de vorige stap hebt geïnstalleerd, wijzigt u de volgende parameters in **/etc/waagent.conf** op de juiste manier:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Meld u het abonnement (indien nodig) uit door de volgende opdracht uit te voeren:

        # subscription-manager unregister

1. Voer de volgende opdrachten uit om de virtuele machine te deprovisioneren en voor te bereiden op het inrichten op Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Schakel de virtuele machine in KVM uit.

1. Converteer de qcow2-afbeelding naar het VHD-formaat.

> [!NOTE]
> Er is een bekende bug in qemu-img versies >=2.2.1 dat resulteert in een onjuist geformatteerde VHD. Het probleem is opgelost in QEMU 2.6. Het wordt aanbevolen om qemu-img 2.2.0 of lager te gebruiken, of te updaten naar 2.6 of hoger. Referentie: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Bereid een RHEL 7 virtuele machine van KVM

1. Download de KVM-afbeelding van RHEL 7 op de Red Hat website. Deze procedure gebruikt RHEL 7 als voorbeeld.

1. Stel een hoofdwachtwoord in.

    Een versleuteld wachtwoord genereren en de uitvoer van de opdracht kopiëren:

        # openssl passwd -1 changeme

    Stel een hoofdwachtwoord in met guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Het tweede veld van rootgebruiker wijzigen van "!!" naar het versleutelde wachtwoord.

1. Maak een virtuele machine in KVM op basis van de qcow2-afbeelding. Stel het schijftype in op **qcow2**en stel het apparaatmodel voor virtuele netwerkinterface in op **virtio**. Start vervolgens de virtuele machine en meld u aan als root.

1. Maak of bewerk het `/etc/sysconfig/network` bestand en voeg de volgende tekst toe:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Maak of bewerk het `/etc/sysconfig/network-scripts/ifcfg-eth0` bestand en voeg de volgende tekst toe:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=ja NM_CONTROLLED=ja

1. Controleer of de netwerkservice op de opstarttijd wordt gestart door de volgende opdracht uit te voeren:

        # sudo systemctl enable network

1. Registreer uw Red Hat-abonnement om de installatie van pakketten uit de RHEL-opslagplaats in te schakelen door de volgende opdracht uit te voeren:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Wijzig de kernelbootlijn in uw grubconfiguratie om extra kernelparameters voor Azure op te nemen. Als u deze `/etc/default/grub` configuratie wilt doen, opent `GRUB_CMDLINE_LINUX` u deze teksteditor en bewerkt u de parameter. Bijvoorbeeld:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Deze opdracht zorgt er ook voor dat alle consoleberichten naar de eerste seriële poort worden verzonden, die Azure-ondersteuning kan helpen bij foutopsporingsproblemen. Het commando schakelt ook de nieuwe RHEL 7 naamgeving conventies voor NIC's. Daarnaast raden we u aan de volgende parameters te verwijderen:
   
        rhgb quiet crashkernel=auto
   
    Grafische en stille boot zijn niet nuttig in een cloud-omgeving waar we willen dat alle logs worden verzonden naar de seriële poort. U `crashkernel` de optie indien gewenst geconfigureerd laten. Houd er rekening mee dat deze parameter de hoeveelheid beschikbaar geheugen in de virtuele machine met 128 MB of meer vermindert, wat problematisch kan zijn bij kleinere virtuele machineformaten.

1. Nadat u klaar bent `/etc/default/grub`met bewerken, voert u de volgende opdracht uit om de grubconfiguratie opnieuw op te bouwen:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Voeg Hyper-V modules toe aan initramfs.

    Inhoud `/etc/dracut.conf` bewerken en toevoegen:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Reconstructie van initramfs:

        # dracut -f -v

1. Verwijder cloud-init:

        # yum remove cloud-init

1. Controleer of de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten:

        # systemctl enable sshd

    Wijzig /etc/ssh/sshd_config om de volgende regels op te nemen:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. De WALinuxAgent `WALinuxAgent-<version>`pakket, , is geduwd om de Red Hat extras repository. Schakel de extra's repository in door de volgende opdracht uit te voeren:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Installeer de Azure Linux Agent door de volgende opdracht uit te voeren:

        # yum install WALinuxAgent

    Schakel de waagent-service in:

        # systemctl enable waagent.service

1. Maak geen wisselruimte op de schijf van het besturingssysteem.

    De Azure Linux Agent kan de swapruimte automatisch configureren met behulp van de lokale resourceschijf die aan de virtuele machine is gekoppeld nadat de virtuele machine is ingericht op Azure. Houd er rekening mee dat de lokale resourceschijf een tijdelijke schijf is en deze mogelijk wordt geleegd als de virtuele machine is gedeprovisioneerd. Nadat u de Azure Linux Agent in de vorige `/etc/waagent.conf` stap hebt geïnstalleerd, wijzigt u de volgende parameters op de juiste manier:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Meld u het abonnement (indien nodig) uit door de volgende opdracht uit te voeren:

        # subscription-manager unregister

1. Voer de volgende opdrachten uit om de virtuele machine te deprovisioneren en voor te bereiden op het inrichten op Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Schakel de virtuele machine in KVM uit.

1. Converteer de qcow2-afbeelding naar het VHD-formaat.

> [!NOTE]
> Er is een bekende bug in qemu-img versies >=2.2.1 dat resulteert in een onjuist geformatteerde VHD. Het probleem is opgelost in QEMU 2.6. Het wordt aanbevolen om qemu-img 2.2.0 of lager te gebruiken, of te updaten naar 2.6 of hoger. Referentie: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Bereid een op Red Hat gebaseerde virtuele machine van VMware
### <a name="prerequisites"></a>Vereisten
In deze sectie wordt ervan uitgegaan dat u al een RHEL virtuele machine in VMware hebt geïnstalleerd. Zie [VMware Guest Operating System Installation Guide](https://partnerweb.vmware.com/GOSIG/home.html)voor meer informatie over het installeren van een besturingssysteem in VMware.

* Wanneer u het Linux-besturingssysteem installeert, raden we u aan standaardpartities te gebruiken in plaats van LVM, wat vaak de standaard is voor veel installaties. Dit voorkomt dat LVM-naam in strijd is met gekloonde virtuele machine, vooral als een schijf van het besturingssysteem ooit moet worden bevestigd aan een andere virtuele machine voor het oplossen van problemen. LVM of RAID kan desgewenst op dataschijven worden gebruikt.
* Configureer geen swappartitie op de schijf van het besturingssysteem. U de Linux-agent configureren om een swapbestand te maken op de tijdelijke resourceschijf. Meer informatie hierover vindt u in de volgende stappen.
* Wanneer u de virtuele harde schijf maakt, selecteert u **Virtuele schijf opslaan als één bestand**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Bereid een RHEL 6 virtuele machine van VMware
1. In RHEL 6 kan NetworkManager de Azure Linux-agent verstoren. Verwijder dit pakket door de volgende opdracht uit te voeren:
   
        # sudo rpm -e --nodeps NetworkManager

1. Maak een **bestand** met de naam netwerk in de map /etc/sysconfig/met de volgende tekst:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Maak of bewerk het `/etc/sysconfig/network-scripts/ifcfg-eth0` bestand en voeg de volgende tekst toe:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Verplaats (of verwijder) de udev-regels om te voorkomen dat statische regels voor de Ethernet-interface worden genereren. Deze regels veroorzaken problemen wanneer u een virtuele machine kloont in Azure of Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Controleer of de netwerkservice op de opstarttijd wordt gestart door de volgende opdracht uit te voeren:

        # sudo chkconfig network on

1. Registreer uw Red Hat-abonnement om de installatie van pakketten uit de RHEL-opslagplaats in te schakelen door de volgende opdracht uit te voeren:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. De WALinuxAgent `WALinuxAgent-<version>`pakket, , is geduwd om de Red Hat extras repository. Schakel de extra's repository in door de volgende opdracht uit te voeren:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Wijzig de kernelbootlijn in uw grubconfiguratie om extra kernelparameters voor Azure op te nemen. Open hiervoor in `/etc/default/grub` een teksteditor en bewerk de `GRUB_CMDLINE_LINUX` parameter. Bijvoorbeeld:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Dit zorgt er ook voor dat alle consoleberichten naar de eerste seriële poort worden verzonden, die Azure-ondersteuning kan helpen bij foutopsporingsproblemen. Daarnaast raden we u aan de volgende parameters te verwijderen:
   
        rhgb quiet crashkernel=auto
   
    Grafische en stille boot zijn niet nuttig in een cloud-omgeving waar we willen dat alle logs worden verzonden naar de seriële poort. U `crashkernel` de optie indien gewenst geconfigureerd laten. Houd er rekening mee dat deze parameter de hoeveelheid beschikbaar geheugen in de virtuele machine met 128 MB of meer vermindert, wat problematisch kan zijn bij kleinere virtuele machineformaten.

1. Voeg Hyper-V modules toe aan initramfs:

    Bewerk `/etc/dracut.conf`en voeg de volgende inhoud toe:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Reconstructie van initramfs:

        # dracut -f -v

1. Zorg ervoor dat de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten, wat meestal de standaard is. Wijzigen `/etc/ssh/sshd_config` om de volgende regel op te nemen:

    ClientAliveInterval 180

1. Installeer de Azure Linux Agent door de volgende opdracht uit te voeren:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. Maak geen wisselruimte op de schijf van het besturingssysteem.

    De Azure Linux Agent kan de swapruimte automatisch configureren met behulp van de lokale resourceschijf die aan de virtuele machine is gekoppeld nadat de virtuele machine is ingericht op Azure. Houd er rekening mee dat de lokale resourceschijf een tijdelijke schijf is en deze mogelijk wordt geleegd als de virtuele machine is gedeprovisioneerd. Nadat u de Azure Linux Agent in de vorige `/etc/waagent.conf` stap hebt geïnstalleerd, wijzigt u de volgende parameters op de juiste manier:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Meld u het abonnement (indien nodig) uit door de volgende opdracht uit te voeren:

        # sudo subscription-manager unregister

1. Voer de volgende opdrachten uit om de virtuele machine te deprovisioneren en voor te bereiden op het inrichten op Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Sluit de virtuele machine af en zet het VMDK-bestand om naar een .vhd-bestand.

> [!NOTE]
> Er is een bekende bug in qemu-img versies >=2.2.1 dat resulteert in een onjuist geformatteerde VHD. Het probleem is opgelost in QEMU 2.6. Het wordt aanbevolen om qemu-img 2.2.0 of lager te gebruiken, of te updaten naar 2.6 of hoger. Referentie: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Bereid een RHEL 7 virtuele machine van VMware
1. Maak of bewerk het `/etc/sysconfig/network` bestand en voeg de volgende tekst toe:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Maak of bewerk het `/etc/sysconfig/network-scripts/ifcfg-eth0` bestand en voeg de volgende tekst toe:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=ja NM_CONTROLLED=ja

1. Controleer of de netwerkservice op de opstarttijd wordt gestart door de volgende opdracht uit te voeren:

        # sudo systemctl enable network

1. Registreer uw Red Hat-abonnement om de installatie van pakketten uit de RHEL-opslagplaats in te schakelen door de volgende opdracht uit te voeren:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Wijzig de kernelbootlijn in uw grubconfiguratie om extra kernelparameters voor Azure op te nemen. Als u deze `/etc/default/grub` wijziging wilt doen, opent `GRUB_CMDLINE_LINUX` u een teksteditor en bewerkt u de parameter. Bijvoorbeeld:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Deze configuratie zorgt er ook voor dat alle consoleberichten naar de eerste seriële poort worden verzonden, die Azure-ondersteuning kan helpen bij foutopsporingsproblemen. Het schakelt ook de nieuwe RHEL 7 naamgeving conventies voor NIC's. Daarnaast raden we u aan de volgende parameters te verwijderen:
   
        rhgb quiet crashkernel=auto
   
    Grafische en stille boot zijn niet nuttig in een cloud-omgeving waar we willen dat alle logs worden verzonden naar de seriële poort. U `crashkernel` de optie indien gewenst geconfigureerd laten. Houd er rekening mee dat deze parameter de hoeveelheid beschikbaar geheugen in de virtuele machine met 128 MB of meer vermindert, wat problematisch kan zijn bij kleinere virtuele machineformaten.

1. Nadat u klaar bent `/etc/default/grub`met bewerken, voert u de volgende opdracht uit om de grubconfiguratie opnieuw op te bouwen:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Voeg Hyper-V modules toe aan initramfs.

    Inhoud `/etc/dracut.conf`bewerken, inhoud toevoegen:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Reconstructie van initramfs:

        # dracut -f -v

1. Controleer of de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten. Deze instelling is meestal de standaardinstelling. Wijzigen `/etc/ssh/sshd_config` om de volgende regel op te nemen:

        ClientAliveInterval 180

1. De WALinuxAgent `WALinuxAgent-<version>`pakket, , is geduwd om de Red Hat extras repository. Schakel de extra's repository in door de volgende opdracht uit te voeren:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Installeer de Azure Linux Agent door de volgende opdracht uit te voeren:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Maak geen wisselruimte op de schijf van het besturingssysteem.

    De Azure Linux Agent kan de swapruimte automatisch configureren met behulp van de lokale resourceschijf die aan de virtuele machine is gekoppeld nadat de virtuele machine is ingericht op Azure. Houd er rekening mee dat de lokale resourceschijf een tijdelijke schijf is en deze mogelijk wordt geleegd als de virtuele machine is gedeprovisioneerd. Nadat u de Azure Linux Agent in de vorige `/etc/waagent.conf` stap hebt geïnstalleerd, wijzigt u de volgende parameters op de juiste manier:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Als u het abonnement wilt uitschrijven, voert u de volgende opdracht uit:

        # sudo subscription-manager unregister

1. Voer de volgende opdrachten uit om de virtuele machine te deprovisioneren en voor te bereiden op het inrichten op Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Sluit de virtuele machine af en zet het VMDK-bestand om naar de VHD-indeling.

> [!NOTE]
> Er is een bekende bug in qemu-img versies >=2.2.1 dat resulteert in een onjuist geformatteerde VHD. Het probleem is opgelost in QEMU 2.6. Het wordt aanbevolen om qemu-img 2.2.0 of lager te gebruiken, of te updaten naar 2.6 of hoger. Referentie: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Bereid een op Red Hat gebaseerde virtuele machine voor vanuit een ISO met automatisch een kickstart-bestand
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Bereid een RHEL 7 virtuele machine uit een kickstart-bestand

1.  Maak een kickstartbestand met de volgende inhoud en sla het bestand op. Voor meer informatie over kickstart installatie, zie de [Kickstart Installatie Gids](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=ja NM_CONTROLLED=ja EOF

        # Deprovision and prepare for Azure if you are creating a generalized image
        waagent -force -deprovision

        %end

1. Plaats het kickstart-bestand waar het installatiesysteem toegang toe heeft.

1. Maak in Hyper-V Manager een nieuwe virtuele machine. Selecteer op de pagina **Virtuele harde schijf verbinden** de optie Later een virtuele harde schijf **koppelen**en voltooi de wizard Nieuwe virtuele machine.

1. Open de instellingen voor virtuele machines:

    a.  Bevestig een nieuwe virtuele harde schijf aan de virtuele machine. Selecteer **VHD-indeling** en **vaste grootte**.

    b.  Bevestig de installatie-ISO aan het dvd-station.

    c.  Stel het BIOS in om op te starten vanaf cd.

1. Start de virtuele machine. Wanneer de installatiehandleiding wordt weergegeven, drukt u op **Tab** om de opstartopties te configureren.

1. Voer `inst.ks=<the location of the kickstart file>` aan het einde van de opstartopties in en druk op **Enter**.

1. Wacht tot de installatie is voltooid. Wanneer deze klaar is, wordt de virtuele machine automatisch uitgeschakeld. Uw Linux VHD is nu klaar om te worden geüpload naar Azure.

## <a name="known-issues"></a>Bekende problemen
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>De Hyper-V driver kon niet worden opgenomen in de eerste RAM-schijf bij het gebruik van een niet-Hyper-V hypervisor

In sommige gevallen, Linux installateurs kunnen niet de drivers voor Hyper-V in de eerste RAM-schijf (initrd of initramfs) tenzij Linux detecteert dat het draait in een Hyper-V-omgeving.

Wanneer u een ander virtualisatiesysteem gebruikt (dat wil zeggen VirtualBox, Xen, enz.) om uw Linux-afbeelding voor te bereiden, moet u mogelijk initrd opnieuw opbouwen om ervoor te zorgen dat ten minste de hv_vmbus- en hv_storvsc-kernelmodules beschikbaar zijn op de oorspronkelijke RAM-schijf. Dit is een bekend probleem op zijn minst op systemen die zijn gebaseerd op de upstream Red Hat distributie.

Als u dit probleem wilt oplossen, voegt u Hyper-V-modules toe aan initramfs en bouwt u het opnieuw op:

Bewerk `/etc/dracut.conf`en voeg de volgende inhoud toe:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Reconstructie van initramfs:

        # dracut -f -v

Zie voor meer informatie de informatie over [de wederopbouw van initramfs.](https://access.redhat.com/solutions/1958)

## <a name="next-steps"></a>Volgende stappen
* U bent nu klaar om uw Virtual Hard Disk van Red Hat Enterprise Linux te gebruiken om nieuwe virtuele machines in Azure te maken. Zie [Een Linux VM maken vanaf een aangepaste schijf](upload-vhd.md#option-1-upload-a-vhd)als dit de eerste keer is dat u het vhd-bestand uploadt naar Azure.
* Voor meer informatie over de hypervisors die zijn gecertificeerd om Red Hat Enterprise Linux draaien, zie [de Red Hat website](https://access.redhat.com/certified-hypervisors).
* Ga voor meer informatie over het gebruik van RHEL BYOS-afbeeldingen die klaar zijn voor productie, naar de documentatiepagina voor [BYOS.](../workloads/redhat/byos.md)
