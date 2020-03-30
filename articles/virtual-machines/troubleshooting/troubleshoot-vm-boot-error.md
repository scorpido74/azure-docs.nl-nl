---
title: Linux VM laarzen aan Grub Rescue
description: Virtuele machine kan niet worden opgestart omdat de virtuele machine een reddingsconsole is binnengegaan
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: c24a840716841d04537ac5b77bcaf26fca4b78cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561946"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Linux VM laarzen aan Grub Rescue

We hebben vastgesteld dat uw Virtual Machine (VM) een reddingsconsole is binnengekomen. Het probleem treedt op wanneer uw Linux VM onlangs kernelwijzigingen heeft toegepast, zoals een kernel-upgrade, en niet meer goed wordt opgestart vanwege kernelfouten tijdens het opstartproces. Tijdens het opstartproces, wanneer de bootloader probeert de Linux-kernel te lokaliseren en de opstartbesturing af te geven, komt de VM in een reddingsconsole wanneer de overdracht mislukt.

Als u merkt dat u in de toekomst geen verbinding meer maken met een virtuele machine, u een schermafbeelding van uw VM bekijken met behulp van het blad voor opstartdiagnose in de Azure-portal. Dit kan helpen bij het vaststellen van de oorzaak van het probleem omdat u kunt bepalen of een vergelijkbare opstartfout mogelijk de oorzaak is.

## <a name="recommended-steps"></a>Aanbevolen stappen

Volg de onderstaande mitigatiestappen, afhankelijk van de fout die u ontvangt:

### <a name="error---unknown-filesystem"></a>Fout - Onbekend bestandssysteem

* Als u de fout **onbekend bestandssysteem ophaalt,** kan deze fout het gevolg zijn van een beschadiging van het bestandssysteem op de opstartpartitie of een onjuiste kernelconfiguratie.

   * Voor problemen met het bestandssysteem, volg de stappen in het artikel [Linux Recovery: Kan Niet SSH naar Linux VM als gevolg van file systeem fouten (fsck, inodes)](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/).
   * Voor kernel problemen, volg de stappen in het artikel [Linux Recovery: Handmatig oplossen van niet-boot problemen in verband met kernel problemen,](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)of [Linux Recovery: Het oplossen van niet-boot problemen in verband met kernel problemen met behulp van chroot](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>Fout - Bestand niet gevonden

* Als u de fout **fout 15: bestand niet gevonden of eerste RAM-schijf** of **initrd / initramfs bestand niet gevonden,** volg de onderstaande stappen.

    * Ga voor `/boot/grub2/grub.cfg` het `initrd/initramfs` ontbrekende bestand of ga verder met het volgende proces:

    1. Zorg `/etc/default/grub` ervoor dat er is en de juiste/gewenste instellingen zijn. Als u niet weet welke standaardinstellingen zijn, u contact opnemen met een werkende vm.

    2. Voer vervolgens de volgende opdracht uit om de configuratie opnieuw te genereren:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Als het ontbrekende bestand `/boot/grub/menu.lst`is, deze fout is voor oudere OS-versies **(RHEL 6.x**, **Centos 6.x** en **Ubuntu 14.04**), zodat de commando's kunnen verschillen. U moet een oude server draaien en testen om ervoor te zorgen dat de juiste commando's worden verstrekt.

### <a name="error---no-such-partition"></a>Fout - Geen dergelijke partitie

* Als u de fout **geen dergelijke partitie**ophaalt, raadpleegt u [casescenario: fout 'geen dergelijke partitie', terwijl u de VM probeert te starten nadat u probeert het besturingssysteem uit te breiden.](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/)

### <a name="error---grubcfg-file-not-found"></a>Fout - grub.cfg-bestand niet gevonden

* Als je krijgt de fout **/ boot / grub2 / grub.cfg bestand niet gevonden,** volg de onderstaande stappen.

    * Ga voor `/boot/grub2/grub.cfg` het `initrd/initramfs` ontbrekende bestand of ga verder met het volgende proces:

    1. Zorg `/etc/default/grub` ervoor dat er is en de juiste/gewenste instellingen zijn. Als u niet weet welke standaardinstellingen zijn, u contact opnemen met een werkende vm.

    2. Voer vervolgens de volgende opdracht uit `grub2-mkconfig -o /boot/grub2/grub.cfg`om de configuratie opnieuw te genereren: .

   * Als het ontbrekende bestand `/boot/grub/menu.lst`is, deze fout is voor oudere OS-versies **(RHEL 6.x**, **Centos 6.x** en **Ubuntu 14.04**), zodat de commando's kunnen uitstellen. Draai een oude server op en test deze om ervoor te zorgen dat de juiste opdrachten worden geleverd.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Azure Virtual Machine Agent](../extensions/agent-windows.md)
* [Virtuele machine-extensies en -functies voor Windows](../extensions/features-windows.md)

