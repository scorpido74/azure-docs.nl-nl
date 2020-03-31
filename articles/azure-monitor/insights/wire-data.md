---
title: Wire Data-oplossing in Azure Monitor | Microsoft Documenten
description: Wire-gegevens zijn geconsolideerde netwerk- en prestatiegegevens van computers met Log Analytics-agents. Netwerkgegevens worden gecombineerd met uw logboekgegevens om te helpen bij het correleren van gegevens.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/03/2018
ms.openlocfilehash: ee7a2f49641eb0cfe1f8a4bffb44c7f8642408fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670641"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Wire Data 2.0 (Preview)-oplossing in Azure Monitor

![Symbool Wire Data](media/wire-data/wire-data2-symbol.png)

Draadgegevens zijn geconsolideerde netwerk- en prestatiegegevens die worden verzameld vanaf met Windows verbonden en met Linux verbonden computers met de Log Analytics-agent, inclusief gegevens die worden gecontroleerd door Operations Manager in uw omgeving. Netwerkgegevens worden gecombineerd met uw andere logboekgegevens om te helpen bij het correleren van gegevens.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Naast de Log Analytics-agent maakt de Wire Data-oplossing gebruik van Microsoft Dependency Agents die u installeert op computers in uw IT-infrastructuur. Agents voor afhankelijkheden controleren netwerkgegevens die worden verzonden naar en van uw computers voor netwerkniveaus 2-3 in het [OSI-model](https://en.wikipedia.org/wiki/OSI_model), met inbegrip van de verschillende gebruikte protocollen en poorten. Gegevens worden vervolgens met behulp van agents naar Azure Monitor verzonden.  

>[!NOTE]
>Als u Service Map al hebt geïmplementeerd of Servicemap of [Azure Monitor voor VM's](../../azure-monitor/insights/vminsights-overview.md)overweegt, is er een nieuwe gegevensset voor verbindingsstatistieken die ze verzamelen en opslaan in Azure Monitor die vergelijkbare informatie biedt als Wire Data.

Azure Monitor registreert standaard gegevens voor CPU-, geheugen-, schijf- en netwerkprestatiegegevens van tellers die zijn ingebouwd in Windows en Linux, evenals andere prestatiemeteritems die u opgeven. Het verzamelen van netwerk- en andere gegevens wordt voor elke agent in realtime uitgevoerd, met inbegrip van subnetten en protocollen op toepassingsniveau die door de computer worden gebruikt.  Wire Data kijkt naar netwerkgegevens op toepassingsniveau, niet naar die op de TCP-transportlaag. De oplossing kijkt niet naar afzonderlijke ACK's en SYN's. Zodra de handshake is voltooid, wordt dit als een live-verbinding beschouwd en wordt deze gemarkeerd als verbonden. Die verbinding blijft actief zolang beide zijden het erover eens zijn dat de socket geopend is en gegevens heen en weer kunnen worden gestuurd. Zodra beide zijden de verbinding sluiten, wordt deze gemarkeerd als Losgekoppeld.  Daarom wordt alleen de bandbreedte van voltooide pakketten meegeteld en wordt niet gemeld of pakketten opnieuw of niet zijn verzonden.

Als u [sFlow](http://www.sflow.org/) of andere software hebt gebruikt met het [NetFlow-protocol van Cisco](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), zullen de statistieken en gegevens die u in gegevens van Wire Data ziet, u bekend voorkomen.

Enkele van de typen ingebouwde query's voor zoeken in logboeken:

- Agents die gegevens van Wire Data leveren
- IP-adressen van agents die gegevens van Wire Data leveren
- Uitgaande communicatie per IP-adres
- Aantal bytes verzonden per toepassingsprotocol
- Aantal bytes verzonden per toepassingsservice
- Bytes ontvangen door verschillende protocollen
- Totaal aantal bytes verzonden en ontvangen per IP-versie
- Gemiddelde latentie voor verbindingen die betrouwbaar zijn gemeten
- Computerprocessen waarmee netwerkverkeer is gestart of ontvangen
- Hoeveelheid netwerkverkeer voor een proces

Wanneer u zoekt met behulp van Wire Data, kunt u gegevens filteren en groeperen zodat u informatie kunt bekijken over de belangrijkste agents en protocollen. U kunt ook zien wanneer bepaalde computers (IP-adressen/MAC-adressen) met elkaar hebben gecommuniceerd, hoelang dat duurde en hoeveel gegevens er zijn verzonden. In wezen bekijkt u metagegevens over het netwerkverkeer, wat op zoeken is gebaseerd.

Maar aangezien u metagegevens bekijkt, is dat niet per se nuttig voor een diepgaande probleemoplossing. Wire data in Azure Monitor is geen volledige opname van netwerkgegevens.  De oplossing is niet bedoeld voor het oplossen van problemen op een diep pakketniveau. Het voordeel van het gebruik van de agent, in vergelijking met andere verzamelmethoden, is dat u geen apparaten hoeft te installeren, uw netwerkswitches opnieuw hoeft te configureren of ingewikkelde configuraties hoeft uit te voeren. Gegevens van Wire Data zijn gewoon gebaseerd op agents: u installeert de agent op een computer en de agent zal dan het eigen netwerkverkeer controleren. Een ander voordeel is wanneer u werkbelastingen in cloudproviders of een serviceprovider of Microsoft Azure wilt hosten, waarbij de gebruiker niet de eigenaar van de infrastructuurlaag is.

## <a name="connected-sources"></a>Verbonden bronnen

Wire Data haalt zijn gegevens uit de Microsoft-agent voor afhankelijkheden. De afhankelijkheidsagent is afhankelijk van de loganalyse-agent voor de verbindingen met Azure Monitor. Dit betekent dat de loganalyse-agent moet zijn geïnstalleerd en geconfigureerd met de afhankelijkheidsagent. De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door Wire Data.

| **Verbonden bron** | **Ondersteund** | **Beschrijving** |
| --- | --- | --- |
| Windows-agents | Ja | Wire Data analyseert en verzamelt gegevens van Windows-agentcomputers. <br><br> Naast de [Log Analytics-agent voor Windows](../platform/agent-windows.md)hebben Windows-agents de Microsoft Dependency-agent nodig. Zie de [ondersteunde besturingssystemen](vminsights-enable-overview.md#supported-operating-systems) voor een volledige lijst met versies van besturingssystemen. |
| Linux-agents | Ja | Wire Data analyseert en verzamelt gegevens van Linux-agentcomputers.<br><br> Naast de [Log Analytics-agent voor Linux](../learn/quick-collect-linux-computer.md)hebben Linux-agents de Microsoft Dependency-agent nodig. Zie de [ondersteunde besturingssystemen](vminsights-enable-overview.md#supported-operating-systems) voor een volledige lijst met versies van besturingssystemen. |
| Beheergroep System Center Operations Manager | Ja | Wire Data analyseert en verzamelt gegevens van Windows- en Linux-agents in een verbonden [System Center Operations Manager-beheergroep](../platform/om-agents.md). <br><br> Er is een directe verbinding vereist van de systeemcentrumoperationsmanager-agentcomputer met Azure Monitor. |
| Azure Storage-account | Nee | Omdat Wire Data gegevens van agentcomputers verzamelt, zijn er geen gegevens te verzamelen van Azure Storage. |

In Windows wordt de Microsoft Monitoring Agent (MMA) gebruikt door zowel System Center Operations Manager als Azure Monitor om gegevens te verzamelen en te verzenden. Afhankelijk van de context wordt de agent de System Center Operations Manager Agent, Log Analytics-agent, MMA of Direct Agent genoemd. System Center Operations Manager en Azure Monitor bieden iets andere versies van de MMA. Deze versies kunnen elk rapport naar System Center Operations Manager, azure monitor of aan beide.

Op Linux verzamelt en verzendt de Log Analytics-agent voor Linux gegevens naar Azure Monitor. U Wire Data gebruiken op servers met agents die rechtstreeks zijn verbonden met Azure Monitor of op servers die verbinding maken met Azure Monitor via beheergroepen van System Center Operations Manager.

De afhankelijkheidsagent verzendt zelf geen gegevens en vereist geen wijzigingen in firewalls of poorten. De gegevens in Wire Data worden altijd door de log analytics-agent rechtstreeks of via de Log Analytics-gateway verzonden naar Azure Monitor.

![diagram van agent](./media/wire-data/agents.png)

Als u een Gebruiker van System Center Operations Manager bent met een beheergroep die is verbonden met Azure Monitor:

- Er is geen extra configuratie vereist wanneer uw System Center Operations Manager-agents toegang hebben tot internet om verbinding te maken met Azure Monitor.
- U moet de Logboekanalysegateway zo configureren dat deze werkt met System Center Operations Manager wanneer uw System Center Operations Manager-agents geen toegang hebben tot Azure Monitor via internet.

Als uw Windows- of Linux-computers niet rechtstreeks verbinding kunnen maken met de service, moet u de medewerker Log Analytics configureren om verbinding te maken met Azure Monitor via de Logboekanalysegateway. U de Logboekanalysegateway downloaden vanuit het [Microsoft Download center.](https://www.microsoft.com/download/details.aspx?id=52666)

## <a name="prerequisites"></a>Vereisten

- Hiervoor is de oplossing [Insight and Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) vereist.
- Als u de vorige versie van Wire Data gebruikt, moet u deze eerst verwijderen. Alle gegevens die zijn vastgelegd via de oorspronkelijke versie van Wire Data zijn echter nog steeds beschikbaar in Wire Data 2.0 en in zoeken in logboeken.
- Beheerdersbevoegdheden zijn vereist om de afhankelijkheidsagent te installeren of te verwijderen.
- De afhankelijkheidsagent moet worden geïnstalleerd op een computer met een 64-bits besturingssysteem.

### <a name="operating-systems"></a>Besturingssystemen

In de volgende secties worden de ondersteunde besturingssystemen voor de afhankelijkheidsagent vermeld. Wire Data biedt geen ondersteuning voor 32-bits architecturen van besturingssystemen.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Windows-bureaublad

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Ondersteunde Linux-besturingssystemen
In de volgende secties worden de ondersteunde besturingssystemen voor de afhankelijkheidsagent op Linux vermeld.  

- Alleen standaard- en SMP Linux kernelversies worden ondersteund.
- Niet-standaard kernelversies, zoals PAE en Xen, worden voor geen enkele Linux-distributie ondersteund. Een systeem met de releasestring van "2.6.16.21-0,8-xen" wordt bijvoorbeeld niet ondersteund.
- Aangepaste kernels, met inbegrip van hercompilaties van standaardkernels, worden niet ondersteund.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7,5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus (CentOSPlus)
| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Versie van het besturingssysteem | Kernelversie
|:--|:--|
| 11 SP4 | 3.0.* |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versie van het besturingssysteem | Kernelversie
|:--|:--|
| 12 SP2 | 4.4.* |
| 12 SP3 | 4.4.* |

### <a name="dependency-agent-downloads"></a>Downloads van afhankelijkheidsagent

| File | OS | Versie | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC4B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Configuratie

Voer de volgende stappen uit om Wire Data te configureren voor uw werkruimten.

1. Schakel de Activity Log Analytics-oplossing in vanuit de [Azure-marktplaats](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) of met behulp van het proces dat is beschreven in [Bewakingsoplossingen toevoegen vanuit de Galerie Oplossingen](../../azure-monitor/insights/solutions.md).
2. Installeer de afhankelijkheidsagent op elke computer waar u gegevens wilt opvragen. De afhankelijkheidsagent kan verbindingen met directe buren controleren, zodat u mogelijk niet op elke computer een agent nodig hebt.

> [!NOTE]
> U kunt de vorige versie van Wire Data niet toevoegen aan nieuwe werkruimten. Als u de oorspronkelijke versie van Wire Data hebt ingeschakeld, kunt u die blijven gebruiken. Als u echter Wire Data 2.0 wilt gebruiken, moet u eerst de oorspronkelijke versie verwijderen.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>De afhankelijkheidsagent op Windows installeren

Er zijn beheerdersbevoegdheden vereist om de agent te installeren of verwijderen.

De afhankelijkheidsagent is geïnstalleerd op computers waarop Windows wordt uitgevoerd via InstallDependencyAgent-Windows.exe. Als u dit uitvoerbare bestand zonder opties uitvoert, wordt er een wizard gestart die u kunt volgen om interactief te installeren.

Gebruik de volgende stappen om de afhankelijkheidsagent te installeren op elke computer waarop Windows wordt uitgevoerd:

1. Installeer de medewerker Logboekanalyse volgens de stappen in [Gegevens verzamelen van Windows-computers die in uw omgeving worden gehost.](../../azure-monitor/platform/agent-windows.md)
2. Download de Windows Dependency-agent via de koppeling in de vorige sectie en voer deze uit met de volgende opdracht:`InstallDependencyAgent-Windows.exe`
3. Volg de wizard om de agent te installeren.
4. Als de afhankelijkheidsagent niet wordt gestart, controleert u de logboeken op gedetailleerde foutgegevens. Voor Windows-agents is dit de logboekmap: %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Windows-opdrachtregel

Gebruik opties uit de volgende tabel om de agent te installeren vanaf een opdrachtregel. Als u een lijst met de installatievlaggen wilt zien, voert u het installatieprogramma uit met behulp van de /? vlag als volgt.

InstallDependencyAgent-Windows.exe /?

| **Vlag** | **Beschrijving** |
| --- | --- |
| <code>/?</code> | Een lijst met de opdrachtregelopties ophalen. |
| <code>/S</code> | Een installatie op de achtergrond uitvoeren zonder gebruikersvragen. |

Bestanden voor de Windows Dependency-agent worden standaard in C:\Program Files\Microsoft Dependency agent geplaatst.

### <a name="install-the-dependency-agent-on-linux"></a>De afhankelijkheidsagent op Linux installeren

Toegang tot de hoofdmap is vereist om de agent te installeren of configureren.

De Dependency-agent is geïnstalleerd op Linux-computers via InstallDependencyAgent-Linux64.bin, een shell script met een zelf-extraherende binaire. U kunt het bestand uitvoeren met behulp van _sh_ of door aan het bestand zelf schrijfrechten toe te kennen.

Gebruik de volgende stappen om de afhankelijkheidsagent op elke Linux-computer te installeren:

1. Installeer de agent Log Analytics volgens de stappen in [Gegevens verzamelen van Linux-computers die in uw omgeving worden gehost.](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key)
2. Download de Linux Dependency agent met behulp van de link in de vorige sectie en installeer deze als root met behulp van de volgende opdracht: sh InstallDependencyAgent-Linux64.bin
3. Als de afhankelijkheidsagent niet wordt gestart, controleert u de logboeken op gedetailleerde foutgegevens. Voor Linux-agents is dit de logboekmap: /var/opt/microsoft/dependency-agent/log.

Als u een overzicht van de installatievlaggen wilt zien, voert u als volgt het installatieprogramma uit met behulp van de `-help`-vlag.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Vlag** | **Beschrijving** |
| --- | --- |
| <code>-help</code> | Een lijst met de opdrachtregelopties ophalen. |
| <code>-s</code> | Een installatie op de achtergrond uitvoeren zonder gebruikersvragen. |
| <code>--check</code> | Controleer machtigingen en het besturingssysteem, maar installeer niet de agent. |

Bestanden voor de afhankelijkheidsagent worden in de volgende mappen geplaatst:

| **Bestanden** | **Locatie** |
| --- | --- |
| Kernbestanden | /opt/microsoft/dependency-agent |
| Logboekbestanden | /var/opt/microsoft/dependency-agent/log |
| Configuratiebestanden | /etc/opt/microsoft/dependency-agent/config |
| Uitvoerbare bestanden van de service | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binaire opslagbestanden | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Voorbeelden van installatiescript

Om de afhankelijkheidsagent eenvoudig op veel servers tegelijk te implementeren, helpt het om een script te gebruiken. U de volgende scriptvoorbeelden gebruiken om de afhankelijkheidsagent op Windows of Linux te downloaden en te installeren.

#### <a name="powershell-script-for-windows"></a>PowerShell-script voor Windows

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Shell-script voor Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Desired State Configuration

Als u de afhankelijkheidsagent wilt implementeren via gewenste statusconfiguratie, u de xPSDesiredStateConfiguration-module en een beetje code als volgt gebruiken:

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>De afhankelijkheidsagent verwijderen

Gebruik de volgende secties om u te helpen de afhankelijkheidsagent te verwijderen.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>De afhankelijkheidsagent van Windows verwijderen

Een beheerder kan de afhankelijkheidsagent voor Windows verwijderen via het Configuratiescherm.

Een beheerder kan ook %Programfiles%\Microsoft Dependency Agent\Uninstall.exe uitvoeren om de afhankelijkheidsagent te verwijderen.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>De afhankelijkheidsagent op Linux verwijderen

Als u de afhankelijkheidsagent volledig wilt verwijderen van Linux, moet u de agent zelf en de connector verwijderen, die automatisch met de agent wordt geïnstalleerd. U kunt beide verwijderen met behulp van de volgende opdracht:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Management packs

Wanneer Wire Data wordt geactiveerd in een Log Analytics-werkruimte, wordt een management pack van 300 kB verzonden naar alle Windows-servers in die werkruimte. Als u System Center Operations Manager-agents in een [verbonden beheergroep](../platform/om-agents.md) gebruikt, wordt het management pack Afhankelijkheidsmonitor geïmplementeerd vanuit System Center Operations Manager. Als de agents rechtstreeks zijn verbonden, levert Azure Monitor het beheerpakket.

De naam van het management pack is Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Het management pack wordt geschreven naar: %Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. De gegevensbron waarvan het management pack gebruikmaakt is: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>De oplossing gebruiken

Gebruik de volgende informatie om de oplossing te installeren en configureren.

- De oplossing Wire Data verkrijgt gegevens van computers met Windows Server 2012 R2, Windows 8.1 en latere besturingssystemen.
- Microsoft .NET Framework 4.0 of hoger is vereist op computers waarvan u draadgegevens wilt ophalen.
- Voeg de oplossing Wire Data toe aan uw Log Analytics-werkruimte met behulp van het proces dat wordt beschreven in [Bewakingsoplossingen toevoegen vanuit de Galerie Oplossingen.](solutions.md) Er is geen verdere configuratie nodig.
- Als u draadgegevens voor een specifieke oplossing wilt weergeven, moet de oplossing al zijn toegevoegd aan uw werkruimte.

Nadat agents zijn geïnstalleerd en u de oplossing installeert, wordt de tegel Wire Data 2.0 weergegeven in de werkruimte.

![Tegel Wire Data](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>De oplossing Wire Data 2.0 gebruiken

Op de pagina **Overzicht** voor uw Log Analytics-werkruimte in de Azure Portal klikt u op de tegel **Wire Data 2.0** om het dashboard van Wire Data te openen. Het dashboard bevat de blades in de volgende tabel. Elke blade bevat maximaal tien items die overeenkomen met de criteria voor het opgegeven bereik en de duur van die blade. U kunt zoeken in logboeken waarmee alle records worden geretourneerd door onderaan in de blade te klikken op **Alles bekijken** of door te klikken op de koptekst van de blade.

| **Blade** | **Beschrijving** |
| --- | --- |
| Agents waarmee het netwerkverkeer wordt vastgelegd | Toont het aantal agents dat netwerkverkeer vastlegt en geeft de eerste 10 computers weer die het meeste verkeer vastleggen. Klik op het nummer om zoeken in logboeken uit te voeren voor <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Klik op een computer in de lijst om zoeken in logboeken uit te voeren waarmee het totale aantal vastgelegde bytes wordt geretourneerd. |
| Lokale subnetten | Toont het aantal lokale subnetten dat door agents is gedetecteerd.  Klik op het nummer om zoeken in logboeken uit te voeren voor <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>, waarmee alle subnetten worden weergegeven met het aantal bytes dat via elk daarvan is verzonden. Klik op een subnet in de lijst om zoeken in logboeken uit te voeren waarmee het totale aantal via het subnet verzonden bytes wordt geretourneerd. |
| Protocollen op toepassingsniveau | Toont het aantal protocollen op toepassingsniveau in gebruik, zoals gedetecteerd door agents. Klik op het nummer om zoeken in logboeken uit te voeren voor <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Klik op een protocol om zoeken in logboeken uit te voeren waarmee het totale aantal met het protocol verzonden bytes wordt geretourneerd. |

![Wire Data-dashboard](./media/wire-data/wire-data-dash.png)

U kunt de blade **Agents waarmee het netwerkverkeer wordt vastgelegd** gebruiken om te bepalen hoeveel netwerkbandbreedte wordt verbruikt door computers. Met deze blade kunt u gemakkelijk de _drukst bezette_ computer in uw omgeving terugvinden. Deze computers kunnen overbelast zijn, zich abnormaal gedragen of meer netwerkbronnen dan normaal gebruiken.

![voorbeeld van zoeken in logboek](./media/wire-data/log-search-example01.png)

Op soortgelijke wijze kunt u de blade **Lokale subnetten** gebruiken om te bepalen hoeveel netwerkverkeer via uw subnetten plaatsvindt. Gebruikers definiëren vaak subnetten rondom essentiële gebieden voor hun toepassingen. Deze blade geeft een beeld van die gebieden.

![voorbeeld van zoeken in logboek](./media/wire-data/log-search-example02.png)

De blade **Protocollen op toepassingsniveau** is handig omdat u hiermee kunt vaststellen welke protocollen er worden gebruikt. U verwacht bijvoorbeeld dat SSH niet in uw netwerkomgeving wordt gebruikt. Aan de hand van de informatie in de blade kunt u dan snel vaststellen of die verwachting al dan niet is uitgekomen.

![voorbeeld van zoeken in logboek](./media/wire-data/log-search-example03.png)

Het is ook handig om te weten als protocolverkeer gedurende een bepaalde periode toe- of afneemt. Als bijvoorbeeld de hoeveelheid gegevens die wordt verzonden door een toepassing toeneemt, is dat misschien iets waarmee u rekening wilt houden.

## <a name="input-data"></a>Invoergegevens

Wire Data verzamelt metagegevens over netwerkverkeer met behulp van de agents die u hebt ingeschakeld. Elke agent verzendt ongeveer elke 15 seconden gegevens.

## <a name="output-data"></a>Uitvoergegevens

Een record met het type _WireData_ is gemaakt voor elk type invoergegevens. WireData-records hebben eigenschappen die worden weergegeven in de volgende tabel:

| Eigenschap | Beschrijving |
|---|---|
| Computer | De naam van de computer waar gegevens zijn verzameld |
| TimeGenerated | Tijd van de record |
| LocalIP | IP-adres van de lokale computer |
| SessionState | Verbonden of verbroken |
| ReceivedBytes | Hoeveelheid ontvangen bytes |
| ProtocolName | Naam van het gebruikte netwerkprotocol |
| IPVersion | IP-versie |
| Richting | Binnenkomend of uitgaand |
| MaliciousIP | IP-adres van een bekende schadelijke bron |
| Severity | Ernst van mogelijke malware |
| RemoteIPCountry | Land/regio van het externe IP-adres |
| ManagementGroupName | Naam van de Operations Manager-beheergroep |
| SourceSystem | Bron waar gegevens zijn verzameld |
| SessionStartTime | Starttijd van de sessie |
| SessionEndTime | Eindtijd van de sessie |
| LocalSubnet | Subnet waar gegevens zijn verzameld |
| LocalPortNumber | Nummer van de lokale poort |
| RemoteIP | Extern IP-adres gebruikt door de externe computer |
| RemotePortNumber | Poortnummer gebruikt door het externe IP-adres |
| SessionID | Een unieke waarde die de communicatiesessie tussen twee IP-adressen aangeeft |
| SentBytes | Aantal verzonden bytes |
| TotalBytes | Totaal aantal bytes dat tijdens de sessie is verzonden |
| ApplicationProtocol | Type van het gebruikte netwerkprotocol   |
| ProcessID | Windows-proces-id |
| ProcessName | Pad en bestandsnaam van het proces |
| RemoteIPLongitude | Waarde IP-lengtegraad |
| RemoteIPLatitude | Waarde IP-breedtegraad |

## <a name="next-steps"></a>Volgende stappen

- [Zoeken in een logboek](../../azure-monitor/log-query/log-query-overview.md) om gedetailleerde zoekrecords van Wire Data te bekijken.
