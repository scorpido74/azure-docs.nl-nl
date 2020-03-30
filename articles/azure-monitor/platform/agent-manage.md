---
title: De Azure Log Analytics-agent beheren
description: In dit artikel worden de verschillende beheertaken beschreven die u doorgaans uitvoert tijdens de levenscyclus van de Windows- of Linux-agent Log Analytics die op een machine wordt geïmplementeerd.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: 4d0ceacd37748e9761903d02fd7e052d70b10e15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275098"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>De Log Analytics-agent voor Windows en Linux beheren en onderhouden

Na de eerste implementatie van de Windows- of Linux-agent Log Analytics in Azure Monitor, moet u de agent mogelijk opnieuw configureren, upgraden of van de computer verwijderen als deze de pensioenfase in de levenscyclus heeft bereikt. U deze routineonderhoudstaken eenvoudig handmatig of via automatisering beheren, wat zowel operationele fouten als kosten vermindert.

## <a name="upgrading-agent"></a>Upgradeagent

De Log Analytics-agent voor Windows en Linux kan handmatig of automatisch worden geüpgraded naar de nieuwste release, afhankelijk van het implementatiescenario en de omgeving waarin de VM wordt uitgevoerd. De volgende methoden kunnen worden gebruikt om de agent te upgraden.

| Omgeving | Installatiemethode | Upgrademethode |
|--------|----------|-------------|
| Azure VM | VM-extensie voor Log Analytics-agent voor Windows/Linux | Agent wordt standaard automatisch geüpgraded, tenzij u uw Azure Resource Manager-sjabloon hebt geconfigureerd om zich af te melden door de *eigenschap autoUpgradeMinorVersion* in te stellen op **false**. |
| Aangepaste Azure VM-afbeeldingen | Handmatiginstalleren van Log Analytics-agent voor Windows/Linux | Het bijwerken van VM's naar de nieuwste versie van de agent moet worden uitgevoerd vanaf de opdrachtregel met het Windows-installatiepakket of linux-zelf-extraherende en installeerbare shellscriptbundel.|
| Niet-Azure VM's | Handmatiginstalleren van Log Analytics-agent voor Windows/Linux | Het bijwerken van VM's naar de nieuwste versie van de agent moet worden uitgevoerd vanaf de opdrachtregel met het Windows-installatiepakket of linux-zelf-extraherende en installeerbare shellscriptbundel. |

### <a name="upgrade-windows-agent"></a>Windows-agent bijwerken 

Als u de agent op een Windows-VM wilt bijwerken naar de nieuwste versie die niet is geïnstalleerd met de VM-extensie\<\>Log Analytics, voert u deze uit vanaf de opdrachtprompt, script- of andere automatiseringsoplossing of met de wizard MMASetup-platform .msi Setup.  

U de nieuwste versie van de Windows-agent downloaden uit uw Log Analytics-werkruimte door de volgende stappen uit te voeren.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Klik in de Azure-portal op **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Logboekanalysewerkruimten**.

3. Selecteer de werkruimte in uw lijst met Logboekanalyse-werkruimten.

4. Selecteer in uw werkruimte Log Analytics **geavanceerde instellingen,** selecteer **vervolgens Verbonden bronnen**en ten slotte **Windows-servers**.

5. Selecteer op de pagina **Windows Servers** de juiste **Windows Agent-versie downloaden** die u wilt downloaden, afhankelijk van de processorarchitectuur van het Windows-besturingssysteem.

>[!NOTE]
>Tijdens de upgrade van de Log Analytics-agent voor Windows biedt deze geen ondersteuning voor het configureren of opnieuw configureren van een werkruimte waaraan u wilt rapporteren. Als u de agent wilt configureren, moet u een van de ondersteunde methoden volgen die worden vermeld onder [Een werkruimte toevoegen of verwijderen.](#adding-or-removing-a-workspace)
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Upgraden met de wizard Setup

1. Meld u aan bij de computer met een account met beheerdersrechten.

2. Voer **MMASetup-platform\<\>.exe uit** om de wizard Setup te starten.

3. Klik op de eerste pagina van de wizard Setup op **Volgende**.

4. Klik in het dialoogvenster **Microsoft Monitoring Agent Setup** op Ik ga akkoord **om** de licentieovereenkomst te accepteren.

5. Klik in het dialoogvenster **Setup van Microsoft Monitoring Agent** op **Bijwerken**. Op de statuspagina wordt de voortgang van de upgrade weergegeven.

6. Wanneer de configuratie van de **Microsoft Monitoring Agent is voltooid.** pagina wordt weergegeven, klik op **Voltooien**.

#### <a name="to-upgrade-from-the-command-line"></a>Upgraden vanaf de opdrachtregel

1. Meld u aan bij de computer met een account met beheerdersrechten.

2. Als u de installatiebestanden van de agent `MMASetup-<platform>.exe /c` wilt extraheren, wordt u vanuit een opdrachtprompt met verhoogde bevoegdheid verwijderd en wordt u gevraagd om het pad om bestanden naar uit te halen. U het pad ook opgeven `MMASetup-<platform>.exe /c /t:<Full Path>`door de argumenten door te geven.

3. Voer de volgende opdracht uit, waarbij D:\ de locatie is van het upgradelogboekbestand.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Linux-agent upgraden 

Een upgrade ten opzichte van eerdere versies (>1.0.0-47) wordt ondersteund. Als u de `--upgrade` installatie met de opdracht uitvoert, worden alle onderdelen van de agent geüupgradet naar de nieuwste versie.

Voer de volgende opdracht uit om de agent te upgraden.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Een werkruimte toevoegen of verwijderen

### <a name="windows-agent"></a>Windows-agent
De stappen in deze sectie zijn nodig wanneer u de Windows-agent niet alleen opnieuw wilt configureren om te rapporteren aan een andere werkruimte of om een werkruimte uit de configuratie te verwijderen, maar ook wanneer u de agent wilt configureren om te rapporteren aan meer dan één werkruimte (vaak multi-homing). Het configureren van de Windows-agent om te rapporteren aan meerdere werkruimten kan alleen worden uitgevoerd na de eerste installatie van de agent en met behulp van de onderstaande methoden.    

#### <a name="update-settings-from-control-panel"></a>Instellingen bijwerken vanuit het Configuratiescherm

1. Meld u aan bij de computer met een account met beheerdersrechten.

2. Configuratiescherm **openen**.

3. Selecteer **Microsoft Monitoring Agent** en klik op het tabblad Azure Log **Analytics.**

4. Als u een werkruimte verwijdert, selecteert u deze en klikt u op **Verwijderen**. Herhaal deze stap voor elke andere werkruimte die de agent niet meer meldt.

5. Als u een werkruimte toevoegt, klikt u op **Toevoegen** en plakt u in het dialoogvenster Werkruimte toevoegen een **logboekanalysewerkruimte,** plakt u de werkruimte-id en werkruimtesleutel (primaire sleutel). Als u de computer wilt laten rapporteren bij een Log Analytics-werkruimte in de Azure Government-cloud, selecteert u Azure US Government in de vervolgkeuzelijst Azure Cloud.

6. Klik op **OK** om uw wijzigingen op te slaan.

#### <a name="remove-a-workspace-using-powershell"></a>Een werkruimte verwijderen met PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Een werkruimte toevoegen in Azure commercial met PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Een werkruimte toevoegen in Azure voor de Amerikaanse overheid met PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Als u de opdrachtregel of het script eerder hebt `EnableAzureOperationalInsights` gebruikt `AddCloudWorkspace` om `RemoveCloudWorkspace`de agent te installeren of te configureren, is deze vervangen door en .
>

### <a name="linux-agent"></a>Linux-agent
De volgende stappen laten zien hoe u de Linux-agent opnieuw configureert als u besluit deze bij een andere werkruimte te registreren of een werkruimte uit de configuratie te verwijderen.

1. Voer de volgende opdracht uit om te controleren of deze is geregistreerd op een werkruimte:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Het moet een status retourneren die vergelijkbaar is met het volgende voorbeeld:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Het is belangrijk dat de status ook laat zien dat de agent wordt uitgevoerd, anders worden de volgende stappen om de agent opnieuw te configureren niet voltooid.

2. Als deze al is geregistreerd bij een werkruimte, verwijdert u de geregistreerde werkruimte door de volgende opdracht uit te voeren. Anders als het niet is geregistreerd, ga dan naar de volgende stap.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Voer de volgende opdracht uit om u bij een andere werkruimte te registreren:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Voer de volgende opdracht uit om te controleren of de wijzigingen van kracht zijn geworden:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Het moet een status retourneren die vergelijkbaar is met het volgende voorbeeld:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

De agentservice hoeft niet opnieuw te worden gestart om de wijzigingen van kracht te laten worden.

## <a name="update-proxy-settings"></a>Proxy-instellingen bijwerken
Als u de agent wilt configureren om na implementatie met de service te communiceren via een proxyserver of [Logboekanalysegateway,](gateway.md) gebruikt u een van de volgende methoden om deze taak te voltooien.

### <a name="windows-agent"></a>Windows-agent

#### <a name="update-settings-using-control-panel"></a>Instellingen bijwerken met configuratiescherm

1. Meld u aan bij de computer met een account met beheerdersrechten.

2. Configuratiescherm **openen**.

3. Selecteer **Microsoft Monitoring Agent** en klik op het tabblad **Proxy-instellingen.**

4. Klik op **Een proxyserver gebruiken** en geef de URL en het poortnummer van de proxyserver of gateway op. Als er voor uw proxyserver of Log Analytics-gateway verificatie is vereist, voert u de gebruikersnaam en het wachtwoord in voor verificatie en klikt u op **OK**.

#### <a name="update-settings-using-powershell"></a>Instellingen bijwerken met PowerShell

Kopieer de volgende powershell-code in het voorbeeld, werk deze bij met informatie die specifiek is voor uw omgeving en sla deze op met een PS1-bestandsnaamextensie. Voer het script uit op elke computer die rechtstreeks verbinding maakt met de werkruimte Log Analytics in Azure Monitor.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Linux-agent
Voer de volgende stappen uit als uw Linux-computers moeten communiceren via een proxyserver of Log Analytics-gateway. De waarde voor de proxyconfiguratie heeft de volgende syntaxis `[protocol://][user:password@]proxyhost[:port]`. De eigenschap *proxyhost* accepteert een volledig gekwalificeerde domeinnaam of het IP-adres van de proxyserver.

1. Bewerk het bestand `/etc/opt/microsoft/omsagent/proxy.conf` door de volgende opdrachten uit te voeren en de waarden aan te passen aan uw specifieke instellingen.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. Start de agent opnieuw door de volgende opdracht uit te voeren:

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>Agent verwijderen
Gebruik een van de volgende procedures om de Windows- of Linux-agent te verwijderen met behulp van de opdrachtregel of de wizard Setup.

### <a name="windows-agent"></a>Windows-agent

#### <a name="uninstall-from-control-panel"></a>Verwijderen uit Configuratiescherm
1. Meld u aan bij de computer met een account met beheerdersrechten.

2. Klik in **het Configuratiescherm**op **Programma's en onderdelen**.

3. Klik in **Programma's en onderdelen**op **Microsoft Monitoring Agent,** klik op **Verwijderen**en klik vervolgens op **Ja**.

>[!NOTE]
>De wizard Agent Setup kan ook worden uitgevoerd door te dubbelklikken op **MMASetup-platform\<\>.exe,** dat beschikbaar is om te downloaden vanuit een werkruimte in de Azure-portal.

#### <a name="uninstall-from-the-command-line"></a>Verwijderen van de opdrachtregel
Het gedownloade bestand voor de agent is een op zichzelf staand installatiepakket dat is gemaakt met IExpress. Het installatieprogramma voor de agent en de ondersteunende bestanden zijn opgenomen in het pakket en moeten worden geëxtraheerd om correct te verwijderen met behulp van de opdrachtregel in het volgende voorbeeld.

1. Meld u aan bij de computer met een account met beheerdersrechten.

2. Als u de installatiebestanden van de agent `extract MMASetup-<platform>.exe` wilt extraheren, wordt u vanuit een opdrachtprompt met verhoogde bevoegdheid verwijderd en wordt u gevraagd om het pad om bestanden naar uit te halen. U het pad ook opgeven `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`door de argumenten door te geven. Zie [Command-line switches voor IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) en werk het voorbeeld bij om aan uw behoeften te voldoen voor meer informatie over de command-line switches die door IExpress worden ondersteund.

3. Typ bij de `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`prompt .

### <a name="linux-agent"></a>Linux-agent
Voer de volgende opdracht op de Linux-computer uit om de agent te verwijderen. Met het argument *--purge* worden de agent en de configuratie ervan volledig verwijderd.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Agent configureren om te rapporteren aan een beheergroep Operations Manager

### <a name="windows-agent"></a>Windows-agent
Voer de volgende stappen uit om de log-analyse-agent voor Windows te configureren om te rapporteren aan een beheergroep van System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Meld u aan bij de computer met een account met beheerdersrechten.

2. Configuratiescherm **openen**.

3. Klik **op Microsoft-controleagent** en klik vervolgens op het tabblad **Operations Manager.**

4. Als uw Operations Manager-servers zijn integratie met Active Directory, klikt u op **Opdrachten voor beheergroepen van AD DS automatisch bijwerken.**

5. Klik **op Toevoegen** om het dialoogvenster Een **beheergroep toevoegen te** openen.

6. Typ in het veld Naam van de **groep Beheer** de naam van uw beheergroep.

7. Typ in het veld **Primaire beheerserver** de computernaam van de primaire beheerserver.

8. Typ in het veld **Beheerserverpoort** het TCP-poortnummer.

9. Kies **onder Agent Action Account**het lokale systeemaccount of een lokaal domeinaccount.

10. Klik op **OK** om het dialoogvenster **Een beheergroep toevoegen** te sluiten en klik vervolgens op **OK** om het dialoogvenster Eigenschappen **van Microsoft-bewakingsagent te** sluiten.

### <a name="linux-agent"></a>Linux-agent
Voer de volgende stappen uit om de log-analyse-agent voor Linux te configureren om te rapporteren aan een beheergroep van System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Het bestand bewerken`/etc/opt/omi/conf/omiserver.conf`

2. Zorg ervoor dat `httpsport=` de lijn die begint met definieert de poort 1270. Zoals:`httpsport=1270`

3. Start de OMI-server opnieuw:`sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Volgende stappen

- Controleer [het oplossen van problemen met de Linux-agent](agent-linux-troubleshoot.md) als u problemen ondervindt tijdens het installeren of beheren van de Linux-agent.

- Controleer [het oplossen van problemen met de Windows-agent](agent-windows-troubleshoot.md) als u problemen ondervindt tijdens het installeren of beheren van de Windows-agent.
