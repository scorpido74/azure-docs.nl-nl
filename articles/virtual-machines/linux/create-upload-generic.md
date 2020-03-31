---
title: Een Linux VHD maken en uploaden
description: Leer een Virtual Hard Disk (Azure virtual hard disk) maken en uploaden die een Linux-besturingssysteem bevat.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: guybo
ms.openlocfilehash: f700dec6486bad9e7024d7c908a70dd0ff2b342c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066762"
---
# <a name="information-for-non-endorsed-distributions"></a>Informatie voor niet-goedgekeurde distributies

Het Azure-platform SLA is alleen van toepassing op virtuele machines waarop het Linux-besturingssysteem wordt uitgevoerd wanneer een van de [goedgekeurde distributies](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) wordt gebruikt. Voor deze goedgekeurde distributies worden vooraf geconfigureerde Linux-afbeeldingen geleverd in de Azure Marketplace.

* [Linux op Azure - Goedgekeurde distributies](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ondersteuning voor Linux-afbeeldingen in Microsoft Azure](https://support.microsoft.com/kb/2941892)

Alle distributies die op Azure worden uitgevoerd, hebben een aantal vereisten. Dit artikel kan niet volledig zijn, omdat elke distributie anders is. Zelfs als u voldoet aan alle onderstaande criteria, moet u mogelijk uw Linux-systeem aanzienlijk aanpassen om het goed te laten werken.

We raden u aan te beginnen met een van de [Linux op Azure Endorsed Distributions.](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) In de volgende artikelen ziet u hoe u de verschillende goedgekeurde Linux-distributies voorbereiden die op Azure worden ondersteund:

* **[CentOS-gebaseerde distributies](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Dit artikel richt zich op algemene richtlijnen voor het uitvoeren van uw Linux-distributie op Azure.

## <a name="general-linux-installation-notes"></a>Algemene Opmerkingen over Linux-installatie
* De VHDX-indeling (Hyper-V virtual hard disk) wordt niet ondersteund in Azure, alleen *vaste VHD.*  U de schijf converteren naar VHD-indeling met Hyper-V Manager of de [Cmdlet Convert-VHD.](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd) Als u VirtualBox gebruikt, selecteert u **Vaste grootte** in plaats van de standaard (dynamisch toegewezen) bij het maken van de schijf.
* Azure ondersteunt Gen1 (BIOS boot) & Gen2 (UEFI boot) Virtuele machines.
* De maximaal toegestane grootte voor de VHD is 1.023 GB.
* Bij het installeren van het Linux-systeem raden we u aan standaard partities te gebruiken, in plaats van Logical Volume Manager (LVM), wat de standaard is voor veel installaties. Met behulp van standaardpartities voorkom je dat LVM-naam conflicten heeft met gekloonde VM's, vooral als een OS-schijf ooit is gekoppeld aan een andere identieke VM voor het oplossen van problemen. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan worden gebruikt op gegevensschijven.
* Kernel ondersteuning voor het monteren van UDF-bestandssystemen is noodzakelijk. Bij het eerste opstarten op Azure wordt de inrichtingsconfiguratie doorgegeven aan de Linux-VM met behulp van udf-geformatteerde media die aan de gast zijn gekoppeld. De Azure Linux-agent moet het UDF-bestandssysteem monteren om de configuratie ervan te lezen en de VM in te richten.
* Linux kernel versies eerder dan 2.6.37 ondersteunen geen NUMA op Hyper-V met grotere VM-formaten. Dit probleem heeft voornamelijk gevolgen voor oudere distributies met behulp van de upstream Red Hat 2.6.32 kernel, en werd opgelost in Red Hat Enterprise Linux (RHEL) 6.6 (kernel-2.6.32-504). Systemen met aangepaste kernels ouder dan 2.6.37 of RHEL-gebaseerde kernels ouder dan 2.6.32-504 moeten de opstartparameter `numa=off` instellen op de kernel command line in grub.conf. Zie [Red Hat KB 436883](https://access.redhat.com/solutions/436883)voor meer informatie.
* Configureer geen swappartitie op de OS-schijf. De Linux-agent kan worden geconfigureerd om een swapbestand te maken op de tijdelijke resourceschijf, zoals beschreven in de volgende stappen.
* Alle VHD's op Azure moeten een virtuele grootte hebben die is uitgelijnd op 1 MB. Bij het converteren van een ruwe schijf naar VHD moet u ervoor zorgen dat de ruwe schijfgrootte een veelvoud van 1 MB is vóór conversie, zoals beschreven in de volgende stappen.

### <a name="installing-kernel-modules-without-hyper-v"></a>Kernelmodules installeren zonder Hyper-V
Azure draait op de Hyper-V hypervisor, dus Linux vereist bepaalde kernelmodules om te draaien in Azure. Als u een VM hebt die buiten Hyper-V is gemaakt, kunnen de Linux-installateurs de stuurprogramma's voor Hyper-V mogelijk niet opnemen in de eerste ramdisk (initrd of initramfs), tenzij de VM detecteert dat deze op een Hyper-V-omgeving wordt uitgevoerd. Wanneer u een ander virtualisatiesysteem gebruikt (zoals VirtualBox, KVM, enzovoort) om uw Linux-afbeelding voor te bereiden, moet u mogelijk de initrd opnieuw opbouwen, zodat ten minste de hv_vmbus en hv_storvsc kernelmodules beschikbaar zijn op de eerste ramdisk.  Dit bekende probleem is voor systemen op basis van de upstream Red Hat distributie, en eventueel anderen.

Het mechanisme voor de wederopbouw van de initrd of initramfs beeld kan variëren afhankelijk van de verdeling. Raadpleeg de documentatie of ondersteuning van uw distributie voor de juiste procedure.  Hier is een voorbeeld voor de wederopbouw `mkinitrd` van de initrd met behulp van het hulpprogramma:

1. Een back-up maken van de bestaande initrd-afbeelding:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Herbouw de initrd met de hv_vmbus en hv_storvsc kernelmodules:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Het formaat van VHD's wijzigen
VHD-afbeeldingen op Azure moeten een virtuele grootte hebben die is uitgelijnd op 1 MB.  VhD's die met Hyper-V zijn gemaakt, zijn doorgaans correct uitgelijnd.  Als de VHD niet correct is uitgelijnd, ontvangt u mogelijk een foutmelding die vergelijkbaar is met het volgende wanneer u een afbeelding van uw VHD probeert te maken.

* De VHD\//\<http: mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd heeft een niet-ondersteunde virtuele grootte van 21475270656 bytes. De grootte moet een heel getal zijn (in M's).

Wijzig in dit geval het formaat van de VM met de Hyper-V Manager-console of de Cmdlet [Resize-VHD](https://technet.microsoft.com/library/hh848535.aspx) PowerShell.  Als u niet in een Windows-omgeving `qemu-img` wordt uitgevoerd, raden we u aan de VHD te converteren (indien nodig) om te zetten en het formaat ervan te wijzigen.

> [!NOTE]
> Er is een [bekende bug in qemu-img](https://bugs.launchpad.net/qemu/+bug/1490611) versies >=2.2.1 dat resulteert in een onjuist geformatteerde VHD. Het probleem is opgelost in QEMU 2.6. We raden `qemu-img` u aan 2.2.0 of lager of 2,6 of hoger te gebruiken.
> 

1. Het formaat van de VHD `qemu-img` rechtstreeks `vbox-manage` wijzigen met behulp van tools zoals of kan resulteren in een niet opstartbare VHD.  We raden u aan de VHD eerst om te zetten naar een RAW-schijfafbeelding.  Als de VM-afbeelding is gemaakt als een RAW-schijfafbeelding (de standaardinstelling voor sommige hypervisors zoals KVM), u deze stap overslaan.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Bereken de vereiste grootte van de schijfafbeelding, zodat de virtuele grootte is uitgelijnd op 1 MB.  De volgende bash `qemu-img info` shell script gebruikt om de virtuele grootte van de schijf afbeelding te bepalen, en berekent vervolgens de grootte naar de volgende 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$(((($size+$MB-1)/$MB)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Wijzig het formaat `$rounded_size` van de ruwe schijf met de bovenstaande set.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Converteer nu de RAW-schijf terug naar een VHD met een vaste grootte.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Of, met qemu versie 2.6+, omvatten de `force_size` optie.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Linux Kernel Vereisten

De Linux Integration Services (LIS) drivers voor Hyper-V en Azure worden rechtstreeks bijgedragen aan de upstream Linux kernel. Veel distributies die een recente Linux-kernel versie (zoals 3.x) bevatten, hebben deze stuurprogramma's al beschikbaar, of bieden anderszins backported versies van deze stuurprogramma's met hun kernels.  Deze stuurprogramma's worden voortdurend bijgewerkt in de upstream-kernel met nieuwe oplossingen en functies, dus waar mogelijk raden we aan om een [goedgekeurde distributie](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) uit te voeren die deze oplossingen en updates bevat.

Als u een variant van Red Hat Enterprise Linux-versies 6.0 tot 6.3 uitvoert, moet u de [nieuwste LIS-stuurprogramma's voor Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)installeren. Te beginnen met RHEL 6.4 + (en derivaten) de LIS drivers zijn al opgenomen met de kernel en dus geen extra installatie pakketten nodig zijn.

Als een aangepaste kernel vereist is, raden we een recente kernelversie (zoals 3.8+) aan. Voor distributies of leveranciers die hun eigen kernel behouden, moet u de LIS-stuurprogramma's regelmatig backporten van de upstreamkernel naar uw aangepaste kernel.  Zelfs als u al een relatief recente kernelversie uitvoert, raden we u ten zeerste aan om eventuele upstream-oplossingen in de LIS-stuurprogramma's bij te houden en ze indien nodig te backporten. De locaties van de LIS-bronbestanden voor stuurprogramma's worden opgegeven in het [BESTAND MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) in de bronstructuur van de Linux-kernel:
```
    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/
```
De volgende patches moeten in de kernel worden opgenomen. Deze lijst kan niet volledig zijn voor alle distributies.

* [ata_piix: stel schijven standaard uit naar de Hyper-V-stuurprogramma's](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: rekening houden met in-transit pakketten in het RESET-pad](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: vermijd het gebruik van WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Write SAME uitschakelen voor RAID- en virtual hostadapterstuurprogramma's](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL pointer dereference fix storvsc: NULL pointer dereference fix storvsc: NULL pointer dereference fix sto](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: ring buffer storingen kunnen resulteren in I / O bevriezen](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: bescherm tegen dubbele uitvoering van __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>De Azure Linux Agent
De [Azure Linux Agent](../extensions/agent-linux.md) `waagent` voorziet in een Virtuele Linux-machine in Azure. U de nieuwste versie, bestandsproblemen of pull-aanvragen indienen bij de [Linux Agent GitHub repo.](https://github.com/Azure/WALinuxAgent)

* De Linux-agent wordt uitgebracht onder de Apache 2.0 licentie. Veel distributies bieden al RPM- of .deb-pakketten voor de agent, en deze pakketten kunnen eenvoudig worden geïnstalleerd en bijgewerkt.
* De Azure Linux Agent vereist Python v2.6+.
* De agent vereist ook de python-pyasn1 module. De meeste distributies bieden deze module als een apart te installeren pakket.
* In sommige gevallen is de Azure Linux Agent mogelijk niet compatibel met NetworkManager. Veel van de RPM/deb-pakketten die door distributies worden geleverd, configureren NetworkManager als een conflict met het waagent-pakket. In deze gevallen verwijdert het NetworkManager wanneer u het Linux-agentpakket installeert.
* De Azure Linux Agent moet zich op of boven de [minimaal ondersteunde versie bevinden.](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

## <a name="general-linux-system-requirements"></a>Algemene Linux-systeemvereisten

1. Wijzig de kernel boot line in GRUB of GRUB2 om de volgende parameters op te nemen, zodat alle consoleberichten naar de eerste seriële poort worden verzonden. Deze berichten kunnen Azure-ondersteuning helpen bij het opsporen van eventuele problemen.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    We raden ook aan om de volgende parameters *te verwijderen* als deze bestaan.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Grafische en stille boot is niet handig in een cloud-omgeving, waar we willen dat alle logs naar de seriële poort worden verzonden. De `crashkernel` optie kan worden overgelaten geconfigureerd indien nodig, maar houd er rekening mee dat deze parameter de hoeveelheid beschikbaar geheugen in de VM vermindert met ten minste 128 MB, wat problematisch kan zijn voor kleinere VM-formaten.

1. Installeer de Azure Linux Agent.
  
    De Azure Linux Agent is vereist voor het inrichten van een Linux-afbeelding op Azure.  Veel distributies bieden de agent als een RPM of .deb pakket (het pakket wordt meestal WALinuxAgent of walinuxagent genoemd).  De agent kan ook handmatig worden geïnstalleerd door de stappen in de [Linux Agent Guide te](../extensions/agent-linux.md)volgen.

1. Controleer of de SSH-server is geïnstalleerd en geconfigureerd om te starten bij het opstarten.  Deze configuratie is meestal de standaard.

1. Maak geen swapruimte op de OS-schijf.
  
    De Azure Linux Agent kan automatisch swapruimte configureren met behulp van de lokale resourceschijf die aan de VM is gekoppeld na inrichten op Azure. De lokale resourceschijf is een *tijdelijke* schijf en kan worden geleegd wanneer de virtuele machine is gedeprovisioneerd. Nadat u de Azure Linux Agent hebt geïnstalleerd (stap 2 hierboven), wijzigt u de volgende parameters in /etc/waagent.conf indien nodig.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Voer de volgende opdrachten uit om de virtuele machine te deprovisioneren.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > Op virtualbox ziet u mogelijk `waagent -force -deprovision` de `[Errno 5] Input/output error`volgende fout na het uitvoeren van de fout met de tekst . Deze foutmelding is niet kritiek en kan worden genegeerd.

* Sluit de virtuele machine af en upload de VHD naar Azure.

