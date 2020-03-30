---
title: Een Oracle Linux VHD maken en uploaden
description: Leer een Virtual Hard Disk (Azure Virtual Hard Disk) maken en uploaden die een Oracle Linux-besturingssysteem bevat.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/10/2019
ms.author: guybo
ms.openlocfilehash: 784d6c01125a9fd6ec291f32e989e4b22e7607af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066581"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Een oracle Linux virtuele machine voorbereiden voor Azure

In dit artikel wordt ervan uitgegaan dat u al een Oracle Linux-besturingssysteem op een virtuele harde schijf hebt geïnstalleerd. Er bestaan meerdere tools om .vhd-bestanden te maken, bijvoorbeeld een virtualisatieoplossing zoals Hyper-V. Zie De [functie Hyper-V installeren en een virtuele machine configureren](https://technet.microsoft.com/library/hh846766.aspx)voor instructies.

## <a name="oracle-linux-installation-notes"></a>Oracle Linux installatienotities
* Zie ook [Algemene Linux Installatienotities](create-upload-generic.md#general-linux-installation-notes) voor meer tips over het voorbereiden van Linux voor Azure.
* Hyper-V en Azure ondersteunen Oracle Linux met de Unbreakable Enterprise Kernel (UEK) of de Red Hat Compatible Kernel.
* Oracle's UEK2 wordt niet ondersteund op Hyper-V en Azure omdat het niet de vereiste stuurprogramma's bevat.
* De VHDX-indeling wordt niet ondersteund in Azure, alleen **vaste VHD**.  U de schijf converteren naar VHD-formaat met Hyper-V Manager of de convert-vhd-cmdlet.
* Bij het installeren van het Linux-systeem is het raadzaam om standaard partities te gebruiken in plaats van LVM (vaak de standaard voor veel installaties). Dit voorkomt dat LVM-naam in strijd is met gekloonde VM's, vooral als een OS-schijf ooit aan een andere VM moet worden gekoppeld voor het oplossen van problemen. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan indien gewenst worden gebruikt op gegevensschijven.
* Linux kernel versies eerder dan 2.6.37 ondersteunen geen NUMA op Hyper-V met grotere VM-formaten. Dit probleem heeft voornamelijk gevolgen voor oudere distributies met behulp van de upstream Red Hat 2.6.32-kernel en is opgelost in Oracle Linux 6.6 en hoger
* Configureer geen swappartitie op de OS-schijf. De Linux-agent kan worden geconfigureerd om een swapbestand op de tijdelijke resourceschijf te maken.  Meer informatie hierover vindt u in de onderstaande stappen.
* Alle VHD's op Azure moeten een virtuele grootte hebben die is uitgelijnd op 1 MB. Bij het converteren van een ruwe schijf naar VHD moet u ervoor zorgen dat de ruwe schijfgrootte een veelvoud van 1 MB is voor conversie. Zie [Opmerkingen voor Linux-installatie](create-upload-generic.md#general-linux-installation-notes) voor meer informatie.
* Zorg ervoor `Addons` dat de repository is ingeschakeld. Bewerk het `/etc/yum.repos.d/public-yum-ol6.repo`bestand (Oracle `/etc/yum.repos.d/public-yum-ol7.repo`Linux 6) of (Oracle `enabled=0` `enabled=1` Linux 7) en wijzig de regel naar **onder [ol6_addons]** of **[ol7_addons]** in dit bestand.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6.4 en hoger
U moet specifieke configuratiestappen uitvoeren in het besturingssysteem om de virtuele machine in Azure uit te voeren.

1. Selecteer in het middelste deelvenster van Hyper-V Manager de virtuele machine.
2. Klik **op Verbinding maken** om het venster voor de virtuele machine te openen.
3. Verwijder NetworkManager door de volgende opdracht uit te voeren:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Let op:** Als het pakket nog niet is geïnstalleerd, mislukt deze opdracht met een foutbericht. Dit is normaal gedrag.
4. Maak een **netwerk** met `/etc/sysconfig/` de naam netwerk met de naam netwerk in de map dat de volgende tekst bevat:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Maak een bestand met de naam **ifcfg-eth0** in de `/etc/sysconfig/network-scripts/` map met de volgende tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Udev-regels wijzigen om te voorkomen dat statische regels voor de Ethernet-interface(s) worden genereren. Deze regels kunnen problemen veroorzaken bij het klonen van een virtuele machine in Microsoft Azure of Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Zorg ervoor dat de netwerkservice bij het opstarten wordt gestart door de volgende opdracht uit te voeren:
   
        # chkconfig network on
8. Installeer python-pyasn1 door de volgende opdracht uit te voeren:
   
        # sudo yum install python-pyasn1
9. Wijzig de kernelbootlijn in uw grubconfiguratie om extra kernelparameters voor Azure op te nemen. Om dit te doen open "/boot/grub/menu.lst" in een teksteditor en zorg ervoor dat de kernel de volgende parameters bevat:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Dit zorgt ervoor dat alle consoleberichten naar de eerste seriële poort worden verzonden, die Azure-ondersteuning kan helpen bij foutopsporingsproblemen.
   
   Naast het bovenstaande wordt aanbevolen om de volgende parameters te *verwijderen:*
   
        rhgb quiet crashkernel=auto
   
   Grafische en stille boot zijn niet nuttig in een cloud-omgeving waar we willen dat alle logs worden verzonden naar de seriële poort.
   
   De `crashkernel` optie kan indien gewenst geconfigureerd worden gelaten, maar houd er rekening mee dat deze parameter de hoeveelheid beschikbaar geheugen in de VM met 128 MB of meer zal verminderen, wat problematisch kan zijn voor de kleinere VM-formaten.
10. Controleer of de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten.  Dit is meestal de standaard.
11. Installeer de Azure Linux Agent door de volgende opdracht uit te voeren. De nieuwste versie is 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Houd er rekening mee dat het installeren van het WALinuxAgent-pakket de pakketten NetworkManager en NetworkManager-gnome verwijdert als ze nog niet zijn verwijderd zoals beschreven in stap 2.
12. Maak geen wisselruimte op de OS-schijf.
    
    De Azure Linux Agent kan automatisch swapruimte configureren met behulp van de lokale resourceschijf die aan de VM is gekoppeld na inrichten op Azure. Houd er rekening mee dat de lokale resourceschijf een *tijdelijke* schijf is en mogelijk wordt geleegd wanneer de virtuele machine is gedeprovisioneerd. Nadat u de Azure Linux Agent hebt geïnstalleerd (zie vorige stap), wijzigt u de volgende parameters in /etc/waagent.conf op de juiste manier:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Voer de volgende opdrachten uit om de virtuele machine te deprovisioneren en voor te bereiden op het inrichten op Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Klik **op Action -> Afsluiten** in Hyper-V Manager. Uw Linux VHD is nu klaar om te worden geüpload naar Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7.0 en hoger
**Veranderingen in Oracle Linux 7**

Het voorbereiden van een Oracle Linux 7 virtuele machine voor Azure is zeer vergelijkbaar met Oracle Linux 6, maar er zijn een aantal belangrijke verschillen vermeldenswaard:

* Azure ondersteunt Oracle Linux met de Unbreakable Enterprise Kernel (UEK) of de Red Hat Compatible Kernel. Oracle Linux met UEK wordt aanbevolen.
* Het NetworkManager-pakket is niet langer in conflict met de Azure Linux-agent. Dit pakket is standaard geïnstalleerd en we raden aan om het niet te verwijderen.
* GRUB2 wordt nu gebruikt als de standaard bootloader, dus de procedure voor het bewerken van kernelparameters is gewijzigd (zie hieronder).
* XFS is nu het standaardbestandssysteem. Het ext4-bestandssysteem kan desgewenst nog steeds worden gebruikt.

**Configuratiestappen**

1. Selecteer in Hyper-V Manager de virtuele machine.
2. Klik **op Verbinding maken** om een consolevenster voor de virtuele machine te openen.
3. Maak een **netwerk** met `/etc/sysconfig/` de naam netwerk met de naam netwerk in de map dat de volgende tekst bevat:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Maak een bestand met de naam **ifcfg-eth0** in de `/etc/sysconfig/network-scripts/` map met de volgende tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Udev-regels wijzigen om te voorkomen dat statische regels voor de Ethernet-interface(s) worden genereren. Deze regels kunnen problemen veroorzaken bij het klonen van een virtuele machine in Microsoft Azure of Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Zorg ervoor dat de netwerkservice bij het opstarten wordt gestart door de volgende opdracht uit te voeren:
   
        # sudo chkconfig network on
7. Installeer het python-pyasn1-pakket door de volgende opdracht uit te voeren:
   
        # sudo yum install python-pyasn1
8. Voer de volgende opdracht uit om de huidige yum-metagegevens te wissen en eventuele updates te installeren:
   
        # sudo yum clean all
        # sudo yum -y update
9. Wijzig de kernelbootlijn in uw grubconfiguratie om extra kernelparameters voor Azure op te nemen. Om dit te doen open "/etc/default/grub" in `GRUB_CMDLINE_LINUX` een teksteditor en de parameter te bewerken, bijvoorbeeld:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Dit zorgt er ook voor dat alle consoleberichten naar de eerste seriële poort worden verzonden, die Azure-ondersteuning kan helpen bij foutopsporingsproblemen. Het schakelt ook de naamgevingsconventies voor NIC's in Oracle Linux 7 uit met de Unbreakable Enterprise Kernel. Naast het bovenstaande wordt aanbevolen om de volgende parameters te *verwijderen:*
   
       rhgb quiet crashkernel=auto
   
   Grafische en stille boot zijn niet nuttig in een cloud-omgeving waar we willen dat alle logs worden verzonden naar de seriële poort.
   
   De `crashkernel` optie kan indien gewenst geconfigureerd worden gelaten, maar houd er rekening mee dat deze parameter de hoeveelheid beschikbaar geheugen in de VM met 128 MB of meer zal verminderen, wat problematisch kan zijn voor de kleinere VM-formaten.
10. Zodra u klaar bent met het bewerken van "/etc/default/grub" per hierboven, voert u de volgende opdracht uit om de grubconfiguratie opnieuw op te bouwen:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Controleer of de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten.  Dit is meestal de standaard.
12. Installeer de Azure Linux Agent door de volgende opdracht uit te voeren:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Maak geen wisselruimte op de OS-schijf.
    
    De Azure Linux Agent kan automatisch swapruimte configureren met behulp van de lokale resourceschijf die aan de VM is gekoppeld na inrichten op Azure. Houd er rekening mee dat de lokale resourceschijf een *tijdelijke* schijf is en mogelijk wordt geleegd wanneer de virtuele machine is gedeprovisioneerd. Nadat u de Azure Linux Agent hebt geïnstalleerd (zie de vorige stap), wijzigt u de volgende parameters in /etc/waagent.conf op de juiste manier:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Voer de volgende opdrachten uit om de virtuele machine te deprovisioneren en voor te bereiden op het inrichten op Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Klik **op Action -> Afsluiten** in Hyper-V Manager. Uw Linux VHD is nu klaar om te worden geüpload naar Azure.

## <a name="next-steps"></a>Volgende stappen
U bent nu klaar om uw Oracle Linux .vhd te gebruiken om nieuwe virtuele machines in Azure te maken. Zie [Een Linux VM maken vanaf een aangepaste schijf](upload-vhd.md#option-1-upload-a-vhd)als dit de eerste keer is dat u het vhd-bestand uploadt naar Azure.

