---
title: Azure Serial Console voor SysRq- en NMI-aanroepen | Microsoft Documenten
description: Seriële console gebruiken voor SysRq- en NMI-oproepen in virtuele Azure-machines.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 3ad68438f5fc015b6a9150d67485b90a095f1a4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250086"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Seriële console gebruiken voor SysRq- en NMI-aanroepen

## <a name="system-request-sysrq"></a>Systeemaanvraag (SysRq)
Een SysRq is een reeks sleutels die worden begrepen door de Kernel van het Linux-besturingssysteem, die een reeks vooraf gedefinieerde acties kan activeren. Deze opdrachten worden vaak gebruikt wanneer het oplossen van problemen met virtuele machines of herstel niet kan worden uitgevoerd via traditioneel beheer (bijvoorbeeld als de virtuele machine niet reageert). Met behulp van de SysRq-functie van Azure Serial Console wordt het indrukken van de SysRq-toets en tekens die op een fysiek toetsenbord zijn ingevoerd, nagebootst.

Zodra de SysRq-reeks is geleverd, bepaalt de kernelconfiguratie hoe het systeem reageert. Zie de[tekstmarkering](https://aka.ms/linuxsysrq)van de *SysRq-beheergids* [text](https://aka.ms/kernelorgsysreqdoc) | voor informatie over het in- en uitschakelen van SysRq.

De Azure Serial Console kan worden gebruikt om een SysRq naar een virtuele Azure-machine te verzenden met behulp van het toetsenbordpictogram in de onderstaande opdrachtbalk.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Als u 'SysRq-opdracht verzenden' kiest, wordt een dialoogvenster geopend, dat algemene SysRq-opties biedt of een reeks SysRq-opdrachten accepteert die in het dialoogvenster zijn ingevoerd.  Dit maakt het mogelijk voor series van SysRq's om een `REISUB`operatie op hoog niveau uit te voeren, zoals een veilige reboot met behulp van: .

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

De opdracht SysRq kan niet worden gebruikt op virtuele machines die zijn gestopt of waarvan de kernel zich in een niet-responsieve toestand bevindt. (bijvoorbeeld een kernel panic).

### <a name="enable-sysrq"></a>SysRq inschakelen
Zoals beschreven in de *SysRq Admin Guide* hierboven, sysrq kan worden geconfigureerd zodanig dat alle, geen, of alleen bepaalde commando's beschikbaar zijn. U alle SysRq-opdrachten inschakelen met behulp van de onderstaande stap, maar het zal een reboot niet overleven:
```
echo "1" >/proc/sys/kernel/sysrq
```
Om de SysReq-configuratie persistent te maken, u het volgende doen om alle SysRq-opdrachten in te schakelen
1. Deze regel toevoegen aan */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Sysctl opnieuw opstarten of bijwerken door sysctl uit te voeren <br>
    `sysctl -p`

### <a name="command-keys"></a>Opdrachttoetsen
Uit de SysRq Admin Guide hierboven:

|Opdracht| Functie
| ------| ----------- |
|``b``  |   Zal onmiddellijk opnieuw opstarten van het systeem zonder synchronisatie of demontage van uw schijven.
|``c``  |   Zal een systeemcrash uitvoeren door een NULL-aanwijzerverwijzing. Een crashdump zal worden genomen indien geconfigureerd.
|``d``  |   Toont alle sloten die worden vastgehouden.
|``e``  |   Stuur een SIGTERM naar alle processen, behalve init.
|``f``  |   Zal de oom moordenaar bellen om een memory hog proces te doden, maar raak niet in paniek als er niets kan worden gedood.
|``g``  |   Gebruikt door kgdb (kernel debugger)
|``h``  |   Zal weer te geven hulp (een andere sleutel dan ``h`` die hier vermeld zal ook hulp weer te geven, maar is gemakkelijk te onthouden :-)
|``i``  |    Stuur een SIGKILL naar alle processen, behalve init.
|``j``  |    Met geweld "Gewoon ontdooien" - bestandssystemen bevroren door de FIFREEZE ioctl.
|``k``  |    Secure Access Key (SAK) Doodt alle programma's op de huidige virtuele console. LET OP: Zie belangrijke opmerkingen hieronder in sak sectie.
|``l``  |    Toont een stack backtrace voor alle actieve CPU's.
|``m``  |    Als u actuele geheugengegevens naar uw console stort.
|``n``  |    Wordt gebruikt om RT-taken mooi te maken
|``o``  |    Hiermee wordt uw systeem uitgeschakeld (indien geconfigureerd en ondersteund).
|``p``  |    Dumpt de huidige registers en vlaggen naar uw console.
|``q``  |    Zal dumpen per CPU lijsten van alle gewapende hrtimers (maar niet regelmatig timer_list timers) en gedetailleerde informatie over alle clockevent apparaten.
|``r``  |    Hiermee schakelt u de onbewerkte modus van het toetsenbord uit en stelt u deze in op XLATE.
|``s``  |    Zal proberen om alle gemonteerde bestandssystemen synchroniseren.
|``t``  |    Als u een lijst met huidige taken en hun gegevens naar uw console stort.
|``u``  |    Zal proberen om alle gemonteerde filesystems alleen te monteren.
|``v``  |    Herstelt met kracht framebufferconsole
|``v``  |    Oorzaken ETM buffer dump [ARM-specifiek]
|``w``  |    Dumpt taken die zich in de status niet-onderbrekend (geblokkeerd) bevinden.
|``x``  |    Gebruikt door xmon interface op ppc / powerpc platforms. Toon wereldwijde PMU Registers op sparc64. Dump alle TLB-vermeldingen op MIPS.
|``y``  |    Globale CPU-registers weergeven [SPARC-64 specifiek]
|``z``  |    Dump de ftrace-buffer
|``0``-``9`` | Hiermee stelt u het consolelogboekniveau in en bepaalt u welke kernelberichten op uw console worden afgedrukt. (``0``, bijvoorbeeld zou maken, zodat alleen noodberichten zoals PANIC's of OOPSes zou maken om uw console.)

### <a name="distribution-specific-documentation"></a>Distributiespecifieke documentatie ###
Zie de onderstaande koppelingen voor distributiespecifieke documentatie over SysRq en stappen om Linux te configureren om een crashdump te maken wanneer deze een SysRq "Crash"-opdracht ontvangt:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Kernel Crash Dump](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Wat is de SysRq-faciliteit en hoe gebruik ik deze?](https://access.redhat.com/articles/231663)
- [De SysRq-faciliteit gebruiken om informatie te verzamelen van een RHEL-server](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Kernel core dump capture configureren](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Crashlogs verzamelen](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Niet-maskeerbare onderbreking (NMI)
Een niet-maskerbare interrupt (NMI) is ontworpen om een signaal te maken dat software op een virtuele machine niet zal negeren. Historisch gezien zijn NMI's gebruikt om te controleren op hardwareproblemen op systemen waarvoor specifieke responstijden vereist zijn.  Tegenwoordig gebruiken programmeurs en systeembeheerders VAAK NMI als een mechanisme om systemen die niet reageren te debuggen of op te lossen.

De seriële console kan worden gebruikt om een NMI naar een virtuele Azure-machine te verzenden met behulp van het toetsenbordpictogram in de onderstaande opdrachtbalk. Zodra het NMI is geleverd, bepaalt de configuratie van de virtuele machine hoe het systeem reageert.  Linux-besturingssystemen kunnen worden geconfigureerd om te crashen en een geheugendump te maken het besturingssysteem ontvangt een NMI.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

### <a name="enable-nmi"></a>NMI inschakelen
Voor Linux-systemen die sysctl ondersteunen voor het configureren van kernelparameters, u paniek inschakelen bij het ontvangen van dit NMI met behulp van het volgende:
1. Deze regel toevoegen aan */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Sysctl opnieuw opstarten of bijwerken door sysctl uit te voeren <br>
    `sysctl -p`

Voor meer informatie over Linux `unknown_nmi_panic`kernel `panic_on_io_nmi`configuraties, waaronder , en `panic_on_unrecovered_nmi`, zie: Documentatie voor [/proc/sys/kernel/*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Zie de onderstaande koppelingen voor distributiespecifieke documentatie over NMI en stappen om Linux te configureren om een crashdump te maken wanneer deze een NMI ontvangt:

### <a name="ubuntu"></a>Ubuntu
 - [Kernel Crash Dump](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat
 - [Wat is een NMI en waar kan ik het voor gebruiken?](https://access.redhat.com/solutions/4127)
 - [Hoe kan ik mijn systeem configureren om te crashen wanneer de NMI-schakelaar wordt ingedrukt?](https://access.redhat.com/solutions/125103)
 - [Gids crashdumpbeheerders](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE
- [Kernel core dump capture configureren](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS
- [Crashlogs verzamelen](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Volgende stappen
* De belangrijkste Serial Console Linux documentatie pagina is [hier](serial-console-linux.md)te vinden.
* Seriële console gebruiken om op te starten in [GRUB en de modus voor één gebruiker in te voeren](serial-console-grub-single-user-mode.md)
* De seriële console is ook beschikbaar voor [Windows](serial-console-windows.md) VM's
* Meer informatie over [opstartdiagnostiek](boot-diagnostics.md)