---
title: Azure Serial Console voor Linux | Microsoft Documenten
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167022"
---
# <a name="azure-serial-console-for-linux"></a>Azure Serial Console voor Linux

De seriële console in de Azure-portal biedt toegang tot een tekstgebaseerde console voor Virtuele Linux-machines (VM's) en exemplaren van virtuele machineschaalsets. Deze seriële verbinding maakt verbinding met de ttys0-seriële poort van de vm- of virtuele machineschaalsetinstantie, waardoor er toegang tot wordt geboden, onafhankelijk van de status van het netwerk of het besturingssysteem. De seriële console is alleen toegankelijk via de Azure-portal en is alleen toegestaan voor gebruikers die een toegangsrol van inzender of hoger hebben tot de VM- of virtuele machineschaalset.

Serial Console werkt op dezelfde manier voor VM's en virtuele machineschaalset-instanties. In dit document bevatten alle vermeldingen aan VM's impliciet exemplaren van virtuele machineschaalsets, tenzij anders vermeld.

Zie [Seriële console voor Windows voor](../windows/serial-console.md)seriële consoledocumentatie voor Windows .

> [!NOTE]
> De seriële console is algemeen beschikbaar in algemene Azure-regio's en in openbare preview in Azure Government. Het is nog niet beschikbaar in de Azure China-cloud.


## <a name="prerequisites"></a>Vereisten

- Uw vm- of virtuele machineschaalset-instantie moet het implementatiemodel voor resourcebeheer gebruiken. Klassieke implementaties worden niet ondersteund.

- Uw account dat gebruikmaakt van seriële console moet de [rol Virtuele machineinzender](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) hebben voor de VM en het opslagaccount [voor opstartdiagnostiek](boot-diagnostics.md)

- Uw vm- of virtuele machineschaalset-instantie moet een op een wachtwoord gebaseerde gebruiker hebben. U er een maken met de [wachtwoordfunctie voor opnieuw instellen](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) van de VM-toegangsextensie. Selecteer **Wachtwoord opnieuw instellen** in de sectie Ondersteuning + **probleemoplossing.**

- De instantie van de vm- of virtuele machineschaalset moet [opstartdiagnose](boot-diagnostics.md) hebben ingeschakeld.

    ![Instellingen voor diagnostische gegevens opstarten](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Zie [Beschikbaarheid van Seriële console Linux-distributie](#serial-console-linux-distribution-availability)voor specifieke instellingen voor Linux-distributie.

- De instantie vm- of virtuele machineschaalset moet `ttys0`zijn geconfigureerd voor seriële uitvoer op . Dit is de standaardinstelling voor Azure-afbeeldingen, maar u wilt dit dubbel controleren op aangepaste afbeeldingen. Details [hieronder](#custom-linux-images).


> [!NOTE]
> De seriële console vereist een lokale gebruiker met een geconfigureerd wachtwoord. VM's of virtuele machineschaalsets die alleen zijn geconfigureerd met een Openbare SSH-sleutel, kunnen zich niet aanmelden bij de seriële console. Als u een lokale gebruiker met een wachtwoord wilt maken, gebruikt u de [VMAccess-extensie](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), die beschikbaar is in de portal door **wachtwoord opnieuw instellen** in de Azure-portal te selecteren en een lokale gebruiker met een wachtwoord te maken.
> U het beheerderswachtwoord in uw account ook opnieuw instellen door [GRUB te gebruiken om op te starten in de modus voor één gebruiker.](./serial-console-grub-single-user-mode.md)

## <a name="serial-console-linux-distribution-availability"></a>Beschikbaarheid van Seriële Console Linux-distributie
Om de seriële console goed te laten functioneren, moet het gastbesturingssysteem zijn geconfigureerd om consoleberichten naar de seriële poort te lezen en te schrijven. De meeste [goedgekeurde Azure Linux-distributies](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) hebben standaard de seriële console geconfigureerd. Als **u seriële console** selecteert in het gedeelte Ondersteuning + **probleemoplossing** van de Azure-portal, krijgt u toegang tot de seriële console.

> [!NOTE]
> Als u niets ziet in de seriële console, controleert u of opstartdiagnose is ingeschakeld op uw vm. Als u **op Enter raakt,** worden vaak problemen opgelost waarbij er niets wordt weergegeven in de seriële console.

Distributie      | Toegang tot seriële console
:-----------|:---------------------
Red Hat Enterprise Linux    | Seriële consoletoegang standaard ingeschakeld.
CentOS      | Seriële consoletoegang standaard ingeschakeld.
Debian      | Seriële consoletoegang standaard ingeschakeld.
Ubuntu      | Seriële consoletoegang standaard ingeschakeld.
CoreOS      | Seriële consoletoegang standaard ingeschakeld.
SUSE        | Nieuwere SLES-afbeeldingen die beschikbaar zijn op Azure hebben standaard toegang tot seriële console.Newer SLES images available on Azure have serial console access enabled by default. Als u oudere versies (10 of eerder) Van SLES op Azure gebruikt, raadpleegt u het [KB-artikel](https://www.novell.com/support/kb/doc.php?id=3456486) om seriële console in te schakelen.
Oracle Linux        | Seriële consoletoegang standaard ingeschakeld.

### <a name="custom-linux-images"></a>Aangepaste Linux-afbeeldingen
Als u de seriële console voor uw aangepaste Linux VM-afbeelding wilt inschakelen, `ttyS0`schakelt u consoletoegang in het bestand */etc/inittab in* om een terminal op te voeren. Bijvoorbeeld: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Het kan ook nodig zijn om een getty op ttyS0 paaien. Dit kan worden `systemctl start serial-getty@ttyS0.service`gedaan met .

U wilt ook ttys0 toevoegen als bestemming voor seriële uitvoer. Zie de algemene systeemvereisten bij [Maken en uploaden van een Linux VHD in Azure voor](https://aka.ms/createuploadvhd#general-linux-system-requirements)meer informatie over het configureren van een aangepaste afbeelding om met de seriële console te werken.

Als u een aangepaste kernel bouwt, `CONFIG_SERIAL_8250=y` u `CONFIG_MAGIC_SYSRQ_SERIAL=y`deze kernelvlaggen inschakelen: en. Het configuratiebestand bevindt zich meestal in het */boot/pad.*

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Veelvoorkomende scenario's voor toegang tot de seriële console

Scenario          | Acties in de seriële console
:------------------|:-----------------------------------------
Gebroken *FSTAB-bestand* | Druk op **Enter** om door te gaan en gebruik een teksteditor om het *FSTAB-bestand* op te lossen. Mogelijk moet u in de modus voor één gebruiker zijn om dit te doen. Zie voor meer informatie het gedeelte van de seriële console van [Hoe fstab-problemen op te lossen](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) en [seriële console gebruiken om toegang te krijgen tot GRUB en de modus voor één gebruiker.](serial-console-grub-single-user-mode.md)
Onjuiste firewallregels |  Als u iptables hebt geconfigureerd om SSH-connectiviteit te blokkeren, u seriële console gebruiken om met uw VM te communiceren zonder SSH nodig te hebben. Meer details zijn te vinden op de [iptables man pagina](https://linux.die.net/man/8/iptables).<br>Als uw firewalld ssh-toegang blokkeert, u de VM openen via de seriële console en firewalls opnieuw configureren. Meer details zijn te vinden in de [firewalld documentatie.](https://firewalld.org/documentation/)
Corruptie/controle van bestandssysteem | Zie het gedeelte seriële console van [Azure Linux VM kan niet worden gestart vanwege fouten in het bestandssysteem](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) voor meer informatie over het oplossen van beschadigde bestandssystemen met behulp van seriële console.
SSH-configuratieproblemen | Toegang tot de seriële console en wijzig de instellingen. Seriële console kan worden gebruikt, ongeacht de SSH-configuratie van een VM, omdat de VM niet hoeft te werken met een netwerkverbinding. Er is een handleiding voor probleemoplossing beschikbaar bij [Het oplossen van SSH-verbindingen met een Azure Linux VM die mislukt, fouten uitmaakt of wordt geweigerd.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection) Meer details zijn beschikbaar bij [gedetailleerde SSH-probleemoplossingsstappen voor problemen met een Linux-vm in Azure](./detailed-troubleshoot-ssh-connection.md)
Interactie met bootloader | Start uw VM opnieuw vanuit het seriële consoleblad om toegang te krijgen tot GRUB op uw Linux-vm. Zie [Seriële console gebruiken om toegang te krijgen tot GRUB en de modus voor één gebruiker voor](serial-console-grub-single-user-mode.md)meer informatie en distro-specifieke informatie.

## <a name="disable-the-serial-console"></a>De seriële console uitschakelen

Standaard hebben alle abonnementen toegang tot seriële console's ingeschakeld. U de seriële console uitschakelen op abonnementsniveau of vm/virtuele machineschaalsetniveau. Ga voor gedetailleerde instructies naar [De Azure Serial Console inschakelen en uitschakelen.](./serial-console-enable-disable.md)

## <a name="serial-console-security"></a>Seriële consolebeveiliging

### <a name="access-security"></a>Toegangsbeveiliging
De toegang tot de seriële console is beperkt tot gebruikers die een toegangsrol van [virtuele machinebijdrager](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) of hoger tot de virtuele machine hebben. Als uw Azure Active Directory-tenant multifactorauthenticatie (MFA) vereist, heeft toegang tot de seriële console ook MFA nodig omdat de toegang van de seriële console via de [Azure-portal](https://portal.azure.com)is.

### <a name="channel-security"></a>Kanaalbeveiliging
Alle gegevens die heen en weer worden verzonden, worden versleuteld op de draad.

### <a name="audit-logs"></a>Auditlogboeken
Alle toegang tot de seriële console is momenteel ingelogd in de [boot diagnostics](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) logs van de virtuele machine. Toegang tot deze logboeken is eigendom van en wordt beheerd door de beheerder van de virtuele machine van Azure.

> [!CAUTION]
> Er worden geen toegangswachtwoorden voor de console geregistreerd. Als opdrachten die binnen de console worden uitgevoerd, echter wachtwoorden, geheimen, gebruikersnamen of enige andere vorm van persoonlijk identificeerbare informatie (PII) bevatten of uitvoeren, worden deze naar de vm-opstartdiagnoselogboeken geschreven. Ze zullen worden geschreven samen met alle andere zichtbare tekst, als onderdeel van de uitvoering van de scroll back-functie van de seriële console. Deze logboeken zijn cirkelvormig en alleen personen met leesmachtigingen voor het opslagaccount voor diagnostiek hebben er toegang toe. Als u gegevens or-opdrachten invoert die geheimen of PII bevatten, raden we u aan SSH te gebruiken, tenzij seriële console absoluut noodzakelijk is.

### <a name="concurrent-usage"></a>Gelijktijdig gebruik
Als een gebruiker is verbonden met de seriële console en een andere gebruiker met succes toegang vraagt tot diezelfde virtuele machine, wordt de eerste gebruiker losgekoppeld en wordt de tweede gebruiker verbonden met dezelfde sessie.

> [!CAUTION]
> Dit betekent dat een gebruiker die de verbinding heeft verbroken, niet wordt uitgelogd. De mogelijkheid om een afmelding af te dwingen bij het verbreken (met behulp van SIGHUP of een soortgelijk mechanisme) is nog steeds op de roadmap. Voor Windows is er een automatische time-out ingeschakeld in Speciale administratieve console (SAC); Voor Linux u echter de terminaltime-outinstelling configureren. Voeg hiervoor uw `export TMOUT=600` *.bash_profile-* of *.profile-bestand* toe voor de gebruiker die u gebruikt om zich aan te melden bij de console. Deze instelling geeft een time-out van de sessie na 10 minuten.

## <a name="accessibility"></a>Toegankelijkheid
Toegankelijkheid is een belangrijke focus voor de Azure Serial Console. Daarom hebben we ervoor gezorgd dat de seriële console volledig toegankelijk is.

### <a name="keyboard-navigation"></a>Toetsenbordnavigatie
Gebruik de **Tab-toets** op het toetsenbord om te navigeren in de seriële console-interface vanuit de Azure-portal. Uw locatie wordt gemarkeerd op het scherm. Als u de focus van het seriële consolevenster wilt verlaten, drukt u op **Ctrl**+**F6** op het toetsenbord.

### <a name="use-serial-console-with-a-screen-reader"></a>Seriële console gebruiken met een schermlezer
De seriële console heeft schermlezer ondersteuning ingebouwd. Als u rondnavigeert met een schermlezer ingeschakeld, kan de alternatieve tekst voor de momenteel geselecteerde knop hardop worden voorgelezen door de schermlezer.

## <a name="known-issues"></a>Bekende problemen
We zijn ons bewust van een aantal problemen met de seriële console en het besturingssysteem van de VM. Hier is een lijst van deze problemen en stappen voor mitigatie voor Linux VM's. Deze problemen en oplossingen zijn van toepassing op zowel VM's als exemplaren van virtuele machineschaalsets. Als deze niet overeenkomen met de fout die u ziet, raadpleegt u de algemene fouten in de seriële consoleservice bij [algemene seriële consolefouten.](./serial-console-errors.md)

Probleem                           |   Oplossing
:---------------------------------|:--------------------------------------------|
Als u op **Enter** drukt nadat de verbindingsbanner is weergegeven, wordt er geen aanmeldingsprompt weergegeven. | GRUB kan niet correct zijn geconfigureerd. Voer de volgende `grub2-mkconfig -o /etc/grub2-efi.cfg` opdrachten uit: en/of `grub2-mkconfig -o /etc/grub2.cfg`. Zie [Slaan doet niets voor](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)meer informatie. Dit probleem kan optreden als u een aangepast VM-, geharde toestel of GRUB-config gebruikt waardoor Linux geen verbinding maakt met de seriële poort.
Seriële consoletekst neemt slechts een deel van de schermgrootte in beslag (vaak na gebruik van een teksteditor). | Seriële consoles geen ondersteuning voor onderhandelingen over venstergrootte ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), wat betekent dat er geen SIGWINCH signaal verzonden naar het scherm grootte bijwerken en de VM zal geen kennis van de grootte van uw terminal hebben. Installeer xterm of een soortgelijk hulpprogramma `resize` om u `resize`de opdracht te geven en voer vervolgens uit.
Lange snaren plakken werkt niet. | De seriële console beperkt de lengte van tekenreeksen die in de terminal zijn geplakt tot 2048 tekens om overbelasting van de bandbreedte van de seriële poort te voorkomen.
Onregelmatige toetsenbordinvoer in SLES BYOS-afbeeldingen. Toetsenbordinvoer wordt slechts sporadisch herkend. | Dit is een probleem met het Plymouth-pakket. Plymouth mag niet in Azure worden uitgevoerd omdat u geen welkomstscherm nodig hebt en Plymouth de platformmogelijkheid om Serial Console te gebruiken verstoort. Verwijder Plymouth `sudo zypper remove plymouth` met en start vervolgens opnieuw op. U ook de kernellijn van uw `plymouth.enable=0` GRUB-config wijzigen door aan het einde van de regel te worden toegevoegd. U dit doen door [het bewerken van de boot entry bij het opstarten,](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)of door het bewerken van de GRUB_CMDLINE_LINUX lijn in, `/etc/default/grub`wederopbouw GRUB met `grub2-mkconfig -o /boot/grub2/grub.cfg`, en vervolgens opnieuw opstarten.


## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V. Hoe kan ik feedback sturen?**

A. Geef feedback door een GitHub-probleem te maken op https://aka.ms/serialconsolefeedback. Als alternatief (minder gewenst), kunt azserialhelp@microsoft.com u feedback sturen https://feedback.azure.comvia of in de virtuele machine categorie van .

**V. Ondersteunt de seriële console kopiëren/plakken?**

A. Ja. Gebruik **Ctrl**+**Shift**+**C** en **Ctrl**+**Shift**+**V** om de terminal te kopiëren en plakken.

**V. Kan ik seriële console gebruiken in plaats van een SSH-verbinding?**

A. Hoewel dit gebruik technisch mogelijk lijkt, is de seriële console bedoeld om voornamelijk te worden gebruikt als probleemoplossingstool in situaties waarin connectiviteit via SSH niet mogelijk is. We raden u aan om de seriële console niet als SSH-vervanging te gebruiken om de volgende redenen:

- De seriële console heeft niet zoveel bandbreedte als SSH. Omdat het een tekst-only verbinding, meer GUI-zware interacties zijn moeilijk.
- Seriële consoletoegang is momenteel alleen mogelijk met een gebruikersnaam en wachtwoord. Omdat SSH-toetsen veel veiliger zijn dan combinaties van gebruikersnaam/wachtwoord, raden we SSH aan via seriële console.

**V. Wie kan de seriële console voor mijn abonnement in- of uitschakelen?**

A. Als u de seriële console op abonnementsniveau wilt in- of uitschakelen, moet u schrijfmachtigingen voor het abonnement hebben. Rollen met schrijftoestemming omvatten beheerders- of eigenaarrollen. Aangepaste rollen kunnen ook schrijfmachtigingen hebben.

**V. Wie heeft toegang tot de seriële console voor mijn VM/virtuele machineschaalset?**

A. U moet de rol Virtuele machineinzender of hoger hebben voor een vm- of virtuele machineschaal die is ingesteld om toegang te krijgen tot de seriële console.

**V. Mijn seriële console geeft niets weer, wat moet ik doen?**

A. Uw afbeelding is waarschijnlijk verkeerd geconfigureerd voor toegang tot seriële consoles. Zie Beschikbaarheid van [Seriële console Linux-distributie](#serial-console-linux-distribution-availability)voor informatie over het configureren van uw afbeelding om de seriële console in te schakelen.

**V. Is de seriële console beschikbaar voor virtuele machineschaalsets?**

A. Ja dat is zo! Zie [Seriële console voor virtuele machineschaalsets](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**V. Als ik mijn VM- of virtuele machineschaalset instel met alleen SSH-sleutelverificatie, kan ik dan nog steeds de seriële console gebruiken om verbinding te maken met mijn vm/virtuele machineschaalsetexemplaar?**

A. Ja. Omdat de seriële console geen SSH-sleutels nodig heeft, hoeft u alleen een combinatie van gebruikersnaam/wachtwoord in te stellen. U dit doen door **wachtwoord opnieuw instellen** te selecteren in de Azure-portal en deze referenties te gebruiken om u aan te melden bij de seriële console.

## <a name="next-steps"></a>Volgende stappen
* Gebruik de seriële console om toegang te [krijgen tot GRUB en de modus voor één gebruiker.](serial-console-grub-single-user-mode.md)
* Gebruik de seriële console voor [NMI- en SysRq-gesprekken](serial-console-nmi-sysrq.md).
* Meer informatie over het gebruik van de seriële console om [GRUB in verschillende distro's in](serial-console-grub-proactive-configuration.md) te schakelen
* De seriële console is ook beschikbaar voor [Windows VM's.](../windows/serial-console.md)
* Meer informatie over [opstartdiagnostiek](boot-diagnostics.md).

