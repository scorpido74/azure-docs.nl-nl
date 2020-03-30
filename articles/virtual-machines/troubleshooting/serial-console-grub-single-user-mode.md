---
title: Azure Serial Console voor GRUB en modus voor één gebruiker | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Serial Console voor GRUB gebruiken in virtuele Azure-machines.
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
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 06cb3fe5d551ddfc95fcbd37cd9620adebd825c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883924"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Seriële console gebruiken om toegang te krijgen tot GRUB en de modus voor één gebruiker
GRand Unified Bootloader (GRUB) is waarschijnlijk het eerste wat je ziet wanneer je een virtuele machine (VM) opstart. Omdat het wordt weergegeven voordat het besturingssysteem is gestart, is GRUB niet toegankelijk via SSH. In GRUB u onder andere uw opstartconfiguratie aanpassen om op te starten in de modus voor één gebruiker.

Single-user mode is een minimale omgeving met minimale functionaliteit. Het kan handig zijn voor het onderzoeken van opstartproblemen, problemen met het bestandssysteem of netwerkproblemen. Er kunnen minder services op de achtergrond worden uitgevoerd en afhankelijk van het runlevel wordt een bestandssysteem mogelijk niet eens automatisch gemonteerd.

De modus voor één gebruiker is ook handig in situaties waarin uw vm kan worden geconfigureerd om alleen SSH-sleutels voor aanmelding te accepteren. In dit geval u mogelijk de modus voor één gebruiker gebruiken om een account met wachtwoordverificatie te maken. 

> [!NOTE]
> Met de Serial Console-service kunnen alleen gebruikers met *een bijdragerniveau* of hogere machtigingen toegang krijgen tot de seriële console van een vm.

Als u de modus voor één gebruiker wilt invoeren, voert u GRUB in wanneer uw VM wordt opgestart en wijzigt u de opstartconfiguratie in GRUB. Zie gedetailleerde instructies voor het invoeren van GRUB in de volgende sectie. Als uw VM is geconfigureerd om GRUB weer te geven, u in het algemeen de knop Opnieuw starten in de seriële console van uw VM gebruiken om de VM opnieuw op te starten en GRUB weer te geven.

![De knop Linux Serial Console Opnieuw opstarten](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Algemene GRUB-toegang
Als u GRUB wilt openen, start u uw VM opnieuw op terwijl het deelvenster Seriële console is geopend. Voor sommige distributies is toetsenbordinvoer vereist om GRUB weer te geven, en andere tonen GRUB automatisch een paar seconden om de invoer van het gebruikerstoetsenbord toe te staan om de time-out te annuleren.

Als u toegang wilt krijgen tot de modus voor één gebruiker, wilt u ervoor zorgen dat GRUB is ingeschakeld op uw VM. Afhankelijk van uw distributie kan enige installatiewerk nodig zijn om ervoor te zorgen dat GRUB is ingeschakeld. Zie de volgende sectie en onze [support voor Linux op Azure-pagina voor](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/) distributiespecifieke informatie.

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Start uw VM opnieuw om toegang te krijgen tot GRUB in Serial Console
U uw VM opnieuw starten in seriële console door de knop **Opnieuw starten** te bekijken en vervolgens VM **opnieuw starten**te selecteren. Onderaan het deelvenster wordt een melding weergegeven over de herstart.

U uw VM ook opnieuw starten door een SysRq b-opdracht uit te voeren als [SysRq](./serial-console-nmi-sysrq.md) is ingeschakeld. Zie de distributiespecifieke instructies in de volgende secties om te weten wat u van GRUB verwachten wanneer u opnieuw wordt opgestart.

![Linux Serial Console opnieuw opstarten](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Toegang tot de algemene modus voor één gebruiker
Mogelijk hebt u handmatige toegang nodig tot de modus voor één gebruiker wanneer u een account met wachtwoordverificatie niet hebt geconfigureerd. Wijzig de GRUB-configuratie om handmatig de modus voor één gebruiker in te voeren. Nadat u dit hebt gedaan, raadpleegt u de sectie 'Modus voor één gebruiker gebruiken om een wachtwoord opnieuw in te stellen of toe te voegen' voor verdere instructies.

Als de VM niet kan opstarten, worden u vaak automatisch in de modus voor één gebruiker of in de noodmodus geplaatst. Andere distributies vereisen echter extra instellingen, zoals het instellen van een hoofdwachtwoord, voordat ze u automatisch in de modus voor één gebruiker of nood kunnen zetten.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>De modus voor één gebruiker gebruiken om een wachtwoord opnieuw te instellen of toe te voegen
Nadat u in de modus voor één gebruiker bent, voegt u een nieuwe gebruiker met sudo-bevoegdheden toe door het volgende te doen:
1. Uitvoeren `useradd <username>` om een gebruiker toe te voegen.
1. Uitvoeren `sudo usermod -a -G sudo <username>` om de nieuwe gebruikersbasisrechten toe te kennen.
1. Met `passwd <username>` gebruiken om het wachtwoord in te stellen voor de nieuwe gebruiker. U zich dan aanmelden als de nieuwe gebruiker.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Toegang voor Red Hat Enterprise Linux (RHEL)
Als RHEL niet normaal kan opstarten, wordt u automatisch in de modus met één gebruiker geplaatst. Als u echter geen root-toegang hebt ingesteld voor de modus voor één gebruiker, hebt u geen hoofdwachtwoord en u zich niet aanmelden. Er is een tijdelijke oplossing (zie de sectie 'Handmatig één gebruiker invoeren in RHEL'), maar we raden u aan root-toegang in eerste instantie in te stellen.

### <a name="grub-access-in-rhel"></a>GRUB toegang in RHEL
RHEL wordt geleverd met GRUB ingeschakeld uit de doos. Als u GRUB wilt invoeren, start u de VM opnieuw op door de vm uit te voeren `sudo reboot`en drukt u op een toets. Het GRUB-deelvenster moet worden weergegeven. Als dit niet het is, moet u ervoor zorgen`/etc/default/grub`dat de volgende regels aanwezig zijn in uw GRUB-bestand :

**Voor RHEL 8**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**Voor RHEL 7**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> Red Hat biedt ook documentatie voor het opstarten in de reddingsmodus, noodmodus of foutopsporingsmodus en voor het opnieuw instellen van het hoofdwachtwoord. Zie Het [bewerken van het menu Terminal tijdens het opstarten voor](https://aka.ms/rhel7grubterminal)instructies.

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Root-toegang instellen voor de modus voor één gebruiker in RHEL
De hoofdgebruiker is standaard uitgeschakeld. De modus voor één gebruiker in RHEL vereist dat de hoofdgebruiker wordt ingeschakeld. Als u de modus voor één gebruiker moet inschakelen, gebruikt u de volgende instructies:

1. Meld je aan bij het Red Hat-systeem via SSH.
1. Overschakelen naar root.
1. Schakel het wachtwoord voor de hoofdgebruiker in met het volgende:
    * Uitvoeren `passwd root` (een sterk hoofdwachtwoord instellen).
1. Zorg ervoor dat de hoofdgebruiker zich alleen via ttyS0 kan aanmelden door het volgende te doen:  
    a. Uitvoeren `edit /etc/ssh/sshd_config`en ervoor zorgen dat PermitRootLogIn is ingesteld op `no`.  
    b. Voer `edit /etc/securetty file` alleen aanmelding toe via ttyS0.

Als het systeem nu wordt opgestart in de modus voor één gebruiker, u zich aanmelden met het hoofdwachtwoord.

Als u voor RHEL 7.4+ of 6.9+ de modus voor één gebruiker wilt inschakelen in de GRUB-prompts, raadpleegt u [Opstarten in de modus voor één gebruiker.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Handmatig de modus voor één gebruiker invoeren in RHEL
Als u GRUB- en roottoegang hebt ingesteld met behulp van de voorgaande instructies, u de modus voor één gebruiker invoeren door het volgende te doen:

1. Als u GRUB wilt invoeren, drukt u op Esc terwijl u de vm opnieuw start.
1. Druk in GRUB op E om het besturingssysteem te bewerken waarin u wilt opstarten. Het OS wordt meestal vermeld op de eerste regel.
1. Zoek de kernellijn. In Azure begint het met *linux16*.
1. Druk op Ctrl+E om naar het einde van de regel te gaan.
1. Voeg aan het einde van de lijn *systemd.unit=rescue.target*toe.
    
    Met deze actie wordt u in de modus voor één gebruiker opstart. Als u de noodmodus wilt gebruiken, voegt u *systemd.unit=emergency.target* toe aan het einde van de lijn (in plaats van *systemd.unit=rescue.target).*

1. Druk op Ctrl+X om af te sluiten en opnieuw op te starten met de toegepaste instellingen.

   U wordt gevraagd om het beheerderswachtwoord voordat u de modus voor één gebruiker invoeren. Dit wachtwoord is het wachtwoord dat u in de vorige instructies hebt gemaakt.

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Voer de modus voor één gebruiker in zonder rootaccount ingeschakeld in RHEL
Als u de hoofdgebruiker niet hebt ingeschakeld door de eerdere instructies te volgen, u het hoofdwachtwoord als volgt opnieuw instellen:

> [!NOTE]
> Als u SELinux gebruikt, moet u bij het opnieuw instellen van het hoofdwachtwoord de aanvullende stappen volgen die worden beschreven in de [Red Hat-documentatie.](https://aka.ms/rhel7grubterminal)

1. Als u GRUB wilt invoeren, drukt u op Esc terwijl u de vm opnieuw start.

1. Druk in GRUB op E om het besturingssysteem te bewerken waarin u wilt opstarten. Het OS wordt meestal vermeld op de eerste regel.
1. Zoek de kernellijn. In Azure begint het met *linux16*.
1. Aan het einde van de regel voegt u *rd.break* toe aan het einde van de regel. Laat een spatie tussen de kernellijn en *rd.break*.

    Deze actie onderbreekt het opstartproces `initramfs` `systemd`voordat de besturingselement wordt doorgegeven van naar , zoals beschreven in de [Red Hat-documentatie](https://aka.ms/rhel7rootpassword).
1. Druk op Ctrl+X om af te sluiten en opnieuw op te starten met de toegepaste instellingen.

   Nadat u opnieuw bent opgestart, wordt u in de noodmodus gedropt met een alleen-lezen bestandssysteem. 
   
1. Voer in de `mount -o remount,rw /sysroot` shell in om het rootbestandssysteem opnieuw te monteren met lees-/schrijfmachtigingen.
1. Nadat u bent opgestart in `chroot /sysroot` de modus `sysroot` voor één gebruiker, voert u deze in om over te schakelen naar de gevangenis.
1. Je bent nu aan de basis. U uw hoofdwachtwoord `passwd` opnieuw instellen door de voorgaande instructies in te voeren en vervolgens te gebruiken om de modus voor één gebruiker in te voeren. 
1. Nadat u klaar bent, voert u het in `reboot -f` om opnieuw op te starten.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> Als u de voorgaande instructies doorloopt, wordt u in de noodshell gebracht, zodat u ook taken uitvoeren, zoals bewerken. `fstab` We stellen echter meestal voor dat u uw hoofdwachtwoord opnieuw instelt en het gebruikt om de modus voor één gebruiker in te voeren.

## <a name="access-for-centos"></a>Toegang voor CentOS
Net als Red Hat Enterprise Linux vereist de modus voor één gebruiker in CentOS dat GRUB en de root-gebruiker moeten worden ingeschakeld.

### <a name="grub-access-in-centos"></a>GRUB-toegang in CentOS
CentOS wordt geleverd met GRUB ingeschakeld uit de doos. Als u GRUB wilt invoeren, start u de VM opnieuw op door deze in te voeren `sudo reboot`en drukt u op een toets. Met deze actie wordt het deelvenster GRUB weergegeven.

### <a name="single-user-mode-in-centos"></a>Modus voor één gebruiker in CentOS
Als u de modus voor één gebruiker in CentOS wilt inschakelen, volgt u de eerdere instructies voor RHEL.

## <a name="access-for-ubuntu"></a>Toegang voor Ubuntu
Ubuntu-afbeeldingen vereisen geen rootwachtwoord. Als het systeem wordt opgestart in de modus voor één gebruiker, u het gebruiken zonder extra referenties.

### <a name="grub-access-in-ubuntu"></a>GRUB toegang in Ubuntu
Als u toegang wilt krijgen tot GRUB, houdt u Esc ingedrukt terwijl de VM wordt opgestart.

Ubuntu-afbeeldingen worden mogelijk niet automatisch het GRUB-deelvenster weergegeven. U de instelling als volgt wijzigen:
1. Open in een teksteditor het bestand */etc/default/grub.d/50-cloudimg-settings.cfg.*

1. Wijzig `GRUB_TIMEOUT` de waarde in een waarde zonder nul.
1. Open in een teksteditor */etc/default/grub*.
1. Commentaar uit `GRUB_HIDDEN_TIMEOUT=1` de lijn.
1. Zorg ervoor dat `GRUB_TIMEOUT_STYLE=menu` er een lijn is.
1. Voer `sudo update-grub` uit.

### <a name="single-user-mode-in-ubuntu"></a>Modus voor één gebruiker in Ubuntu
Als Ubuntu niet normaal kan opstarten, wordt u automatisch in de modus met één gebruiker geplaatst. Ga als volgt te werk om de modus voor één gebruiker handmatig in te voeren:

1. Druk in GRUB op E om je opstartitem (de Ubuntu-vermelding) te bewerken.
1. Kijk voor de lijn die begint met *linux,* en kijk dan voor *ro*.
1. Voeg *single* na *ro,* ervoor te zorgen dat er een ruimte voor en na *single*.
1. Druk op Ctrl+X om opnieuw op te starten met deze instellingen en voer de modus voor één gebruiker in.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Gebruik GRUB om bash in Ubuntu aan te roepen
Nadat u de voorgaande instructies hebt geprobeerd, is er mogelijk een situatie (zoals een vergeten hoofdwachtwoord) waarin u nog steeds geen toegang hebt tot de modus voor één gebruiker in uw Ubuntu-VM. U de kernel `/bin/bash` ook vertellen om als init uit te voeren, in plaats van het systeem init. Deze actie geeft u een bash shell en zorgt voor systeemonderhoud. Gebruik de volgende instructies:

1. Druk in GRUB op E om je opstartitem (de Ubuntu-vermelding) te bewerken.

1. Kijk voor de lijn die begint met *linux,* en kijk dan voor *ro*.
1. Vervang *ro* door *rw init=/bin/bash*.

    Deze actie monteert uw bestandssysteem als `/bin/bash` read-write en gebruikt als het init-proces.
1. Druk op Ctrl+X om opnieuw op te starten met deze instellingen.

## <a name="access-for-coreos"></a>Toegang voor CoreOS
Voor de modus voor één gebruiker in CoreOS moet GRUB worden ingeschakeld.

### <a name="grub-access-in-coreos"></a>GRUB-toegang in CoreOS
Als u toegang wilt krijgen tot GRUB, drukt u op een toets terwijl uw VM wordt opgestart.

### <a name="single-user-mode-in-coreos"></a>Modus voor één gebruiker in CoreOS
Als CoreOS niet normaal kan opstarten, wordt u automatisch in de modus voor één gebruiker geplaatst. Ga als volgt te werk om de modus voor één gebruiker handmatig in te voeren:

1. Druk in GRUB op E om je opstartitem te bewerken.

1. Kijk voor de lijn die begint met *linux $*. Er moeten twee exemplaren van de lijn, elk ingekapseld in een andere *als ... else* clausule.
1. Append *coreos.autologin=ttyS0* aan het einde van elke *linux$* lijn.
1. Druk op Ctrl+X om opnieuw op te starten met deze instellingen en voer de modus voor één gebruiker in.

## <a name="access-for-suse-sles"></a>Toegang voor SUSE SLES
Nieuwere beelden van SLES 12 SP3+ geven toegang via de seriële console als het systeem in noodmodus wordt opgestart.

### <a name="grub-access-in-suse-sles"></a>GRUB toegang in SUSE SLES
Grub-toegang in SLES vereist een bootloaderconfiguratie via YaST. Ga als volgt te werk om de configuratie te maken:

1. Gebruik SSH om u aan te melden `sudo yast bootloader`bij uw SLES VM en voer vervolgens uit. Druk op Tab, druk op Enter en gebruik de pijltoetsen om door het menu te navigeren.

1. Ga naar **Kernel Parameters**en schakel het selectievakje **Seriële console gebruiken** in.
1. Toevoegen `serial --unit=0 --speed=9600 --parity=no` aan de **argumenten console.**
1. Druk op F10 om uw instellingen en afsluiten op te slaan.
1. Als u GRUB wilt invoeren, start u de VM opnieuw op en drukt u op een willekeurige toets tijdens de opstartreeks om het GRUB-deelvenster weer te geven.

    De standaard time-out voor GRUB is **1s**. U deze instelling `GRUB_TIMEOUT` wijzigen door de variabele in het bestand */etc/default/grub te* wijzigen.

![De configuratie van de bootloader initialiseren](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modus voor één gebruiker in SUSE SLES
Als SLES niet normaal kan opstarten, wordt u automatisch in de noodschaal gedropt. Ga als volgt te werk om de noodhuls handmatig in te voeren:

1. Druk in GRUB op E om uw opstartitem (de SLES-vermelding) te bewerken.

1. Kijk voor de kernel lijn die begint met *linux*.
1. Toeteweid *systemd.unit=emergency.target* aan het einde van de kernel lijn.
1. Druk op Ctrl+X om opnieuw op te starten met deze instellingen en voer de noodshell in.

   > [!NOTE]
   > Deze actie brengt u in de noodschaal met een alleen-lezen bestandssysteem. Als u bestanden wilt bewerken, monteert u het bestandssysteem opnieuw met lees-schrijfmachtigingen. Voer hiervoor de `mount -o remount,rw /` schaal in.

## <a name="access-for-oracle-linux"></a>Toegang voor Oracle Linux
Net als Red Hat Enterprise Linux, single-user mode in Oracle Linux vereist GRUB en de root gebruiker worden ingeschakeld.

### <a name="grub-access-in-oracle-linux"></a>GRUB-toegang in Oracle Linux
Oracle Linux wordt geleverd met GRUB ingeschakeld out of the box. Als u GRUB wilt invoeren, start u de VM opnieuw op door de VM uit te voeren `sudo reboot`en drukt u op Esc. Met deze actie wordt het deelvenster GRUB weergegeven. Als het GRUB-deelvenster niet wordt weergegeven, moet `GRUB_TERMINAL` u ervoor zorgen dat `GRUB_TERMINAL="serial console"`de waarde van de regel *seriële console* bevat (dat wil zeggen). Herbouw `grub2-mkconfig -o /boot/grub/grub.cfg`GRUB met .

### <a name="single-user-mode-in-oracle-linux"></a>Modus voor één gebruiker in Oracle Linux
Als u de modus voor één gebruiker in Oracle Linux wilt inschakelen, volgt u de eerdere instructies voor RHEL.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over Serial Console:
* [Linux Serial Console-documentatie](serial-console-linux.md)
* [Seriële console gebruiken om GRUB in verschillende distributies in te schakelen](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Seriële console gebruiken voor NMI- en SysRq-aanroepen](serial-console-nmi-sysrq.md)
* [Seriële console voor Windows VM's](serial-console-windows.md)
* [Diagnostische gegevens over opstarten](boot-diagnostics.md)
