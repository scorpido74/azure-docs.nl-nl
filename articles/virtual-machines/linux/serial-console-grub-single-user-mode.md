---
title: Azure Serial Console voor GRUB en single user mode
description: Seriële console gebruiken voor larven in virtuele Azure-machines.
services: virtual-machines-linux
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 2aa7110ab4e52fdc5c3804bd27be5f41081fb435
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758507"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Seriële console gebruiken om toegang te krijgen tot GRUB en de modus voor één gebruiker
GRUB is de GRand Unified Bootloader. Van GRUB bent u in staat om uw boot configuratie te wijzigen om op te starten in een gebruiker modus, onder andere dingen.

Single user mode is een minimale omgeving met minimale functionaliteit. Het kan handig zijn voor het onderzoeken van opstartproblemen, bestandssysteemproblemen of netwerkproblemen. Er kunnen minder services op de achtergrond worden uitgevoerd en afhankelijk van het runlevel kan een bestandssysteem niet eens automatisch worden gemonteerd.

De modus Voor één gebruiker is ook handig in situaties waarin uw vm alleen kan worden geconfigureerd om SSH-sleutels te accepteren om in te loggen. In dit geval u mogelijk de modus voor één gebruiker gebruiken om een account met wachtwoordverificatie te maken.

Als u de modus voor één gebruiker wilt invoeren, moet u GRUB invoeren wanneer uw VM wordt opgestart en de opstartconfiguratie in GRUB wijzigen. Dit kan worden gedaan met de VM-seriële console.

## <a name="general-grub-access"></a>Algemene GRUB-toegang
Om toegang te krijgen tot GRUB, moet u uw VM opnieuw opstarten terwijl u het seriële consoleblad open houdt. Sommige distro's vereisen toetsenbordinvoer om GRUB weer te geven, terwijl anderen GRUB automatisch een paar seconden laten zien en gebruikerstoetsenbordinvoer toestaan om de time-out te annuleren. 

U wilt ervoor zorgen dat GRUB is ingeschakeld op uw VM om toegang te krijgen tot de modus voor één gebruiker. Afhankelijk van uw distro, kan er een aantal setup werk om ervoor te zorgen dat GRUB is ingeschakeld. Distro-specifieke informatie is hieronder beschikbaar.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>Uw VM opnieuw opstarten om toegang te krijgen tot GRUB in Serial Console
Het opnieuw opstarten van uw VM met het geopende `'b'` seriële consoleblad kan met een SysRq-opdracht als [SysRq](./serial-console-nmi-sysrq.md) is ingeschakeld, of door op de knop Opnieuw starten in het overzichtsblad te klikken (open de VM in een nieuw browsertabblad om opnieuw op te starten zonder het seriële consoleblad te sluiten). Volg de distro-specifieke instructies hieronder om te leren wat je verwachten van GRUB wanneer je opnieuw opstart.

## <a name="general-single-user-mode-access"></a>Toegang tot de algemene modus voor één gebruiker
Handmatige toegang tot de modus voor één gebruiker kan nodig zijn in situaties waarin u een account niet hebt geconfigureerd met wachtwoordverificatie. U moet de GRUB-configuratie wijzigen om handmatig de modus voor één gebruiker in te voeren. Zie De modus Eén gebruiker gebruiken om een wachtwoord opnieuw in te stellen of toe te voegen voor verdere instructies.

In gevallen waarin de VM niet kan opstarten, zullen distro's u vaak automatisch in de modus of noodmodus van één gebruiker zetten. Anderen vereisen echter extra instellingen voordat ze u automatisch in de noodmodus voor één gebruiker of noodkunnen zetten (zoals het instellen van een hoofdwachtwoord).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>De modus Één gebruiker gebruiken om een wachtwoord opnieuw te instellen of toe te voegen
Ga als volgt te werk om een nieuwe gebruiker met sudo-bevoegdheden toe te voegen zodra u zich in de modus voor één gebruiker bevindt:
1. Uitvoeren `useradd <username>` om een gebruiker toe te voegen
1. Uitvoeren `sudo usermod -a -G sudo <username>` om de nieuwe gebruikershoofdrechten toe te kennen
1. Met `passwd <username>` gebruiken om het wachtwoord in te stellen voor de nieuwe gebruiker. U dan inloggen als de nieuwe gebruiker

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Toegang voor Red Hat Enterprise Linux (RHEL)
RHEL brengt u automatisch in de modus voor één gebruiker als deze niet normaal kan opstarten. Als u echter geen root-toegang hebt ingesteld voor de modus voor één gebruiker, hebt u geen hoofdwachtwoord en u zich niet aanmelden. Er is een tijdelijke oplossing (Zie 'Handmatig invoeren van de modus voor één gebruiker' hieronder), maar de suggestie is om root-toegang in eerste instantie in te stellen.

### <a name="grub-access-in-rhel"></a>GRUB toegang in RHEL
RHEL wordt geleverd met GRUB ingeschakeld uit de doos. Als u GRUB wilt `sudo reboot` invoeren, start u uw VM opnieuw op en drukt u op een toets. U ziet de GRUB scherm verschijnen.

> Opmerking: Red Hat biedt ook documentatie voor het opstarten in de reddingsmodus, noodmodus, foutopsporingsmodus en het opnieuw instellen van het hoofdwachtwoord. [Klik hier om toegang te krijgen.](https://aka.ms/rhel7grubterminal)

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Root-toegang instellen voor de modus voor één gebruiker in RHEL
De modus voor één gebruiker in RHEL vereist dat de hoofdgebruiker is ingeschakeld, wat standaard is uitgeschakeld. Als u de modus voor één gebruiker moet inschakelen, gebruikt u de volgende instructies:

1. Log in bij het Red Hat systeem via SSH
1. Overschakelen naar root
1. Wachtwoord inschakelen voor hoofdgebruiker 
    * `passwd root`(stel een sterk hoofdwachtwoord in)
1. Zorg ervoor dat root-gebruiker alleen kan inloggen via ttyS0
    * `edit /etc/ssh/sshd_config`en ervoor te zorgen dat PermitRootLogIn is ingesteld op geen
    * `edit /etc/securetty file`om alleen in te loggen via ttyS0 toe te staan 

Nu als het systeem opstart in de modus voor één gebruiker, u inloggen via het hoofdwachtwoord.

Als alternatief voor RHEL 7.4+ of 6.9+ u de modus voor één gebruiker inschakelen in de GRUB-prompts, zie [instructies hier](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Handmatig één gebruikersmodus invoeren in RHEL
Als u GRUB- en roottoegang hebt ingesteld met de bovenstaande instructies, u de modus Voor één gebruiker invoeren met de volgende instructies:

1. Druk op 'Esc' terwijl u de VM opnieuw start om GRUB in te voeren
1. Druk in GRUB op 'e' om het geselecteerde besturingssysteem te bewerken waarin u wilt opstarten (meestal de eerste regel)
1. Zoek de kernellijn - in Azure begint dit met`linux16`
1. Druk op Ctrl + E om naar het einde van de regel te gaan
1. Voeg het volgende toe aan het einde van de regel:`systemd.unit=rescue.target`
    * Dit zal u in enige gebruikerswijze opstarten. Als u de noodmodus `systemd.unit=emergency.target` wilt gebruiken, voegt u het einde van de regel toe in plaats van`systemd.unit=rescue.target`
1. Druk op Ctrl + X om af te sluiten en opnieuw op te starten met de toegepaste instellingen
1. U wordt gevraagd om het beheerderswachtwoord voordat u de modus voor één gebruiker invoeren - dit is hetzelfde wachtwoord dat u in de bovenstaande instructies hebt gemaakt    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Voer de modus voor één gebruiker in zonder rootaccount ingeschakeld in RHEL
Als u de bovenstaande stappen niet hebt doorlopen om de hoofdgebruiker in te schakelen, u uw hoofdwachtwoord nog steeds opnieuw instellen. Gebruik de volgende instructies:

> Opmerking: Als u SELinux gebruikt, moet u ervoor zorgen dat u de aanvullende stappen hebt genomen die [hier](https://aka.ms/rhel7grubterminal) in de Red Hat-documentatie zijn beschreven bij het opnieuw instellen van het hoofdwachtwoord.

1. Druk op 'Esc' terwijl u de VM opnieuw start om GRUB in te voeren
1. Druk in GRUB op 'e' om het geselecteerde besturingssysteem te bewerken waarin u wilt opstarten (meestal de eerste regel)
1. Zoek de kernellijn - in Azure begint dit met`linux16`
1. Voeg `rd.break` toe aan het einde van de `rd.break` regel, zodat er een ruimte voor (zie voorbeeld hieronder)
    - Dit zal het opstartproces onderbreken `initramfs` `systemd`voordat de besturing wordt doorgegeven van naar , zoals beschreven in de Red Hat documentatie [hier](https://aka.ms/rhel7rootpassword).
1. Druk op Ctrl + X om af te sluiten en opnieuw op te starten met de toegepaste instellingen
1. Zodra u opstart, wordt u in de noodmodus gedropt met een alleen-lezen bestandssysteem. Voer `mount -o remount,rw /sysroot` de shell in om het hoofdbestandssysteem opnieuw te monteren met lees-/schrijfmachtigingen
1. Zodra u opstart in de `chroot /sysroot` modus voor `sysroot` één gebruiker, typt u in om over te schakelen naar de gevangenis
1. Je bent nu wortel. U uw hoofdwachtwoord opnieuw instellen en `passwd` vervolgens de bovenstaande instructies gebruiken om de modus voor één gebruiker in te voeren. Typ `reboot -f` om opnieuw op te starten zodra u klaar bent.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Opmerking: Als u de bovenstaande instructies doorloopt, wordt u in de `fstab`noodschaal gebracht, zodat u ook taken uitvoeren, zoals bewerken. De algemeen aanvaarde suggestie is echter om uw hoofdwachtwoord opnieuw in te stellen en dat te gebruiken om de modus voor één gebruiker in te voeren. 


## <a name="access-for-centos"></a>Toegang voor CentOS
Net als Red Hat Enterprise Linux vereist de modus voor één gebruiker in CentOS dat GRUB en de root-gebruiker moeten worden ingeschakeld. 

### <a name="grub-access-in-centos"></a>GRUB-toegang in CentOS
CentOS wordt geleverd met GRUB ingeschakeld uit de doos. Als u GRUB wilt `sudo reboot` invoeren, start u uw VM opnieuw op en drukt u op een toets. U ziet de GRUB scherm verschijnen.

### <a name="single-user-mode-in-centos"></a>Modus voor één gebruiker in CentOS
Volg de bovenstaande instructies voor RHEL om de modus voor één gebruiker in CentOS in te schakelen.

## <a name="access-for-ubuntu"></a>Toegang voor Ubuntu 
Ubuntu-afbeeldingen vereisen geen rootwachtwoord. Als het systeem wordt opgestart in de modus voor één gebruiker, u het gebruiken zonder extra referenties. 

### <a name="grub-access-in-ubuntu"></a>GRUB toegang in Ubuntu
Als u toegang wilt krijgen tot GRUB, houdt u 'Esc' ingedrukt terwijl de VM wordt opgestart. 

Ubuntu-afbeeldingen tonen standaard niet automatisch het GRUB-scherm. Dit kan worden gewijzigd met de volgende instructies:
1. Openen `/etc/default/grub.d/50-cloudimg-settings.cfg` in een teksteditor naar keuze
1. De `GRUB_TIMEOUT` waarde wijzigen in een waarde zonder nul
1. Openen `/etc/default/grub` in een teksteditor naar keuze
1. Commentaar uit `GRUB_HIDDEN_TIMEOUT=1` de regel
1. Voer `sudo update-grub` uit.

### <a name="single-user-mode-in-ubuntu"></a>Single user mode in Ubuntu
Ubuntu brengt u automatisch in de modus voor één gebruiker als deze niet normaal kan opstarten. Als u handmatig de modus voor één gebruiker wilt invoeren, gebruikt u de volgende instructies:

1. Druk vanuit GRUB op 'e' om je bootentry te bewerken (de Ubuntu-vermelding)
1. Zoek naar de lijn `linux`die begint met, dan op zoek naar`ro`
1. Voeg `single` `ro`na, ervoor te zorgen dat er een ruimte voor en na`single`
1. Druk op Ctrl + X om opnieuw op te starten met deze instellingen en voer de modus voor één gebruiker in

## <a name="access-for-coreos"></a>Toegang voor CoreOS
Voor de modus Eén gebruiker in CoreOS moet GRUB worden ingeschakeld. 

### <a name="grub-access-in-coreos"></a>GRUB-toegang in CoreOS
Als u toegang wilt krijgen tot GRUB, drukt u op een toets wanneer uw VM wordt opgestart.

### <a name="single-user-mode-in-coreos"></a>Modus voor één gebruiker in CoreOS
CoreOS brengt u automatisch in de modus voor één gebruiker als deze niet normaal kan worden opgestart. Als u handmatig de modus voor één gebruiker wilt invoeren, gebruikt u de volgende instructies:
1. Druk vanuit GRUB op 'e' om je bootentry te bewerken
1. Zoek naar de lijn `linux$`die begint met . Er moeten 2, ingekapseld in verschillende als / anders clausules
1. Toevoegen `coreos.autologin=ttyS0` aan het einde `linux$` van beide regels
1. Druk op Ctrl + X om opnieuw op te starten met deze instellingen en voer de modus voor één gebruiker in

## <a name="access-for-suse-sles"></a>Toegang voor SUSE SLES
Nieuwere beelden van SLES 12 SP3+ geven toegang via de seriële console voor het geval het systeem in noodmodus wordt opgestart. 

### <a name="grub-access-in-suse-sles"></a>GRUB toegang in SUSE SLES
GRUB-toegang in SLES vereist bootloader configuratie via YaST. Volg hiervoor de volgende instructies:

1. ssh in uw SLES `sudo yast bootloader`VM en draaien . Gebruik `tab` de `enter` toetsen van de toets, de toets en de pijlom door het menu te navigeren. 
1. Navigeer `Kernel Parameters`naar en `Use serial console`controleer . 
1. Toevoegen `serial --unit=0 --speed=9600 --parity=no` aan de argumenten console

1. Druk op F10 om uw instellingen en afsluiten op te slaan
1. Als u GRUB wilt invoeren, start u uw VM opnieuw op en drukt u op een toets tijdens de opstartvolgorde om GRUB op het scherm te laten blijven
    - De standaard time-out voor GRUB is 1s. U dit wijzigen `GRUB_TIMEOUT` door de variabele in`/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modus voor één gebruiker in SUSE SLES
U wordt automatisch in noodschulp gedropt als SLES niet normaal kan opstarten. Gebruik de volgende instructies om handmatig de noodhuls in te voeren:

1. Druk vanuit GRUB op 'e' om je bootentry te bewerken (de SLES-vermelding)
1. Zoek naar de kernel lijn zal beginnen met`linux`
1. Toevoegen `systemd.unit=emergency.target` aan het einde van de regel
1. Druk op Ctrl + X om opnieuw op te starten met deze instellingen en voer de noodshell in
   > Houd er rekening mee dat u in noodshell met een _alleen-lezen_ bestandssysteem wordt gedropt. Als u wijzigingen wilt aanbrengen in bestanden, moet u het bestandssysteem opnieuw monteren met lees-schrijfmachtigingen. Om dit te `mount -o remount,rw /` doen, voer in de shell

## <a name="access-for-oracle-linux"></a>Toegang voor Oracle Linux
Net als Red Hat Enterprise Linux, single user mode in Oracle Linux vereist GRUB en de root gebruiker worden ingeschakeld. 

### <a name="grub-access-in-oracle-linux"></a>GRUB-toegang in Oracle Linux
Oracle Linux wordt geleverd met GRUB ingeschakeld out of the box. Als u GRUB wilt `sudo reboot` invoeren, start u uw VM opnieuw op en drukt u op 'Esc'. U ziet de GRUB scherm verschijnen.

### <a name="single-user-mode-in-oracle-linux"></a>Modus voor één gebruiker in Oracle Linux
Volg de bovenstaande instructies voor RHEL om de modus voor één gebruiker in Oracle Linux in te schakelen.

## <a name="next-steps"></a>Volgende stappen
* De belangrijkste seriële console Linux documentatie pagina is [hier](serial-console.md)te vinden.
* Seriële console gebruiken voor [NMI- en SysRq-aanroepen](serial-console-nmi-sysrq.md)
* De seriële console is ook beschikbaar voor [Windows](../windows/serial-console.md) VM's
* Meer informatie over [opstartdiagnostiek](boot-diagnostics.md)
