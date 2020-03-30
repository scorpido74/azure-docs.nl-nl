---
title: Een Debian Linux VHD voorbereiden
description: Meer informatie over het maken van Debian VHD-afbeeldingen voor VM-implementaties in Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/13/2018
ms.author: guybo
ms.openlocfilehash: d54f7a11d929c31fee29a788eb3a2ae2cc8f2703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066717"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Een Debian VHD voorbereiden voor Azure
## <a name="prerequisites"></a>Vereisten
In deze sectie wordt ervan uitgegaan dat u al een Debian Linux-besturingssysteem hebt geïnstalleerd van een .iso-bestand dat is gedownload van de [Debian-website](https://www.debian.org/distrib/) naar een virtuele harde schijf. Er bestaan meerdere hulpprogramma's om .vhd-bestanden te maken; Hyper-V is slechts één voorbeeld. Zie [De functie Hyper-V installeren en een virtuele machine configureren](https://technet.microsoft.com/library/hh846766.aspx)voor instructies met Hyper-V.

## <a name="installation-notes"></a>Installatienotities
* Zie ook [Algemene Linux Installatienotities](create-upload-generic.md#general-linux-installation-notes) voor meer tips over het voorbereiden van Linux voor Azure.
* De nieuwere VHDX-indeling wordt niet ondersteund in Azure. U de schijf converteren naar VHD-formaat met Hyper-V Manager of de **convert-vhd-cmdlet.**
* Bij het installeren van het Linux-systeem wordt aanbevolen dat u standaardpartities gebruikt in plaats van LVM (vaak de standaardinstelling voor veel installaties). Dit voorkomt dat LVM-naam in strijd is met gekloonde VM's, vooral als een OS-schijf ooit aan een andere VM moet worden gekoppeld voor het oplossen van problemen. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan indien gewenst worden gebruikt op gegevensschijven.
* Configureer geen swappartitie op de OS-schijf. De Azure Linux-agent kan worden geconfigureerd om een swapbestand te maken op de tijdelijke resourceschijf. Meer informatie is te vinden in de onderstaande stappen.
* Alle VHD's op Azure moeten een virtuele grootte hebben die is uitgelijnd op 1 MB. Wanneer u van een ruwe schijf naar VHD converteert, moet u ervoor zorgen dat de ruwe schijfgrootte een veelvoud van 1 MB is vóór conversie. Zie [Opmerkingen voor Linux-installatie voor](create-upload-generic.md#general-linux-installation-notes)meer informatie.

## <a name="use-azure-manage-to-create-debian-vhds"></a>Azure-Manage gebruiken om Debian VHD's te maken
Er zijn hulpprogramma's beschikbaar voor het genereren van Debian VHD's voor Azure, zoals de [azure-manage scripts](https://github.com/credativ/azure-manage) van [Credativ.](https://www.credativ.com/) Dit is de aanbevolen aanpak versus het maken van een afbeelding vanaf nul. Als u bijvoorbeeld een Debian 8 VHD wilt maken, voert u de volgende opdrachten uit om het hulpprogramma (en afhankelijkheden) `azure-manage` te downloaden en het `azure_build_image` script uit te voeren:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Een Debian VHD handmatig voorbereiden
1. Selecteer in Hyper-V Manager de virtuele machine.
2. Klik **op Verbinding maken** om een consolevenster voor de virtuele machine te openen.
3. Als u het besturingssysteem hebt geïnstalleerd met behulp van`deb cdrom`een `/etc/apt/source.list`ISO, geeft u commentaar op elke regel met betrekking tot " " in .

4. Bewerk `/etc/default/grub` het bestand en wijzig de **parameter GRUB_CMDLINE_LINUX** als volgt om extra kernelparameters voor Azure op te nemen.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Herbouw de rups en ren uit:

        # sudo update-grub

6. Voeg Debian's Azure-repositories toe aan /etc/apt/sources.list voor Debian 8 of 9:

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x "Stretch"**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. Installeer de Azure Linux Agent:
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. Voor Debian 9+ is het raadzaam om de nieuwe Debian Cloud-kernel te gebruiken voor gebruik met VM's in Azure. Om deze nieuwe kernel te installeren, maak je eerst een bestand genaamd /etc/apt/preferences.d/linux.pref met de volgende inhoud:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Voer vervolgens "sudo apt-get install linux-image-cloud-amd64" uit om de nieuwe Debian Cloud kernel te installeren.

9. Deprovisioneer de virtuele machine en bereid deze voor op het inrichten op Azure en uitvoeren:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Klik **op Action** -> Afsluiten in Hyper-V Manager. Uw Linux VHD is nu klaar om te worden geüpload naar Azure.

## <a name="next-steps"></a>Volgende stappen
U bent nu klaar om uw Virtuele Harde Schijf van Debian te gebruiken om nieuwe virtuele machines in Azure te maken. Zie [Een Linux VM maken vanaf een aangepaste schijf](upload-vhd.md#option-1-upload-a-vhd)als dit de eerste keer is dat u het vhd-bestand uploadt naar Azure.

