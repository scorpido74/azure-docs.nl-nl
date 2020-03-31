---
title: Een Ubuntu Linux VHD maken en uploaden in Azure
description: Leer een Virtual Hard Disk (HHD) van Azure maken en uploaden die een Ubuntu Linux-besturingssysteem bevat.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: guybo
ms.openlocfilehash: 5fa3415d8663f358bf0ae48be46ac52b8f8b4b06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066732"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Een virtuele Ubuntu-machine voor Azure voorbereiden


Ubuntu publiceert nu officiële Azure VHDs voor download op [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/). Als u uw eigen gespecialiseerde Ubuntu-afbeelding voor Azure moet bouwen, wordt het aanbevolen om te beginnen met deze bekende werkende VHD's en indien nodig aan te passen. De nieuwste afbeeldingsreleases zijn altijd te vinden op de volgende locaties:

* Ubuntu 12.04/Precise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64.vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)
* Ubuntu 18.10/Cosmic: [cosmic-server-cloudimg-amd64.vhd.zip](http://cloud-images.ubuntu.com/releases/cosmic/release/ubuntu-18.10-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u al een Ubuntu Linux-besturingssysteem op een virtuele harde schijf hebt geïnstalleerd. Er bestaan meerdere tools om .vhd-bestanden te maken, bijvoorbeeld een virtualisatieoplossing zoals Hyper-V. Zie De [functie Hyper-V installeren en een virtuele machine configureren](https://technet.microsoft.com/library/hh846766.aspx)voor instructies.

**Ubuntu installatienotities**

* Zie ook [Algemene Linux Installatienotities](create-upload-generic.md#general-linux-installation-notes) voor meer tips over het voorbereiden van Linux voor Azure.
* De VHDX-indeling wordt niet ondersteund in Azure, alleen **vaste VHD**.  U de schijf converteren naar VHD-formaat met Hyper-V Manager of de convert-vhd-cmdlet.
* Bij het installeren van het Linux-systeem is het raadzaam om standaard partities te gebruiken in plaats van LVM (vaak de standaard voor veel installaties). Dit voorkomt dat LVM-naam in strijd is met gekloonde VM's, vooral als een OS-schijf ooit aan een andere VM moet worden gekoppeld voor het oplossen van problemen. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan indien gewenst worden gebruikt op gegevensschijven.
* Configureer geen swappartitie op de OS-schijf. De Linux-agent kan worden geconfigureerd om een swapbestand op de tijdelijke resourceschijf te maken.  Meer informatie hierover vindt u in de onderstaande stappen.
* Alle VHD's op Azure moeten een virtuele grootte hebben die is uitgelijnd op 1 MB. Bij het converteren van een ruwe schijf naar VHD moet u ervoor zorgen dat de ruwe schijfgrootte een veelvoud van 1 MB is voor conversie. Zie [Opmerkingen voor Linux-installatie](create-upload-generic.md#general-linux-installation-notes) voor meer informatie.

## <a name="manual-steps"></a>Handmatige stappen
> [!NOTE]
> Voordat u probeert uw eigen aangepaste Ubuntu-afbeelding voor Azure te maken, u overwegen om de vooraf gebouwde en geteste afbeeldingen van plaats te [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) maken.
> 
> 

1. Selecteer in het middelste deelvenster van Hyper-V Manager de virtuele machine.

2. Klik **op Verbinding maken** om het venster voor de virtuele machine te openen.

3. Vervang de huidige opslagplaatsen in de afbeelding om de Azure-repository van Ubuntu te gebruiken. De stappen variëren enigszins afhankelijk van de Ubuntu-versie.
   
    Voordat u `/etc/apt/sources.list`deze bewerking bewerkt, wordt het aanbevolen om een back-up te maken:
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. De Ubuntu Azure-afbeeldingen volgen nu de HWE-kernel *(Hardware Enablement).* Werk het besturingssysteem bij naar de nieuwste kernel door de volgende opdrachten uit te voeren:

    Ubuntu 12.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    Ubuntu 18.04.04:
    
        # sudo apt-get update
        # sudo apt-get install --install-recommends linux-generic-hwe-18.04 xserver-xorg-hwe-18.04
        # sudo apt-get install --install-recommends linux-cloud-tools-generic-hwe-18.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    **Zie ook:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Wijzig de kernelbootlijn voor Grub om extra kernelparameters voor Azure op te nemen. Als u `/etc/default/grub` dit wilt openen in een `GRUB_CMDLINE_LINUX_DEFAULT` teksteditor, zoekt u de variabele aangeroepen (of voeg deze indien nodig toe) en bewerkt u deze om de volgende parameters op te nemen:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Sla dit bestand op en `sudo update-grub`sluit dit en voer het bestand uit en voer het vervolgens uit. Dit zorgt ervoor dat alle consoleberichten naar de eerste seriële poort worden verzonden, die Azure technische ondersteuning kan bieden bij foutopsporingsproblemen.

6. Controleer of de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten.  Dit is meestal de standaard.

7. Installeer de Azure Linux Agent:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  Het `walinuxagent` pakket kan `NetworkManager` `NetworkManager-gnome` de en pakketten verwijderen, als ze zijn geïnstalleerd.


1. Voer de volgende opdrachten uit om de virtuele machine te deprovisioneren en voor te bereiden op het inrichten op Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Klik **op Action -> Afsluiten** in Hyper-V Manager. Uw Linux VHD is nu klaar om te worden geüpload naar Azure.

## <a name="references"></a>Verwijzingen
[Ubuntu hardware enablement (HWE) kernel](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Volgende stappen
U bent nu klaar om uw Virtuele Ubuntu Linux-harde schijf te gebruiken om nieuwe virtuele machines in Azure te maken. Zie [Een Linux VM maken vanaf een aangepaste schijf](upload-vhd.md#option-1-upload-a-vhd)als dit de eerste keer is dat u het vhd-bestand uploadt naar Azure.

