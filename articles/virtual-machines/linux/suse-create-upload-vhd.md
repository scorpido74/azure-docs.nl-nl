---
title: Een SUSE Linux VHD maken en uploaden in Azure
description: Leer een Virtual Hard Disk (Azure Virtual Hard Disk) maken en uploaden die een SUSE Linux-besturingssysteem bevat.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: guybo
ms.openlocfilehash: fe057cb4e831fd95669ad5d5f93332a3952f16ef
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460933"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Een op SLES of openSUSE gebaseerde virtuele machine voor Azure voorbereiden


In dit artikel wordt ervan uitgegaan dat u al een SUSE- of openSUSE Linux-besturingssysteem op een virtuele harde schijf hebt geïnstalleerd. Er bestaan meerdere tools om .vhd-bestanden te maken, bijvoorbeeld een virtualisatieoplossing zoals Hyper-V. Zie De [functie Hyper-V installeren en een virtuele machine configureren](https://technet.microsoft.com/library/hh846766.aspx)voor instructies.

## <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE installatienotities
* Zie ook [Algemene Linux Installatienotities](create-upload-generic.md#general-linux-installation-notes) voor meer tips over het voorbereiden van Linux voor Azure.
* De VHDX-indeling wordt niet ondersteund in Azure, alleen **vaste VHD**.  U de schijf converteren naar VHD-formaat met Hyper-V Manager of de convert-vhd-cmdlet.
* Bij het installeren van het Linux-systeem is het raadzaam om standaard partities te gebruiken in plaats van LVM (vaak de standaard voor veel installaties). Dit voorkomt dat LVM-naam in strijd is met gekloonde VM's, vooral als een OS-schijf ooit aan een andere VM moet worden gekoppeld voor het oplossen van problemen. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan indien gewenst worden gebruikt op gegevensschijven.
* Configureer geen swappartitie op de OS-schijf. De Linux-agent kan worden geconfigureerd om een swapbestand op de tijdelijke resourceschijf te maken.  Meer informatie hierover vindt u in de onderstaande stappen.
* Alle VHD's op Azure moeten een virtuele grootte hebben die is uitgelijnd op 1 MB. Bij het converteren van een ruwe schijf naar VHD moet u ervoor zorgen dat de ruwe schijfgrootte een veelvoud van 1 MB is voor conversie. Zie [Opmerkingen voor Linux-installatie](create-upload-generic.md#general-linux-installation-notes) voor meer informatie.

## <a name="use-suse-studio"></a>SUSE Studio gebruiken
[SUSE Studio](http://www.susestudio.com) kan eenvoudig uw SLES- en openSUSE-afbeeldingen voor Azure en Hyper-V maken en beheren. Dit is de aanbevolen aanpak voor het aanpassen van uw eigen SLES en openSUSE-afbeeldingen.

Als alternatief voor het bouwen van uw eigen VHD publiceert SUSE ook BYOS (Bring Your Own Subscription) afbeeldingen voor SLES op [VMDepot.](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf)

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>SUSE Linux Enterprise Server 11 SP4 voorbereiden
1. Selecteer in het middelste deelvenster van Hyper-V Manager de virtuele machine.
2. Klik **op Verbinding maken** om het venster voor de virtuele machine te openen.
3. Registreer uw SUSE Linux Enterprise-systeem zodat het updates kan downloaden en pakketten kan installeren.
4. Werk het systeem bij met de nieuwste patches:
   
        # sudo zypper update
5. Installeer de Azure Linux Agent vanuit de SLES-repository:
   
        # sudo zypper install python-azure-agent
6. Controleer of waagent is ingesteld op "aan" in chkconfig, en zo niet, inschakelen voor autostart:
   
        # sudo chkconfig waagent on
7. Controleer of de waagent-service wordt uitgevoerd en zo niet, start deze op: 
   
        # sudo service waagent start
8. Wijzig de kernelbootlijn in uw grubconfiguratie om extra kernelparameters voor Azure op te nemen. Open hiervoor "/boot/grub/menu.lst" in een teksteditor en zorg ervoor dat de standaardkernel de volgende parameters bevat:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Dit zorgt ervoor dat alle consoleberichten naar de eerste seriële poort worden verzonden, die Azure-ondersteuning kan helpen bij foutopsporingsproblemen.
9. Bevestig dat /boot/grub/menu.lst en /etc/fstab beide verwijzen naar de schijf met behulp van de UUID (by-uuid) in plaats van de schijf-ID (by-id). 
   
    Schijf UUID opdoen
   
        # ls /dev/disk/by-uuid/
   
    Als /dev/disk/by-id/ wordt gebruikt, werkt u zowel /boot/grub/menu.lst als /etc/fstab bij met de juiste door-uuid waarde
   
    Voor wijziging
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Na wijziging
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Udev-regels wijzigen om te voorkomen dat statische regels voor de Ethernet-interface(s) worden genereren. Deze regels kunnen problemen veroorzaken bij het klonen van een virtuele machine in Microsoft Azure of Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Het wordt aanbevolen om het bestand "/etc/sysconfig/network/dhcp" `DHCLIENT_SET_HOSTNAME` te bewerken en de parameter als volgt te wijzigen:
    
     DHCLIENT_SET_HOSTNAME="nee"
12. Geef in "/etc/sudoers" commentaar op of verwijder de volgende regels als ze bestaan:
    
    ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```
13. Controleer of de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten.  Dit is meestal de standaard.
14. Maak geen wisselruimte op de OS-schijf.
    
    De Azure Linux Agent kan automatisch swapruimte configureren met behulp van de lokale resourceschijf die aan de VM is gekoppeld na inrichten op Azure. Houd er rekening mee dat de lokale resourceschijf een *tijdelijke* schijf is en mogelijk wordt geleegd wanneer de virtuele machine is gedeprovisioneerd. Nadat u de Azure Linux Agent hebt geïnstalleerd (zie vorige stap), wijzigt u de volgende parameters in /etc/waagent.conf op de juiste manier:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## OPMERKING: stel dit in op wat u maar wilt.
15. Voer de volgende opdrachten uit om de virtuele machine te deprovisioneren en voor te bereiden op het inrichten op Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Klik **op Action -> Afsluiten** in Hyper-V Manager. Uw Linux VHD is nu klaar om te worden geüpload naar Azure.

---
## <a name="prepare-opensuse-131"></a>OpenSUSE 13.1+ voorbereiden
1. Selecteer in het middelste deelvenster van Hyper-V Manager de virtuele machine.
2. Klik **op Verbinding maken** om het venster voor de virtuele machine te openen.
3. Voer op de schaal`zypper lr`het commando uit ' '. Als deze opdracht de uitvoer als het volgende retourneert, worden de repositories geconfigureerd als verwachte- er zijn geen aanpassingen nodig (merk op dat versienummers kunnen variëren):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Als de opdracht wordt geretourneerd als "Geen repositories gedefinieerd..." gebruik vervolgens de volgende opdrachten om deze repo's toe te voegen:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    U vervolgens controleren of de repositories`zypper lr`zijn toegevoegd door de opdracht ' opnieuw uit te voeren.You can then verify the repositories have been added by running the command ' again. Als een van de relevante updaterepositories niet is ingeschakeld, schakelt u deze in met de volgende opdracht:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Werk de kernel bij naar de laatst beschikbare versie:
   
        # sudo zypper up kernel-default
   
    Of om het systeem te updaten met de nieuwste patches:
   
        # sudo zypper update
5. Installeer de Azure Linux Agent.
   
        # sudo zypper install WALinuxAgent
6. Wijzig de kernelbootlijn in uw grubconfiguratie om extra kernelparameters voor Azure op te nemen. Open hiervoor "/boot/grub/menu.lst" in een teksteditor en zorg ervoor dat de standaardkernel de volgende parameters bevat:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Dit zorgt ervoor dat alle consoleberichten naar de eerste seriële poort worden verzonden, die Azure-ondersteuning kan helpen bij foutopsporingsproblemen. Verwijder bovendien de volgende parameters uit de opstartlijn van de kernel als ze bestaan:
   
     libata.atapi_enabled=0 reserve=0x1f0,0x8
7. Het wordt aanbevolen om het bestand "/etc/sysconfig/network/dhcp" `DHCLIENT_SET_HOSTNAME` te bewerken en de parameter als volgt te wijzigen:
   
     DHCLIENT_SET_HOSTNAME="nee"
8. **Belangrijk:** Geef in "/etc/sudoers" commentaar op of verwijder de volgende regels als ze bestaan:
     
     ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```

9. Controleer of de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten.  Dit is meestal de standaard.
10. Maak geen wisselruimte op de OS-schijf.
    
    De Azure Linux Agent kan automatisch swapruimte configureren met behulp van de lokale resourceschijf die aan de VM is gekoppeld na inrichten op Azure. Houd er rekening mee dat de lokale resourceschijf een *tijdelijke* schijf is en mogelijk wordt geleegd wanneer de virtuele machine is gedeprovisioneerd. Nadat u de Azure Linux Agent hebt geïnstalleerd (zie vorige stap), wijzigt u de volgende parameters in /etc/waagent.conf op de juiste manier:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## OPMERKING: stel dit in op wat u maar wilt.
11. Voer de volgende opdrachten uit om de virtuele machine te deprovisioneren en voor te bereiden op het inrichten op Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Controleer of de Azure Linux Agent wordt uitgevoerd bij het opstarten:
    
        # sudo systemctl enable waagent.service
13. Klik **op Action -> Afsluiten** in Hyper-V Manager. Uw Linux VHD is nu klaar om te worden geüpload naar Azure.

## <a name="next-steps"></a>Volgende stappen
U bent nu klaar om uw Virtuele Harde Schijf van SUSE Linux te gebruiken om nieuwe virtuele machines in Azure te maken. Zie [Een Linux VM maken vanaf een aangepaste schijf](upload-vhd.md#option-1-upload-a-vhd)als dit de eerste keer is dat u het vhd-bestand uploadt naar Azure.
