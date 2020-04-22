---
title: Uw Linux-VM optimaliseren voor Azure
description: Lees enkele optimalisatietips om ervoor te zorgen dat u uw Linux-vm hebt ingesteld voor optimale prestaties op Azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 87776c14e45ff4bb3cce6661323d74a1315c8ab2
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757098"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Uw Linux-VM optimaliseren voor Azure
Het maken van een Linux virtuele machine (VM) is eenvoudig te doen vanaf de opdrachtregel of vanaf de portal. In deze zelfstudie ziet u hoe u ervoor zorgen dat u deze hebt ingesteld om de prestaties ervan op het Microsoft Azure-platform te optimaliseren. Dit onderwerp maakt gebruik van een Ubuntu Server VM, maar u ook Linux virtuele machine met behulp van [uw eigen afbeeldingen als sjablonen](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Vereisten
In dit onderwerp wordt ervan uitgegaan dat u al een werkend Azure-abonnement hebt[(gratis aanmelden voor proefversies)](https://azure.microsoft.com/pricing/free-trial/)en al een VM in uw Azure-abonnement hebt ingericht. Zorg ervoor dat u de nieuwste [Azure CLI hebt](/cli/azure/install-az-cli2) geïnstalleerd en ingelogd op uw Azure-abonnement met [az-aanmelding](/cli/azure/reference-index) voordat u [een VM maakt.](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-os-disk"></a>Azure OS-schijf
Zodra u een Linux-vm in Azure hebt gemaakt, zijn er twee schijven aan gekoppeld. **/ dev / sda** is uw OS schijf, **/ dev / sdb** is uw tijdelijke schijf.  Gebruik de hoofdschijf van het besturingssysteem **(/dev/sda)** niet voor iets behalve het besturingssysteem, omdat deze is geoptimaliseerd voor een snelle VM-opstarttijd en geen goede prestaties biedt voor uw workloads. U wilt een of meer schijven aan uw vm koppelen om permanente en geoptimaliseerde opslag voor uw gegevens te krijgen. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Schijven toevoegen voor grootte- en prestatiedoelen
Op basis van de VM-grootte u maximaal 16 extra schijven op een A-serie, 32 schijven op een D-serie en 64 schijven op een machine uit de G-serie - elk tot 32 TB groot - koppelen. U voegt extra schijven toe naar behoefte per uw ruimte en IOps-vereisten. Elke schijf heeft een prestatiedoel van 500 IOps voor standaardopslag en maximaal 20.000 IOps per schijf voor Premium Storage.

Om de hoogste IOps op Premium Storage-schijven te bereiken waar hun cache-instellingen zijn ingesteld op **ReadOnly** of **Geen,** moet u **barrières** uitschakelen tijdens het monteren van het bestandssysteem in Linux. U hebt geen barrières nodig omdat de schrijfbewerkingen naar back-schijven met ondersteuning van Premium Storage duurzaam zijn voor deze cache-instellingen.

* Als u **reiserFS**gebruikt, schakel `barrier=none` barrières uit met `barrier=flush`behulp van de houderoptie (Voor het inschakelen van barrières, gebruik)
* Als u **ext3/ext4**gebruikt, schakel `barrier=0` barrières uit met `barrier=1`de houderoptie (Voor het inschakelen van barrières, gebruik)
* Als u **XFS**gebruikt, schakelt `nobarrier` u barrières uit met `barrier`de houderoptie (Voor het inschakelen van barrières, gebruikt u de optie)

## <a name="unmanaged-storage-account-considerations"></a>Overwegingen voor onbeheerde opslagaccount
De standaardactie wanneer u een VM maakt met de Azure CLI, is het gebruik van Azure Managed Disks.  Deze schijven worden behandeld door het Azure-platform en vereisen geen voorbereiding of locatie om ze op te slaan.  Onbeheerde schijven vereisen een opslagaccount en hebben een aantal aanvullende prestatieoverwegingen.  Zie voor meer informatie over beheerde schijven [overzicht Azure Managed Disks](../windows/managed-disks-overview.md).  In de volgende sectie worden prestatieoverwegingen alleen beschreven wanneer u onbeheerde schijven gebruikt.  Nogmaals, de standaard en aanbevolen opslagoplossing is het gebruik van beheerde schijven.

Als u een vm met niet-beheerde schijven maakt, moet u schijven van opslagaccounts in dezelfde regio als uw vm koppelen om de nabijheid te garanderen en de netwerklatentie te minimaliseren.  Elk Standaard opslagaccount heeft een maximum van 20k IOps en een capaciteit van 500 TB.  Deze limiet werkt uit tot ongeveer 40 veelgebruikte schijven, waaronder zowel de OS-schijf als alle gegevensschijven die u maakt. Voor Premium Storage-accounts is er geen limiet voor iOps, maar er is een limiet van 32 TB. 

Wanneer u te maken hebt met hoge IOps-workloads en u hebt gekozen voor Standaardopslag voor uw schijven, moet u de schijven mogelijk splitsen over meerdere opslagaccounts om ervoor te zorgen dat u de limiet van 20.000 IOps voor Standaardopslagaccounts niet hebt bereikt. Uw VM kan een mix van schijven bevatten van verschillende opslagaccounts en opslagaccounttypen om uw optimale configuratie te bereiken.
 

## <a name="your-vm-temporary-drive"></a>Uw VM-tijdelijke station
Standaard wanneer u een VM maakt, biedt Azure u een OS-schijf **(/dev/sda)** en een tijdelijke schijf (**/dev/sdb**).  Alle extra schijven die u toevoegt, worden weergegeven als **/dev/sdc**, **/dev/sdd,** **/dev/sde** enzovoort. Alle gegevens op uw tijdelijke schijf **(/dev/sdb)** zijn niet duurzaam en kunnen verloren gaan als specifieke gebeurtenissen zoals VM Resizing, redeployment of maintenance een herstart van uw VM afdwingen.  De grootte en het type van uw tijdelijke schijf is gerelateerd aan de VM-grootte die u hebt gekozen tijdens de implementatietijd. Alle premium formaat VM's (DS, G en DS_V2 serie) de tijdelijke schijf worden ondersteund door een lokale SSD voor extra prestaties van maximaal 48k IOps. 

## <a name="linux-swap-partition"></a>Linux-swappartitie
Als uw Azure VM afkomstig is van een Ubuntu- of CoreOS-afbeelding, u CustomData gebruiken om een cloud-config naar cloud-init te verzenden. Als u [een aangepaste Linux-afbeelding hebt geüpload](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) die cloud-init gebruikt, configureert u ook swappartities met behulp van cloud-init.

Op Ubuntu Cloud Images moet u cloud-init gebruiken om de swappartitie te configureren. Zie [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions)voor meer informatie .

Voor afbeeldingen zonder ondersteuning voor cloud-init hebben VM-afbeeldingen die zijn geïmplementeerd vanuit de Azure Marketplace een VM Linux-agent die is geïntegreerd met het besturingssysteem. Met deze agent kan de VM communiceren met verschillende Azure-services. Ervan uitgaande dat u een standaardafbeelding van de Azure Marketplace hebt geïmplementeerd, moet u het volgende doen om uw Linux-swapbestandsinstellingen correct te configureren:

Zoek en wijzig twee vermeldingen in het bestand **/etc/waagent.conf.** Zij controleren het bestaan van een speciaal swapbestand en de grootte van het swapbestand. De parameters die u `ResourceDisk.EnableSwap` moet verifiëren zijn en`ResourceDisk.SwapSizeMB` 

Als u een goed ingeschakeldschijf en een gemonteerd swapbestand wilt inschakelen, moet u ervoor zorgen dat de parameters de volgende instellingen hebben:

* ResourceDisk.enableswap=Y
* ResourceDisk.SwapSizeMB={grootte in MB om aan uw behoeften te voldoen} 

Zodra u de wijziging hebt aangebracht, moet u de waagent opnieuw opstarten of uw Linux-VM opnieuw starten om deze wijzigingen weer te geven.  U weet dat de wijzigingen zijn geïmplementeerd en dat er `free` een swapbestand is gemaakt wanneer u de opdracht gebruikt om de vrije ruimte weer te geven. In het volgende voorbeeld wordt een swapbestand van 512 MB gemaakt als gevolg van het wijzigen van het **bestand waagent.conf:**

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>I/O-planningsalgoritme voor Premium Storage
Met de 2.6.18 Linux kernel werd het standaard I/O-planningsalgoritme gewijzigd van Deadline naar CFQ (Completely fair queuing algorithm). Voor random access I/O-patronen is er een verwaarloosbaar verschil in prestatieverschillen tussen CFQ en Deadline.  Voor SSD-schijven waarbij het i/o-patroon van de schijf overwegend sequentiële is, kan het terugschakelen naar het NOOP- of Deadline-algoritme betere I/O-prestaties bereiken.

### <a name="view-the-current-io-scheduler"></a>De huidige I/O-planner weergeven
Gebruik de volgende opdracht:  

```bash
cat /sys/block/sda/queue/scheduler
```

U ziet volgende uitvoer, die de huidige planner aangeeft.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Het huidige apparaat (/dev/sda) van i/o-planningsalgoritme wijzigen
Gebruik de volgende opdrachten:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Het toepassen van deze instelling voor **/dev/sda** alleen is niet handig. Stel in op alle gegevensschijven waar sequentiële I/O het I/O-patroon domineert.  

U ziet de volgende uitvoer, die aangeeft dat **grub.cfg** opnieuw is opgebouwd en dat de standaardplanner is bijgewerkt naar NOOP.  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

Voor de Red Hat-distributiefamilie hebt u alleen de volgende opdracht nodig:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Software RAID gebruiken om hogere I/Ops te bereiken
Als uw workloads meer IOps vereisen dan een enkele schijf kan bieden, moet u een RAID-configuratie van meerdere schijven gebruiken. Omdat Azure al schijftolerantie uitvoert op de lokale fabriclaag, bereikt u het hoogste prestatieniveau van een RAID-0-stripconfiguratie.  Inrichten en maken van schijven in de Azure-omgeving en deze koppelen aan uw Linux-vm voordat u de stations partitioneert, opmaakt en monteert.  Meer informatie over het configureren van een raid-installatie voor software op uw Linux VM in azure vindt u in het **[RAID-document Configureren van software op Linux.](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Als alternatief voor een traditionele RAID-configuratie u er ook voor kiezen om Logical Volume Manager (LVM) te installeren om een aantal fysieke schijven te configureren in één gestreept logisch opslagvolume. In deze configuratie worden lees- en schrijfbewerkingen gedistribueerd naar meerdere schijven in de volumegroep (vergelijkbaar met RAID0). Om prestatieredenen, is het waarschijnlijk dat u wilt strepen uw logische volumes, zodat leest en schrijft gebruik maken van al uw bijgevoegde gegevens schijven.  Meer informatie over het configureren van een gestreept logisch volume op uw Linux VM in Azure vindt u in de **[LVM configureren op een Linux VM in Azure-document.](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

## <a name="next-steps"></a>Volgende stappen
Vergeet niet, zoals bij alle optimalisatiediscussies, moet u tests uitvoeren voor en na elke wijziging om de impact van de wijziging te meten.  Optimalisatie is een stap voor stap proces dat verschillende resultaten heeft over verschillende machines in uw omgeving.  Wat werkt voor een configuratie werkt mogelijk niet voor anderen.

Enkele nuttige links naar aanvullende bronnen:

* [Gebruikershandleiding voor Azure Linux Agent](../extensions/agent-linux.md)
* [Software RAID configureren op Linux](configure-raid.md)
