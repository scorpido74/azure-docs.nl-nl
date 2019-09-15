---
title: Azure Serial console voor Windows | Microsoft Docs
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
ms.openlocfilehash: ebf7b712dda19b396b044235bf194a5dd402ffac
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996423"
---
# <a name="azure-serial-console-for-windows"></a>Azure Serial console voor Windows

De seriële console in de Azure Portal biedt toegang tot een op tekst gebaseerde console voor virtuele Windows-machines (Vm's) en exemplaren van virtuele-machine schaal sets. Deze seriële verbinding maakt verbinding met de COM1 seriële poort van de VM of virtuele-machine Scale set-instantie, waardoor deze onafhankelijk is van de status van het netwerk of het besturings systeem. De seriële console is alleen toegankelijk via de Azure Portal en is alleen toegestaan voor gebruikers met een toegangs rol van Inzender of een hoger niveau voor virtuele-machine schaal sets.

Seriële console werkt op dezelfde manier voor Vm's en exemplaren van virtuele-machine schaal sets. In dit document bevatten alle vermeldingen aan Vm's impliciet instanties voor schaal sets voor virtuele machines, tenzij anders vermeld.

Zie [Azure Serial console voor Linux](serial-console-linux.md)voor meer informatie over de seriële console voor Linux.

> [!NOTE]
> De seriële console is algemeen beschikbaar in de wereld wijde Azure-regio's. Het is nog niet beschikbaar in de Azure government en Azure China clouds.


## <a name="prerequisites"></a>Vereisten

* De virtuele machine of het exemplaar van de VM-schaalset moet gebruikmaken van het Resource Management-implementatie model. Klassieke implementaties worden niet ondersteund.

- Uw account dat gebruikmaakt van seriële console, moet de [rol Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) hebben voor de VM en het opslag account voor [Diagnostische gegevens over opstarten](boot-diagnostics.md)

- Uw virtuele machine of exemplaar van de VM-schaalset moet een gebruiker met een wacht woord zijn. U kunt maken met de [wachtwoord opnieuw instellen](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) functie van de VM-extensie voor toegang. Selecteer **wachtwoord opnieuw instellen** uit de **ondersteuning en probleemoplossing** sectie.

* Voor de VM voor het exemplaar van de virtuele-machine schaalset moet [Diagnostische gegevens over opstarten](boot-diagnostics.md) zijn ingeschakeld.

    ![De instellingen voor diagnostische gegevens over opstarten](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>De functionaliteit van seriële console inschakelen voor Windows Server

> [!NOTE]
> Als u niets in de seriële console ziet, zorg er dan voor dat de diagnostische gegevens over opstarten zijn ingeschakeld op de virtuele machine of in de schaalset van virtual machines.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Inschakelen van de seriële console in aangepaste of oudere installatiekopieën
Nieuwere Windows Server-installatie kopieën op Azure hebben standaard een [speciale beheer console](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) ingeschakeld. SAC wordt ondersteund in serverversies van Windows, maar is niet beschikbaar in clientversies (bijvoorbeeld Windows 10, Windows 8 of Windows 7).

Voor oudere Windows Server-installatiekopieën (die zijn gemaakt vóór februari 2018), kunt u automatisch de seriële console via de Azure-portal RunCommand-functie inschakelen. Selecteer in de Azure Portal de **opdracht uitvoeren**en selecteer vervolgens de opdracht met de naam **EnableEMS** in de lijst.

![Lijst met opdrachten uitvoeren](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

U kunt ook de seriële console voor Windows Vm's/virtuele-machine schaal sets die zijn gemaakt vóór 2018 februari, als volgt hand matig inschakelen:

1. Verbinding maken met uw Windows-machine via Extern bureaublad
1. Voer de volgende opdrachten vanaf een opdrachtprompt met beheerdersrechten:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Start opnieuw op het systeem voor de console SAC zijn ingeschakeld.

    ![SAC-console](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Indien nodig, kan de SAC ook worden ingeschakeld offline:

1. De windows-schijf waarvan u SAC geconfigureerd als een gegevensschijf aan de bestaande virtuele machine wilt koppelen.

1. Voer de volgende opdrachten vanaf een opdrachtprompt met beheerdersrechten:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Hoe weet ik of SAC is ingeschakeld?

Als [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) niet is ingeschakeld, wordt niet de seriële console weergegeven de SAC-prompt. In sommige gevallen, gegevens over de servicestatus van de virtuele machine wordt weergegeven en in andere gevallen zijn de velden leeg. Als u een installatiekopie van Windows Server die zijn gemaakt vóór februari 2018, SAC waarschijnlijk niet ingeschakeld.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Het opstartmenu Windows in de seriële console inschakelen

Als u nodig hebt om in te schakelen Windows boot loader aanwijzingen om weer te geven in de seriële console, kunt u de volgende aanvullende opties toevoegen aan uw opstartconfiguratiegegevens. Zie voor meer informatie, [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Maak verbinding met uw Windows-VM of een installatie kopie van een virtuele machine met behulp van Extern bureaublad.

1. Voer de volgende opdrachten vanaf een opdrachtprompt met beheerdersrechten:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Opnieuw opstarten van het systeem voor het opstartmenu worden ingeschakeld

> [!NOTE]
> De time-out die u hebt ingesteld voor het opstartmenu manager om weer te geven is van invloed op de opstarttijd van uw besturingssysteem. Als u denkt dat de 10 seconden time-outwaarde is te lang of te kort dat, doet u het op een andere waarde.

## <a name="use-serial-console"></a>Seriële console gebruiken

### <a name="use-cmd-or-powershell-in-serial-console"></a>CMD of Power shell gebruiken in de seriële console

1. Verbinding maken met de seriële console. Als u bent verbonden, is het de prompt **SAC >** :

    ![Verbinding maken met SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Voer `cmd` om een kanaal waaraan een CMD-exemplaar te maken.

1.  Voer `ch -si 1` sneltoetsen in `<esc>+<tab>` of druk op ENTER om over te scha kelen naar het kanaal waarop het cmd-exemplaar wordt uitgevoerd.

1.  Druk op **Enter**, en geef vervolgens referenties aanmelden met beheerdersmachtigingen.

1.  Nadat u geldige referenties hebt ingevoerd, wordt de CMD-instantie wordt geopend.

1.  Voer een PowerShell-sessie starten, `PowerShell` in de CMD-exemplaar, en druk vervolgens op **Enter**.

    ![Open PowerShell-exemplaar](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Gebruik de seriële console voor NMI aanroepen
Een niet-maskeren interrupt (NMI) is ontworpen voor het maken van een signaal dat software op een virtuele machine wordt niet negeren. In het verleden zijn NMIs gebruikt om te controleren op hardwareproblemen op systemen die specifieke reactietijden vereist. Tegenwoordig gebruiken programmeurs en systeem beheerders NMI vaak als een mechanisme voor het opsporen van problemen met systemen die niet reageren.

De seriële console kan worden gebruikt voor het verzenden van een NMI met een Azure-machine met behulp van het toetsenbordpictogram in de opdrachtbalk. Nadat de NMI wordt geleverd, wordt de virtuele-machineconfiguratie bepalen hoe het systeem reageert. Windows kan worden geconfigureerd voor crashes en een geheugendumpbestand maken wanneer er een NMI worden ontvangen.

![NMI verzenden](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Zie voor meer informatie over het configureren van Windows voor het maken van een crashdumpbestand wanneer deze een NMI ontvangt [het genereren van een crashdump-bestand met behulp van een NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>De functietoetsen gebruiken in de seriële console
Functietoetsen zijn ingeschakeld voor gebruik voor de seriële console in de Windows-VM's. De F8 in de vervolgkeuzelijst de seriële console biedt het gemak van het invoeren van de geavanceerde instellingen voor opstarten menu eenvoudig, maar de seriële console compatibel is met alle andere functietoetsen. Mogelijk moet u op **FN** + **F1** drukken (of F2, F3, etc.) op het toetsen bord, afhankelijk van de computer waarvan u de seriële console gebruikt.

### <a name="use-wsl-in-serial-console"></a>Gebruik WSL in seriële console
Het Windows-subsysteem voor Linux (WSL) is ingeschakeld voor Windows Server 2019 of hoger, dus het is ook mogelijk om in te schakelen WSL voor gebruik binnen de seriële console als u werkt met Windows Server 2019 of hoger. Dit kan nuttig zijn voor gebruikers die ook een vertrouwd bent met Linux-opdrachten hebben zijn. Zie voor instructies voor het inschakelen van WSL voor Windows Server, de [installatiehandleiding](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Uw Windows VM/virtual machine Scale set-exemplaar opnieuw starten binnen een seriële console
U kunt een herstart starten binnen de seriële console door te navigeren naar de aan/uit-knop en op VM opnieuw opstarten te klikken. Hiermee start u het opnieuw opstarten van een virtuele machine en ziet u een melding binnen het Azure Portal met betrekking tot de herstart.

Dit is handig in situaties waarin u toegang wilt krijgen tot het opstart menu zonder dat u de seriële console-ervaring hoeft te verlaten.

![Windows seriële console opnieuw opstarten](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

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
> Dit betekent dat een gebruiker die niet verbonden wordt niet worden afgemeld. De mogelijkheid om af te dwingen een afmelden bij het verbreken van de verbinding (met behulp van SIGHUP of een vergelijkbaar mechanisme) is nog steeds in de roadmap. Voor Windows, er is een automatische time-out ingeschakeld in SAC; u kunt de terminal time-outinstelling configureren voor Linux.

## <a name="accessibility"></a>Toegankelijkheid
Toegankelijkheid is een belangrijke focus voor de seriële console van Azure. Wat dat betreft, hebben we ervoor gezorgd dat de seriële console is toegankelijk voor het visuele element en verminderde horen, evenals mensen die mogelijk niet langer een muis te gebruiken.

### <a name="keyboard-navigation"></a>Toetsenbordnavigatie
Gebruik de **tabblad** sleutel op het toetsenbord om te navigeren in de interface van de seriële console van de Azure-portal. Uw locatie wordt gemarkeerd op het scherm worden weergegeven. Als u wilt de focus van de seriële console-venster laten, drukt u op **Ctrl**+**F6** op het toetsenbord.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Gebruik de seriële console met een schermlezer
De seriële console heeft ingebouwde ondersteuning voor schermlezers. Navigeren om met een schermlezer ingeschakeld, kunnen de alt-tekst voor de geselecteerde knop om te worden door de schermlezer voorgelezen.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Algemene scenario's voor het openen van de seriële console

Scenario          | Acties in de seriële console
:------------------|:-----------------------------------------
Onjuiste firewall-regels | Toegang tot de seriële console en de oplossing Windows firewall-regels.
Bestandssysteem beschadigd/selectievakje | Toegang tot de seriële console en het bestandssysteem herstellen.
Problemen met RDP-configuratie | Toegang tot de seriële console en de instellingen wijzigen. Zie voor meer informatie de [RDP documentatie](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Systeem voor het vergrendelen van netwerk | Toegang tot de seriële console van de Azure-portal voor het beheren van het systeem. Sommige netwerk opdrachten worden weer gegeven [in Windows-opdrachten: CMD en Power](serial-console-cmd-ps-commands.md)shell.
Interactie met de bootloader | Toegang tot het BCD via de seriële console. Zie voor meer informatie, [inschakelen het opstartmenu Windows in de seriële console](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Bekende problemen
We zijn op de hoogte van problemen met de seriële console. Hier volgt een lijst van deze problemen beschreven en stappen voor risicobeperking. Deze problemen en oplossingen zijn van toepassing voor zowel Vm's als virtuele-machine schaal sets.

Probleem                             |   Oplossing
:---------------------------------|:--------------------------------------------|
Drukken **Enter** nadat de banner van de verbinding niet leidt een aanmeldingsprompt tot moet worden weergegeven. | Zie voor meer informatie, [Hitting invoeren, gebeurt er niets](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Deze fout kan optreden als u werkt met een aangepaste VM, beperkte toestel of boot-configuratie die ervoor zorgt Windows dat niet correct verbinding maken met de seriële poort. Deze fout treedt ook op als u een Windows 10-VM uitvoert, omdat alleen virtuele machines met Windows Server zijn geconfigureerd om EMS te kunnen inschakelen.
Alleen gegevens over de servicestatus wordt weergegeven bij het verbinden met een Windows-VM| Deze fout treedt op als de speciale beheer console niet is ingeschakeld voor uw Windows-installatie kopie. Zie [inschakelen van de seriële console in aangepaste of oudere installatiekopieën](#enable-the-serial-console-in-custom-or-older-images) voor instructies over het handmatig inschakelen SAC op uw Windows-VM. Zie voor meer informatie, [Windows health signalen](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
Kan niet naar het type op SAC vragen als kernelfoutopsporing is ingeschakeld. | RDP-verbinding VM en voer `bcdedit /debug {current} off` vanaf een opdrachtprompt met verhoogde bevoegdheid. Als u niet de RDP-verbinding, kunt u in plaats daarvan de besturingssysteemschijf koppelen aan een andere Azure-virtuele machine en wijzigen terwijl als een gegevensschijf gekoppeld door uit te voeren `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, klikt u vervolgens wisselen van de schijf weer.
Plakken in PowerShell in SAC resulteert in een derde teken als de oorspronkelijke inhoud beschikt over een herhalende teken. | Voor een tijdelijke oplossing Voer `Remove-Module PSReadLine` te verwijderen van de module PSReadLine uit de huidige sessie. Deze actie wordt niet verwijderen of de module verwijderen.
Sommige invoer toetsenbord vreemd SAC uitvoer produceren (bijvoorbeeld **[A**, **[3 ~** ). | [VT100](https://aka.ms/vtsequences) escapereeksen worden niet ondersteund door de SAC-prompt.
Lange tekenreeksen plakken werkt niet. | De seriële console beperkt de lengte van tekenreeksen in de terminal naar 2048 tekens om te voorkomen dat de seriële poort-bandbreedte overbelasten geplakt.
Seriële console werkt niet met een opslag account met behulp van Azure Data Lake Storage Gen2 met hiërarchische naam ruimten. | Dit is een bekend probleem met hiërarchische naam ruimten. Als u wilt beperken, moet u ervoor zorgen dat het opslag account voor diagnostische gegevens over opstarten van de virtuele machine niet is gemaakt met behulp van Azure Data Lake Storage Gen2. Deze optie kan alleen worden ingesteld bij het maken van een opslag account. Mogelijk moet u een afzonderlijk opslag account voor diagnostische gegevens over opstarten maken zonder dat Azure Data Lake Storage Gen2 ingeschakeld om dit probleem te verhelpen.


## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Q. Hoe kan ik feedback verzenden?**

A. Feedback geven door het maken van een GitHub-probleem aan https://aka.ms/serialconsolefeedback. U kunt ook (minder bij voorkeur), kunt u feedback via verzenden azserialhelp@microsoft.com of in de categorie van de virtuele machine van https://feedback.azure.com.

**Q. Biedt ondersteuning voor de seriële console kopiëren/plakken?**

A. Ja. Gebruik **Ctrl**+**Shift**+**C** en **Ctrl**+**Shift** + **V** kopiëren en plakken in de terminal.

**Q. Wie kunt inschakelen of uitschakelen van de seriële console voor mijn abonnement?**

A. Als u wilt in- of uitschakelen van de seriële console op het niveau van een brede, door het abonnement, moet u hebt schrijfmachtigingen voor het abonnement. Rollen die gemachtigd schrijven bevatten beheerder of eigenaar rollen. Aangepaste rollen kunnen ook schrijfmachtigingen hebben.

**Q. Wie toegang heeft tot de seriële console voor mijn VM?**

A. U moet de rol Inzender voor virtuele machines hebben of hoger voor een virtuele machine voor toegang tot de seriële console van de virtuele machine.

**Q. Mijn seriële console van alles zijn, niet wordt weergegeven wat moet ik doen?**

A. Uw installatiekopie is waarschijnlijk niet goed is geconfigureerd voor toegang tot de seriële console. Zie voor meer informatie over het configureren van de afbeelding om in te schakelen van de seriële console [inschakelen van de seriële console in aangepaste of oudere installatiekopieën](#enable-the-serial-console-in-custom-or-older-images).

**Q. Is de seriële console beschikbaar voor virtuele-machineschaalsets?**

A. Ja dat is zo! Zie de [seriële console voor Virtual Machine Scale sets](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Volgende stappen
* Zie [Windows-opdrachten voor een diep gaande hand leiding voor cmd-en Power shell-opdrachten die u kunt gebruiken in Windows SAC: CMD en Power](serial-console-cmd-ps-commands.md)shell.
* Seriële console van het is ook beschikbaar voor [Linux](serial-console-linux.md) VM's.
* Meer informatie over [diagnostische gegevens over opstarten](boot-diagnostics.md).
