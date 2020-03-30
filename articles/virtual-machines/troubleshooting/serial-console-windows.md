---
title: Azure Serial Console voor Windows | Microsoft Documenten
description: Bidirectionele seriële console voor Azure Virtual Machines en Virtual Machine Scale Sets.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 87ccb1c4995337b385f685797980a9fc3962bc6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266999"
---
# <a name="azure-serial-console-for-windows"></a>Azure Serial Console voor Windows

De seriële console in de Azure-portal biedt toegang tot een op tekst gebaseerde console voor virtuele Windows-machines (VM's) en exemplaren van virtuele machineschaalsets. Deze seriële verbinding maakt verbinding met de COM1-seriële poort van de vm- of virtuele machineschaalsetinstantie, waardoor er toegang tot wordt geboden, onafhankelijk van de status van het netwerk of het besturingssysteem. De seriële console is alleen toegankelijk via de Azure-portal en is alleen toegestaan voor gebruikers die een toegangsrol van inzender of hoger hebben tot de VM- of virtuele machineschaalset.

Serial Console werkt op dezelfde manier voor VM's en virtuele machineschaalset-instanties. In dit document bevatten alle vermeldingen aan VM's impliciet exemplaren van virtuele machineschaalsets, tenzij anders vermeld.

Zie Azure Serial Console voor Linux voor seriële consoledocumentatie [voor Linux.](serial-console-linux.md)

> [!NOTE]
> De seriële console is algemeen beschikbaar in algemene Azure-regio's en in openbare preview in Azure Government. Het is nog niet beschikbaar in de Azure China-cloud.


## <a name="prerequisites"></a>Vereisten

* Uw vm- of virtuele machineschaalset-instantie moet het implementatiemodel voor resourcebeheer gebruiken. Klassieke implementaties worden niet ondersteund.

- Uw account dat gebruikmaakt van seriële console moet de [rol Virtuele machineinzender](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) hebben voor de VM en het opslagaccount [voor opstartdiagnostiek](boot-diagnostics.md)

- Uw vm- of virtuele machineschaalset-instantie moet een op een wachtwoord gebaseerde gebruiker hebben. U er een maken met de [wachtwoordfunctie voor opnieuw instellen](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) van de VM-toegangsextensie. Selecteer **Wachtwoord opnieuw instellen** in de sectie Ondersteuning + **probleemoplossing.**

* De instantie VM voor virtuele machineschaalmoet [opstartdiagnose](boot-diagnostics.md) hebben ingeschakeld.

    ![Instellingen voor diagnostische gegevens opstarten](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Seriële consolefunctionaliteit inschakelen voor Windows Server

> [!NOTE]
> Als u niets ziet in de seriële console, controleert u of opstartdiagnose is ingeschakeld op uw VM- of virtuele machineschaalset.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>De seriële console inschakelen in aangepaste of oudere afbeeldingen
Nieuwere Windows Server-afbeeldingen op Azure hebben standaard een SAC [(Special Administration Console).](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) SAC wordt ondersteund op serverversies van Windows, maar is niet beschikbaar op clientversies (bijvoorbeeld Windows 10, Windows 8 of Windows 7).

Voor oudere Windows Server-afbeeldingen (gemaakt vóór februari 2018), u de seriële console automatisch inschakelen via de opdrachtfunctie voor uitvoeren van de Azure-portal. Selecteer in de Azure-portal **de opdracht Uitvoeren**en selecteer vervolgens de opdracht Inschakelen **in** de lijst.

![Opdrachtlijst uitvoeren](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Als u de seriële console voor Windows VM's/virtuele machineschaalset die vóór februari 2018 is gemaakt, handmatig wilt inschakelen, voert u de volgende stappen uit:

1. Verbinding maken met uw virtuele Windows-machine met Extern bureaublad
1. Voer vanuit een opdrachtprompt de volgende opdrachten uit:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Start het systeem opnieuw op om de SAC-console in te schakelen.

    ![SAC-console](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Indien nodig kan de SAC ook offline worden ingeschakeld:

1. Bevestig de windowsschijf waarvoor u SAC wilt configureren als gegevensschijf aan de bestaande VM.

1. Voer vanuit een opdrachtprompt de volgende opdrachten uit:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Hoe weet ik of SAC is ingeschakeld?

Als [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) niet is ingeschakeld, wordt de SAC-prompt niet weergegeven in de seriële console. In sommige gevallen wordt vm-statusgegevens weergegeven en in andere gevallen is deze leeg. Als u een Windows Server-afbeelding gebruikt die vóór februari 2018 is gemaakt, wordt SAC waarschijnlijk niet ingeschakeld.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Het opstartmenu van Windows inschakelen in de seriële console

Als u vragen voor het opstarten van Windows moet inschakelen om weer te geven in de seriële console, u de volgende extra opties toevoegen aan uw opstartconfiguratiegegevens. Zie voor meer informatie [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Maak verbinding met uw Windows VM- of virtuele machineschaalset-instantie met Extern bureaublad.

1. Voer vanuit een opdrachtprompt de volgende opdrachten uit:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Het systeem opnieuw opstarten om het opstartmenu in te schakelen

> [!NOTE]
> De time-out die u instelt voor het menu opstarten opstartbeheer, heeft invloed op de opstarttijd van uw besturingssysteem. Als u denkt dat de time-outwaarde van 10 seconden te kort of te lang is, stelt u deze in op een andere waarde.

## <a name="use-serial-console"></a>Seriële console gebruiken

### <a name="use-cmd-or-powershell-in-serial-console"></a>CMD of PowerShell gebruiken in seriële console

1. Maak verbinding met de seriële console. Als u met succes verbinding maakt, is de prompt **SAC>: **

    ![Verbinding maken met SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Voer `cmd` in om een kanaal te maken met een CMD-instantie.

1.  Voer `ch -si 1` sneltoetstoetsen in of druk op `<esc>+<tab>` om over te schakelen naar het kanaal waarop de CMD-instantie wordt uitgevoerd.

1.  Druk op **Enter**en voer aanmeldingsreferenties in met beheerdersmachtigingen.

1.  Nadat u geldige referenties hebt ingevoerd, wordt de CMD-instantie geopend.

1.  Als u een PowerShell-instantie wilt starten, voert u `PowerShell` de CMD-instantie in en drukt u op **Enter**.

    ![PowerShell-exemplaar openen](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>De seriële console gebruiken voor NMI-aanroepen
Een niet-maskerbare interrupt (NMI) is ontworpen om een signaal te maken dat software op een virtuele machine niet zal negeren. Historisch gezien zijn NMI's gebruikt om te controleren op hardwareproblemen op systemen waarvoor specifieke responstijden vereist zijn. Tegenwoordig gebruiken programmeurs en systeembeheerders VAAK NMI als een mechanisme om systemen die niet reageren te debuggen of op te lossen.

De seriële console kan worden gebruikt om een NMI naar een virtuele Azure-machine te verzenden met behulp van het toetsenbordpictogram in de opdrachtbalk. Nadat het NMI is geleverd, bepaalt de configuratie van de virtuele machine hoe het systeem reageert. Windows kan worden geconfigureerd om te crashen en een geheugendumpbestand te maken wanneer u een NMI ontvangt.

![NMI verzenden](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Zie [Een crashdumpbestand genereren met een NMI voor](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)informatie over het configureren van Windows om een crashdumpbestand te maken wanneer het een NMI ontvangt.

### <a name="use-function-keys-in-serial-console"></a>Functietoetsen gebruiken in seriële console
Functietoetsen zijn ingeschakeld voor gebruik voor seriële console in Windows VM's. De F8 in de vervolgkeuzelijst van de seriële console biedt het gemak om eenvoudig het menu Geavanceerde opstartinstellingen in te voeren, maar de seriële console is compatibel met alle andere functietoetsen. Het kan nodig zijn om **fn** + **F1** (of F2, F3, enz.) op uw toetsenbord te drukken, afhankelijk van de computer die u gebruikt seriële console van.

### <a name="use-wsl-in-serial-console"></a>WSL gebruiken in seriële console
Het Windows Subsysteem voor Linux (WSL) is ingeschakeld voor Windows Server 2019 of hoger, dus het is ook mogelijk om WSL in te schakelen voor gebruik binnen de seriële console als u Windows Server 2019 of hoger gebruikt. Dit kan gunstig zijn voor gebruikers die ook vertrouwd zijn met Linux-commando's. Zie de [installatiehandleiding](https://docs.microsoft.com/windows/wsl/install-on-server)voor instructies om WSL voor Windows Server in te schakelen.

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Start de scale-set instance van Windows VM/virtuele machine opnieuw binnen Seriële console
U een herstart starten in de seriële console door naar de aan/uit-knop te navigeren en op VM opnieuw opstarten te klikken. Hiermee wordt een vm-herstart gestart en wordt er binnen de Azure-portal een melding weergegeven met betrekking tot de herstart.

Dit is handig in situaties waarin u mogelijk toegang wilt krijgen tot het opstartmenu zonder de seriële console-ervaring te verlaten.

![Windows Serial Console opnieuw opstarten](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

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
> Er worden geen toegangswachtwoorden voor de console geregistreerd. Als opdrachten die binnen de console worden uitgevoerd, echter wachtwoorden, geheimen, gebruikersnamen of enige andere vorm van persoonlijk identificeerbare informatie (PII) bevatten of uitvoeren, worden deze naar de vm-opstartdiagnoselogboeken geschreven. Ze zullen worden geschreven samen met alle andere zichtbare tekst, als onderdeel van de uitvoering van de scroll back-functie van de seriële console. Deze logboeken zijn cirkelvormig en alleen personen met leesmachtigingen voor het opslagaccount voor diagnostiek hebben er toegang toe. We raden u echter aan de beste praktijken te volgen om het Extern bureaublad te gebruiken voor alles wat geheimen en/of PII kan bevatten.

### <a name="concurrent-usage"></a>Gelijktijdig gebruik
Als een gebruiker is verbonden met de seriële console en een andere gebruiker met succes toegang vraagt tot diezelfde virtuele machine, wordt de eerste gebruiker losgekoppeld en wordt de tweede gebruiker verbonden met dezelfde sessie.

> [!CAUTION]
> Dit betekent dat een gebruiker die de verbinding heeft verbroken, niet wordt uitgelogd. De mogelijkheid om een afmelding af te dwingen bij het verbreken (met behulp van SIGHUP of een soortgelijk mechanisme) is nog steeds in de roadmap. Voor Windows is er een automatische time-out ingeschakeld in SAC; voor Linux u de instelling voor een eindtime-out configureren.

## <a name="accessibility"></a>Toegankelijkheid
Toegankelijkheid is een belangrijke focus voor de Azure-seriële console. Daarom hebben we ervoor gezorgd dat de seriële console toegankelijk is voor slechtzienden en slechthorenden, evenals mensen die mogelijk geen muis kunnen gebruiken.

### <a name="keyboard-navigation"></a>Toetsenbordnavigatie
Gebruik de **Tab-toets** op het toetsenbord om te navigeren in de seriële console-interface vanuit de Azure-portal. Uw locatie wordt gemarkeerd op het scherm. Als u de focus van het seriële consolevenster wilt verlaten, drukt u op **Ctrl**+**F6** op het toetsenbord.

### <a name="use-the-serial-console-with-a-screen-reader"></a>De seriële console gebruiken met een schermlezer
De seriële console heeft schermlezer ondersteuning ingebouwd. Als u rondnavigeert met een schermlezer ingeschakeld, kan de alternatieve tekst voor de momenteel geselecteerde knop hardop worden voorgelezen door de schermlezer.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Veelvoorkomende scenario's voor toegang tot de seriële console

Scenario          | Acties in de seriële console
:------------------|:-----------------------------------------
Onjuiste firewallregels | Toegang tot seriële console en fix Windows firewall regels.
Corruptie/controle van bestandssysteem | Toegang tot de seriële console en herstel het bestandssysteem.
PROBLEMEN met RDP-configuratie | Toegang tot de seriële console en wijzig de instellingen. Zie voor meer informatie de [RDP-documentatie](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Netwerkvergrendelingssysteem | Toegang tot de seriële console vanuit de Azure-portal om het systeem te beheren. Sommige netwerkopdrachten worden weergegeven in [Windows-opdrachten: CMD en PowerShell](serial-console-cmd-ps-commands.md).
Interactie met bootloader | Toegang tot BCD via de seriële console. Zie Het [opstartmenu van Windows inschakelen in de seriële console](#enable-the-windows-boot-menu-in-the-serial-console)voor meer informatie .

## <a name="known-issues"></a>Bekende problemen
We zijn ons bewust van een aantal problemen met de seriële console en het besturingssysteem van de VM. Hier volgt een lijst met deze problemen en stappen voor mitigatie voor Windows VM's. Deze problemen en oplossingen zijn van toepassing op zowel VM's als exemplaren van virtuele machineschaalsets. Als deze niet overeenkomen met de fout die u ziet, raadpleegt u de algemene fouten in de seriële consoleservice bij [algemene seriële consolefouten.](./serial-console-errors.md)

Probleem                             |   Oplossing
:---------------------------------|:--------------------------------------------|
Als u op **Enter** drukt nadat de verbindingsbanner is weergegeven, wordt er geen aanmeldingsprompt weergegeven. | Zie [Slaan doet niets voor](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)meer informatie. Deze fout kan optreden als u een aangepaste VM, gehard toestel of opstartconfig uitvoert waardoor Windows niet goed verbinding maakt met de seriële poort. Deze fout treedt ook op als u een Windows 10 VM uitvoert, omdat alleen Windows Server VM's zijn geconfigureerd om EMS te hebben ingeschakeld.
Alleen gezondheidsinformatie wordt weergegeven wanneer u verbinding maakt met een Windows-vm| Deze fout treedt op als de speciale beheerconsole niet is ingeschakeld voor uw Windows-afbeelding. Zie [De seriële console inschakelen in aangepaste of oudere afbeeldingen](#enable-the-serial-console-in-custom-or-older-images) voor instructies over het handmatig inschakelen van SAC op uw Windows-vm. Zie [Windows-statussignalen voor](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)meer informatie.
SAC neemt niet het hele serial console-gebied in de browser in beslag | Dit is een bekend probleem met Windows en de terminal emulator. We zijn het bijhouden van dit probleem met beide teams, maar voor nu is er geen mitigatie.
Kan niet typen bij SAC-prompt als kerneldebugging is ingeschakeld. | RDP naar VM `bcdedit /debug {current} off` en uitvoeren vanaf een opdrachtprompt met verhoogde bevoegdheid. Als u RDP niet, u de OS-schijf in plaats daarvan aan een `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`andere Azure-vm koppelen en deze wijzigen terwijl u als gegevensschijf bent verbonden door de schijf terug te draaien.
Het plakken in PowerShell in SAC resulteert in een derde teken als de oorspronkelijke inhoud een herhalend teken had. | Voer voor een `Remove-Module PSReadLine` tijdelijke oplossing de PSReadLine-module uit de huidige sessie. Met deze actie wordt de module niet verwijderd of verwijderd.
Sommige toetsenbordingangen produceren vreemde SAC-uitgang (bijvoorbeeld **[A**, **[3~**). | [VT100](https://aka.ms/vtsequences) escape sequenties worden niet ondersteund door de SAC prompt.
Lange snaren plakken werkt niet. | De seriële console beperkt de lengte van tekenreeksen die in de terminal zijn geplakt tot 2048 tekens om overbelasting van de bandbreedte van de seriële poort te voorkomen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V. Hoe kan ik feedback sturen?**

A. Geef feedback door een GitHub-probleem te maken op https://aka.ms/serialconsolefeedback. Als alternatief (minder gewenst), kunt azserialhelp@microsoft.com u feedback sturen https://feedback.azure.comvia of in de virtuele machine categorie van .

**V. Ondersteunt de seriële console kopiëren/plakken?**

A. Ja. Gebruik **Ctrl**+**Shift**+**C** en **Ctrl**+**Shift**+**V** om de terminal te kopiëren en plakken.

**V. Wie kan de seriële console voor mijn abonnement in- of uitschakelen?**

A. Als u de seriële console op abonnementsniveau wilt in- of uitschakelen, moet u schrijfmachtigingen voor het abonnement hebben. Rollen met schrijftoestemming omvatten beheerders- of eigenaarrollen. Aangepaste rollen kunnen ook schrijfmachtigingen hebben.

**V. Wie heeft toegang tot de seriële console voor mijn VM?**

A. U moet de rol Virtuele machineinzender of hoger hebben om toegang te krijgen tot de seriële console van de VM.

**V. Mijn seriële console geeft niets weer, wat moet ik doen?**

A. Uw afbeelding is waarschijnlijk verkeerd geconfigureerd voor toegang tot seriële consoles. Zie [De seriële console inschakelen in aangepaste of oudere afbeeldingen](#enable-the-serial-console-in-custom-or-older-images)voor informatie over het configureren van de afbeelding om de seriële console in te schakelen.

**V. Is de seriële console beschikbaar voor virtuele machineschaalsets?**

A. Ja dat is zo! Zie [Seriële console voor virtuele machineschaalsets](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Volgende stappen
* Zie [Windows-opdrachten: CMD en PowerShell](serial-console-cmd-ps-commands.md)voor een uitgebreide handleiding voor CMD- en PowerShell-opdrachten die u in de Windows SAC gebruiken.
* De seriële console is ook beschikbaar voor [Linux](serial-console-linux.md) VM's.
* Meer informatie over [opstartdiagnostiek](boot-diagnostics.md).
