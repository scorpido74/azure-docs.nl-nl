---
title: Azure seriële console voor GRUB en de modus voor één gebruiker
description: Met behulp van seriële console voor grub in azure virtual machines.
services: virtual-machines-linux
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 2aa7110ab4e52fdc5c3804bd27be5f41081fb435
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758507"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Seriële console gebruiken om toegang te krijgen tot GRUB en de modus voor één gebruiker
GRUB is de GRand Unified Bootloader. Vanuit GRUB kunt u de opstart configuratie wijzigen om onder andere de modus voor één gebruiker op te starten.

De modus voor één gebruiker is een minimale omgeving met minimale functionaliteit. Dit kan handig zijn voor het onderzoeken van opstart problemen, bestandssysteem problemen of netwerk problemen. Minder services kunnen op de achtergrond worden uitgevoerd en, afhankelijk van de runlevel, kan een bestands systeem niet zelfs automatisch worden gekoppeld.

De modus voor één gebruiker is ook handig in situaties waarin uw virtuele machine alleen mag worden geconfigureerd voor het accepteren van SSH-sleutels om u aan te melden. In dit geval kunt u mogelijk de modus voor één gebruiker gebruiken om een account met wachtwoord verificatie te maken.

Als u de modus voor één gebruiker wilt opgeven, moet u GRUB invoeren wanneer de virtuele machine wordt opgestart en de opstart configuratie in GRUB wijzigen. Dit kan worden gedaan met de seriële VM-console.

## <a name="general-grub-access"></a>Algemene GRUB-toegang
Voor toegang tot GRUB moet u de virtuele machine opnieuw opstarten terwijl de Blade seriële console geopend blijven. Voor sommige distributies is een toetsenbord invoer vereist om GRUB weer te geven, terwijl andere automatisch GRUB gedurende een paar seconden worden weer gegeven en de gebruikers toetsenbord invoer de time-out kan annuleren. 

U moet ervoor zorgen dat GRUB is ingeschakeld op uw virtuele machine om toegang te kunnen krijgen tot de modus voor één gebruiker. Afhankelijk van uw distributie is het mogelijk dat er een bepaalde installatie wordt uitgevoerd om ervoor te zorgen dat GRUB is ingeschakeld. Distributie-specifieke informatie is hieronder beschikbaar.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>Start uw VM opnieuw op om toegang te krijgen tot GRUB in de seriële console
Het opnieuw opstarten van uw VM met de Blade seriële console open kan worden uitgevoerd met `'b'` een SYSRQ-opdracht als [SYSRQ](./serial-console-nmi-sysrq.md) is ingeschakeld, of door te klikken op de knop opnieuw opstarten op de Blade overzicht (Open de virtuele machine in een nieuw browser tabblad om opnieuw op te starten zonder de Blade seriële console te sluiten). Volg de distributie-specifieke instructies hieronder voor meer informatie over wat u kunt verwachten van GRUB wanneer u de computer opnieuw opstart.

## <a name="general-single-user-mode-access"></a>Algemene toegang tot de modus voor één gebruiker
Er zijn mogelijk hand matige toegang tot de modus voor één gebruiker nodig in situaties waarin u geen account met wachtwoord verificatie hebt geconfigureerd. U moet de GRUB-configuratie wijzigen om de modus voor één gebruiker hand matig in te voeren. Als u dit hebt gedaan, raadpleegt u de modus voor één gebruiker gebruiken om een wacht woord opnieuw in te stellen of toe te voegen voor verdere instructies.

In gevallen waarin de virtuele machine niet kan worden opgestart, wordt door distributies vaak automatisch naar de modus voor één gebruiker of een nood herstel modus. Andere vereisen echter aanvullende instellingen voordat ze u automatisch naar de modus met één gebruiker of nood herstel kunnen verplaatsen (zoals het instellen van een basis wachtwoord).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>De modus voor één gebruiker gebruiken om een wacht woord opnieuw in te stellen of toe te voegen
Als u zich in de modus voor één gebruiker bevindt, gaat u als volgt te werk om een nieuwe gebruiker toe te voegen met sudo-bevoegdheden:
1. Uitvoeren `useradd <username>` om een gebruiker toe te voegen
1. Uitvoeren `sudo usermod -a -G sudo <username>` om de nieuwe gebruikers hoofd bevoegdheden te verlenen
1. Gebruiken `passwd <username>` om het wacht woord voor de nieuwe gebruiker in te stellen. U kunt zich vervolgens aanmelden als de nieuwe gebruiker

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Toegang voor Red Hat Enterprise Linux (RHEL)
RHEL gaat automatisch naar de modus voor één gebruiker als deze niet normaal kan worden opgestart. Als u echter geen hoofd toegang hebt ingesteld voor de modus voor één gebruiker, hebt u geen hoofd wachtwoord en kunt u zich niet aanmelden. Er is een tijdelijke oplossing (Zie ' hand matig toegang tot de modus voor één gebruiker maken ' hieronder), maar u kunt het voor stel het eerst gebruiken om toegang tot de hoofdmap in te stellen.

### <a name="grub-access-in-rhel"></a>GRUB-toegang in RHEL
RHEL wordt geleverd met GRUB ingeschakeld. Als u GRUB wilt invoeren, start u `sudo reboot` de VM opnieuw op en drukt u op een wille keurige toets. Het scherm GRUB wordt weer gegeven.

> Opmerking: Red Hat biedt ook documentatie voor opstarten in de herstel modus, de nood herstel modus, de foutopsporingsmodus en het opnieuw instellen van het hoofd wachtwoord. [Klik hier om het te openen](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Hoofd toegang instellen voor de modus voor één gebruiker in RHEL
Voor de modus voor één gebruiker in RHEL moet de hoofd gebruiker zijn ingeschakeld. deze is standaard uitgeschakeld. Als u de modus voor één gebruiker wilt inschakelen, gebruikt u de volgende instructies:

1. Meld u aan bij het Red Hat-systeem via SSH
1. Overschakelen naar hoofdmap
1. Wacht woord voor hoofd gebruiker inschakelen 
    * `passwd root`(een sterk wacht woord instellen)
1. Controleren of de hoofd gebruiker zich alleen kan aanmelden via ttyS0
    * `edit /etc/ssh/sshd_config`Zorg ervoor dat PermitRootLogIn is ingesteld op Nee
    * `edit /etc/securetty file`aanmelden via ttyS0 alleen toestaan 

Als het systeem wordt opgestart in de modus voor één gebruiker, kunt u zich aanmelden via het hoofd wachtwoord.

Als alternatief voor RHEL 7.4 + of 6,9 + kunt u in de GRUB-prompts de modus voor één gebruiker inschakelen. Raadpleeg [hier](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single) de instructies

### <a name="manually-enter-single-user-mode-in-rhel"></a>De modus voor één gebruiker hand matig invoeren in RHEL
Als u GRUB en toegang tot de hoofdmap hebt ingesteld met de bovenstaande instructies, kunt u de modus voor één gebruiker invoeren met de volgende instructies:

1. Druk op ESC tijdens het opnieuw starten van de virtuele machine om GRUB in te voeren
1. Druk in GRUB op ' e ' om het geselecteerde besturings systeem te bewerken waarop u wilt opstarten (doorgaans de eerste regel)
1. Zoek de kernel-regel-in Azure. dit begint met`linux16`
1. Druk op CTRL + E om naar het einde van de regel te gaan
1. Voeg het volgende toe aan het einde van de regel:`systemd.unit=rescue.target`
    * Hiermee wordt u opgestart naar de modus voor één gebruiker. Als u de nood herstel modus wilt gebruiken, `systemd.unit=emergency.target` voegt u aan het einde van de regel toe, in plaats van`systemd.unit=rescue.target`
1. Druk op CTRL + X om af te sluiten en opnieuw op te starten met de toegepaste instellingen
1. U wordt gevraagd om het beheerders wachtwoord voordat de modus voor één gebruiker kan worden opgegeven. Dit is het wacht woord dat u hebt gemaakt in de bovenstaande instructies    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>De modus voor één gebruiker opgeven zonder dat het hoofd account is ingeschakeld in RHEL
Als u de bovenstaande stappen niet hebt door lopen om de hoofd gebruiker in te scha kelen, kunt u uw basis wachtwoord nog steeds opnieuw instellen. Gebruik de volgende instructies:

> Opmerking: als u SELinux gebruikt, moet u ervoor zorgen dat u de aanvullende stappen hebt uitgevoerd die [hier](https://aka.ms/rhel7grubterminal) worden beschreven in de Red Hat-documentatie wanneer u het basis wachtwoord opnieuw instelt.

1. Druk op ESC tijdens het opnieuw starten van de virtuele machine om GRUB in te voeren
1. Druk in GRUB op ' e ' om het geselecteerde besturings systeem te bewerken waarop u wilt opstarten (doorgaans de eerste regel)
1. Zoek de kernel-regel-in Azure. dit begint met`linux16`
1. Voeg `rd.break` aan het einde van de regel toe, zodat er ruimte is voor `rd.break` (Zie het onderstaande voor beeld)
    - Hiermee wordt het opstart proces onderbroken voordat het besturings element wordt `initramfs` door `systemd`gegeven van naar, zoals beschreven in de documentatie van Red Hat [hier](https://aka.ms/rhel7rootpassword).
1. Druk op CTRL + X om af te sluiten en opnieuw op te starten met de toegepaste instellingen
1. Zodra u de computer hebt opgestart, wordt u in de nood herstel modus met een alleen-lezen bestands systeem verwijderd. Voer `mount -o remount,rw /sysroot` in de shell in om het hoofd bestands systeem opnieuw te koppelen met de machtigingen lezen/schrijven
1. Wanneer u de modus voor één gebruiker opstart, typt `chroot /sysroot` u in om over `sysroot` te scha kelen naar de jailbroken
1. U bent nu het hoofd. U kunt uw basis wachtwoord opnieuw instellen `passwd` met en vervolgens de bovenstaande instructies gebruiken om de modus voor één gebruiker in te voeren. Typ `reboot -f` om opnieuw op te starten wanneer u klaar bent.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Opmerking: door de bovenstaande instructies uit te voeren, gaat u naar de nood shell, zodat u ook taken zoals bewerken `fstab`kunt uitvoeren. De algemeen geaccepteerde suggestie is het opnieuw instellen van uw basis wachtwoord en gebruiken om de modus voor één gebruiker in te voeren. 


## <a name="access-for-centos"></a>Toegang voor CentOS
Net als Red Hat Enterprise Linux vereist de modus voor één gebruiker in CentOS GRUB en moet de hoofd gebruiker worden ingeschakeld. 

### <a name="grub-access-in-centos"></a>GRUB-toegang in CentOS
CentOS wordt geleverd met GRUB ingeschakeld. Als u GRUB wilt invoeren, start u `sudo reboot` de VM opnieuw op en drukt u op een wille keurige toets. Het scherm GRUB wordt weer gegeven.

### <a name="single-user-mode-in-centos"></a>Modus voor één gebruiker in CentOS
Volg de instructies voor RHEL hierboven om de modus voor één gebruiker in CentOS in te scha kelen.

## <a name="access-for-ubuntu"></a>Toegang voor Ubuntu 
Voor Ubuntu-installatie kopieën is geen hoofd wachtwoord vereist. Als het systeem wordt opgestart in de modus voor één gebruiker, kunt u dit zonder aanvullende referenties gebruiken. 

### <a name="grub-access-in-ubuntu"></a>GRUB-toegang in Ubuntu
Als u toegang wilt krijgen tot GRUB, houdt u ESC ingedrukt terwijl de virtuele machine wordt opgestart. 

Standaard worden in Ubuntu-afbeeldingen niet automatisch het scherm GRUB weer gegeven. U kunt dit wijzigen met de volgende instructies:
1. Open `/etc/default/grub.d/50-cloudimg-settings.cfg` in een tekst editor naar keuze
1. Wijzig de `GRUB_TIMEOUT` waarde in een andere waarde dan nul
1. Open `/etc/default/grub` in een tekst editor naar keuze
1. Commentaar toevoegen aan `GRUB_HIDDEN_TIMEOUT=1` de regel
1. Voer `sudo update-grub` uit.

### <a name="single-user-mode-in-ubuntu"></a>Modus voor één gebruiker in Ubuntu
Ubuntu gaat automatisch naar de modus voor één gebruiker als deze niet normaal kan worden opgestart. Gebruik de volgende instructies om de modus voor één gebruiker hand matig in te voeren:

1. Druk vanuit GRUB op ' e ' om de opstart vermelding (de Ubuntu-vermelding) te bewerken.
1. Zoek naar de regel die begint met `linux`en zoek naar`ro`
1. Erna `single` `ro`toevoegen, zodat er ruimte is voor en na`single`
1. Druk op CTRL + X om opnieuw op te starten met deze instellingen en voer de modus voor één gebruiker in

## <a name="access-for-coreos"></a>Toegang voor CoreOS
Voor de modus voor één gebruiker in CoreOS moet GRUB zijn ingeschakeld. 

### <a name="grub-access-in-coreos"></a>GRUB-toegang in CoreOS
Om toegang te krijgen tot GRUB, drukt u op een wille keurige toets wanneer de virtuele machine wordt opgestart.

### <a name="single-user-mode-in-coreos"></a>Modus voor één gebruiker in CoreOS
CoreOS gaat automatisch naar de modus voor één gebruiker als deze niet normaal kan worden opgestart. Gebruik de volgende instructies om de modus voor één gebruiker hand matig in te voeren:
1. Druk vanuit GRUB op ' e ' om de opstart vermelding te bewerken
1. Zoek naar de regel die begint met `linux$`. Er moet 2 zijn ingekapseld in verschillende if/else-componenten
1. Toevoegen `coreos.autologin=ttyS0` aan het einde van beide `linux$` regels
1. Druk op CTRL + X om opnieuw op te starten met deze instellingen en voer de modus voor één gebruiker in

## <a name="access-for-suse-sles"></a>Toegang voor SUSE SLES
Nieuwere installatie kopieën van SLES 12 SP3 + bieden toegang via de seriële console als het systeem wordt opgestart in de nood herstel modus. 

### <a name="grub-access-in-suse-sles"></a>GRUB-toegang in SUSE SLES
GRUB-toegang in SLES vereist configuratie van bootloader via YaST. Volg hiervoor de volgende instructies:

1. SSH in uw SLES-VM en `sudo yast bootloader`Voer uit. Gebruik de `tab` sleutel- `enter` , sleutel-en PIJL toetsen om door het menu te navigeren. 
1. Navigeer naar `Kernel Parameters`en controleer `Use serial console`. 
1. Toevoegen `serial --unit=0 --speed=9600 --parity=no` aan de-console argumenten

1. Druk op F10 om uw instellingen op te slaan en af te sluiten
1. Als u GRUB wilt invoeren, start u de virtuele machine opnieuw op en drukt u op een wille keurige toets tijdens de opstart procedure om GRUB op het scherm
    - De standaardtime-outwaarde voor GRUB is 1S. U kunt dit wijzigen door de `GRUB_TIMEOUT` variabele in te wijzigen in`/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modus voor één gebruiker in SUSE SLES
Als SLES niet normaal kan worden opgestart, wordt u automatisch verwijderd naar de nood situatie. Gebruik de volgende instructies om de nood-shell hand matig in te voeren:

1. Druk vanuit GRUB op ' e ' om de opstart vermelding (de SLES-vermelding) te bewerken.
1. Zoek naar de kernel-regel die wordt gestart met`linux`
1. Toevoegen `systemd.unit=emergency.target` aan het einde van de regel
1. Druk op CTRL + X om opnieuw op te starten met deze instellingen en voer nood herstel in
   > Houd er rekening mee dat u met een _alleen-lezen_ bestands systeem in een nood geval wordt verwijderd. Als u wijzigingen wilt aanbrengen in bestanden, moet u het bestands systeem opnieuw koppelen met de machtigingen lezen/schrijven. Als u dit wilt doen `mount -o remount,rw /` , voert u de shell in

## <a name="access-for-oracle-linux"></a>Toegang voor Oracle Linux
Net als Red Hat Enterprise Linux vereist de modus voor één gebruiker in Oracle Linux GRUB en de hoofd gebruiker moeten worden ingeschakeld. 

### <a name="grub-access-in-oracle-linux"></a>GRUB toegang in Oracle Linux
Oracle Linux is GRUB ingeschakeld. Als u GRUB wilt invoeren, start u `sudo reboot` de VM opnieuw op en drukt u op ESC. Het scherm GRUB wordt weer gegeven.

### <a name="single-user-mode-in-oracle-linux"></a>Modus voor één gebruiker in Oracle Linux
Volg de instructies voor RHEL hierboven om de modus voor één gebruiker in Oracle Linux in te scha kelen.

## <a name="next-steps"></a>Volgende stappen
* De documentatie pagina voor de hoofd console Linux bevindt zich [hier](serial-console.md).
* Seriële console gebruiken voor [NMI-en SYSRQ-aanroepen](serial-console-nmi-sysrq.md)
* De seriële console is ook beschikbaar voor virtuele [Windows](../windows/serial-console.md) -machines
* Meer informatie over [Diagnostische gegevens over opstarten](boot-diagnostics.md)
