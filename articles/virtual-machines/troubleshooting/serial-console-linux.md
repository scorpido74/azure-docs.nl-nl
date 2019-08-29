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
ms.openlocfilehash: f6e08f113e29b44e4ec94d14624d62c1c3d48d45
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124467"
---
# <a name="azure-serial-console-for-linux"></a>Azure Serial console voor Linux

De seriële console in de Azure Portal biedt toegang tot een op tekst gebaseerde console voor virtuele Linux-machines (Vm's) en exemplaren van virtuele-machine schaal sets. Deze seriële verbinding maakt verbinding met de ttyS0-seriële poort van de VM of de virtuele-machine Scale set-instantie, waardoor deze onafhankelijk is van de status van het netwerk of het besturings systeem. De seriële console is alleen toegankelijk via de Azure Portal en is alleen toegestaan voor gebruikers met een toegangs rol van Inzender of een hoger niveau voor virtuele-machine schaal sets.

Seriële console werkt op dezelfde manier voor Vm's en exemplaren van virtuele-machine schaal sets. In dit document bevatten alle vermeldingen aan Vm's impliciet instanties voor schaal sets voor virtuele machines, tenzij anders vermeld.

Zie [seriële console voor Windows](../windows/serial-console.md)voor informatie over de seriële console voor Windows.

> [!NOTE]
> De seriële console is algemeen beschikbaar in de wereld wijde Azure-regio's. Het is nog niet beschikbaar in de Azure government en Azure China clouds.


## <a name="prerequisites"></a>Vereisten

- De virtuele machine of het exemplaar van de VM-schaalset moet gebruikmaken van het Resource Management-implementatie model. Klassieke implementaties worden niet ondersteund.

- Uw account dat gebruikmaakt van seriële console, moet de [rol Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) hebben voor de VM en het opslag account voor [Diagnostische gegevens over opstarten](boot-diagnostics.md)

- Uw virtuele machine of exemplaar van de VM-schaalset moet een gebruiker met een wacht woord zijn. U kunt maken met de [wachtwoord opnieuw instellen](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) functie van de VM-extensie voor toegang. Selecteer **wachtwoord opnieuw instellen** uit de **ondersteuning en probleemoplossing** sectie.

- Voor de virtuele machine of het exemplaar van de VM-schaalset moet [Diagnostische gegevens over opstarten](boot-diagnostics.md) zijn ingeschakeld.

    ![De instellingen voor diagnostische gegevens over opstarten](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Zie [seriële console Linux-distributie Beschik baarheid](#serial-console-linux-distribution-availability)voor instellingen die specifiek zijn voor Linux-distributies.

- Uw virtuele machine of exemplaar van de VM-schaalset moet worden geconfigureerd voor `ttys0`seriële uitvoer op. Dit is de standaard instelling voor Azure-installatie kopieën, maar u kunt dit ook controleren op aangepaste installatie kopieën. Details [hieronder](#custom-linux-images).


> [!NOTE]
> De seriële console vereist een lokale gebruiker met een geconfigureerd wacht woord. Vm's of virtuele-machine schaal sets die alleen met een open bare SSH-sleutel zijn geconfigureerd, kunnen zich niet aanmelden bij de seriële console. Als u een lokale gebruiker met een wacht woord wilt maken, gebruikt u de [uitbrei ding VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), die beschikbaar is in de portal door **wacht woord opnieuw instellen** in de Azure portal te selecteren en een lokale gebruiker met een wacht woord te maken.
> U kunt ook het beheerders wachtwoord opnieuw instellen in uw account door [grub te gebruiken om op te starten in de modus voor één gebruiker](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Beschik baarheid van Linux-distributie voor seriële console
Voor een juiste werking van de seriële console moet het gast besturingssysteem worden geconfigureerd om console berichten te lezen en te schrijven naar de seriële poort. Voor de meeste [goedgekeurde Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -distributies is de seriële console standaard geconfigureerd. Als u **seriële console** selecteert in het gedeelte **ondersteuning en probleem oplossing** van de Azure Portal, hebt u toegang tot de seriële console.

> [!NOTE]
> Als u niets in de seriële console ziet, zorg er dan voor dat diagnostische gegevens over opstarten zijn ingeschakeld op de virtuele machine. Bij op te **geven** , worden vaak problemen opgelost waarbij er niets wordt weer gegeven in de seriële console.

Distributie      | Toegang tot seriële console
:-----------|:---------------------
Red Hat Enterprise Linux    | Seriële console toegang is standaard ingeschakeld.
CentOS      | Seriële console toegang is standaard ingeschakeld.
Ubuntu      | Seriële console toegang is standaard ingeschakeld.
CoreOS      | Seriële console toegang is standaard ingeschakeld.
SUSE        | Voor nieuwere SLES-installatie kopieën die beschikbaar zijn op Azure, is seriële console toegang standaard ingeschakeld. Als u oudere versies (10 of eerder) van SLES op Azure gebruikt, raadpleegt u het [KB-artikel](https://www.novell.com/support/kb/doc.php?id=3456486) om seriële console in te scha kelen.
Oracle Linux        | Seriële console toegang is standaard ingeschakeld.

### <a name="custom-linux-images"></a>Aangepaste Linux-installatie kopieën
Als u de seriële console voor uw aangepaste Linux VM-installatie kopie wilt inschakelen, schakelt u toegang tot de console in het `ttyS0`bestand/etc/inittab in om een Terminal op uit te voeren. Bijvoorbeeld: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Mogelijk moet u ook een getty op ttyS0 starten. U kunt dit doen met `systemctl start serial-getty@ttyS0.service`.

U moet ook ttyS0 toevoegen als doel voor de seriële uitvoer. Voor meer informatie over het configureren van een aangepaste installatie kopie voor gebruik met de seriële console raadpleegt u de algemene systeem vereisten bij het [maken en uploaden van een Linux-VHD in azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Als u een aangepaste kernel bouwt, kunt u overwegen om deze kernel- `CONFIG_SERIAL_8250=y` vlaggen `CONFIG_MAGIC_SYSRQ_SERIAL=y`in te scha kelen: en. Het configuratie bestand bevindt zich doorgaans in het pad */boot/* .

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Algemene scenario's voor toegang tot de seriële console

Scenario          | Acties in de seriële console
:------------------|:-----------------------------------------
Gebroken *FSTAB* -bestand | Druk op **Enter** om door te gaan en gebruik een tekst editor om het *FSTAB* -bestand te herstellen. Mogelijk moet u zich in de modus voor één gebruiker bevindt. Zie voor meer informatie het gedeelte seriële console voor het [oplossen van fstab-problemen](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) en het [gebruik van seriële console voor toegang tot grub en de modus voor één gebruiker](serial-console-grub-single-user-mode.md).
Onjuiste firewall-regels |  Als u iptables hebt geconfigureerd voor het blok keren van SSH-connectiviteit, kunt u de seriële console gebruiken om te communiceren met uw virtuele machine zonder SSH te hoeven uitvoeren. Meer informatie vindt u op de [pagina iptables man](https://linux.die.net/man/8/iptables).<br>Als uw firewall de SSH-toegang blokkeert, hebt u ook toegang tot de virtuele machine via de seriële console en kunt u de firewall opnieuw configureren. Meer informatie vindt u in de [documentatie over Firewall](https://firewalld.org/documentation/).
Bestandssysteem beschadigd/selectievakje | Zie de sectie seriële console van [Azure Linux VM kan niet worden gestart vanwege bestandssysteem fouten](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) voor meer informatie over het oplossen van problemen met beschadigde bestands systemen met behulp van seriële console.
Problemen met SSH-configuratie | Toegang tot de seriële console en de instellingen wijzigen. Seriële console kan worden gebruikt ongeacht de SSH-configuratie van een virtuele machine, omdat er geen netwerk verbinding nodig is voor de virtuele machine. Er is een probleemoplossings handleiding beschikbaar bij het [oplossen van problemen met ssh-verbindingen met een virtuele Azure Linux-machine die mislukt, fouten of geweigerd zijn](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Meer informatie vindt u op [gedetailleerde stappen voor het oplossen van problemen met een virtuele Linux-machine in azure](./detailed-troubleshoot-ssh-connection.md)
Interactie met de bootloader | Start de virtuele machine vanuit de seriële console-Blade opnieuw op om toegang te krijgen tot GRUB op uw virtuele Linux-machine. Zie voor meer informatie en distributie informatie [gebruik seriële console gebruiken voor toegang tot grub en de modus voor één gebruiker](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>De seriële console uitschakelen

Standaard hebben alle abonnementen seriële console toegang ingeschakeld. U kunt de seriële console uitschakelen op het niveau van het abonnement of de VM/virtuele-machine schaalset. Ga voor gedetailleerde instructies naar [de Azure Serial console inschakelen en uitschakelen](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Seriële console-beveiliging

### <a name="access-security"></a>Beveiliging voor toegang
Toegang tot de seriële console is beperkt tot gebruikers die beschikken over een toegangsrol van [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) of hoger op de virtuele machine. Als uw Azure Active Directory-tenant is multi-factor authentication (MFA) vereist, wordt toegang tot de seriële console ook de MFA moet omdat de seriële console-toegang via de [Azure-portal](https://portal.azure.com).

### <a name="channel-security"></a>Beveiliging van het kanaal
Alle gegevens die worden verzonden heen en weer worden versleuteld op de kabel.

### <a name="audit-logs"></a>Controlelogboeken
Alle toegang tot de seriële console momenteel is aangemeld de [diagnostische gegevens over opstarten](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) logboeken van de virtuele machine. Toegang tot deze logboeken zijn eigendom van en beheerd door de beheerder van de virtuele machine van Azure.

> [!CAUTION]
> Er zijn geen wachtwoorden voor toegang voor de console worden geregistreerd. Echter, als opdrachten worden uitgevoerd binnen de console bevat of uitvoer van wachtwoorden, geheimen, gebruikersnamen of enige andere vorm van persoonlijk identificeerbare informatie (PII), die wordt geschreven naar de VM boot diagnostics-Logboeken. Ze worden geschreven, samen met andere zichtbare tekst, als onderdeel van de implementatie van de seriële console Schuif terug functie. Deze logboeken zijn circulaire en alleen personen met leesmachtigingen voor het opslagaccount voor diagnostische gegevens over de toegang tot hebben. We raden echter aan na de aanbevolen procedure van het gebruik van de extern bureaublad voor alles wat die hebben mogelijk betrekking op geheimen en/of PII.

### <a name="concurrent-usage"></a>Gelijktijdig gebruik
Als een gebruiker is verbonden met de seriële console en een andere gebruiker is toegang tot deze virtuele machine met dezelfde aanvraagt, wordt de eerste gebruiker verbroken en wordt de tweede gebruiker heeft verbinding gemaakt met dezelfde sessie.

> [!CAUTION]
> Dit betekent dat een gebruiker die niet verbonden wordt niet worden afgemeld. De mogelijkheid om een afmelding af te dwingen bij het verbreken van de verbinding (met behulp van SIGHUP of soortgelijk mechanisme) bevindt zich nog in het schema Voor Windows is er een automatische time-out ingeschakeld in speciale beheer console (SAC); voor Linux kunt u echter de instelling time-out terminal configureren. Als u dit wilt doen `export TMOUT=600` , voegt u in uw *. bash_profile* -of *. profile* -bestand toe aan de gebruiker die u gebruikt om u aan te melden bij de-console. Deze instelling is een time-out voor de sessie na 10 minuten.

## <a name="accessibility"></a>Toegankelijkheid
Toegankelijkheid is een belang rijke focus voor de Azure Serial console. Daarom hebben we ervoor gezorgd dat de seriële console volledig toegankelijk is.

### <a name="keyboard-navigation"></a>Toetsenbordnavigatie
Gebruik de **tabblad** sleutel op het toetsenbord om te navigeren in de interface van de seriële console van de Azure-portal. Uw locatie wordt gemarkeerd op het scherm worden weergegeven. Als u wilt de focus van de seriële console-venster laten, drukt u op **Ctrl**+**F6** op het toetsenbord.

### <a name="use-serial-console-with-a-screen-reader"></a>Seriële console gebruiken met een scherm lezer
De seriële console heeft ingebouwde ondersteuning voor schermlezers. Navigeren om met een schermlezer ingeschakeld, kunnen de alt-tekst voor de geselecteerde knop om te worden door de schermlezer voorgelezen.

## <a name="known-issues"></a>Bekende problemen
We zijn op de hoogte van problemen met de seriële console. Hier volgt een lijst van deze problemen beschreven en stappen voor risicobeperking. Deze problemen en oplossingen zijn van toepassing voor zowel Vm's als virtuele-machine schaal sets.

Probleem                           |   Oplossing
:---------------------------------|:--------------------------------------------|
Drukken **Enter** nadat de banner van de verbinding niet leidt een aanmeldingsprompt tot moet worden weergegeven. | Zie voor meer informatie, [Hitting invoeren, gebeurt er niets](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Dit probleem kan optreden als u een aangepaste VM, een vrijgemaakt apparaat of een GRUB-configuratie uitvoert die ervoor zorgt dat Linux geen verbinding kan maken met de seriële poort.
Seriële console tekst neemt een deel van de scherm grootte in beslag (vaak na het gebruik van een tekst editor). | Seriële consoles bieden geen ondersteuning voor onderhandelen over de venster grootte ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)). Dit betekent dat er geen SIGWINCH-signaal wordt verzonden naar de scherm grootte van de update en dat de virtuele machine geen kennis heeft van de grootte van uw Terminal. Installeer xterm of een soortgelijk hulp programma om u de `resize` opdracht te geven en vervolgens uit te voeren. `resize`
Lange tekenreeksen plakken werkt niet. | De seriële console beperkt de lengte van tekenreeksen in de terminal naar 2048 tekens om te voorkomen dat de seriële poort-bandbreedte overbelasten geplakt.
Seriële console werkt niet met een firewall voor opslag accounts. | Seriële console per ontwerp kan niet werken met firewalls voor opslag accounts die zijn ingeschakeld in het opslag account voor diagnostische gegevens over opstarten.
Seriële console werkt niet met een opslag account met behulp van Azure Data Lake Storage Gen2 met hiërarchische naam ruimten. | Dit is een bekend probleem met hiërarchische naam ruimten. Als u wilt beperken, moet u ervoor zorgen dat het opslag account voor diagnostische gegevens over opstarten van de virtuele machine niet is gemaakt met behulp van Azure Data Lake Storage Gen2. Deze optie kan alleen worden ingesteld bij het maken van een opslag account. Mogelijk moet u een afzonderlijk opslag account voor diagnostische gegevens over opstarten maken zonder dat Azure Data Lake Storage Gen2 ingeschakeld om dit probleem te verhelpen.
Er zijn geen toetsenbord invoer in SLES BYOS-installatie kopieën. Toetsenbord invoer wordt alleen sporadisch herkend. | Dit is een probleem met het Plymouth-pakket. Plymouth moet niet worden uitgevoerd in azure omdat u geen welkomst scherm nodig hebt en Plymouth de mogelijkheid biedt om de seriële console te gebruiken. Verwijder Plymouth met `sudo zypper remove plymouth` en start de computer opnieuw op. U kunt ook de kernel-regel van uw grub-configuratie wijzigen door `plymouth.enable=0` toe te voegen aan het einde van de regel. U kunt dit doen door [de opstart vermelding tijdens het opstarten te bewerken](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)of door de GRUB_CMDLINE_LINUX-regel in `/etc/default/grub`te bewerken, grub opnieuw `grub2-mkconfig -o /boot/grub2/grub.cfg`te maken met en vervolgens opnieuw op te starten.


## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Q. Hoe kan ik feedback verzenden?**

A. Geef feedback door het maken van een GitHub https://aka.ms/serialconsolefeedback -probleem op. U kunt ook (minder bij voorkeur), kunt u feedback via verzenden azserialhelp@microsoft.com of in de categorie van de virtuele machine van https://feedback.azure.com.

**Q. Biedt ondersteuning voor de seriële console kopiëren/plakken?**

A. Ja. Gebruik **Ctrl**+**Shift**+**C** en **Ctrl**+**Shift** + **V** kopiëren en plakken in de terminal.

**Q. Kan ik een seriële console gebruiken in plaats van een SSH-verbinding?**

A. Hoewel dit gebruik technisch mogelijk lijkt, is de seriële console voornamelijk bedoeld om te worden gebruikt als hulp programma voor probleem oplossing in situaties waarin connectiviteit via SSH niet mogelijk is. U wordt aangeraden de seriële console als SSH-vervanging te gebruiken om de volgende redenen:

- De seriële console heeft niet zoveel band breedte als SSH. Omdat het een alleen-tekst verbinding is, zijn er lastigere interacties in meerdere gebruikers.
- Seriële console toegang is momenteel alleen mogelijk als u een gebruikers naam en wacht woord gebruikt. Omdat SSH-sleutels veel veiliger zijn dan combi Naties van gebruikers namen en wacht woorden, wordt u aangeraden SSH via seriële console te maken.

**Q. Wie kan de seriële console in-of uitschakelen voor mijn abonnement?**

A. Als u wilt in- of uitschakelen van de seriële console op het niveau van een brede, door het abonnement, moet u hebt schrijfmachtigingen voor het abonnement. Rollen die gemachtigd schrijven bevatten beheerder of eigenaar rollen. Aangepaste rollen kunnen ook schrijfmachtigingen hebben.

**Q. Wie heeft toegang tot de seriële console voor mijn virtuele machine/VM-schaalset?**

A. U moet de rol Inzender voor virtuele machines of hoger hebben voor een VM of virtuele-machine schaalset om toegang te krijgen tot de seriële console.

**Q. Mijn seriële console van alles zijn, niet wordt weergegeven wat moet ik doen?**

A. Uw installatiekopie is waarschijnlijk niet goed is geconfigureerd voor toegang tot de seriële console. Zie [seriële console Linux-distributie Beschik baarheid](#serial-console-linux-distribution-availability)voor meer informatie over het configureren van uw installatie kopie om de seriële console in te scha kelen.

**Q. Is de seriële console beschikbaar voor virtuele-machineschaalsets?**

A. Ja dat is zo! Zie de [seriële console voor Virtual Machine Scale sets](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**Q. Als ik mijn VM of virtuele-machine schaalset stel met alleen authenticatie van SSH-sleutel, kan ik dan nog steeds de seriële console gebruiken om verbinding te maken met mijn virtuele machine voor de VM-schaalset?**

A. Ja. Omdat de seriële console geen SSH-sleutels nodig heeft, hoeft u alleen een combi natie van gebruikers naam en wacht woord in te stellen. U kunt dit doen door **wacht woord opnieuw instellen** in het Azure portal te selecteren en deze referenties te gebruiken om u aan te melden bij de seriële console.

## <a name="next-steps"></a>Volgende stappen
* Gebruik de seriële console om [toegang te krijgen tot de grub en de modus voor één gebruiker](serial-console-grub-single-user-mode.md).
* Gebruik de seriële console voor [NMI-en SYSRQ](serial-console-nmi-sysrq.md)-aanroepen.
* Meer informatie over het gebruik van de seriële console om [grub in te scha kelen in verschillende distributies](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* De seriële console is ook beschikbaar voor [virtuele Windows-machines](../windows/serial-console.md).
* Meer informatie over [diagnostische gegevens over opstarten](boot-diagnostics.md).

