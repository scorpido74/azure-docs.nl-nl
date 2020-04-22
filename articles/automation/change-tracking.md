---
title: Wijzigingen bijhouden met Azure Automation
description: Met de oplossing Voor het bijhouden van wijzigingen u software- en Windows-servicewijzigingen identificeren die zich in uw omgeving voordoen.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: d84566c7680081561f60d4825f25a9ce19e02b24
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682978"
---
# <a name="track-environment-changes-with-change-tracking"></a>Omgevingswijzigingen bijhouden met Change Tracking

In dit artikel u de oplossing Voor het bijhouden van wijzigingen gebruiken om eenvoudig wijzigingen in uw omgeving te identificeren. De oplossing houdt de volgende configuratiewijzigingen bij om u te helpen operationele problemen op te sporen:

- Windows-software
- Linux software (pakketten)
    >[!NOTE]
    >Change Tracking houdt alleen software bij die wordt beheerd met de distributiepakketbeheerder.

- Windows- en Linux-bestanden
- Windows-registersleutels
- Windows-services
- Linux daemons

Nadat de oplossing is ingeschakeld, u het overzicht van wijzigingen voor uw bewaakte computers bekijken door **Bijhouden wijzigen** te selecteren onder **Configuratiebeheer** in uw automatiseringsaccount.

> [!NOTE]
> Azure Automation Change Tracking houdt wijzigingen in virtuele machines bij. Zie de [wijzigingsgeschiedenis](../governance/resource-graph/how-to/get-resource-changes.md)van Azure Resource Graph als u wijzigingen in de eigenschap Azure Resource Manager wilt bijhouden.

U wijzigingen in uw computers weergeven en vervolgens details inzoomen voor elke gebeurtenis. Vervolgkeuzewaarden zijn beschikbaar boven aan de grafiek om de grafiek te beperken en gedetailleerde informatie op basis van het type wijziging en de tijdbereiken. U ook op de grafiek klikken en slepen om een aangepast tijdsbereik te selecteren. **Type wijzigen** is een van de volgende waarden **Gebeurtenissen**, **Daemons**, **Bestanden**, **Register**, **Software**, **Windows Services**. Categorie toont u het type wijziging en kan worden **toegevoegd,** **gewijzigd**of **verwijderd**.

![afbeelding van het dashboard Wijzigingsbijhouden](./media/change-tracking/change-tracking-dash01.png)

Als u op een wijziging of gebeurtenis klikt, wordt de gedetailleerde informatie over die wijziging weergegeven. Zoals u in het voorbeeld zien, is het opstarttype van de service gewijzigd van Handmatig naar Automatisch.

![afbeelding van details voor het bijhouden van wijzigingen](./media/change-tracking/change-tracking-details.png)

Wijzigingen in geïnstalleerde software, Windows-services, Windows-register en bestanden en Linux-daemons op de bewaakte servers worden verzonden naar de Azure Monitor-service in de cloud voor verwerking. Logica wordt toegepast op de ontvangen gegevens en de gegevens worden vastgelegd door de cloudservice. Door de informatie op het dashboard Wijzigingstracking te gebruiken, u eenvoudig de wijzigingen zien die zijn aangebracht in uw serverinfrastructuur.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

### <a name="windows-operating-systems"></a>Windows-besturingssystemen

De volgende versies van het Windows-besturingssysteem worden officieel ondersteund voor de Windows-agent:

* Windows Server 2008 R2 of later

### <a name="linux-operating-systems"></a>Linux-besturingssystemen

De volgende Linux distributies worden officieel ondersteund. Echter, de Linux-agent kan ook draaien op andere distributies niet vermeld. Tenzij anders vermeld, worden alle kleine releases ondersteund voor elke belangrijke versie die wordt vermeld.

#### <a name="64-bit"></a>64-bits

* CentOS 6 en 7
* Amazon Linux 2017.09
* Oracle Linux 6 en 7
* Red Hat Enterprise Linux Server 6 en 7
* Debian GNU/Linux 8 en 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS en 18.04 LTS
* SUSE Linux Enterprise Server 12

#### <a name="32-bit"></a>32-bits

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 en 9
* Ubuntu Linux 14.04 LTS en 16.04 LTS

## <a name="limitations"></a>Beperkingen

De oplossing Voor het bijhouden van wijzigingen ondersteunt momenteel niet de volgende items:

* Recursion voor Windows-registertracking
* Netwerkbestandssystemen
* Verschillende installatiemethoden
* ***.exe-bestanden** voor Windows

Andere beperkingen:

* De kolom En waarden **van de maximale bestandsgrootte** worden niet gebruikt in de huidige implementatie.
* Als u meer dan 2500 bestanden verzamelt in een verzamelingscyclus van 30 minuten, kunnen de prestaties van de oplossing worden aangetast.
* Wanneer het netwerkverkeer hoog is, kan het tot zes uur duren voordat wijzigingsrecords worden weergegeven.
* Als u de configuratie wijzigt terwijl een computer wordt afgesloten, kan de computer wijzigingen plaatsen die behoren tot de vorige configuratie.

## <a name="known-issues"></a>Bekende problemen

De oplossing Voor het bijhouden van wijzigingen ondervindt momenteel de volgende problemen:

* Hotfix-updates worden niet verzameld op Windows Server 2016 Core RS3-machines.
* Linux Daemons kunnen een veranderde status laten zien, ook al is er geen verandering. Dit is te `SvcRunLevels` wijten aan hoe het veld wordt vastgelegd.

## <a name="network-requirements"></a>Netwerkvereisten

Voor het bijhouden van wijzigingen zijn specifiek de volgende adressen vereist. Communicatie naar deze adressen maakt gebruik van poort 443.

|Openbare Azure-peering  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="wildcard-recursion-and-environment-settings"></a>Wildcard-, recursie- en omgevingsinstellingen

Met Recursion u wildcards opgeven om het bijhouden van mappen en omgevingsvariabelen te vereenvoudigen, zodat u bestanden bijhouden in verschillende omgevingen met meerdere of dynamische schijfnamen. In de volgende lijst worden algemene gegevens weergegeven die u moet weten bij het configureren van recursie:

* Wildcards zijn vereist voor het bijhouden van meerdere bestanden.
* Wildcards die alleen worden gebruikt in het laatste segment van\\een pad, bijvoorbeeld c:\mapbestand* of /etc/*.conf.
* Als een omgevingsvariabele een ongeldig pad heeft, slaagt validatie, maar dat pad mislukt wanneer Voorraad wordt uitgevoerd.
* Vermijd algemene paden bij het instellen van het pad, omdat dit type instelling ervoor kan zorgen dat er te veel mappen worden doorkruisen.

## <a name="change-tracking-data-collection-details"></a>Gegevensover het bijhouden van gegevensverzameling wijzigen

In de volgende tabel ziet u de frequentie van het verzamelen van gegevens voor de typen wijzigingen. Voor elk type wordt de gegevensmomentopname van de huidige status ook ten minste om de 24 uur vernieuwd:

| **Type wijzigen** | **Frequentie** |
| --- | --- |
| Windows-register | 50 minuten |
| Windows-bestand | 30 minuten |
| Linux-bestand | 15 minuten |
| Windows-services | 10 seconden tot 30 minuten</br> Standaard: 30 minuten |
| Linux daemons | 5 minuten |
| Windows-software | 30 minuten |
| Linux-software | 5 minuten |

In de volgende tabel worden de bijgehouden itemlimieten per machine voor Het bijhouden van wijzigingen weergegeven.

| **Resource** | **Limiet**| **Opmerkingen** |
|---|---|---|
|File|500||
|Register|250||
|Windows-software|250|Bevat geen softwarehotfixes|
|Linux pakketten|1250||
|Services|250||
|Daemon|250||

Het gemiddelde gegevensgebruik van Log Analytics voor een machine met change tracking is ongeveer 40 MB per maand. Deze waarde is slechts een benadering en kan worden gewijzigd op basis van uw omgeving. Het wordt aanbevolen dat u uw omgeving controleert om het exacte gebruik te zien dat u hebt.

### <a name="windows-service-tracking"></a>Windows-servicetracking

De standaardverzamelingsfrequentie voor Windows-services is 30 minuten. Als u de frequentie wilt configureren, gaat u naar **Bijhouden wijzigen**. Onder **Instellingen bewerken** op het tabblad Windows **Services** is er een schuifregelaar waarmee u de verzamelfrequentie voor Windows-services wijzigen van 10 seconden tot wel 30 minuten. Verplaats de schuifbalk naar de gewenste frequentie en deze slaat deze automatisch op.

![Schuifregelaar windows-services](./media/change-tracking/windowservices.png)

De agent houdt alleen wijzigingen bij, dit optimaliseert de prestaties van de agent. Als u een hoge drempelwaarde instelt, kunnen wijzigingen ontbreken als de service is teruggezet naar de oorspronkelijke status. Als u de frequentie instelt op een kleinere waarde, u wijzigingen opvangen die anders mogelijk worden gemist.

> [!NOTE]
> Hoewel de agent wijzigingen kan bijhouden tot een interval van 10 seconden, duurt het nog enkele minuten voordat de gegevens in de portal worden weergegeven. Wijzigingen gedurende de tijd die u in de portal wilt weergeven, worden nog steeds bijgehouden en geregistreerd.

### <a name="registry-key-change-tracking"></a>Registersleutelwijziging bijhouden

Het doel van het bewaken van wijzigingen in registersleutels is het lokaliseren van uitbreidbaarheidspunten waar code en malware van derden kunnen worden geactiveerd. In de volgende lijst ziet u de lijst met vooraf geconfigureerde registersleutels. Deze sleutels zijn geconfigureerd, maar niet ingeschakeld. Als u deze registersleutels wilt bijhouden, moet u ze allemaal inschakelen.

> [!div class="mx-tdBreakAll"]
> |Registersleutel | Doel |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Controleert veelvoorkomende automatischestartvermeldingen die rechtstreeks in Windows Verkenner worden aangesloten en worden meestal uitgevoerd met Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Controleert scripts die worden uitgevoerd bij het opstarten.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Controleert scripts die worden uitgevoerd bij afsluiten.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Controleert sleutels die worden geladen voordat de gebruiker zich aanmeldt bij zijn Windows-account. De sleutel wordt gebruikt voor 32-bits programma's die worden uitgevoerd op 64-bits computers.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Hiermee worden wijzigingen in de toepassingsinstellingen bewaakt.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Controleert veelvoorkomende automatischestartvermeldingen die rechtstreeks in Windows Verkenner worden aangesloten en worden meestal uitgevoerd met Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Controleert veelvoorkomende automatischestartvermeldingen die rechtstreeks in Windows Verkenner worden aangesloten en worden meestal uitgevoerd met Explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitors voor registratie van pictogramoverlayhandlers.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitors voor registratie van pictogramoverlayhandlers voor 32-bits programma's die worden uitgevoerd op 64-bits computers.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Controleert op nieuwe plug-ins voor browserhelperobjecten voor Internet Explorer. Wordt gebruikt om toegang te krijgen tot het Document Object Model (DOM) van de huidige pagina en om de navigatie te beheren.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Controleert op nieuwe plug-ins voor browserhelperobjecten voor Internet Explorer. Wordt gebruikt om toegang te krijgen tot het Document Object Model (DOM) van de huidige pagina en om de navigatie te regelen voor 32-bits programma's die op 64-bits computers worden uitgevoerd.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Beeldschermen voor nieuwe Internet Explorer-extensies, zoals aangepaste gereedschapsmenu's en aangepaste werkbalkknoppen.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Beeldschermen voor nieuwe Internet Explorer-extensies, zoals aangepaste gereedschapsmenu's en aangepaste werkbalkknoppen voor 32-bits programma's die op 64-bits computers worden uitgevoerd.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Bewaakt de 32-bits drivers in verband met wavemapper, wave1 en wave2, msacm.imaadpcm, .msadpcm, .msgsm610 en vidc. Vergelijkbaar met de [drivers] sectie in het systeem. INI-bestand.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Bewaakt de 32-bits drivers die worden geassocieerd met wavemapper, wave1 en wave2, msacm.imaadpcm, .msadpcm, .msgsm610 en vidc voor 32-bits programma's die op 64-bits computers worden uitgevoerd. Vergelijkbaar met de [drivers] sectie in het systeem. INI-bestand.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Controleert de lijst met bekende of veelgebruikte systeemdllen; dit systeem voorkomt dat mensen van het exploiteren van zwakke applicatie directory machtigingen door te laten vallen in Trojaans paard versies van systeem DLL's.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Bewaakt de lijst met pakketten die gebeurtenismeldingen kunnen ontvangen van Winlogon, het interactieve ondersteuningsmodel voor aanmelding voor het Windows-besturingssysteem.

## <a name="enable-change-tracking"></a><a name="onboard"></a>Change Tracking inschakelen

Als u wijzigingen wilt bijhouden, moet u de oplossing voor het bijhouden van wijzigingen inschakelen. Er zijn veel manieren om machines aan boord te gaan om tracking te wijzigen. De volgende zijn de aanbevolen en ondersteunde manieren om aan boord van de oplossing.

* [Van een virtuele machine](automation-onboard-solutions-from-vm.md)
* [Van het browsen op meerdere machines](automation-onboard-solutions-from-browse.md)
* [Vanaf uw Automatiseringsaccount](automation-onboard-solutions-from-automation-account.md)
* [Met een Azure Automation-runbook](automation-onboard-solutions.md)

## <a name="configure-change-tracking"></a>Wijzigingstracking configureren

Zie [Onboarding Automation-oplossingen](automation-onboard-solutions-from-automation-account.md)voor meer informatie over het instappen van computers voor de oplossing. Zodra u een machine onboarding met de Change Tracking-oplossing hebt, u de items configureren om bij te houden. Wanneer u een nieuw bestand of registersleutel inschakelt om bij te houden, is deze ingeschakeld voor beide Change Tracking.

Voor het bijhouden van wijzigingen in bestanden op zowel Windows als Linux, md5 hashes van de bestanden worden gebruikt. Deze hashes worden vervolgens gebruikt om te detecteren of er een wijziging is aangebracht sinds de laatste voorraad.

## <a name="enable-file-integrity-monitoring-in-azure-security-center"></a>Bestandsintegriteitscontrole inschakelen in Azure Security Center

Azure Security Center heeft FiM (File Integrity Monitoring) toegevoegd, die is gebaseerd op Azure Change Tracking. Hoewel FIM alleen bestanden en registers controleert, bevat de volledige oplossing voor het bijhouden van wijzigingen ook:

- Softwarewijzigingen
- Windows-services
- Linux Daemons

Als u FIM al hebt ingeschakeld en de volledige oplossing voor het bijhouden van wijzigingen wilt uitproberen, moet u de volgende stappen uitvoeren. U instellingen worden niet verwijderd door dit proces.

> [!NOTE]
> Het inschakelen van de volledige oplossing voor het bijhouden van wijzigingen kan extra kosten met zich meebrengen, zie [Automatiseringsprijzen](https://azure.microsoft.com/pricing/details/automation/)voor meer informatie.

1. Verwijder de bewakingsoplossing door naar de werkruimte te navigeren en deze te vinden in de [lijst met geïnstalleerde bewakingsoplossingen.](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)
2. Klik op de naam van de oplossing om de overzichtspagina te openen en klik vervolgens op Verwijderen, zoals beschreven in [Een bewakingsoplossing verwijderen.](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)
3. Schakel de oplossing opnieuw in door naar het automatiseringsaccount te navigeren en **Wijzigingstracking** te selecteren onder **Configuratiebeheer**.
4. Bevestig de details van de instelling van uw werkruimte en klik op **Inschakelen**.

## <a name="configure-file-content-change-tracking"></a>Tracking van bestandsinhoudswijzigingen configureren

U de inhoud voor en na bestandswijziging bekijken met het bijhouden van bestandsinhoudswijzigingen. Deze functie is beschikbaar voor Windows- en Linux-bestanden. Voor elke wijziging in een bestand wordt de inhoud van het bestand opgeslagen in een opslagaccount. Het bestand wordt voor en na de wijziging weergegeven, inline of naast elkaar. Zie [De inhoud van een bijgehouden bestand weergeven](change-tracking-file-contents.md)voor meer informatie.

![wijzigingen in een bestand weergeven](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="configure-windows-registry-keys-to-track"></a>Windows-registersleutels configureren om bij te houden

Gebruik de volgende stappen om registersleuteltracking op Windows-computers te configureren:

1. Selecteer in uw automatiseringsaccount De optie **Tracking wijzigen** onder **Configuratiebeheer**. Klik **op Instellingen bewerken** (het tandwielsymbool).
2. Selecteer op de pagina Bijhouden wijzigen de optie **Windows-register**en klik op **+ Toevoegen** om een nieuwe registersleutel toe te voegen om bij te houden.
3. Voer in het **Windows-register toevoegen voor bijhouden van wijzigingen**de gegevens in voor de sleutel om bij te houden en klik op **Opslaan**.

|Eigenschap  |Beschrijving  |
|---------|---------|
|Ingeschakeld     | Hiermee bepaalt u of de instelling wordt toegepast.        |
|Itemnaam     | Vriendelijke naam van de registersleutel die moet worden bijgehouden.        |
|Groep     | Een groepsnaam voor het logisch groeperen van registersleutels.        |
|Windows-registersleutel   | Het pad om te controleren op de registersleutel. Bijvoorbeeld: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="configure-file-tracking-on-windows"></a>Bestandstracking configureren op Windows

Gebruik de volgende stappen om bestandstracking op Windows-computers te configureren:

1. Selecteer in uw automatiseringsaccount De optie **Tracking wijzigen** onder **Configuratiebeheer**. Klik **op Instellingen bewerken** (het tandwielsymbool).
2. Selecteer op de pagina Bijhouden wijzigen de optie **Windows-bestanden**en klik op **+ Toevoegen** om een nieuw bestand toe te voegen dat u wilt bijhouden.
3. Voer in het **Windows-bestand voor bijhouden van wijzigingen**de gegevens in voor het bestand dat u wilt bijhouden en klik op **Opslaan**.

|Eigenschap  |Beschrijving  |
|---------|---------|
|Ingeschakeld     | True als de instelling wordt toegepast en Onwaar anders.        |
|Itemnaam     | Vriendelijke naam van het bestand dat moet worden bijgehouden.        |
|Groep     | Een groepsnaam voor het logisch groeperen van bestanden.        |
|Pad invoeren     | Het pad om te controleren op het bestand, bijvoorbeeld **c:\temp\\\*.txt**<br>U ook omgevingsvariabelen gebruiken, zoals `%winDir%\System32\\\*.*`.       |
|Recursie     | True als recursie wordt gebruikt bij het zoeken naar het item dat moet worden bijgehouden en False anders.        |
|Bestandsinhoud uploaden voor alle instellingen| Trouw aan het uploaden van bestandsinhoud op bijgehouden wijzigingen en False anders.|

## <a name="configure-file-tracking-on-linux"></a>Bestandstracking configureren op Linux

Gebruik de volgende stappen om bestandstracking op Linux-computers te configureren:

1. Selecteer in uw automatiseringsaccount De optie **Tracking wijzigen** onder **Configuratiebeheer**. Klik **op Instellingen bewerken** (het tandwielsymbool).
2. Selecteer op de pagina Bijhouden wijzigen de optie **Linux-bestanden**en klik op **+ Toevoegen** om een nieuw bestand toe te voegen dat u wilt bijhouden.
3. Voer in het **Add Linux-bestand voor het bijhouden van wijzigingen**de gegevens voor het bestand of de map in om bij te houden en op Opslaan te **klikken**.

|Eigenschap  |Beschrijving  |
|---------|---------|
|Ingeschakeld     | Hiermee bepaalt u of de instelling wordt toegepast.        |
|Itemnaam     | Vriendelijke naam van het bestand dat moet worden bijgehouden.        |
|Groep     | Een groepsnaam voor het logisch groeperen van bestanden.        |
|Pad invoeren     | Het pad om te controleren op het bestand. Bijvoorbeeld: "/etc/*.conf"       |
|Padtype     | Type item dat moet worden bijgehouden, mogelijke waarden zijn Bestand en Directory.        |
|Recursie     | Bepaalt of recursie wordt gebruikt bij het zoeken naar het item dat moet worden bijgehouden.        |
|Sudo gebruiken     | Deze instelling bepaalt of sudo wordt gebruikt bij het controleren op het item.         |
|Koppelingen     | Deze instelling bepaalt hoe symbolische koppelingen worden afgehandeld bij het doorlopen van mappen.<br> **Negeren** - Negeert symbolische koppelingen en bevat niet de bestanden / mappen waarnaar wordt verwezen.<br>**Follow** - Volgt de symbolische links tijdens de recursie en bevat ook de bestanden / mappen waarnaar wordt verwezen.<br>**Manage** - Volgt de symbolische links en maakt het wijzigen van geretourneerde inhoud mogelijk.     |
|Bestandsinhoud uploaden voor alle instellingen| Schakelt uploaden van bestandsinhoud bij bijgehouden wijzigingen in of uit. Beschikbare opties: **Waar** of **Onwaar**.|

> [!NOTE]
> Het gebruik van de optie 'Beheren' voor koppelingen wordt niet aanbevolen. Het ophalen van bestandsinhoud wordt niet ondersteund.

## <a name="search-logs"></a>Logboeken zoeken

U verschillende zoekopdrachten uitvoeren tegen de logboeken voor wijzigingsrecords. Als de pagina Bijhouden wijzigen is geopend, klikt u op **Logboekanalyse**, hiermee wordt de pagina Logboeken geopend. In de volgende tabel vindt u voorbeeldlogboekzoekopdrachten voor wijzigingsrecords die door deze oplossing zijn verzameld:

|Query’s uitvoeren  |Beschrijving  |
|---------|---------|
|ConfiguratieGegevens<br>&#124; waar ConfigDataType == "WindowsServices" en SvcStartupType == "Auto"<br>&#124; waar SvcState == "Gestopt"<br>&#124; arg_max(TimeGenerated, *) samenvatten op SoftwareName, Computer         | Toont de meest recente voorraadrecords voor Windows Services die zijn ingesteld op Automatisch, maar die zijn gerapporteerd als gestopt<br>Resultaten zijn beperkt tot de meest recente record voor die SoftwareName en Computer      |
|Configuratiewijzigen<br>&#124; waar ConfigChangeType == "Software" en ChangeCategory == "Removed"<br>&#124; order door TimeGenerated desc|Toont de wijzigingsrecords voor verwijderde software|

## <a name="alert-on-changes"></a>Waarschuwing over wijzigingen

Een belangrijke mogelijkheid van Change Tracking is waarschuwing over de configuratiestatus en eventuele wijzigingen in de configuratiestatus van uw hybride omgeving. In het volgende voorbeeld wordt weergegeven dat het bestand **C:\windows\system32\drivers\etc\hosts** is gewijzigd op een machine. Dit bestand is belangrijk omdat Windows het gebruikt om hostnamen op te lossen naar IP-adressen. Deze bewerking heeft voorrang op DNS en kan leiden tot verbindingsproblemen of de omleiding van verkeer naar schadelijke of anderszins gevaarlijke websites.

![Een grafiek met de wijziging van het hosts-bestand](./media/change-tracking/changes.png)

Als u deze wijziging verder wilt analyseren, gaat u naar Zoeken aanmelden door op **Logboekanalyse**te klikken . Zoek in De query `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`naar inhoudswijzigingen in het hosts-bestand. In deze query wordt gezocht naar wijzigingen die een wijziging van de bestandsinhoud bevatten voor bestanden waarvan het volledig gekwalificeerde pad het woord 'hosts' bevat. U ook om een specifiek bestand vragen door het padgedeelte te wijzigen in de volledig gekwalificeerde vorm (zoals `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

Nadat de query de gewenste resultaten heeft geretourneerd, klikt u op **Nieuwe waarschuwingsregel** in de logboekzoekopdracht om de pagina voor het maken van waarschuwingen te openen. U ook naar deze ervaring navigeren via **Azure Monitor** in de Azure-portal. 

Controleer uw query opnieuw en wijzig de waarschuwingslogica. In dit geval wilt u dat de waarschuwing wordt geactiveerd als er zelfs één wijziging wordt gedetecteerd voor alle machines in de omgeving.

![Een afbeelding met de wijzigingsquery voor het bijhouden van wijzigingen in het hosts-bestand](./media/change-tracking/change-query.png)

Nadat de voorwaardelogica is ingesteld, wijst u actiegroepen toe om acties uit te voeren als reactie op de waarschuwing die wordt geactiveerd. In dit geval heb ik het opzetten van e-mails worden verzonden en een ITSM ticket worden gemaakt.  Er kunnen ook veel andere nuttige acties worden uitgevoerd, zoals het activeren van een Azure-functie, automatiseringsrunbook, webhook of logische app.

![Een afbeelding die een actiegroep configureert om te waarschuwen voor de wijziging](./media/change-tracking/action-groups.png)

Nadat alle parameters en logica zijn ingesteld, kunnen we de waarschuwing toepassen op de omgeving.

### <a name="alert-suggestions"></a>Waarschuwingssuggesties

Hoewel het waarschuwen voor wijzigingen in het hosts-bestand een goede toepassing is van waarschuwingen voor wijzigingstracking- of voorraadgegevens, zijn er veel meer scenario's voor waarschuwingen, waaronder de aanvragen die samen met hun voorbeeldquery's in de sectie hieronder zijn gedefinieerd.

|Query’s uitvoeren  |Beschrijving  |
|---------|---------|
|Configuratiewijzigen <br>&#124; waar ConfigChangeType == "Files" en FileSystemPath "\\\\c:\\windows\\system32 drivers " bevat|Handig voor het bijhouden van wijzigingen in systeemkritieke bestanden|
|Configuratiewijzigen <br>&#124; waar FieldsChanged "FileContentChecksum" en FileSystemPath\\== "c:\\windows system32\\drivers\\etc\\hosts" bevat|Handig voor het bijhouden van wijzigingen in belangrijke configuratiebestanden|
|Configuratiewijzigen <br>&#124; waar ConfigChangeType == "WindowsServices" en SvcName "w3svc" en SvcState == "Gestopt" bevat|Handig voor het bijhouden van wijzigingen in systeemkritieke services|
|Configuratiewijzigen <br>&#124; waar ConfigChangeType == "Daemons" en SvcName "ssh" en SvcState bevat != "Running"|Handig voor het bijhouden van wijzigingen in systeemkritieke services|
|Configuratiewijzigen <br>&#124; waar ConfigChangeType == "Software" en ChangeCategory == "Toegevoegd"|Handig voor omgevingen waarvoor vergrendelde softwareconfiguraties nodig zijn|
|ConfiguratieGegevens <br>&#124; waar SoftwareName "Monitoring Agent" en CurrentVersion bevat != "8.0.11081.0"|Handig om te zien welke machines een verouderde of niet-conforme softwareversie hebben geïnstalleerd. Het rapporteert de laatst gerapporteerde configuratiestatus, geen wijzigingen.|
|Configuratiewijzigen <br>&#124; waar RegistryKey ==\\\\@"HKEY_LOCAL_MACHINE SOFTWARE Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Handig voor het bijhouden van wijzigingen in cruciale antivirustoetsen|
|Configuratiewijzigen <br>&#124; waarbij RegistryKey\\@"HKEY_LOCAL_MACHINE SYSTEM\\\\\\\\\\CurrentControlSet Services SharedAccess Parameters FirewallPolicy" bevat| Handig voor het bijhouden van wijzigingen in firewall-instellingen|

## <a name="next-steps"></a>Volgende stappen

Ga naar de zelfstudie over Change Tracking voor meer informatie over het gebruik van de oplossing:

> [!div class="nextstepaction"]
> [Problemen met wijzigingen in uw omgeving oplossen](automation-tutorial-troubleshoot-changes.md)

* Gebruik [Logboekzoekopdrachten in Azure Monitor-logboeken](../log-analytics/log-analytics-log-searches.md) om gedetailleerde gegevens voor het bijhouden van wijzigingen weer te geven.
