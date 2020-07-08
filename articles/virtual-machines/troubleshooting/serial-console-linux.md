---
title: Azure Serial console voor Linux | Microsoft Docs
description: Bidirectionele seriële console voor Azure Virtual Machines en Virtual Machine Scale Sets.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: b1f7708c9bd213e201ba4eb8837a191dca68ca9e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77167022"
---
# <a name="azure-serial-console-for-linux"></a>Azure Serial Console voor Linux

De seriële console in de Azure Portal biedt toegang tot een op tekst gebaseerde console voor virtuele Linux-machines (Vm's) en exemplaren van virtuele-machine schaal sets. Deze seriële verbinding maakt verbinding met de ttyS0-seriële poort van de VM of de virtuele-machine Scale set-instantie, waardoor deze onafhankelijk is van de status van het netwerk of het besturings systeem. De seriële console is alleen toegankelijk via de Azure Portal en is alleen toegestaan voor gebruikers met een toegangs rol van Inzender of een hoger niveau voor virtuele-machine schaal sets.

Seriële console werkt op dezelfde manier voor Vm's en exemplaren van virtuele-machine schaal sets. In dit document bevatten alle vermeldingen aan Vm's impliciet instanties voor schaal sets voor virtuele machines, tenzij anders vermeld.

Zie [seriële console voor Windows](../windows/serial-console.md)voor informatie over de seriële console voor Windows.

> [!NOTE]
> De seriële console is algemeen beschikbaar in de wereld wijde Azure-regio's en in de open bare preview-versie van Azure Government. Het is nog niet beschikbaar in de Azure China-Cloud.


## <a name="prerequisites"></a>Vereisten

- De virtuele machine of het exemplaar van de VM-schaalset moet gebruikmaken van het Resource Management-implementatie model. Klassieke implementaties worden niet ondersteund.

- Uw account dat gebruikmaakt van seriële console, moet de [rol Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) hebben voor de VM en het opslag account voor [Diagnostische gegevens over opstarten](boot-diagnostics.md)

- Uw virtuele machine of exemplaar van de VM-schaalset moet een gebruiker met een wacht woord zijn. U kunt er een maken met de functie [wacht woord opnieuw instellen](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) van de extensie VM-toegang. Selecteer **wacht woord opnieuw instellen** in het gedeelte **ondersteuning en probleem oplossing** .

- Voor de virtuele machine of het exemplaar van de VM-schaalset moet [Diagnostische gegevens over opstarten](boot-diagnostics.md) zijn ingeschakeld.

    ![Instellingen voor diagnostische gegevens over opstarten](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Zie [seriële console Linux-distributie Beschik baarheid](#serial-console-linux-distribution-availability)voor instellingen die specifiek zijn voor Linux-distributies.

- Uw virtuele machine of exemplaar van de VM-schaalset moet worden geconfigureerd voor seriële uitvoer op `ttys0` . Dit is de standaard instelling voor Azure-installatie kopieën, maar u kunt dit ook controleren op aangepaste installatie kopieën. Details [hieronder](#custom-linux-images).


> [!NOTE]
> De seriële console vereist een lokale gebruiker met een geconfigureerd wacht woord. Vm's of virtuele-machine schaal sets die alleen met een open bare SSH-sleutel zijn geconfigureerd, kunnen zich niet aanmelden bij de seriële console. Als u een lokale gebruiker met een wacht woord wilt maken, gebruikt u de [uitbrei ding VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), die beschikbaar is in de portal door **wacht woord opnieuw instellen** in de Azure portal te selecteren en een lokale gebruiker met een wacht woord te maken.
> U kunt ook het beheerders wachtwoord opnieuw instellen in uw account door [grub te gebruiken om op te starten in de modus voor één gebruiker](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Beschik baarheid van Linux-distributie voor seriële console
Voor een juiste werking van de seriële console moet het gast besturingssysteem worden geconfigureerd om console berichten te lezen en te schrijven naar de seriële poort. Voor de meeste [goedgekeurde Azure Linux-distributies](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) is de seriële console standaard geconfigureerd. Als u **seriële console** selecteert in het gedeelte **ondersteuning en probleem oplossing** van de Azure Portal, hebt u toegang tot de seriële console.

> [!NOTE]
> Als u niets in de seriële console ziet, zorg er dan voor dat diagnostische gegevens over opstarten zijn ingeschakeld op de virtuele machine. Bij op te **geven** , worden vaak problemen opgelost waarbij er niets wordt weer gegeven in de seriële console.

Distributie      | Toegang tot seriële console
:-----------|:---------------------
Red Hat Enterprise Linux    | Seriële console toegang is standaard ingeschakeld.
CentOS      | Seriële console toegang is standaard ingeschakeld.
Debian      | Seriële console toegang is standaard ingeschakeld.
Ubuntu      | Seriële console toegang is standaard ingeschakeld.
CoreOS      | Seriële console toegang is standaard ingeschakeld.
SUSE        | Voor nieuwere SLES-installatie kopieën die beschikbaar zijn op Azure, is seriële console toegang standaard ingeschakeld. Als u oudere versies (10 of eerder) van SLES op Azure gebruikt, raadpleegt u het [KB-artikel](https://www.novell.com/support/kb/doc.php?id=3456486) om seriële console in te scha kelen.
Oracle Linux        | Seriële console toegang is standaard ingeschakeld.

### <a name="custom-linux-images"></a>Aangepaste Linux-installatie kopieën
Als u de seriële console voor uw aangepaste Linux VM-installatie kopie wilt inschakelen, schakelt u toegang tot de console in het bestand */etc/inittab* in om een Terminal op uit te voeren `ttyS0` . Bijvoorbeeld: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Mogelijk moet u ook een getty op ttyS0 starten. U kunt dit doen met `systemctl start serial-getty@ttyS0.service` .

U moet ook ttyS0 toevoegen als doel voor de seriële uitvoer. Voor meer informatie over het configureren van een aangepaste installatie kopie voor gebruik met de seriële console raadpleegt u de algemene systeem vereisten bij het [maken en uploaden van een Linux-VHD in azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Als u een aangepaste kernel bouwt, kunt u overwegen om deze kernel-vlaggen in te scha kelen: `CONFIG_SERIAL_8250=y` en `CONFIG_MAGIC_SYSRQ_SERIAL=y` . Het configuratie bestand bevindt zich doorgaans in het pad */boot/* .

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Algemene scenario's voor toegang tot de seriële console

Scenario          | Acties in de seriële console
:------------------|:-----------------------------------------
Gebroken *FSTAB* -bestand | Druk op **Enter** om door te gaan en gebruik een tekst editor om het *FSTAB* -bestand te herstellen. Mogelijk moet u zich in de modus voor één gebruiker bevindt. Zie voor meer informatie het gedeelte seriële console voor het [oplossen van fstab-problemen](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) en het [gebruik van seriële console voor toegang tot grub en de modus voor één gebruiker](serial-console-grub-single-user-mode.md).
Onjuiste firewall regels |  Als u iptables hebt geconfigureerd voor het blok keren van SSH-connectiviteit, kunt u de seriële console gebruiken om te communiceren met uw virtuele machine zonder SSH te hoeven uitvoeren. Meer informatie vindt u op de [pagina iptables man](https://linux.die.net/man/8/iptables).<br>Als uw firewall de SSH-toegang blokkeert, hebt u ook toegang tot de virtuele machine via de seriële console en kunt u de firewall opnieuw configureren. Meer informatie vindt u in de [documentatie over Firewall](https://firewalld.org/documentation/).
Beschadiging/controle van bestands systeem | Zie de sectie seriële console van [Azure Linux VM kan niet worden gestart vanwege bestandssysteem fouten](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) voor meer informatie over het oplossen van problemen met beschadigde bestands systemen met behulp van seriële console.
Problemen met SSH-configuratie | Toegang tot de seriële console en de instellingen te wijzigen. Seriële console kan worden gebruikt ongeacht de SSH-configuratie van een virtuele machine, omdat er geen netwerk verbinding nodig is voor de virtuele machine. Er is een probleemoplossings handleiding beschikbaar bij het [oplossen van problemen met ssh-verbindingen met een virtuele Azure Linux-machine die mislukt, fouten of geweigerd zijn](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Meer informatie vindt u op [gedetailleerde stappen voor het oplossen van problemen met een virtuele Linux-machine in azure](./detailed-troubleshoot-ssh-connection.md)
Interactie met bootloader | Start de virtuele machine vanuit de seriële console-Blade opnieuw op om toegang te krijgen tot GRUB op uw virtuele Linux-machine. Zie voor meer informatie en distributie informatie [gebruik seriële console gebruiken voor toegang tot grub en de modus voor één gebruiker](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>De seriële console uitschakelen

Standaard hebben alle abonnementen seriële console toegang ingeschakeld. U kunt de seriële console uitschakelen op het niveau van het abonnement of de VM/virtuele-machine schaalset. Ga voor gedetailleerde instructies naar [de Azure Serial console inschakelen en uitschakelen](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Seriële console beveiliging

### <a name="access-security"></a>Toegang tot beveiliging
Toegang tot de seriële console is beperkt tot gebruikers die de rol van toegang tot de [virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) of een hoger niveau van de virtuele machine hebben. Als uw Azure Active Directory-Tenant multi-factor Authentication (MFA) vereist, heeft toegang tot de seriële console ook MFA nodig omdat de toegang tot de seriële console via de [Azure Portal](https://portal.azure.com).

### <a name="channel-security"></a>Kanaal beveiliging
Alle gegevens die worden verzonden, worden versleuteld op de kabel.

### <a name="audit-logs"></a>Auditlogboeken
Alle toegang tot de seriële console is momenteel geregistreerd in de logboeken voor [Diagnostische gegevens over opstarten](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) van de virtuele machine. Toegang tot deze logboeken is eigendom van en beheerd door de beheerder van de virtuele Azure-machine.

> [!CAUTION]
> Er zijn geen toegangs wachtwoorden voor de console vastgelegd. Als opdrachten echter worden uitgevoerd in de-console bevatten of uitvoer wachtwoorden, geheimen, gebruikers namen of andere vormen van persoons gegevens (PII), worden deze geschreven naar de logboeken voor diagnostische opstart gegevens van de VM. Ze worden samen met alle andere zicht bare tekst geschreven, als onderdeel van de implementatie van de functie scroll back van de seriële console. Deze logboeken zijn circulaire en alleen personen met lees machtigingen voor het diagnostische-opslag account hebben toegang tot ze. Als u een gegevensfactory met geheimen of PII insluit, kunt u het beste SSH gebruiken tenzij de seriële console absoluut nood zakelijk is.

### <a name="concurrent-usage"></a>Gelijktijdig gebruik
Als een gebruiker is verbonden met de seriële console en een andere gebruiker de toegang tot dezelfde virtuele machine heeft gevraagd, wordt de verbinding met de eerste gebruiker verbroken en wordt de tweede gebruiker verbonden met dezelfde sessie.

> [!CAUTION]
> Dit betekent dat een gebruiker die de verbinding is verbroken niet afmeldt. De mogelijkheid om een afmelding af te dwingen bij het verbreken van de verbinding (met behulp van SIGHUP of soortgelijk mechanisme) bevindt zich nog in het schema Voor Windows is er een automatische time-out ingeschakeld in speciale beheer console (SAC); voor Linux kunt u echter de instelling time-out terminal configureren. Als u dit wilt doen, voegt `export TMOUT=600` u het bestand *. bash_profile* of *. profile* toe aan de gebruiker die u gebruikt om u aan te melden bij de-console. Deze instelling is een time-out voor de sessie na 10 minuten.

## <a name="accessibility"></a>Toegankelijkheid
Toegankelijkheid is een belang rijke focus voor de Azure Serial console. Daarom hebben we ervoor gezorgd dat de seriële console volledig toegankelijk is.

### <a name="keyboard-navigation"></a>Toetsenbordnavigatie
Gebruik de **Tab** -toets op het toetsen bord om te navigeren in de interface van de seriële console van de Azure Portal. Uw locatie wordt op het scherm gemarkeerd. Als u de focus van het venster van de seriële console wilt verlaten, drukt u op **CTRL** + **F6** op het toetsen bord.

### <a name="use-serial-console-with-a-screen-reader"></a>Seriële console gebruiken met een scherm lezer
De seriële console heeft ingebouwde ondersteuning voor scherm lezers. Door te navigeren met een ingeschakelde scherm lezer, kan de ALT-tekst voor de momenteel geselecteerde knop hardop worden gelezen door de scherm lezer.

## <a name="known-issues"></a>Bekende problemen
Er zijn enkele problemen met de seriële console en het besturings systeem van de virtuele machine. Hier volgt een lijst met deze problemen en stappen voor het beperken van de oplossing voor Linux-Vm's. Deze problemen en oplossingen zijn van toepassing voor zowel Vm's als virtuele-machine schaal sets. Als deze niet overeenkomen met de fout die u ziet, raadpleegt u de common Serial console-service fouten bij [veelvoorkomende seriële console fouten](./serial-console-errors.md).

Probleem                           |   Oplossing
:---------------------------------|:--------------------------------------------|
Wanneer u op **Enter** drukt na de verbindings banner, wordt er geen aanmeldings prompt weer gegeven. | GRUB is mogelijk niet juist geconfigureerd. Voer de volgende opdrachten uit: `grub2-mkconfig -o /etc/grub2-efi.cfg` en/of `grub2-mkconfig -o /etc/grub2.cfg` . Voor meer informatie, zie op ' door [voeren Enter ' niets](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Dit probleem kan optreden als u een aangepaste VM, een vrijgemaakt apparaat of een GRUB-configuratie uitvoert die ervoor zorgt dat Linux geen verbinding kan maken met de seriële poort.
Seriële console tekst neemt een deel van de scherm grootte in beslag (vaak na het gebruik van een tekst editor). | Seriële consoles bieden geen ondersteuning voor onderhandelen over de venster grootte ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)). Dit betekent dat er geen SIGWINCH-signaal wordt verzonden naar de scherm grootte van de update en dat de virtuele machine geen kennis heeft van de grootte van uw Terminal. Installeer xterm of een soortgelijk hulp programma om u de opdracht te geven `resize` en vervolgens uit te voeren `resize` .
Het plakken van lange teken reeksen werkt niet. | De seriële console beperkt de lengte van teken reeksen die in de terminal worden geplakt tot 2048 tekens om te voor komen dat de band breedte van de seriële poort wordt overbelast.
Er zijn geen toetsenbord invoer in SLES BYOS-installatie kopieën. Toetsenbord invoer wordt alleen sporadisch herkend. | Dit is een probleem met het Plymouth-pakket. Plymouth moet niet worden uitgevoerd in azure omdat u geen welkomst scherm nodig hebt en Plymouth de mogelijkheid biedt om de seriële console te gebruiken. Verwijder Plymouth met `sudo zypper remove plymouth` en start de computer opnieuw op. U kunt ook de kernel-regel van uw GRUB-configuratie wijzigen door `plymouth.enable=0` toe te voegen aan het einde van de regel. U kunt dit doen door [de opstart vermelding tijdens het opstarten te bewerken](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)of door de GRUB_CMDLINE_LINUX regel in `/etc/default/grub` te bewerken, grub opnieuw te maken met en vervolgens opnieuw op te `grub2-mkconfig -o /boot/grub2/grub.cfg` starten.


## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V. Hoe kan ik feedback verzenden?**

A. Geef feedback door het maken van een GitHub-probleem op https://aka.ms/serialconsolefeedback . Als alternatief (minder voor keur) kunt u feedback verzenden via azserialhelp@microsoft.com of in de virtuele-machine categorie van https://feedback.azure.com .

**V. ondersteunt de seriële console kopiëren/plakken?**

A. Ja. Gebruik **CTRL** + **SHIFT** + **C** en **CTRL** + **SHIFT** + **V** om de Terminal te kopiëren en plakken.

**V. kan ik seriële console gebruiken in plaats van een SSH-verbinding?**

A. Hoewel dit gebruik technisch mogelijk lijkt, is de seriële console voornamelijk bedoeld om te worden gebruikt als hulp programma voor probleem oplossing in situaties waarin connectiviteit via SSH niet mogelijk is. U wordt aangeraden de seriële console als SSH-vervanging te gebruiken om de volgende redenen:

- De seriële console heeft niet zoveel band breedte als SSH. Omdat het een alleen-tekst verbinding is, zijn er lastigere interacties in meerdere gebruikers.
- Seriële console toegang is momenteel alleen mogelijk als u een gebruikers naam en wacht woord gebruikt. Omdat SSH-sleutels veel veiliger zijn dan combi Naties van gebruikers namen en wacht woorden, wordt u aangeraden SSH via seriële console te maken.

**V. wie kan seriële console in-of uitschakelen voor mijn abonnement?**

A. Als u de seriële console op het niveau van een abonnement wilt in-of uitschakelen, moet u schrijf machtigingen hebben voor het abonnement. Rollen met schrijf machtiging zijn onder andere beheerders-of eigenaar rollen. Aangepaste rollen kunnen ook schrijf machtigingen hebben.

**V. wie kan toegang krijgen tot de seriële console voor mijn virtuele machine/VM-schaalset?**

A. U moet de rol Inzender voor virtuele machines of hoger hebben voor een VM of virtuele-machine schaalset om toegang te krijgen tot de seriële console.

**V. mijn seriële console geeft niets weer, wat moet ik doen?**

A. De installatie kopie is waarschijnlijk onjuist geconfigureerd voor toegang tot seriële consoles. Zie [seriële console Linux-distributie Beschik baarheid](#serial-console-linux-distribution-availability)voor meer informatie over het configureren van uw installatie kopie om de seriële console in te scha kelen.

**V. is de seriële console beschikbaar voor schaal sets voor virtuele machines?**

A. Ja dat is zo! Zie de [seriële console voor Virtual Machine Scale sets](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**V. als ik mijn VM of schaalset voor virtuele machines heb ingesteld met behulp van alleen SSH-sleutel verificatie, kan ik dan nog steeds de seriële console gebruiken om verbinding te maken met mijn VM/virtual machine Scale set-exemplaar?**

A. Ja. Omdat de seriële console geen SSH-sleutels nodig heeft, hoeft u alleen een combi natie van gebruikers naam en wacht woord in te stellen. U kunt dit doen door **wacht woord opnieuw instellen** in het Azure portal te selecteren en deze referenties te gebruiken om u aan te melden bij de seriële console.

## <a name="next-steps"></a>Volgende stappen
* Gebruik de seriële console om [toegang te krijgen tot de grub en de modus voor één gebruiker](serial-console-grub-single-user-mode.md).
* Gebruik de seriële console voor [NMI-en SYSRQ-aanroepen](serial-console-nmi-sysrq.md).
* Meer informatie over het gebruik van de seriële console om [grub in te scha kelen in verschillende distributies](serial-console-grub-proactive-configuration.md)
* De seriële console is ook beschikbaar voor [virtuele Windows-machines](../windows/serial-console.md).
* Meer informatie over [Diagnostische gegevens over opstarten](boot-diagnostics.md).

