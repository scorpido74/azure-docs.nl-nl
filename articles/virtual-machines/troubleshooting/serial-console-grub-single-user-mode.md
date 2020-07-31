---
title: Azure seriële console voor GRUB en modus voor één gebruiker | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de seriële console gebruikt voor GRUB in azure virtual machines.
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
ms.openlocfilehash: 5341cc62a7d02c3072df90becf893dec18427ac2
ms.sourcegitcommit: 14bf4129a73de2b51a575c3a0a7a3b9c86387b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87439544"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Seriële console gebruiken om toegang te krijgen tot de GRUB en de modus voor één gebruiker
GRand Unified Bootloader (GRUB) is waarschijnlijk het eerste wat u ziet wanneer u een virtuele machine opstart (VM). Omdat deze wordt weer gegeven voordat het besturings systeem is gestart, is GRUB niet toegankelijk via SSH. In GRUB kunt u de opstart configuratie wijzigen zodat deze wordt opgestart in de modus voor één gebruiker, onder andere.

De modus voor één gebruiker is een minimale omgeving met minimale functionaliteit. Dit kan handig zijn voor het onderzoeken van opstart problemen, problemen met het bestands systeem of netwerk problemen. Minder services kunnen op de achtergrond worden uitgevoerd en, afhankelijk van de runlevel, kan een bestands systeem niet zelfs automatisch worden gekoppeld.

De modus voor één gebruiker is ook handig in situaties waarin uw virtuele machine kan worden geconfigureerd om alleen SSH-sleutels voor aanmelding te accepteren. In dit geval kunt u mogelijk de modus voor één gebruiker gebruiken om een account met wachtwoord verificatie te maken.

> [!NOTE]
> De seriële console service heeft alleen toegang tot de seriële console van een virtuele machine voor gebruikers met een *Inzender* niveau of hogere machtiging.

Als u de modus voor één gebruiker wilt invoeren, voert u GRUB in wanneer de virtuele machine wordt opgestart en wijzigt u de opstart configuratie in GRUB. Zie de gedetailleerde instructies voor het invoeren van GRUB in de volgende sectie. Als uw virtuele machine in het algemeen is geconfigureerd om GRUB weer te geven, kunt u de knop opnieuw starten in de seriële console van uw VM gebruiken om de virtuele machine opnieuw te starten en GRUB weer te geven.

![De knop voor het opnieuw opstarten van de Linux-seriële console](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Algemene GRUB-toegang
Als u toegang wilt krijgen tot GRUB, start u de VM opnieuw op terwijl het deel venster seriële console is geopend. Voor sommige distributies is toetsenbord invoer vereist om GRUB weer te geven, en andere worden automatisch GRUB gedurende een paar seconden weer gegeven zodat gebruikers toetsenbord invoer de time-out kan annuleren.

Als u toegang wilt krijgen tot de modus voor één gebruiker, moet u ervoor zorgen dat GRUB is ingeschakeld op de virtuele machine. Afhankelijk van uw distributie kan het nodig zijn om ervoor te zorgen dat GRUB is ingeschakeld. Zie de volgende sectie voor informatie die specifiek is voor distributie.

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>De virtuele machine opnieuw opstarten om toegang te krijgen tot GRUB in de seriële console
U kunt de virtuele machine opnieuw opstarten binnen een seriële console door de muis aanwijzer over de knop **opnieuw opstarten** te wijzen en vervolgens **VM opnieuw starten**te selecteren. Onder aan het deel venster wordt een melding weer gegeven over het opnieuw opstarten.

U kunt ook de virtuele machine opnieuw starten door een SysRq "b" opdracht uit te voeren als [SYSRQ](./serial-console-nmi-sysrq.md) is ingeschakeld. Zie de distributie-specifieke instructies in de volgende secties voor meer informatie over wat u kunt verwachten van GRUB wanneer u de computer opnieuw opstart.

![Linux-seriële console opnieuw opstarten](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Algemene toegang voor de modus met één gebruiker
U moet mogelijk hand matige toegang hebben tot de modus voor één gebruiker wanneer u geen account met wachtwoord verificatie hebt geconfigureerd. Wijzig de GRUB-configuratie om de modus voor één gebruiker hand matig in te voeren. Nadat u dit hebt gedaan, gaat u naar de sectie ' de modus voor één gebruiker gebruiken om een wacht woord opnieuw in te stellen of toe te voegen ' voor verdere instructies.

Als de virtuele machine niet kan worden opgestart, worden de distributies vaak automatisch naar de modus met één gebruiker of in de nood herstel modus. Andere distributies vereisen echter aanvullende instellingen, zoals het instellen van een basis wachtwoord, voordat ze u automatisch naar de modus met één gebruiker of nood herstel kunnen verplaatsen.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Gebruik de modus voor één gebruiker om een wacht woord opnieuw in te stellen of toe te voegen
Nadat u zich in de modus voor één gebruiker bevindt, kunt u als volgt een nieuwe gebruiker met sudo-bevoegdheden toevoegen:
1. Voer uit `useradd <username>` om een gebruiker toe te voegen.
1. Voer uit `sudo usermod -a -G sudo <username>` om de nieuwe gebruikers hoofd bevoegdheden te verlenen.
1. Gebruiken `passwd <username>` om het wacht woord voor de nieuwe gebruiker in te stellen. U kunt zich vervolgens aanmelden als de nieuwe gebruiker.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Toegang voor Red Hat Enterprise Linux (RHEL)
Als RHEL niet normaal kan worden opgestart, wordt u automatisch naar de modus voor één gebruiker verzonden. Als u echter geen hoofd toegang hebt ingesteld voor de modus voor één gebruiker, hebt u geen hoofd wachtwoord en kunt u zich niet aanmelden. Er is een tijdelijke oplossing (Zie de sectie ' hand matig een modus voor één gebruiker opgeven in RHEL '), maar we raden u echter aan om in eerste instantie hoofd toegang in te stellen.

### <a name="grub-access-in-rhel"></a>GRUB-toegang in RHEL
RHEL wordt geleverd met GRUB ingeschakeld. Als u GRUB wilt invoeren, start u de VM opnieuw op door uit te voeren `sudo reboot` en drukt u op een wille keurige toets. Het deel venster GRUB moet worden weer gegeven. Als dat niet het geval is, moet u ervoor zorgen dat de volgende regels aanwezig zijn in uw GRUB-bestand ( `/etc/default/grub` ):

**Voor RHEL 8**

>[!NOTE]
> Red Hat raadt aan om grubby te gebruiken voor het configureren van kernel-opdracht regel parameters in RHEL 8 +. Het is momenteel niet mogelijk om de grub-out en Terminal parameters bij te werken met grubby. Als u het argument GRUB_CMDLINE_LINUX voor alle opstart vermeldingen wilt wijzigen, voert u uit `grubby --update-kernel=ALL --args="console=ttyS0,115200 console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"` . Meer informatie vindt u [hier](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel).

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
> Red Hat biedt ook documentatie voor het opstarten van de opstart modus, de nood herstel modus of de foutopsporingsmodus, en voor het opnieuw instellen van het wacht woord voor de hoofdmap. Zie voor instructies [Terminal menu bewerken tijdens het opstarten](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Hoofd toegang instellen voor de modus voor één gebruiker in RHEL
De hoofd gebruiker is standaard uitgeschakeld. Voor de modus voor één gebruiker in RHEL moet de hoofd gebruiker worden ingeschakeld. Als u de modus voor één gebruiker wilt inschakelen, gebruikt u de volgende instructies:

1. Meld u via SSH aan bij het Red Hat-systeem.
1. Schakel over naar de hoofdmap.
1. Schakel het wacht woord voor de hoofd gebruiker in door het volgende te doen:
    * Uitvoeren `passwd root` (Stel een sterk hoofd wachtwoord in).
1. Zorg ervoor dat de hoofd gebruiker zich alleen kan aanmelden via ttyS0 door het volgende te doen: a. Voer uit `edit /etc/ssh/sshd_config` en zorg ervoor dat PermitRootLogIn is ingesteld op `no` .
    b. Voer `edit /etc/securetty file` alleen uit om de aanmelding via ttyS0 toe te staan.

Als het systeem nu wordt opgestart in de modus voor één gebruiker, kunt u zich aanmelden met het hoofd wachtwoord.

U kunt ook voor RHEL 7.4 + of 6,9 + de modus voor één gebruiker inschakelen in de GRUB-prompts, Zie [opstarten naar de modus voor één gebruiker](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>De modus voor één gebruiker hand matig invoeren in RHEL
Als u GRUB en toegang tot de hoofdmap hebt ingesteld met behulp van de voor gaande instructies, kunt u de modus voor één gebruiker als volgt opgeven:

1. Als u GRUB wilt invoeren, drukt u op ESC wanneer u de virtuele machine opnieuw opstart.
1. Druk in GRUB op E om het besturings systeem te bewerken waarop u wilt opstarten. Het besturings systeem wordt doorgaans op de eerste regel weer gegeven.
1. Zoek de kernel-regel. In azure begint deze met *linux16*.
1. Druk op CTRL + E om naar het einde van de regel te gaan.
1. Voeg aan het einde van de regel *systemed. unit = hulpverlening. target*toe.

    Met deze actie wordt de modus voor één gebruiker opgestart. Als u de nood herstel modus wilt gebruiken, voegt u *systemed. unit = Emergency. target* toe aan het einde van de regel (in plaats van *systemed. unit = hulpverlening. target*).

1. Druk op CTRL + X om af te sluiten en opnieuw op te starten met de toegepaste instellingen.

   U wordt gevraagd om het beheerders wachtwoord voordat u de modus voor één gebruiker kunt invoeren. Dit wacht woord is de naam die u hebt gemaakt in de vorige instructies.

    ![Afbeelding met animatie met een opdracht regel interface. De gebruiker selecteert een server, zoekt het einde van de kernel-regel en voert vervolgens de opgegeven tekst in.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Voer de modus voor één gebruiker in zonder dat het hoofd account is ingeschakeld in RHEL
Als u de hoofd gebruiker niet hebt ingeschakeld door de eerdere instructies te volgen, kunt u het basis wachtwoord nog steeds opnieuw instellen door het volgende te doen:

> [!NOTE]
> Als u SELinux gebruikt, moet u, wanneer u het basis wachtwoord opnieuw instelt, de extra stappen volgen die worden beschreven in de [Red Hat-documentatie](https://aka.ms/rhel7grubterminal).

1. Als u GRUB wilt invoeren, drukt u op ESC wanneer u de virtuele machine opnieuw opstart.

1. Druk in GRUB op E om het besturings systeem te bewerken waarop u wilt opstarten. Het besturings systeem wordt doorgaans op de eerste regel weer gegeven.
1. Zoek de kernel-regel. In azure begint deze met *linux16*.
1. Voeg aan het einde van de regel *rd. Store* toe aan het einde van de regel. Plaats een spatie tussen de kernel-regel en *rd. Restore*.

    Met deze actie wordt het opstart proces onderbroken voordat het besturings element wordt door gegeven van `initramfs` naar `systemd` , zoals beschreven in de [Red Hat-documentatie](https://aka.ms/rhel7rootpassword).
1. Druk op CTRL + X om af te sluiten en opnieuw op te starten met de toegepaste instellingen.

   Nadat u opnieuw hebt opgestart, wordt u in de nood herstel modus met een alleen-lezen bestands systeem verwijderd.

1. Voer in de shell `mount -o remount,rw /sysroot` het hoofd bestands systeem opnieuw koppelen aan de machtigingen lezen/schrijven.
1. Nadat u de modus voor één gebruiker hebt opgestart, voert u in `chroot /sysroot` om over te scha kelen naar de `sysroot` jailbroken.
1. U bent nu het hoofd. U kunt het wacht woord opnieuw instellen door in te voeren `passwd` en vervolgens de voor gaande instructies gebruiken om de modus voor één gebruiker in te voeren.
1. Wanneer u klaar bent, voert u deze `reboot -f` in om opnieuw op te starten.

![Afbeelding met animatie met een opdracht regel interface. De gebruiker selecteert een server, zoekt het einde van de kernel-regel en voert de opgegeven opdrachten in.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> Door de voor gaande instructies uit te voeren, gaat u naar de nood shell, zodat u ook taken zoals bewerken kunt uitvoeren `fstab` . Normaal gesp roken wordt u echter aangeraden om uw basis wachtwoord opnieuw in te stellen en dit te gebruiken om de modus voor één gebruiker in te voeren.

## <a name="access-for-centos"></a>Toegang voor CentOS
Net als Red Hat Enterprise Linux vereist de modus voor één gebruiker in CentOS GRUB en de hoofd gebruiker om in te scha kelen.

### <a name="grub-access-in-centos"></a>GRUB-toegang in CentOS
CentOS wordt geleverd met GRUB ingeschakeld. Als u GRUB wilt invoeren, start u de VM opnieuw op door in te voeren `sudo reboot` en drukt u op een wille keurige toets. Met deze actie wordt het deel venster GRUB weer gegeven.

### <a name="single-user-mode-in-centos"></a>Modus voor één gebruiker in CentOS
Als u de modus voor één gebruiker in CentOS wilt inschakelen, volgt u de eerdere instructies voor RHEL.

## <a name="access-for-ubuntu"></a>Toegang voor Ubuntu
Voor Ubuntu-installatie kopieën is geen hoofd wachtwoord vereist. Als het systeem wordt opgestart in de modus voor één gebruiker, kunt u dit zonder aanvullende referenties gebruiken.

### <a name="grub-access-in-ubuntu"></a>GRUB-toegang in Ubuntu
Als u toegang wilt krijgen tot GRUB, houdt u ESC ingedrukt terwijl de virtuele machine wordt opgestart.

Standaard wordt in Ubuntu-afbeeldingen het deel venster GRUB mogelijk niet automatisch weer gegeven. U kunt de instelling als volgt wijzigen:
1. Open het */etc/default/grub.d/50-cloudimg-Settings.cfg* -bestand in een tekst editor.

1. Wijzig de `GRUB_TIMEOUT` waarde in een andere waarde dan nul.
1. Open */etc/default/grub*in een tekst editor.
1. Opmerkingen bij de `GRUB_HIDDEN_TIMEOUT=1` regel.
1. Zorg ervoor dat er een `GRUB_TIMEOUT_STYLE=menu` regel is.
1. Voer `sudo update-grub` uit.

### <a name="single-user-mode-in-ubuntu"></a>Modus voor één gebruiker in Ubuntu
Als Ubuntu niet normaal kan worden opgestart, wordt u automatisch naar de modus voor één gebruiker verzonden. Ga als volgt te werk om de modus voor één gebruiker hand matig in te voeren:

1. Druk in GRUB op E om de opstart vermelding (de Ubuntu-vermelding) te bewerken.
1. Zoek naar de regel die begint met *Linux*en zoek vervolgens naar *ro*.
1. Voeg *één* na *ro*toe en zorg ervoor dat er een spatie voor en na *één*wordt gemaakt.
1. Druk op CTRL + X om opnieuw op te starten met deze instellingen en de modus voor één gebruiker in te voeren.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>GRUB gebruiken om bash aan te roepen in Ubuntu
Nadat u de voor gaande instructies hebt uitgevoerd, is er mogelijk een situatie (zoals een verg eten basis wachtwoord) waar u nog steeds geen toegang hebt tot de modus voor één gebruiker in uw Ubuntu-VM. U kunt ook aangeven dat de kernel moet worden uitgevoerd `/bin/bash` als init, in plaats van het systeem init. Deze actie geeft u een bash-shell en maakt systeem onderhoud mogelijk. Gebruik de volgende instructies:

1. Druk in GRUB op E om de opstart vermelding (de Ubuntu-vermelding) te bewerken.

1. Zoek naar de regel die begint met *Linux*en zoek vervolgens naar *ro*.
1. Vervang *ro* door *rw init =/bin/bash*.

    Met deze actie wordt uw bestands systeem gekoppeld aan de lees-en schrijf bewerkingen en wordt `/bin/bash` het gebruikt als het init-proces.
1. Druk op CTRL + X om opnieuw op te starten met deze instellingen.

## <a name="access-for-coreos"></a>Toegang voor CoreOS
Voor de modus voor één gebruiker in CoreOS moet GRUB zijn ingeschakeld.

### <a name="grub-access-in-coreos"></a>GRUB-toegang in CoreOS
Om toegang te krijgen tot GRUB, drukt u op een wille keurige toets terwijl uw virtuele machine wordt opgestart.

### <a name="single-user-mode-in-coreos"></a>Modus voor één gebruiker in CoreOS
Als CoreOS niet normaal kan worden opgestart, wordt u automatisch naar de modus voor één gebruiker verzonden. Ga als volgt te werk om de modus voor één gebruiker hand matig in te voeren:

1. Druk in GRUB op E om de opstart vermelding te bewerken.

1. Zoek naar de regel die begint met *Linux $*. Er moeten twee exemplaren van de regel zijn die zijn ingekapseld in een andere *if... else* -component.
1. Voeg *coreos. autologin = ttyS0* toe aan het einde van elke *Linux $* -regel.
1. Druk op CTRL + X om opnieuw op te starten met deze instellingen en de modus voor één gebruiker in te voeren.

## <a name="access-for-suse-sles"></a>Toegang voor SUSE SLES
Nieuwere installatie kopieën van SLES 12 SP3 + bieden toegang via de seriële console als het systeem wordt opgestart in de nood herstel modus.

### <a name="grub-access-in-suse-sles"></a>GRUB-toegang in SUSE SLES
GRUB-toegang in SLES vereist een configuratie van de bootloader via YaST. Ga als volgt te werk om de configuratie te maken:

1. Gebruik SSH om u aan te melden bij uw SLES-VM en voer vervolgens uit `sudo yast bootloader` . Druk op tab, druk op ENTER en gebruik vervolgens de pijl toetsen om door het menu te navigeren.

1. Ga naar **kernel-para meters**en schakel het selectie vakje **seriële console gebruiken** in.
1. Toevoegen `serial --unit=0 --speed=9600 --parity=no` aan de- **console** argumenten.
1. Druk op F10 om uw instellingen op te slaan en af te sluiten.
1. Als u GRUB wilt invoeren, start u de virtuele machine opnieuw op en drukt u op een wille keurige toets tijdens de opstart procedure om het deel venster GRUB weer te geven.

    De standaardtime-outwaarde voor GRUB is **1s**. U kunt deze instelling wijzigen door de `GRUB_TIMEOUT` variabele in het */etc/default/grub* -bestand te wijzigen.

![De configuratie van de bootloader initialiseren](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modus voor één gebruiker in SUSE SLES
Als SLES niet normaal kan worden opgestart, wordt u automatisch verwijderd naar de nood-shell. Ga als volgt te werk om de nood shell hand matig in te voeren:

1. Druk in GRUB op E om de opstart vermelding (de SLES-vermelding) te bewerken.

1. Zoek naar de kernel-regel die begint met *Linux*.
1. Voeg *systemed. unit = Emergency. target* toe aan het einde van de kernel-regel.
1. Druk op CTRL + X om opnieuw op te starten met deze instellingen en voer de nood situatie in.

   > [!NOTE]
   > Met deze actie wordt u naar de nood situatie met een alleen-lezen bestands systeem verzonden. Als u bestanden wilt bewerken, moet u het bestands systeem opnieuw koppelen met de machtigingen lezen/schrijven. Als u dit wilt doen, voert u `mount -o remount,rw /` in de shell in.

## <a name="access-for-oracle-linux"></a>Toegang voor Oracle Linux
Net als Red Hat Enterprise Linux, is voor de modus voor één gebruiker in Oracle Linux GRUB vereist en moet de hoofd gebruiker worden ingeschakeld.

### <a name="grub-access-in-oracle-linux"></a>GRUB toegang in Oracle Linux
Oracle Linux is GRUB ingeschakeld. Als u GRUB wilt invoeren, start u de VM opnieuw op door uit te voeren `sudo reboot` en drukt u vervolgens op ESC. Met deze actie wordt het deel venster GRUB weer gegeven. Als het deel venster GRUB niet wordt weer gegeven, controleert u of de waarde van de `GRUB_TERMINAL` regel een *seriële console* bevat (dat wil zeggen `GRUB_TERMINAL="serial console"` ). Maak GRUB opnieuw met `grub2-mkconfig -o /boot/grub/grub.cfg` .

### <a name="single-user-mode-in-oracle-linux"></a>Modus voor één gebruiker in Oracle Linux
Volg de eerdere instructies voor RHEL om de modus voor één gebruiker in Oracle Linux in te scha kelen.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over seriële console:
* [Documentatie voor Linux-seriële console](serial-console-linux.md)
* [Seriële console gebruiken om GRUB in diverse distributies in te scha kelen](http://linuxonazure.azurewebsites.net/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Seriële console gebruiken voor NMI-en SysRq-aanroepen](serial-console-nmi-sysrq.md)
* [Seriële console voor Windows-Vm's](serial-console-windows.md)
* [Diagnostische gegevens over opstarten](boot-diagnostics.md)
