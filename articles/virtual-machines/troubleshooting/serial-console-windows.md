---
title: Azure Serial console voor Windows | Microsoft Docs
description: Bidirectionele seriële console voor Azure Virtual Machines en Virtual Machine Scale Sets met behulp van een Windows-voor beeld.
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
ms.openlocfilehash: c5c139cb94358d70d1f23b68f2a369adb953da08
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325977"
---
# <a name="azure-serial-console-for-windows"></a>Azure Serial console voor Windows

De seriële console in de Azure Portal biedt toegang tot een op tekst gebaseerde console voor virtuele Windows-machines (Vm's) en exemplaren van virtuele-machine schaal sets. Deze seriële verbinding maakt verbinding met de COM1 seriële poort van de VM of virtuele-machine Scale set-instantie, waardoor deze onafhankelijk is van de status van het netwerk of het besturings systeem. De seriële console is alleen toegankelijk via de Azure Portal en is alleen toegestaan voor gebruikers met een toegangs rol van Inzender of een hoger niveau voor virtuele-machine schaal sets.

Seriële console werkt op dezelfde manier voor Vm's en exemplaren van virtuele-machine schaal sets. In dit document bevatten alle vermeldingen aan Vm's impliciet instanties voor schaal sets voor virtuele machines, tenzij anders vermeld.

Seriële console is algemeen beschikbaar in de wereld wijde Azure-regio's en in de open bare preview-versie van Azure Government. Het is nog niet beschikbaar in de Azure China-Cloud.

Zie [Azure Serial console voor Linux](serial-console-linux.md)voor meer informatie over de seriële console voor Linux.

> [!NOTE]
> Seriële console is momenteel niet compatibel met een beheerd opslag account voor diagnostische gegevens over opstarten. Als u een seriële console wilt gebruiken, moet u ervoor zorgen dat u een aangepast opslag account gebruikt.


## <a name="prerequisites"></a>Vereisten

* De virtuele machine of het exemplaar van de VM-schaalset moet gebruikmaken van het Resource Management-implementatie model. Klassieke implementaties worden niet ondersteund.

- Uw account dat gebruikmaakt van seriële console, moet de [rol Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) hebben voor de VM en het opslag account voor [Diagnostische gegevens over opstarten](boot-diagnostics.md)

- Uw virtuele machine of exemplaar van de VM-schaalset moet een gebruiker met een wacht woord zijn. U kunt er een maken met de functie [wacht woord opnieuw instellen](../extensions/vmaccess.md#reset-password) van de extensie VM-toegang. Selecteer **wacht woord opnieuw instellen** in het gedeelte **ondersteuning en probleem oplossing** .

* Voor de VM voor het exemplaar van de virtuele-machine schaalset moet [Diagnostische gegevens over opstarten](boot-diagnostics.md) zijn ingeschakeld.

    ![Instellingen voor diagnostische gegevens over opstarten](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>De functionaliteit van seriële console inschakelen voor Windows Server

> [!NOTE]
> Als u niets in de seriële console ziet, zorg er dan voor dat de diagnostische gegevens over opstarten zijn ingeschakeld op de virtuele machine of in de schaalset van virtual machines.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>De seriële console inschakelen in aangepaste of oudere installatie kopieën
Nieuwere Windows Server-installatie kopieën op Azure hebben standaard een [speciale beheer console](/previous-versions/windows/it-pro/windows-server-2003/cc787940(v=ws.10)) (SAC) ingeschakeld. SAC wordt ondersteund op Server versies van Windows, maar is niet beschikbaar op client versies (bijvoorbeeld Windows 10, Windows 8 of Windows 7).

Voor oudere Windows Server-installatie kopieën (gemaakt vóór 2018 februari), kunt u de seriële console automatisch inschakelen met behulp van de opdracht functie voor het uitvoeren van de Azure Portal. Selecteer in de Azure Portal de **opdracht uitvoeren**en selecteer vervolgens de opdracht met de naam **EnableEMS** in de lijst.

![Opdracht lijst uitvoeren](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

U kunt ook de seriële console voor Windows Vm's/virtuele-machine schaal sets die zijn gemaakt vóór 2018 februari, als volgt hand matig inschakelen:

1. Verbinding maken met uw virtuele Windows-machine met behulp van Extern bureaublad
1. Voer de volgende opdrachten uit vanaf een opdracht prompt:
    - `bcdedit /ems {current} on`, of `bcdedit /ems '{current}' on` Als u Power shell gebruikt
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Start het systeem opnieuw op om de SAC-console in te scha kelen.

    ![SAC-console](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Als dat nodig is, kan de SAC ook offline worden ingeschakeld:

1. Koppel de Windows-schijf waarvoor u SAC wilt configureren als een gegevens schijf voor de bestaande virtuele machine.

1. Voer de volgende opdrachten uit vanaf een opdracht prompt:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Hoe kan ik weet of SAC is ingeschakeld?

Als [SAC](/previous-versions/windows/it-pro/windows-server-2003/cc787940(v=ws.10)) niet is ingeschakeld, wordt de SAC-prompt niet weer gegeven door de seriële console. In sommige gevallen wordt informatie over de status van de virtuele machine weer gegeven en in andere gevallen is deze leeg. Als u een Windows Server-installatie kopie gebruikt die vóór februari 2018 is gemaakt, wordt SAC waarschijnlijk niet ingeschakeld.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Het Windows-opstart menu inschakelen in de seriële console

Als u het Windows-opstart laad programma moet inschakelen om weer te geven in de seriële console, kunt u de volgende extra opties toevoegen aan de opstart configuratie gegevens. Zie [bcdedit](/windows-hardware/drivers/devtest/bcdedit--set)voor meer informatie.

1. Maak verbinding met uw Windows-VM of een installatie kopie van een virtuele machine met behulp van Extern bureaublad.

1. Voer de volgende opdrachten uit vanaf een opdracht prompt:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Start het systeem opnieuw op om het opstart menu in te scha kelen

> [!NOTE]
> De time-out die u voor de weer gave van het menu opstart beheer instelt, is van invloed op de opstart tijd van het besturings systeem. Als u denkt dat de time-outwaarde van 10 seconden te kort of te lang is, stelt u deze in op een andere waarde.

## <a name="use-serial-console"></a>Seriële console gebruiken

### <a name="use-cmd-or-powershell-in-serial-console"></a>CMD of Power shell gebruiken in de seriële console

1. Maak verbinding met de seriële console. Als u verbinding hebt gemaakt, is de prompt **SAC>**:

   ![Verbinding maken met SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)
1. Voer `cmd` in om een kanaal te maken dat een cmd-exemplaar heeft.

1. Typ `ch -si 1` of druk op de sneltoetsen `<esc>+<tab>` om over te scha kelen naar het kanaal waarop het cmd-exemplaar wordt uitgevoerd.

1. Druk op **Enter**en voer aanmeldings referenties in met beheerders machtigingen.

1. Nadat u geldige referenties hebt ingevoerd, wordt het CMD-exemplaar geopend.

1. Als u een Power shell-exemplaar wilt starten, voert u `PowerShell` in het cmd-exemplaar in en drukt u vervolgens op **Enter**.

   ![Power shell-exemplaar openen](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>De seriële console gebruiken voor NMI-aanroepen
Een niet-maskeer bare interrupt (NMI) is ontworpen om een signaal te maken dat software op een virtuele machine niet wordt genegeerd. In het verleden werden NMIs gebruikt voor het bewaken van hardwareproblemen op systemen waarop specifieke reactie tijden vereist zijn. Tegenwoordig gebruiken programmeurs en systeem beheerders NMI vaak als een mechanisme voor het opsporen van problemen met systemen die niet reageren.

De seriële console kan worden gebruikt om een NMI te verzenden naar een virtuele Azure-machine met behulp van het toetsenbord pictogram in de opdracht balk. Nadat de NMI is geleverd, wordt door de configuratie van de virtuele machine bepaald hoe het systeem reageert. Windows kan worden geconfigureerd om te crashen en een geheugen dump bestand te maken wanneer er een NMI wordt ontvangen.

![NMI verzenden](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Voor informatie over het configureren van Windows voor het maken van een crash dump bestand bij het ontvangen van een NMI, Zie [How to generate an crash dump file (een bestand met NMI genereren](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)).

### <a name="use-function-keys-in-serial-console"></a>Functie toetsen gebruiken in seriële console
Functie sleutels zijn ingeschakeld voor gebruik van seriële console in Windows-Vm's. Met de F8 in de vervolg keuzelijst van de seriële console kunt u eenvoudig het menu Geavanceerde opstart instellingen openen, maar de seriële console is compatibel met alle andere functie sleutels. Mogelijk moet u op **FN**  +  **F1** drukken (of F2, F3, etc.) op het toetsen bord, afhankelijk van de computer waarvan u de seriële console gebruikt.

### <a name="use-wsl-in-serial-console"></a>WSL gebruiken in de seriële console
Het Windows-subsysteem voor Linux (WSL) is ingeschakeld voor Windows Server 2019 of hoger, dus het is ook mogelijk om WSL in te scha kelen voor gebruik in de seriële console als u Windows Server 2019 of hoger uitvoert. Dit kan handig zijn voor gebruikers die ook vertrouwd zijn met Linux-opdrachten. Raadpleeg de [installatie handleiding](/windows/wsl/install-on-server)voor instructies voor het inschakelen van WSL voor Windows Server.

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Uw Windows VM/virtual machine Scale set-exemplaar opnieuw starten binnen een seriële console
U kunt een herstart starten binnen de seriële console door te navigeren naar de aan/uit-knop en op VM opnieuw opstarten te klikken. Hiermee start u het opnieuw opstarten van een virtuele machine en ziet u een melding binnen het Azure Portal met betrekking tot de herstart.

Dit is handig in situaties waarin u toegang wilt krijgen tot het opstart menu zonder dat u de seriële console-ervaring hoeft te verlaten.

![Windows seriële console opnieuw opstarten](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>De seriële console uitschakelen
Standaard hebben alle abonnementen seriële console toegang ingeschakeld. U kunt de seriële console uitschakelen op het niveau van het abonnement of de VM/virtuele-machine schaalset. Ga voor gedetailleerde instructies naar [de Azure Serial console inschakelen en uitschakelen](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Seriële console beveiliging

### <a name="access-security"></a>Toegang tot beveiliging
Toegang tot de seriële console is beperkt tot gebruikers die de rol van toegang tot de [virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) of een hoger niveau van de virtuele machine hebben. Als uw Azure Active Directory-Tenant multi-factor Authentication (MFA) vereist, heeft toegang tot de seriële console ook MFA nodig omdat de toegang tot de seriële console via de [Azure Portal](https://portal.azure.com).

### <a name="channel-security"></a>Kanaal beveiliging
Alle gegevens die worden verzonden, worden versleuteld op de kabel.

### <a name="audit-logs"></a>Auditlogboeken
Alle toegang tot de seriële console is momenteel geregistreerd in de logboeken voor [Diagnostische gegevens over opstarten](./boot-diagnostics.md) van de virtuele machine. Toegang tot deze logboeken is eigendom van en beheerd door de beheerder van de virtuele Azure-machine.

> [!CAUTION]
> Er zijn geen toegangs wachtwoorden voor de console vastgelegd. Als opdrachten echter worden uitgevoerd in de-console bevatten of uitvoer wachtwoorden, geheimen, gebruikers namen of andere vormen van persoons gegevens (PII), worden deze geschreven naar de logboeken voor diagnostische opstart gegevens van de VM. Ze worden samen met alle andere zicht bare tekst geschreven, als onderdeel van de implementatie van de functie scroll back van de seriële console. Deze logboeken zijn circulaire en alleen personen met lees machtigingen voor het diagnostische-opslag account hebben toegang tot ze. We raden u echter aan de best practice van het gebruik van de Extern bureaublad te volgen voor alles wat geheimen en/of PII kan inhouden.

### <a name="concurrent-usage"></a>Gelijktijdig gebruik
Als een gebruiker is verbonden met de seriële console en een andere gebruiker de toegang tot dezelfde virtuele machine heeft gevraagd, wordt de verbinding met de eerste gebruiker verbroken en wordt de tweede gebruiker verbonden met dezelfde sessie.

> [!CAUTION]
> Dit betekent dat een gebruiker die de verbinding is verbroken niet afmeldt. De mogelijkheid om een afmelding af te dwingen bij het verbreken van de verbinding (met behulp van SIGHUP of soortgelijk mechanisme) bevindt zich nog in het schema Voor Windows is er een automatische time-out in SAC ingeschakeld. voor Linux kunt u de time-outinstelling van de terminal configureren.

## <a name="accessibility"></a>Toegankelijkheid
Toegankelijkheid is een belang rijke focus voor de Azure Serial console. Daarom hebben we ervoor gezorgd dat de seriële console toegankelijk is voor mensen met een slechtere bijzondere waardevermindering of voor wie er sprake is van een moeilijk te gebruiken persoon.

### <a name="keyboard-navigation"></a>Toetsenbordnavigatie
Gebruik de **Tab** -toets op het toetsen bord om te navigeren in de interface van de seriële console van de Azure Portal. Uw locatie wordt op het scherm gemarkeerd. Als u de focus van het venster van de seriële console wilt verlaten, drukt u op **CTRL** + **F6** op het toetsen bord.

### <a name="use-the-serial-console-with-a-screen-reader"></a>De seriële console gebruiken met een scherm lezer
De seriële console heeft ingebouwde ondersteuning voor scherm lezers. Door te navigeren met een ingeschakelde scherm lezer, kan de ALT-tekst voor de momenteel geselecteerde knop hardop worden gelezen door de scherm lezer.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Algemene scenario's voor toegang tot de seriële console

Scenario          | Acties in de seriële console
:------------------|:-----------------------------------------
Onjuiste firewall regels | Toegang tot seriële console en Windows Firewall-regels herstellen.
Beschadiging/controle van bestands systeem | Open de seriële console en herstel het bestands systeem.
Problemen met de RDP-configuratie | Toegang tot de seriële console en de instellingen te wijzigen. Zie de [RDP-documentatie](/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)voor meer informatie.
Netwerk vergrendeling systeem | Open de seriële console vanuit het Azure Portal om het systeem te beheren. Sommige netwerk opdrachten worden weer gegeven in [Windows-opdrachten: cmd en Power shell](serial-console-cmd-ps-commands.md).
Interactie met bootloader | Toegang tot BCD via de seriële console. Zie [het Windows-opstart menu inschakelen in de seriële console](#enable-the-windows-boot-menu-in-the-serial-console)voor meer informatie.

## <a name="known-issues"></a>Bekende problemen
Er zijn enkele problemen met de seriële console en het besturings systeem van de virtuele machine. Hier volgt een lijst met deze problemen en stappen voor het beperken van de oplossing voor Windows-Vm's. Deze problemen en oplossingen zijn van toepassing voor zowel Vm's als virtuele-machine schaal sets. Als deze niet overeenkomen met de fout die u ziet, raadpleegt u de common Serial console-service fouten bij [veelvoorkomende seriële console fouten](./serial-console-errors.md).

Probleem                             |   Oplossing
:---------------------------------|:--------------------------------------------|
Wanneer u op **Enter** drukt na de verbindings banner, wordt er geen aanmeldings prompt weer gegeven. | Voor meer informatie, zie op ' door [voeren Enter ' niets](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Deze fout kan optreden als u een aangepaste VM, een vrijgemaakt apparaat of een opstart configuratie uitvoert die ervoor zorgt dat Windows niet op de juiste wijze verbinding maakt met de seriële poort. Deze fout treedt ook op als u een Windows 10-VM uitvoert, omdat alleen virtuele machines met Windows Server zijn geconfigureerd om EMS te kunnen inschakelen.
Er wordt alleen status informatie weer gegeven wanneer u verbinding maakt met een Windows-VM| Deze fout treedt op als de speciale beheer console niet is ingeschakeld voor uw Windows-installatie kopie. Zie [de seriële console inschakelen in aangepaste of oudere installatie kopieën](#enable-the-serial-console-in-custom-or-older-images) voor instructies over het hand matig inschakelen van SAC op uw Windows-VM. Zie [Windows Health Signals](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)(Engelstalig) voor meer informatie.
SAC behaalt niet het volledige seriële console gebied in de browser | Dit is een bekend probleem met Windows en de terminal emulator. We volgen dit probleem met beide teams, maar nu is er geen oplossing.
Kan niet op SAC-prompt typen als fout opsporing voor kernel is ingeschakeld. | RDP naar VM en wordt uitgevoerd `bcdedit /debug {current} off` vanaf een opdracht prompt met verhoogde bevoegdheid. Als u geen RDP hebt, kunt u in plaats daarvan de besturingssysteem schijf aan een andere Azure-VM koppelen en deze wijzigen terwijl deze wordt gekoppeld als een gegevens schijf door uit `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` te voeren. vervolgens wordt de schijf opnieuw gewisseld.
Plakken in Power shell in SAC resulteert in een derde teken als de oorspronkelijke inhoud een herhalend teken bevat. | Voer voor een tijdelijke oplossing `Remove-Module PSReadLine` uit om de PSReadLine-module uit de huidige sessie te verwijderen. Met deze actie wordt de module niet verwijderd of verwijderd.
Sommige toetsenbord ingangen produceren een vreemde SAC-uitvoer (bijvoorbeeld **[A**, **[3 ~**). | [VT100](https://aka.ms/vtsequences) -escape reeksen worden niet ondersteund door de SAC-prompt.
Het plakken van lange teken reeksen werkt niet. | De seriële console beperkt de lengte van teken reeksen die in de terminal worden geplakt tot 2048 tekens om te voor komen dat de band breedte van de seriële poort wordt overbelast.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Nils. Hoe kan ik feedback verzenden?**

A. Geef feedback door het maken van een GitHub-probleem op https://aka.ms/serialconsolefeedback . Als alternatief (minder voor keur) kunt u feedback verzenden via azserialhelp@microsoft.com of in de virtuele-machine categorie van https://feedback.azure.com .

**Nils. Ondersteunt de seriële console kopiëren/plakken?**

A. Ja. Gebruik **CTRL** + **SHIFT** + **C** en **CTRL** + **SHIFT** + **V** om de Terminal te kopiëren en plakken.

**Nils. Wie kan de seriële console voor mijn abonnement in-of uitschakelen?**

A. Als u de seriële console op het niveau van een abonnement wilt in-of uitschakelen, moet u schrijf machtigingen hebben voor het abonnement. Rollen met schrijf machtiging zijn onder andere beheerders-of eigenaar rollen. Aangepaste rollen kunnen ook schrijf machtigingen hebben.

**Nils. Wie heeft toegang tot de seriële console voor mijn VM?**

A. U moet de rol Inzender voor virtuele machines of hoger hebben voor een VM om toegang te krijgen tot de seriële console van de VM.

**Nils. Mijn seriële console geeft niets weer, wat moet ik doen?**

A. De installatie kopie is waarschijnlijk onjuist geconfigureerd voor toegang tot seriële consoles. Voor informatie over het configureren van uw installatie kopie om de seriële console in te scha kelen, raadpleegt u [de seriële console inschakelen in aangepaste of oudere installatie kopieën](#enable-the-serial-console-in-custom-or-older-images).

**Nils. Is de seriële console beschikbaar voor schaal sets voor virtuele machines?**

A. Ja dat is zo! Zie de [seriële console voor Virtual Machine Scale sets](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Volgende stappen
* Zie [Windows-opdrachten: cmd en Power shell](serial-console-cmd-ps-commands.md)(Engelstalig) voor een diep gaande hand leiding voor cmd-en Power shell-opdrachten die u kunt gebruiken in Windows SAC.
* De seriële console is ook beschikbaar voor [Linux](serial-console-linux.md) -vm's.
* Meer informatie over [Diagnostische gegevens over opstarten](boot-diagnostics.md).
