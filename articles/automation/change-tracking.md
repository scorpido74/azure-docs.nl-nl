---
title: Overzicht van Azure Automation Wijzigingen bijhouden en inventaris
description: In dit artikel wordt de functie Wijzigingen bijhouden en inventaris beschreven, waarmee u de wijzigingen in de software en micro soft-Services in uw omgeving kunt identificeren.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: 7a1c5d5371663f3520e76060c9c2a8df0a18449c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117528"
---
# <a name="change-tracking-and-inventory-overview"></a>Overzicht van Wijzigingen bijhouden en inventaris

In dit artikel wordt beschreven hoe u Wijzigingen bijhouden en inventariseert in Azure Automation. Deze functie houdt wijzigingen in de virtuele machines en server infrastructuur bij, zodat u operationele en omgevings problemen kunt lokaliseren met software die wordt beheerd door Distribution Package Manager. Items die worden bijgehouden door Wijzigingen bijhouden en inventaris omvatten: 

- Windows-software
- Linux-software (pakketten)
- Windows-en Linux-bestanden
- Windows-register sleutels
- Micro soft-Services
- Linux-daemons

> [!NOTE]
> Zie de [wijzigings geschiedenis](../governance/resource-graph/how-to/get-resource-changes.md)van de Azure-resource grafiek voor het bijhouden van wijzigingen in de eigenschappen van Azure Resource Manager.

Wijzigingen bijhouden en de inventarisatie verkrijgen de gegevens van Azure Monitor. Virtuele machines die zijn verbonden met Log Analytics-werk ruimten, gebruiken Log Analytics agents om gegevens te verzamelen over wijzigingen in geïnstalleerde software, micro soft-Services, Windows-REGI ster en-bestanden en Linux-daemons op bewaakte servers. Wanneer de gegevens beschikbaar zijn, sturen de agents deze naar Azure Monitor voor verwerking. Azure Monitor past logica toe op de ontvangen gegevens, registreert deze en maakt deze beschikbaar. 

> [!NOTE]
> Als u de functie voor Wijzigingen bijhouden en inventaris wilt gebruiken, moet u alle virtuele machines in hetzelfde abonnement en dezelfde regio van het Automation-account zoeken.

Wijzigingen bijhouden en inventaris bieden momenteel geen ondersteuning voor de volgende items:

* Recursie voor het bijhouden van Windows-REGI ster
* Netwerk bestandssysteem
* Verschillende installatie methoden
* ***exe** -bestanden voor Windows

Andere beperkingen:

* De kolom en waarden voor de **maximale bestands grootte** worden niet gebruikt in de huidige implementatie.
* Als u meer dan 2500 bestanden in een verzamelings cyclus van 30 minuten verzamelt, kunnen de prestaties van Wijzigingen bijhouden en de inventaris worden verslechterd.
* Wanneer het netwerk verkeer hoog is, kan het tot zes uur duren voordat wijzigings records worden weer gegeven.
* Als u een configuratie wijzigt terwijl een computer is uitgeschakeld, kan het zijn dat de computer wijzigingen post die horen bij de vorige configuratie.

Wijzigingen bijhouden en inventaris ondervindt momenteel de volgende problemen:

* Hotfix-updates worden niet verzameld op Windows Server 2016 core RS3-machines.
* Linux-daemons kunnen een gewijzigde status weer geven, zelfs als er geen wijziging heeft plaatsgevonden. Dit probleem doet zich voor vanwege de manier waarop de `SvcRunLevels` gegevens in het [ConfigurationChange](https://docs.microsoft.com/azure/azure-monitor/reference/tables/configurationchange) -logboek van Azure monitor worden vastgelegd.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Wijzigingen bijhouden en inventarisatie wordt ondersteund op alle besturings systemen die voldoen aan de vereisten van Log Analytics agent. De officiële versies van het besturings systeem zijn Windows Server 2008 SP1 of hoger en Windows 7 SP1 of hoger. De functie wordt ook ondersteund op een aantal Linux-besturings systemen. Zie [overzicht van log Analytics agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)voor besturings systemen die log Analytics ondersteunen. 

## <a name="network-requirements"></a>Netwerkvereisten

Voor Wijzigingen bijhouden en de inventarisatie zijn specifieke netwerk adressen in de volgende tabel vereist. Voor communicatie met deze adressen gebruikt u poort 443.

|Openbare Azure-peering  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net | *. blob.core.usgovcloudapi.net|
|*.azure-automation.net | *. azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>Gebruikers interface van Wijzigingen bijhouden en inventaris

Gebruik Wijzigingen bijhouden en inventaris in de Azure Portal om het overzicht van de wijzigingen voor bewaakte computers weer te geven. De functie is beschikbaar als u een van de opties voor het bijhouden van **wijzigingen** of **inventarisatie** onder **configuratie beheer** in uw Automation-account selecteert.  

![Wijzigingen bijhouden dash board](./media/change-tracking/change-tracking-dash01.png)

Er zijn vervolg keuzelijsten aan de bovenkant van het dash board beschikbaar om de grafiek voor het bijhouden van wijzigingen en gedetailleerde informatie op basis van het wijzigings type en peri Oden te beperken. U kunt ook op het diagram klikken en slepen om een aangepast tijds bereik te selecteren. 

U kunt op een wijziging of gebeurtenis klikken om de details ervan weer te geven. De beschik bare wijzigings typen zijn:

* Gebeurtenissen
* Daemons
* Files
* Register
* Software
* Micro soft-Services

U kunt elke wijziging toevoegen, wijzigen of verwijderen. In het onderstaande voor beeld ziet u een wijziging in het opstart type van een service van hand matig in auto.

![Details van Wijzigingen bijhouden en inventaris](./media/change-tracking/change-tracking-details.png)

## <a name="fim-support-in-azure-security-center"></a>FIM-ondersteuning in Azure Security Center

Wijzigingen bijhouden en inventaris maakt gebruik van [Azure Security Center File Integrity Monitoring (FIM)](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring). Hoewel FIM alleen bestanden en registers bewaakt, omvat de functie volledige Wijzigingen bijhouden en inventaris ook tracering voor:

- Software wijzigingen
- Micro soft-Services
- Linux-daemons

> [!NOTE]
> Het inschakelen van de functie volledige Wijzigingen bijhouden en inventarisatie kan extra kosten veroorzaken. Zie [prijzen voor Automation](https://azure.microsoft.com/pricing/details/automation/). U kunt FIM verwijderen uit de [lijst met geïnstalleerde bewakings oplossingen](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions) die beschikbaar zijn in de Azure Portal. Zie [een bewakings oplossing verwijderen](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).

## <a name="tracking-of-file-changes"></a>Het bijhouden van bestands wijzigingen

Voor het bijhouden van wijzigingen in bestanden op Windows en Linux maakt Wijzigingen bijhouden en inventarisatie gebruik van MD5-hashes van de bestanden. De functie maakt gebruik van de hashes om te detecteren of er wijzigingen zijn aangebracht sinds de laatste inventarisatie.

## <a name="tracking-of-file-content-changes"></a>Wijzigingen van bestands inhoud bijhouden

Met Wijzigingen bijhouden en inventaris kunt u de inhoud van een Windows-of Linux-bestand weer geven. Voor elke wijziging in een bestand bevat Wijzigingen bijhouden en inventaris de inhoud van het bestand in een [Azure Storage-account](../storage/common/storage-create-storage-account.md). Wanneer u een bestand volgt, kunt u de inhoud ervan weer geven vóór of na een wijziging. De bestands inhoud kan in line of naast elkaar worden weer gegeven. 

![Wijzigingen in een bestand weer geven](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Register sleutels bijhouden

Met Wijzigingen bijhouden en inventaris kunt u wijzigingen in Windows-register sleutels controleren. Met bewaking kunt u uitbreid bare punten herkennen waarbij code en malware van derden kunnen worden geactiveerd. De volgende tabel geeft een lijst van vooraf geconfigureerde register sleutels (maar niet ingeschakeld). Als u deze sleutels wilt bijhouden, moet u deze inschakelen.

> [!div class="mx-tdBreakAll"]
> |Registersleutel | Doel |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Bewaakt de scripts die worden uitgevoerd bij het opstarten.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Bewaakt de scripts die worden uitgevoerd bij het afsluiten.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Bewaakt sleutels die worden geladen voordat de gebruiker zich aanmeldt bij het Windows-account. De sleutel wordt gebruikt voor 32-bits toepassingen die worden uitgevoerd op 64-bits computers.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Hiermee worden wijzigingen in toepassings instellingen gecontroleerd.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Bewaakt context menu-handlers die rechtstreeks in Windows Verkenner zijn aangesloten en die gewoonlijk in-process met **Explorer. exe**worden uitgevoerd.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Hiermee worden Kopieer Hook-handlers gecontroleerd die rechtstreeks in Windows Verkenner zijn aangesloten en normaal gesp roken met **Explorer. exe**worden uitgevoerd.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitor voor de registratie van het pictogram-overlay-handler.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitors voor de registratie van pictogram-overlaysoftware-handler voor 32-bits toepassingen die worden uitgevoerd op 64-bits computers.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitors voor nieuwe browserhelperobjecten voor Internet Explorer. Wordt gebruikt om toegang te krijgen tot de Document Object Model (DOM) van de huidige pagina en om de navigatie te beheren.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitors voor nieuwe browserhelperobjecten voor Internet Explorer. Wordt gebruikt voor toegang tot de Document Object Model (DOM) van de huidige pagina en voor het beheren van de navigatie voor 32-bits toepassingen die worden uitgevoerd op 64-bits computers.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Controleert op nieuwe Internet Explorer-uitbrei dingen, zoals menu's voor aangepaste gereedschappen en aangepaste werkbalk knoppen.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Controleert op nieuwe Internet Explorer-uitbrei dingen, zoals aangepaste hulpprogramma menu's en aangepaste werkbalk knoppen voor 32-bits toepassingen die worden uitgevoerd op 64-bits computers.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Bewaakt 32-bits Stuur Programma's die zijn gekoppeld aan wavemapper, wave1 en wave2, Msacm. imaadpcm,. msadpcm,. msgsm610 en vidc. Vergelijkbaar met de sectie [drivers] in het **System. ini** -bestand.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Bewaakt 32-bits Stuur Programma's die zijn gekoppeld aan wavemapper, wave1 en wave2, Msacm. imaadpcm,. msadpcm,. msgsm610 en vidc voor 32-bits-toepassingen die worden uitgevoerd op 64-bits computers. Vergelijkbaar met de sectie [drivers] in het **System. ini** -bestand.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Hiermee wordt de lijst met bekende of veelgebruikte systeem-Dll's gecontroleerd. Door te controleren wordt voor komen dat gebruikers zwakke machtigingen voor toepassings mappen kunnen exploiteren door de Trojaanse paarden-versies van systeem-Dll's te verwijderen.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Bewaakt de lijst met pakketten die gebeurtenis meldingen kunnen ontvangen van **Winlogon. exe**, het model voor ondersteuning van interactieve aanmelding voor Windows.

## <a name="recursion-support"></a>Ondersteuning voor recursie

Wijzigingen bijhouden en inventarisatie ondersteunt recursie, waarmee u Joker tekens kunt opgeven om het bijhouden van meerdere mappen te vereenvoudigen. Recursie biedt ook omgevings variabelen waarmee u bestanden kunt volgen in omgevingen met meerdere of dynamische stations. De volgende lijst bevat algemene informatie die u moet kennen bij het configureren van recursie:

* Joker tekens zijn vereist voor het bijhouden van meerdere bestanden.
* U kunt alleen joker tekens gebruiken in het laatste segment van een bestandspad, bijvoorbeeld **c:\folder \\ File*** of **/etc/*. conf**.
* Als een omgevings variabele een ongeldig pad heeft, wordt de validatie uitgevoerd, maar het pad mislukt tijdens de uitvoering.
* Vermijd algemene padnamen bij het instellen van het pad, omdat dit type instelling ertoe kan leiden dat er te veel mappen worden gepasseerd.

## <a name="change-tracking-and-inventory-data-collection"></a>Gegevens verzameling Wijzigingen bijhouden en inventaris

In de volgende tabel wordt de frequentie van gegevens verzameling weer gegeven voor de typen wijzigingen die worden ondersteund door Wijzigingen bijhouden en inventaris. Voor elk type wordt de gegevens momentopname van de huidige status ook ten minste elke 24 uur vernieuwd.

| **Type wijzigen** | **Frequentie** |
| --- | --- |
| Windows-REGI ster | 50 minuten |
| Windows-bestand | 30 minuten |
| Linux-bestand | 15 minuten |
| Micro soft-Services | 10 seconden tot 30 minuten</br> Standaard: 30 minuten |
| Linux-daemons | 5 minuten |
| Windows-software | 30 minuten |
| Linux-software | 5 minuten |

De volgende tabel bevat de limieten voor bijgehouden items per computer voor Wijzigingen bijhouden en inventaris.

| **Resource** | **Limiet** |
|---|---|---|
|File|500|
|Register|250|
|Windows-software (exclusief hotfixes) |250|
|Linux-pakketten|1250|
|Services|250|
|Daemons|250|

Het gemiddelde Log Analytics gegevens gebruik voor een machine met Wijzigingen bijhouden en inventaris is ongeveer 40 MB per maand, afhankelijk van uw omgeving. Met de functie voor gebruik en geschatte kosten van de Log Analytics-werk ruimte kunt u de gegevens weer geven die zijn opgenomen door Wijzigingen bijhouden en inventaris in een gebruiks diagram. Gebruik deze gegevens weergave om uw gegevens gebruik te evalueren en te bepalen hoe deze van invloed is op uw factuur. Bekijk [inzicht in uw gebruik en geschatte kosten](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs). 

### <a name="microsoft-service-data"></a>Micro soft-service gegevens

De standaard frequentie voor het verzamelen van micro soft-Services is 30 minuten. U kunt de frequentie configureren met behulp van een schuif regelaar op het tabblad **micro soft-Services** onder **Instellingen bewerken**.

![Micro soft Services-schuif regelaar](./media/change-tracking/windowservices.png)

Om de prestaties te optimaliseren, houdt de Log Analytics agent alleen wijzigingen bij. Het instellen van een hoge drempel waarde kan wijzigingen missen als de service terugkeert naar de oorspronkelijke staat. Door de frequentie in te stellen op een lagere waarde, kunt u wijzigingen ondervangen die anders kunnen worden gemist.

> [!NOTE]
> Hoewel de agent wijzigingen kan bijhouden in een interval van 10 seconden, duurt het enkele minuten voordat de gegevens in de Azure Portal worden weer gegeven. Wijzigingen die zich voordoen tijdens de tijd die in de portal worden weer gegeven, worden nog steeds bijgehouden en geregistreerd.

## <a name="support-for-alerts-on-configuration-state"></a>Ondersteuning voor waarschuwingen over de configuratie status

Een belang rijke mogelijkheid van Wijzigingen bijhouden en inventarisatie wordt gewaarschuwd voor wijzigingen in de configuratie status van uw hybride omgeving. Er zijn veel nuttige acties beschikbaar om te activeren als reactie op waarschuwingen, zoals acties voor Azure functions, Automation-runbooks, webhooks en dergelijke. Een waarschuwing over wijzigingen in het **c:\Windows\System32\drivers\etc\hosts** -bestand voor een machine is een goede toepassing van waarschuwingen voor wijzigingen bijhouden-en inventaris gegevens. Er zijn veel meer scenario's voor waarschuwingen, zoals de query scenario's die in de volgende tabel zijn gedefinieerd. 

|Query’s uitvoeren  |Beschrijving  |
|---------|---------|
|ConfigurationChange <br>&#124; waarbij ConfigChangeType = = "files" en FileSystemPath bevat "c: \\ Windows \\ System32 \\ drivers \\ "|Dit is handig voor het bijhouden van wijzigingen in essentiële bestanden van het systeem.|
|ConfigurationChange <br>&#124; waarbij FieldsChanged ' FileContentChecksum ' en FileSystemPath = = "c: \\ Windows \\ System32 \\ drivers etc- \\ \\ hosts ' bevat.|Dit is handig voor het bijhouden van wijzigingen in sleutel configuratie bestanden.|
|ConfigurationChange <br>&#124; waarbij ConfigChangeType = = "micro soft-Services" en SvcName bevat "W3SVC" en SvcState = = "gestopt"|Dit is handig voor het bijhouden van wijzigingen in essentiële services van het systeem.|
|ConfigurationChange <br>&#124; waarbij ConfigChangeType = = "daemons" en SvcName bevat "SSH" en SvcState! = "running"|Dit is handig voor het bijhouden van wijzigingen in essentiële services van het systeem.|
|ConfigurationChange <br>&#124; waarbij ConfigChangeType = = "software" en ChangeCategory = = "toegevoegd"|Dit is handig voor omgevingen waarvoor een vergrendelde software configuratie nodig is.|
|ConfigurationData <br>&#124; waarbij de softwarenaam "bewakings agent" en CurrentVersion! = "8.0.11081.0" bevat|Nuttig als u wilt zien op welke computers een verouderde of niet-compatibele software versie is geïnstalleerd. Deze query rapporteert de laatst gerapporteerde configuratie status, maar brengt geen wijzigingen aan.|
|ConfigurationChange <br>&#124; waarbij RegistryKey = = @ "HKEY_LOCAL_MACHINE \\ software \\ micro soft \\ Windows \\ CurrentVersion \\ QualityCompat"| Dit is handig voor het bijhouden van wijzigingen in essentiële antivirus sleutels.|
|ConfigurationChange <br>&#124; waarbij RegistryKey @ "HKEY_LOCAL_MACHINE \\ System \\ CurrentControlSet \\ Services \\ SharedAccess \\ para meters \\ FirewallPolicy" bevat| Dit is handig voor het bijhouden van wijzigingen in Firewall-instellingen.|

## <a name="next-steps"></a>Volgende stappen

* Zie [Wijzigingen bijhouden en inventaris inschakelen vanuit een Automation-account](automation-enable-changes-from-auto-acct.md)om de functie in te scha kelen vanuit een Automation-account.
* Als u de functie wilt inschakelen door te bladeren door de Azure Portal, raadpleegt u [Wijzigingen bijhouden en inventaris inschakelen in de Azure Portal](automation-onboard-solutions-from-browse.md).
* Zie [Wijzigingen bijhouden en inventaris inschakelen vanuit een runbook](automation-enable-changes-from-runbook.md)om de functie in te scha kelen in een runbook.
* Als u de functie wilt inschakelen vanaf een virtuele Azure-machine, raadpleegt u [Wijzigingen bijhouden en inventaris inschakelen op een virtuele Azure-machine](automation-enable-changes-from-vm.md).