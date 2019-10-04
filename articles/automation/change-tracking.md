---
title: Wijzigingen bijhouden met Azure Automation
description: De Wijzigingen bijhouden oplossing helpt u bij het identificeren van de software-en Windows-service wijzigingen die zich in uw omgeving voordoen.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: bobbytreed
ms.author: robreed
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8a1395c89b047bb120c7f7e2d2d9bb9b4d2b0c50
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959961"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Wijzigingen in uw omgeving bijhouden met de Wijzigingen bijhouden oplossing

Dit artikel helpt u bij het gebruik van de Wijzigingen bijhouden-oplossing om eenvoudig wijzigingen in uw omgeving te identificeren. De oplossing houdt wijzigingen bij van Windows-en Linux-software, Windows-en Linux-bestanden, Windows-register sleutels, Windows-Services en Linux-daemons. Het identificeren van configuratie wijzigingen kan u helpen bij het lokaliseren van operationele problemen.

Wijzigingen in geïnstalleerde software, Windows-Services, Windows-REGI ster en-bestanden en Linux-daemons op de bewaakte servers worden verzonden naar de Azure Monitor-service in de Cloud voor verwerking. Logica wordt toegepast op de ontvangen gegevens en de cloudservice registreert de gegevens. Met behulp van de informatie op het Wijzigingen bijhouden dash board kunt u eenvoudig de wijzigingen zien die zijn aangebracht in uw server infrastructuur.

> [!NOTE]
> Azure Automation Wijzigingen bijhouden houdt wijzigingen in de virtuele machines bij. Zie de [wijzigings geschiedenis](../governance/resource-graph/how-to/get-resource-changes.md)van de Azure-resource grafiek om de wijzigingen in de Azure Resource Manager-eigenschappen bij te houden.

## <a name="supported-windows-operating-systems"></a>Ondersteunde Windows-besturingssystemen

De volgende versies van het Windows-besturingssysteem worden officieel ondersteund voor de Windows-agent:

* Windows Server 2008 R2 of hoger

## <a name="supported-linux-operating-systems"></a>Ondersteunde Linux-besturingssystemen

De volgende Linux-distributies worden officieel ondersteund. De Linux-agent kan echter ook worden uitgevoerd op andere distributies die niet worden vermeld. Tenzij anders vermeld, worden alle secundaire releases ondersteund voor elke primaire versie die wordt vermeld.

### <a name="64-bit"></a>64-bits

* CentOS 6 en 7
* Amazon Linux 2017,09
* Oracle Linux 6 en 7
* Red Hat Enterprise Linux Server 6 en 7
* Debian GNU/Linux 8 en 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS en 18,04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32-bits

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 en 9
* Ubuntu Linux 14,04 LTS en 16,04 LTS

## <a name="onboard"></a>Wijzigingen bijhouden en inventaris inschakelen

Als u wijzigingen wilt bijhouden, moet u de Wijzigingen bijhouden-en inventaris oplossing inschakelen. Er zijn veel manieren om machines op Wijzigingen bijhouden en inventaris te voorbereiden. Hieronder vindt u de aanbevolen en ondersteunde manieren om de oplossing vrij te maken.

* [Van een virtuele machine](automation-onboard-solutions-from-vm.md)
* [Van surfen op meerdere computers](automation-onboard-solutions-from-browse.md)
* [Vanuit uw Automation-account](automation-onboard-solutions-from-automation-account.md)
* [Met een Azure Automation runbook](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Wijzigingen bijhouden en inventaris configureren

Ga voor meer informatie over het onboarden van computers naar de oplossing naar: [Onboarding van Automation-oplossingen](automation-onboard-solutions-from-automation-account.md). Wanneer u een computer hebt met de Wijzigingen bijhouden-en inventaris oplossing, kunt u de items configureren die moeten worden gevolgd. Wanneer u een nieuw bestand of register sleutel inschakelt om bij te houden, wordt deze ingeschakeld voor zowel Wijzigingen bijhouden als inventarisatie.

Voor het bijhouden van wijzigingen in bestanden in zowel Windows als Linux, worden MD5-hashes van de bestanden gebruikt. Deze hashes worden vervolgens gebruikt om te detecteren of er sinds de laatste inventarisatie een wijziging is aangebracht.

### <a name="file-integrity-monitoring-in-azure-security-center"></a>Controle van bestands integriteit in Azure Security Center

Azure Security Center heeft FIM (File Integrity Monitoring) op basis van Azure Wijzigingen bijhouden toegevoegd. Hoewel FIM alleen bestanden en registers bewaakt, omvat de volledige Wijzigingen bijhouden-oplossing ook:

- Software wijzigingen
- Windows-Services
- Linux-daemons

Als u FIM al hebt ingeschakeld en de volledige Wijzigingen bijhouden-oplossing wilt uitproberen, moet u de volgende stappen uitvoeren. De instellingen worden niet verwijderd door dit proces.

> [!NOTE]
> Als u de volledige Wijzigingen bijhouden-oplossing inschakelt, kunnen er extra kosten in rekening worden gebracht. Zie [prijzen voor Automation](https://azure.microsoft.com/en-us/pricing/details/automation/)voor meer informatie.

1. Verwijder de bewakings oplossing door te navigeren naar de werk ruimte en te zoeken in de [lijst met geïnstalleerde bewakings oplossingen](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Klik op de naam van de oplossing om de overzichts pagina te openen en klik vervolgens op verwijderen, zoals beschreven in [een bewakings oplossing verwijderen](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Schakel de oplossing opnieuw in door naar het Automation-account te gaan en **Wijzigingen bijhouden** te selecteren in het menu resource onder **configuratie beheer**.
4. Bevestig de details van de werk ruimte-instelling en klik op **inschakelen**.

### <a name="configure-linux-files-to-track"></a>Linux-bestanden configureren om bij te houden

Gebruik de volgende stappen om bestands tracering op Linux-computers te configureren:

1. Selecteer in uw Automation-account de optie **Wijzigingen bijhouden** onder **configuratie beheer**. Klik op **Instellingen bewerken** (het tandwiel symbool).
2. Selecteer op de pagina **Wijzigingen bijhouden** **Linux-bestanden**en klik vervolgens op **toevoegen** om een nieuw bestand toe te voegen aan de tracering.
3. Voer op het **Linux-bestand voor wijzigingen bijhouden toevoegen**de informatie in voor het bestand of de map die u wilt bijhouden en klik op **Opslaan**.

|Eigenschap  |Description  |
|---------|---------|
|Enabled     | Hiermee wordt bepaald of de instelling wordt toegepast.        |
|Itemnaam     | Beschrijvende naam van het bestand dat moet worden bijgehouden.        |
|Groep     | Een groeps naam voor het logisch groeperen van bestanden.        |
|Pad invoeren     | Het pad om het bestand te controleren. Bijvoorbeeld: "/etc/*. conf"       |
|Type pad     | Type item dat moet worden bijgehouden, mogelijke waarden zijn bestand en Directory.        |
|Recursie     | Bepaalt of recursie wordt gebruikt bij het zoeken naar het item dat moet worden bijgehouden.        |
|Sudo gebruiken     | Deze instelling bepaalt of sudo wordt gebruikt bij het controleren op het item.         |
|Koppelingen     | Deze instelling bepaalt hoe symbolische koppelingen worden afgehandeld bij het doorlopen van mappen.<br> **Negeren** : Hiermee worden symbolische koppelingen genegeerd en worden de bestanden/mappen waarnaar wordt verwezen, niet meegenomen.<br>**Follow** -volgt de symbolische koppelingen tijdens recursie en bevat ook de bestanden/mappen waarnaar wordt verwezen.<br>**Beheren** : volgt de symbolische koppelingen en staat het wijzigen van geretourneerde inhoud toe.     |
|Bestandsinhoud uploaden voor alle instellingen| Schakelt uploaden van bestandsinhoud bij bijgehouden wijzigingen in of uit. Beschikbare opties: **Waar** of **Onwaar**.|

> [!NOTE]
> Het gebruik van de optie 'Beheren' voor koppelingen wordt niet aanbevolen. Het ophalen van bestandsinhoud wordt niet ondersteund.

### <a name="configure-windows-files-to-track"></a>Windows-bestanden configureren voor het bijhouden van

Gebruik de volgende stappen om het bijhouden van bestanden op Windows-computers te configureren:

1. Selecteer in uw Automation-account de optie **Wijzigingen bijhouden** onder **configuratie beheer**. Klik op **Instellingen bewerken** (het tandwiel symbool).
2. Selecteer op de pagina **Wijzigingen bijhouden** **Windows-bestanden**en klik vervolgens op **toevoegen** om een nieuw bestand toe te voegen om bij te houden.
3. Voer op het **Windows-bestand voor wijzigingen bijhouden toevoegen**de informatie in voor het bestand dat u wilt bijhouden en klik op **Opslaan**.

|Eigenschap  |Description  |
|---------|---------|
|Enabled     | Hiermee wordt bepaald of de instelling wordt toegepast.        |
|Itemnaam     | Beschrijvende naam van het bestand dat moet worden bijgehouden.        |
|Groep     | Een groeps naam voor het logisch groeperen van bestanden.        |
|Pad invoeren     | Het pad voor het controleren op het bestand, bijvoorbeeld: "C:\temp\\\*.txt"<br>U kunt ook omgevingsvariabelen gebruiken zoals ' %winDir%\System32\\\*. * "       |
|Recursie     | Bepaalt of recursie wordt gebruikt bij het zoeken naar het item dat moet worden bijgehouden.        |
|Bestandsinhoud uploaden voor alle instellingen| Schakelt uploaden van bestandsinhoud bij bijgehouden wijzigingen in of uit. Beschikbare opties: **Waar** of **Onwaar**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Joker tekens, recursie en omgevings instellingen

Met recursie kunt u Joker tekens opgeven om het bijhouden van alle directory's en omgevings variabelen te vereenvoudigen, zodat u bestanden in verschillende omgevingen met meerdere of dynamische stations kunt bijhouden. De volgende lijst bevat algemene informatie die u moet kennen bij het configureren van recursie:

* Joker tekens zijn vereist voor het bijhouden van meerdere bestanden
* Als u Joker tekens gebruikt, kunnen ze alleen worden gebruikt in het laatste segment van een pad. (zoals `c:\folder\*file*` of `/etc/*.conf`)
* Als een omgevings variabele een ongeldig pad heeft, wordt de validatie uitgevoerd, maar wordt het pad mislukt wanneer de inventarisatie wordt uitgevoerd.
* Vermijd algemene paden zoals `c:\*.*` bij het instellen van het pad, omdat dit ertoe leidt dat er te veel mappen worden gepasseerd.

## <a name="configure-file-content-tracking"></a>Tracering van bestands inhoud configureren

U kunt de inhoud voor en na een wijziging van een bestand met bestands inhoud weer geven Wijzigingen bijhouden. Dit is beschikbaar voor Windows-en Linux-bestanden voor elke wijziging van het bestand wordt de inhoud van het bestand opgeslagen in een opslag account en wordt het bestand voor en na de wijziging, inline of naast elkaar weer gegeven. Zie [de inhoud van een bijgehouden bestand weer geven](change-tracking-file-contents.md)voor meer informatie.

![wijzigingen in een bestand weer geven](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Windows-register sleutels configureren voor het bijhouden van

Gebruik de volgende stappen om het bijhouden van register sleutels op Windows-computers te configureren:

1. Selecteer in uw Automation-account de optie **Wijzigingen bijhouden** onder **configuratie beheer**. Klik op **Instellingen bewerken** (het tandwiel symbool).
2. Selecteer op de pagina **Wijzigingen bijhouden** **Windows-REGI ster**en klik vervolgens op **toevoegen** om een nieuwe register sleutel toe te voegen die u wilt bijhouden.
3. Voer in het **Windows-REGI ster voor wijzigingen bijhouden toevoegen**de informatie in voor de sleutel die u wilt bijhouden en klik op **Opslaan**.

|Eigenschap  |Description  |
|---------|---------|
|Enabled     | Hiermee wordt bepaald of de instelling wordt toegepast.        |
|Itemnaam     | Beschrijvende naam van de register sleutel die moet worden bijgehouden.        |
|Groep     | Een groeps naam voor het logisch groeperen van register sleutels.        |
|Windows-registersleutel   | Het pad om de register sleutel te controleren. Bijvoorbeeld: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup      |

## <a name="limitations"></a>Beperkingen

De Wijzigingen bijhouden-oplossing biedt momenteel geen ondersteuning voor de volgende items:

* Recursie voor het bijhouden van Windows-REGI ster
* Netwerk bestands systemen

Andere beperkingen:

* De kolom en waarden voor de **maximale bestands grootte** worden niet gebruikt in de huidige implementatie.
* Als u meer dan 2500 bestanden in de verzamelings cyclus van 30 minuten verzamelt, kunnen de prestaties van de oplossing afnemen.
* Wanneer het netwerk verkeer hoog is, kan het tot zes uur duren voordat wijzigings records worden weer gegeven.
* Als u de configuratie wijzigt terwijl een computer is uitgeschakeld, kan het zijn dat de computer wijzigingen post die bij de vorige configuratie horen.

## <a name="known-issues"></a>Bekende problemen

De Wijzigingen bijhouden oplossing ondervindt momenteel de volgende problemen:

* Hotfix-updates worden niet verzameld op computers met Windows Server 2016 core RS3.
* Linux-daemons kunnen een gewijzigde status tonen, zelfs als er geen wijzigingen zijn. Dit komt door de manier waarop het veld `SvcRunLevels` wordt vastgelegd.

## <a name="change-tracking-data-collection-details"></a>Details van het verzamelen van Wijzigingen bijhouden gegevens

In de volgende tabel wordt de frequentie van gegevens verzameling voor de typen wijzigingen weer gegeven. Voor elk type wordt de moment opname van de huidige status ook ten minste elke 24 uur vernieuwd:

| **Type wijzigen** | **Frequentie** |
| --- | --- |
| Windows-REGI ster | 50 minuten |
| Windows-bestand | 30 minuten |
| Linux-bestand | 15 minuten |
| Windows-services | 10 seconden tot 30 minuten</br> Prijs 30 minuten |
| Linux-daemons | 5 minuten |
| Windows-software | 30 minuten |
| Linux-software | 5 minuten |

De volgende tabel bevat de limieten voor het bijgehouden item per computer voor Wijzigingen bijhouden.

| **Resource** | **Limiet**| **Opmerkingen** |
|---|---|---|
|File|500||
|Register|250||
|Windows-software|250|Bevat geen software-hotfixes|
|Linux-pakketten|1250||
|Services|250||
|Daemon|250||

Het gemiddelde Log Analytics gegevens gebruik voor een machine met Wijzigingen bijhouden en inventaris is ongeveer 40MB per maand. Deze waarde is alleen een benadering en kan worden gewijzigd op basis van uw omgeving. Het is raadzaam om uw omgeving te controleren om precies het gebruik te zien dat u hebt.

### <a name="windows-service-tracking"></a>Windows-service bijhouden

De standaard frequentie voor het verzamelen van Windows-Services is 30 minuten. Als u de frequentie wilt configureren, gaat u naar **Wijzigingen bijhouden**. Onder **Instellingen bewerken** op het tabblad **Windows-Services** ziet u een schuif regelaar waarmee u de verzamelings frequentie voor Windows-Services kunt wijzigen van 10 seconden tot 30 minuten. Verplaats de schuif regelaar naar de gewenste frequentie, waarna deze automatisch wordt opgeslagen.

![Schuif regelaar voor Windows-Services](./media/change-tracking/windowservices.png)

De agent traceert alleen wijzigingen, Hiermee optimaliseert u de prestaties van de agent. Als een hoge drempel waarde wordt ingesteld, kunnen wijzigingen verloren gaan als de oorspronkelijke status van de service is hersteld. Door de frequentie in te stellen op een lagere waarde, kunt u wijzigingen ondervangen die anders kunnen worden gemist.

> [!NOTE]
> Hoewel de agent wijzigingen kan bijhouden in een interval van 10 seconden, duurt het enkele minuten voordat de gegevens worden weer gegeven in de portal. Wijzigingen tijdens de tijd die in de portal worden weer gegeven, worden nog steeds bijgehouden en geregistreerd.

### <a name="registry-key-change-tracking"></a>Register sleutel wijzigingen bijhouden

Het doel van het bewaken van wijzigingen in register sleutels is het lokaliseren van uitbreidings punten waarbij code en malware van derden kunnen worden geactiveerd. De volgende lijst bevat de lijst met vooraf geconfigureerde register sleutels. Deze sleutels zijn geconfigureerd, maar niet ingeschakeld. Als u deze register sleutels wilt bijhouden, moet u deze inschakelen.

> [!div class="mx-tdBreakAll"]
> |Registersleutel | Doel |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Bewaakt veelvoorkomende auto start-vermeldingen die rechtstreeks in Windows Verkenner zijn aangesloten en die meestal in-process worden uitgevoerd met Explorer. exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Bewaakt de scripts die worden uitgevoerd bij het opstarten.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Bewaakt de scripts die worden uitgevoerd bij het afsluiten.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Bewaakt sleutels die worden geladen voordat de gebruiker zich aanmeldt bij het Windows-account. De sleutel wordt gebruikt voor 32-bits Program ma's die worden uitgevoerd op 64-bits computers.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Hiermee worden wijzigingen in toepassings instellingen gecontroleerd.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Bewaakt veelvoorkomende auto start-vermeldingen die rechtstreeks in Windows Verkenner zijn aangesloten en die meestal in-process worden uitgevoerd met Explorer. exe.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Bewaakt veelvoorkomende auto start-vermeldingen die rechtstreeks in Windows Verkenner zijn aangesloten en die meestal in-process worden uitgevoerd met Explorer. exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitor voor de registratie van het pictogram-overlay-handler.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitor voor de registratie van pictogram-overlaysoftware-handler voor 32-bits Program ma's die worden uitgevoerd op 64-bits computers.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitors voor nieuwe browserhelperobjecten voor Internet Explorer. Wordt gebruikt om toegang te krijgen tot de Document Object Model (DOM) van de huidige pagina en om de navigatie te beheren.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitors voor nieuwe browserhelperobjecten voor Internet Explorer. Wordt gebruikt voor toegang tot de Document Object Model (DOM) van de huidige pagina en voor het beheren van de navigatie voor 32-bits Program ma's die worden uitgevoerd op 64-bits computers.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Controleert op nieuwe Internet Explorer-uitbrei dingen, zoals menu's voor aangepaste gereedschappen en aangepaste werkbalk knoppen.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Controleert op nieuwe Internet Explorer-uitbrei dingen, zoals aangepaste hulpprogramma menu's en aangepaste werkbalk knoppen voor 32-bits Program ma's die worden uitgevoerd op 64-bits computers.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Bewaakt de 32-bits Stuur Programma's die zijn gekoppeld aan wavemapper, wave1 en wave2, Msacm. imaadpcm,. msadpcm,. msgsm610 en vidc. Vergelijkbaar met de sectie [drivers] in het systeem. INI-bestand.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Bewaakt de 32-bits Stuur Programma's die zijn gekoppeld aan wavemapper, wave1 en wave2, Msacm. imaadpcm,. msadpcm,. msgsm610 en vidc voor 32-bits-Program ma's die worden uitgevoerd op 64-bits computers. Vergelijkbaar met de sectie [drivers] in het systeem. INI-bestand.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Hiermee wordt de lijst met bekende of veelgebruikte systeem-Dll's gecontroleerd. Dit systeem voor komt dat gebruikers zwakke machtigingen voor de toepassingsmap kunnen exploiteren door de Trojaanse paarden-versies van systeem-Dll's te verwijderen.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Bewaakt de lijst met pakketten die gebeurtenis meldingen van Winlogon kunnen ontvangen, het interactieve aanmeldings model voor ondersteuning voor het Windows-besturings systeem.

## <a name="network-requirements"></a>Netwerkvereisten

De volgende adressen zijn specifiek vereist voor Wijzigingen bijhouden. De communicatie met deze adressen geschiedt via poort 443.

|Open bare Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*. azure-automation.us|

## <a name="use-change-tracking"></a>Wijzigingen bijhouden gebruiken

Nadat de oplossing is ingeschakeld, kunt u het overzicht van de wijzigingen voor uw bewaakte computers bekijken door **Wijzigingen bijhouden** te selecteren onder **configuratie beheer** in uw Automation-account.

U kunt wijzigingen in uw computers bekijken en vervolgens Details inzoomen voor elke gebeurtenis. Er zijn vervolg keuzelijsten aan de bovenkant van de grafiek beschikbaar om het diagram en gedetailleerde informatie te beperken op basis van het wijzigings type en de peri Oden. U kunt ook op het diagram klikken en slepen om een aangepast tijds bereik te selecteren. **Het wijzigings type** is een van de volgende waarden: **gebeurtenissen**, **daemons**, **bestanden**, **REGI ster**, **Software**, **Windows-Services**. Categorie toont u het type wijziging en kan worden **toegevoegd**, **gewijzigd**of **verwijderd**.

![afbeelding van Wijzigingen bijhouden dash board](./media/change-tracking/change-tracking-dash01.png)

Als u op een wijziging of gebeurtenis klikt, wordt gedetailleerde informatie over die wijziging beschreven. Zoals u in het voor beeld kunt zien, is het opstart type van de service gewijzigd van hand matig in auto.

![afbeelding van Details van het bijhouden van wijzigingen](./media/change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Logboeken zoeken

Naast de details die in de portal zijn opgenomen, kunnen Zoek opdrachten worden uitgevoerd op basis van de logboeken. Klik op **log Analytics**op de pagina **Wijzigingen bijhouden** om de pagina **Logboeken** te openen.

### <a name="sample-queries"></a>Voorbeeldquery's

De volgende tabel bevat voor beelden van zoek opdrachten in Logboeken voor wijzigings records die door deze oplossing worden verzameld:

|Query’s uitvoeren  |Description  |
|---------|---------|
|ConfigurationData<br>&#124;Where ConfigDataType = = "WindowsServices" and SvcStartupType = = "auto"<br>&#124;Where SvcState = = "stopped"<br>&#124;arg_max (TimeGenerated, *) samenvatten op software naam, computer         | Toont de meest recente inventaris records voor Windows-services die zijn ingesteld op auto, maar die zijn gerapporteerd als gestopt<br>De resultaten zijn beperkt tot de meest recente record voor die software naam en computer      |
|ConfigurationChange<br>&#124;waarbij ConfigChangeType = = "software" en ChangeCategory = = "verwijderd"<br>&#124;order by TimeGenerated desc|Toont de wijzigings records voor verwijderde software|

## <a name="alert-on-changes"></a>Waarschuwing bij wijzigingen

Een belang rijke mogelijkheid van Wijzigingen bijhouden en inventaris is de mogelijkheid om een waarschuwing te sturen over de configuratie status en eventuele wijzigingen in de configuratie status van uw hybride omgeving.

In het volgende voor beeld ziet u in de scherm afbeelding dat het bestand `C:\windows\system32\drivers\etc\hosts` op een machine is gewijzigd. Dit bestand is belang rijk omdat het hosts-bestand door Windows wordt gebruikt voor het omzetten van hostnamen naar IP-adressen en voor rang heeft boven zelfs DNS, wat kan leiden tot verbindings problemen of het omleiden van verkeer naar kwaad aardige of anderszins gevaarlijke websites.

![Een grafiek met de wijziging van het bestand hosts](./media/change-tracking/changes.png)

Ga naar zoeken in Logboeken om deze wijziging verder te analyseren en klik op **log Analytics**. Zoek in logboek zoeken naar inhoud wijzigingen in het bestand hosts met de query `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Deze query zoekt naar wijzigingen die een wijziging van de bestands inhoud bevatten voor bestanden waarvan het volledige pad het woord hosts bevat. U kunt ook om een specifiek bestand vragen door het pad naar de volledig gekwalificeerde vorm te wijzigen (bijvoorbeeld `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

Nadat de query de gewenste resultaten heeft geretourneerd, klikt u op de knop **nieuwe waarschuwings regel** in de zoek functie voor Logboeken om de pagina voor het maken van een waarschuwing te openen. U kunt ook naar deze ervaring navigeren via **Azure monitor** in de Azure Portal. Controleer bij het maken van de waarschuwing de query opnieuw en wijzig de logica van de waarschuwing. In dit geval moet de waarschuwing worden geactiveerd als er zelfs één wijziging wordt gedetecteerd op alle computers in de omgeving.

![Een afbeelding waarin de wijzigings query wordt weer gegeven voor het bijhouden van wijzigingen aan het hosts-bestand](./media/change-tracking/change-query.png)

Nadat de logica van de voor waarde is ingesteld, wijst u actie groepen toe om acties uit te voeren als reactie op de waarschuwing die wordt geactiveerd. In dit geval moet ik e-mail berichten laten verzenden en een ITSM-ticket maken.  Er kunnen ook veel andere nuttige acties worden uitgevoerd, zoals het activeren van een Azure-functie, Automation-runbook, webhook of logische app.

![Een installatie kopie die een actie groep configureert voor waarschuwing over de wijziging](./media/change-tracking/action-groups.png)

Nadat alle para meters en logica zijn ingesteld, kunnen we de waarschuwing Toep assen op de omgeving.

### <a name="alert-suggestions"></a>Waarschuwings suggesties

Het is een goede toepassing van waarschuwingen voor Wijzigingen bijhouden-of inventaris gegevens, maar er zijn veel meer scenario's voor waarschuwingen, waaronder de cases die zijn gedefinieerd samen met hun voorbeeld query's in de onderstaande sectie.

|Query’s uitvoeren  |Description  |
|---------|---------|
|ConfigurationChange <br>&#124;waarbij ConfigChangeType = = "files" en FileSystemPath bevat "c: \\windows @ no__t-2system32 @ no__t-3drivers @ no__t-4"|Handig voor het bijhouden van wijzigingen in essentiële bestanden van het systeem|
|ConfigurationChange <br>&#124;waarbij FieldsChanged ' FileContentChecksum ' en FileSystemPath = = ' c: \\windows @ no__t-2system32 @ no__t-3drivers @ no__t-4etc @ no__t-5hosts ' bevat.|Handig voor het bijhouden van wijzigingen in sleutel configuratie bestanden|
|ConfigurationChange <br>&#124;Where ConfigChangeType = = "WindowsServices" en SvcName bevat "W3SVC" en SvcState = "gestopt"|Handig voor het bijhouden van wijzigingen in essentiële systeem services|
|ConfigurationChange <br>&#124;Where ConfigChangeType = = "daemons" en SvcName bevat "SSH" en SvcState! = "running"|Handig voor het bijhouden van wijzigingen in essentiële systeem services|
|ConfigurationChange <br>&#124;Where ConfigChangeType = = "software" and ChangeCategory = = "Added"|Handig voor omgevingen waarvoor software configuraties moeten worden vergrendeld|
|ConfigurationData <br>&#124;waarbij softwarenaam "Monitoring Agent" en CurrentVersion! = "8.0.11081.0" bevat|Nuttig als u wilt zien op welke computers een verouderde of niet-compatibele software versie is geïnstalleerd. Het rapporteert de laatst gerapporteerde configuratie status, niet de wijzigingen.|
|ConfigurationChange <br>&#124;Where RegistryKey = = @ "HKEY_LOCAL_MACHINE @ no__t-1SOFTWARE @ no__t-2Microsoft @ no__t-3Windows @ no__t-4CurrentVersion @ no__t-5QualityCompat"| Handig voor het bijhouden van wijzigingen in essentiële anti-virus sleutels|
|ConfigurationChange <br>&#124;waar RegistryKey bevat @ "HKEY_LOCAL_MACHINE @ no__t-1SYSTEM @ no__t-2CurrentControlSet @ no__t-3Services @ no__t-4SharedAccess @ no__t-5Parameters @ no__t-6FirewallPolicy"| Handig voor het bijhouden van wijzigingen in Firewall-instellingen|

## <a name="next-steps"></a>Volgende stappen

Ga naar de zelf studie over Wijzigingen bijhouden voor meer informatie over het gebruik van de oplossing:

> [!div class="nextstepaction"]
> [Problemen met wijzigingen in uw omgeving oplossen](automation-tutorial-troubleshoot-changes.md)

* [Zoek opdrachten in Logboeken in azure monitor logboeken](../log-analytics/log-analytics-log-searches.md) gebruiken om gedetailleerde gegevens voor het bijhouden van wijzigingen weer te geven.
