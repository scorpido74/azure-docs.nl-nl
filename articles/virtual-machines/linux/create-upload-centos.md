---
title: Een CentOS-gebaseerde Linux VHD maken en uploaden
description: Leer een Virtual Hard Disk (Azure Virtual Hard Disk) maken en uploaden die een Op CentOS gebaseerd Linux-besturingssysteem bevat.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/25/2019
ms.author: guybo
ms.openlocfilehash: 8899249fd284f69fa26bab8cd70aaf6a67fbb83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066789"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Een op CentOS gebaseerde virtuele Azure-machine voorbereiden

Leer een Virtual Hard Disk (Azure Virtual Hard Disk) maken en uploaden die een Op CentOS gebaseerd Linux-besturingssysteem bevat.

* [Een CentOS 6.x-virtuele machine voorbereiden op Azure](#centos-6x)
* [Een CentOS 7.0+ virtuele machine voorbereiden voor Azure](#centos-70)


## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al een CentOS (of vergelijkbaar afgeleid) Linux-besturingssysteem op een virtuele harde schijf hebt geïnstalleerd. Er bestaan meerdere tools om .vhd-bestanden te maken, bijvoorbeeld een virtualisatieoplossing zoals Hyper-V. Zie De [functie Hyper-V installeren en een virtuele machine configureren](https://technet.microsoft.com/library/hh846766.aspx)voor instructies.

**CentOS-installatienotities**

* Zie ook [Algemene Linux Installatienotities](create-upload-generic.md#general-linux-installation-notes) voor meer tips over het voorbereiden van Linux voor Azure.
* De VHDX-indeling wordt niet ondersteund in Azure, alleen **vaste VHD**.  U de schijf converteren naar VHD-formaat met Hyper-V Manager of de convert-vhd-cmdlet. Als u VirtualBox gebruikt, betekent dit dat u **de vaste grootte** moet selecteren in tegenstelling tot de standaardinstelling die dynamisch wordt toegewezen bij het maken van de schijf.
* Bij het installeren van het Linux-systeem is het *raadzaam* om standaard partities te gebruiken in plaats van LVM (vaak de standaard voor veel installaties). Dit voorkomt dat LVM-naam in strijd is met gekloonde VM's, vooral als een OS-schijf ooit moet worden gekoppeld aan een andere identieke VM voor het oplossen van problemen. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan worden gebruikt op gegevensschijven.
* Kernel ondersteuning voor het monteren van UDF-bestandssystemen is vereist. Bij het eerste opstarten op Azure wordt de inrichtingsconfiguratie doorgegeven aan de Linux VM via udf-geformatteerde media die aan de gast is gekoppeld. De Azure Linux-agent moet het UDF-bestandssysteem kunnen monteren om de configuratie en inrichting van de VM te lezen.
* Linux kernel versies onder 2.6.37 ondersteunen geen NUMA op Hyper-V met grotere VM-formaten. Dit probleem heeft voornamelijk gevolgen voor oudere distributies met behulp van de upstream Red Hat 2.6.32 kernel en is opgelost in RHEL 6.6 (kernel-2.6.32-504). Systemen met aangepaste kernels ouder dan 2.6.37 of RHEL-gebaseerde kernels ouder dan 2.6.32-504 moeten de opstartparameter `numa=off` instellen op de kernel command-line in grub.conf. Voor meer informatie zie Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Configureer geen swappartitie op de OS-schijf. De Linux-agent kan worden geconfigureerd om een swapbestand op de tijdelijke resourceschijf te maken.  Meer informatie hierover vindt u in de onderstaande stappen.
* Alle VHD's op Azure moeten een virtuele grootte hebben die is uitgelijnd op 1 MB. Bij het converteren van een ruwe schijf naar VHD moet u ervoor zorgen dat de ruwe schijfgrootte een veelvoud van 1 MB is voor conversie. Zie [Opmerkingen voor Linux-installatie](create-upload-generic.md#general-linux-installation-notes) voor meer informatie.

## <a name="centos-6x"></a>CentOS 6.x

1. Selecteer in Hyper-V Manager de virtuele machine.

2. Klik **op Verbinding maken** om een consolevenster voor de virtuele machine te openen.

3. In CentOS 6 kan NetworkManager de Azure Linux-agent verstoren. Verwijder dit pakket door de volgende opdracht uit te voeren:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Maak of bewerk `/etc/sysconfig/network` het bestand en voeg de volgende tekst toe:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Maak of bewerk `/etc/sysconfig/network-scripts/ifcfg-eth0` het bestand en voeg de volgende tekst toe:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Udev-regels wijzigen om te voorkomen dat statische regels voor de Ethernet-interface(s) worden genereren. Deze regels kunnen problemen veroorzaken bij het klonen van een virtuele machine in Microsoft Azure of Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Zorg ervoor dat de netwerkservice bij het opstarten wordt gestart door de volgende opdracht uit te voeren:

    ```bash
    sudo chkconfig network on
    ```

8. Als u de OpenLogic-spiegels wilt gebruiken die worden gehost `/etc/yum.repos.d/CentOS-Base.repo` in de Azure-datacenters, vervangt u het bestand door de volgende opslagplaatsen.  Dit voegt ook de **[openlogic]** repository toe die aanvullende pakketten bevat, zoals de Azure Linux-agent:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0

   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #contrib - packages by Centos Users
   [contrib]
   name=CentOS-$releasever - Contrib
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

    > [!Note]
    > De rest van deze handleiding gaat ervan `[openlogic]` uit dat u ten minste de repo gebruikt, die zal worden gebruikt om de Azure Linux-agent hieronder te installeren.

9. Voeg de volgende regel toe aan /etc/yum.conf:

    ```console
    http_caching=packages
    ```

10. Voer de volgende opdracht uit om de huidige yum-metagegevens te wissen en het systeem bij te werken met de nieuwste pakketten:

    ```bash
    yum clean all
    ```

    Tenzij u een afbeelding maakt voor een oudere versie van CentOS, is het raadzaam om alle pakketten naar het laatste nieuws bij te werken:

    ```bash
    sudo yum -y update
    ```

    Een reboot kan nodig zijn na het uitvoeren van deze opdracht.

11. (Optioneel) Installeer de stuurprogramma's voor de Linux Integration Services (LIS).

    > [!IMPORTANT]
    > De stap is **vereist** voor CentOS 6.3 en eerder, en optioneel voor latere releases.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    U ook de handmatige installatie-instructies volgen op de [lis-downloadpagina](https://www.microsoft.com/download/details.aspx?id=51612) om de rpm op uw VM te installeren.

12. Installeer de Azure Linux Agent en afhankelijkheden. Start en schakel waagent service in:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    Het WALinuxAgent-pakket verwijdert de Pakketten NetworkManager en NetworkManager-gnome als ze nog niet zijn verwijderd zoals beschreven in stap 3.

13. Wijzig de kernelbootlijn in uw grubconfiguratie om extra kernelparameters voor Azure op te nemen. Open hiervoor in `/boot/grub/menu.lst` een teksteditor en zorg ervoor dat de standaardkernel de volgende parameters bevat:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Dit zorgt er ook voor dat alle consoleberichten naar de eerste seriële poort worden verzonden, die Azure-ondersteuning kan helpen bij foutopsporingsproblemen.

    Naast het bovenstaande wordt aanbevolen om de volgende parameters te *verwijderen:*

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafische en stille boot zijn niet nuttig in een cloud-omgeving waar we willen dat alle logs worden verzonden naar de seriële poort.  De `crashkernel` optie kan indien gewenst geconfigureerd worden gelaten, maar houd er rekening mee dat deze parameter de hoeveelheid beschikbaar geheugen in de VM met 128 MB of meer zal verminderen, wat problematisch kan zijn voor de kleinere VM-formaten.

    > [!Important]
    > CentOS 6.5 en eerder moeten ook `numa=off`de kernelparameter instellen. Zie Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Controleer of de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten.  Dit is meestal de standaard.

15. Maak geen wisselruimte op de OS-schijf.

    De Azure Linux Agent kan automatisch swapruimte configureren met behulp van de lokale resourceschijf die aan de VM is gekoppeld na inrichten op Azure. Houd er rekening mee dat de lokale resourceschijf een *tijdelijke* schijf is en mogelijk wordt geleegd wanneer de virtuele machine is gedeprovisioneerd. Nadat u de Azure Linux Agent hebt geïnstalleerd (zie vorige stap), wijzigt u de volgende parameters `/etc/waagent.conf` op de juiste manier:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Voer de volgende opdrachten uit om de virtuele machine te deprovisioneren en voor te bereiden op het inrichten op Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

17. Klik **op Action -> Afsluiten** in Hyper-V Manager. Uw Linux VHD is nu klaar om te worden geüpload naar Azure.



## <a name="centos-70"></a>CentOS 7.0+

**Wijzigingen in CentOS 7 (en vergelijkbare derivaten)**

Het voorbereiden van een CentOS 7 virtuele machine voor Azure is zeer vergelijkbaar met CentOS 6, maar er zijn een aantal belangrijke verschillen vermeldenswaard:

* Het NetworkManager-pakket is niet langer in conflict met de Azure Linux-agent. Dit pakket is standaard geïnstalleerd en we raden aan om het niet te verwijderen.
* GRUB2 wordt nu gebruikt als de standaard bootloader, dus de procedure voor het bewerken van kernelparameters is gewijzigd (zie hieronder).
* XFS is nu het standaardbestandssysteem. Het ext4-bestandssysteem kan desgewenst nog steeds worden gebruikt.

**Configuratiestappen**

1. Selecteer in Hyper-V Manager de virtuele machine.

2. Klik **op Verbinding maken** om een consolevenster voor de virtuele machine te openen.

3. Maak of bewerk `/etc/sysconfig/network` het bestand en voeg de volgende tekst toe:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Maak of bewerk `/etc/sysconfig/network-scripts/ifcfg-eth0` het bestand en voeg de volgende tekst toe:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. Udev-regels wijzigen om te voorkomen dat statische regels voor de Ethernet-interface(s) worden genereren. Deze regels kunnen problemen veroorzaken bij het klonen van een virtuele machine in Microsoft Azure of Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Als u de OpenLogic-spiegels wilt gebruiken die worden gehost `/etc/yum.repos.d/CentOS-Base.repo` in de Azure-datacenters, vervangt u het bestand door de volgende opslagplaatsen.  Dit voegt ook de **[openlogic]** repository toe die pakketten bevat voor de Azure Linux-agent:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0
    
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   ```
    
   > [!Note]
   > De rest van deze handleiding gaat ervan `[openlogic]` uit dat u ten minste de repo gebruikt, die zal worden gebruikt om de Azure Linux-agent hieronder te installeren.

7. Voer de volgende opdracht uit om de huidige yum-metagegevens te wissen en eventuele updates te installeren:

    ```bash
    sudo yum clean all
    ```

    Tenzij u een afbeelding maakt voor een oudere versie van CentOS, is het raadzaam om alle pakketten naar het laatste nieuws bij te werken:

    ```bash
    sudo yum -y update
    ```

    Een reboot misschien nodig na het uitvoeren van deze opdracht.

8. Wijzig de kernelbootlijn in uw grubconfiguratie om extra kernelparameters voor Azure op te nemen. Open hiervoor in `/etc/default/grub` een teksteditor en `GRUB_CMDLINE_LINUX` bewerk de parameter bijvoorbeeld:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Dit zorgt er ook voor dat alle consoleberichten naar de eerste seriële poort worden verzonden, die Azure-ondersteuning kan helpen bij foutopsporingsproblemen. Het schakelt ook de nieuwe CentOS 7 naamgevingconventies voor NIC's uit. Naast het bovenstaande wordt aanbevolen om de volgende parameters te *verwijderen:*

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafische en stille boot zijn niet nuttig in een cloud-omgeving waar we willen dat alle logs worden verzonden naar de seriële poort. De `crashkernel` optie kan indien gewenst geconfigureerd worden gelaten, maar houd er rekening mee dat deze parameter de hoeveelheid beschikbaar geheugen in de VM met 128 MB of meer zal verminderen, wat problematisch kan zijn voor de kleinere VM-formaten.

9. Zodra u klaar bent `/etc/default/grub` met het bewerken per hierboven, voert u de volgende opdracht uit om de grubconfiguratie opnieuw op te bouwen:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Als u de afbeelding van **VMware, VirtualBox of KVM bouwt:** zorg ervoor dat de Hyper-V-stuurprogramma's zijn opgenomen in de initramfs:

    Inhoud `/etc/dracut.conf`bewerken, inhoud toevoegen:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Herbouw de initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Installeer de Azure Linux Agent en afhankelijkheden:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Maak geen wisselruimte op de OS-schijf.

    De Azure Linux Agent kan automatisch swapruimte configureren met behulp van de lokale resourceschijf die aan de VM is gekoppeld na inrichten op Azure. Houd er rekening mee dat de lokale resourceschijf een *tijdelijke* schijf is en mogelijk wordt geleegd wanneer de virtuele machine is gedeprovisioneerd. Nadat u de Azure Linux Agent hebt geïnstalleerd (zie vorige stap), wijzigt u de volgende parameters `/etc/waagent.conf` op de juiste manier:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Voer de volgende opdrachten uit om de virtuele machine te deprovisioneren en voor te bereiden op het inrichten op Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

14. Klik **op Action -> Afsluiten** in Hyper-V Manager. Uw Linux VHD is nu klaar om te worden geüpload naar Azure.

## <a name="next-steps"></a>Volgende stappen

U bent nu klaar om uw Virtuele Harde Schijf centos Linux te gebruiken om nieuwe virtuele machines in Azure te maken. Zie [Een Linux VM maken vanaf een aangepaste schijf](upload-vhd.md#option-1-upload-a-vhd)als dit de eerste keer is dat u het vhd-bestand uploadt naar Azure.
