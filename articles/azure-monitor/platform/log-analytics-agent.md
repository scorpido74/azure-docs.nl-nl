---
title: Overzicht van log-analyses
description: Met dit onderwerp u begrijpen hoe u gegevens verzamelt en computers bewaakt die worden gehost in Azure, on-premises of andere cloudomgevingmet Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 1ca03cde57a9496054d0860fbb70bd286caabe46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533246"
---
# <a name="log-analytics-agent-overview"></a>Overzicht van log-analyses
De Azure Log Analytics-agent is ontwikkeld voor uitgebreid beheer voor virtuele machines in elke cloud, on-premises machines en machines die worden gecontroleerd door [System Center Operations Manager.](https://docs.microsoft.com/system-center/scom/) De Windows- en Linux-agents verzenden verzamelde gegevens uit verschillende bronnen naar uw Log Analytics-werkruimte in Azure Monitor, evenals unieke logboeken of statistieken zoals gedefinieerd in een bewakingsoplossing. De agent Log Analytics ondersteunt ook inzichten en andere services in Azure Monitor, zoals [Azure Monitor for VMs,](../insights/vminsights-enable-overview.md) [Azure Security Center](/azure/security-center/)en [Azure Automation.](../../automation/automation-intro.md)

In dit artikel vindt u een gedetailleerd overzicht van de vereisten voor de agent, het systeem en het netwerk en de verschillende implementatiemethoden.

> [!NOTE]
> U ook de Log Analytics-agent zien die de Microsoft Monitoring Agent (MMA) of OMS Linux-agent wordt genoemd.

> [!NOTE]
> Azure Diagnostics-extensie is een van de agents die beschikbaar is om bewakingsgegevens te verzamelen van het gastbesturingssysteem van compute resources. Zie [Overzicht van de Azure Monitor-agents](agents-overview.md) voor een beschrijving van de verschillende agents en richtlijnen voor het selecteren van de juiste agents voor uw vereisten.

## <a name="comparison-to-azure-diagnostics-extension"></a>Vergelijking met Azure-diagnostische extensie
De [Azure-diagnostische extensie](diagnostics-extension-overview.md) in Azure Monitor kan ook worden gebruikt om bewakingsgegevens te verzamelen van het gastbesturingssysteem van azure virtuele machines. U ervoor kiezen om een van beide of beide te gebruiken, afhankelijk van uw vereisten. Zie [Overzicht van de Azure Monitor-agents](agents-overview.md) voor een gedetailleerde vergelijking van de Azure Monitor-agents. 

De belangrijkste verschillen om te overwegen zijn:

- Azure Diagnostics Extension kan alleen worden gebruikt met virtuele Azure-machines. De log-analyse-agent kan worden gebruikt met virtuele machines in Azure, andere clouds en on-premises.
- Azure Diagnostics-extensie verzendt gegevens naar Azure Storage, [Azure Monitor Metrics](data-platform-metrics.md) (alleen Windows) en Event Hubs. De medewerker Logboekanalyse verzamelt gegevens naar [Azure Monitor Logs](data-platform-logs.md).
- De log-analyse-agent is vereist voor [oplossingen,](../monitor-reference.md#insights-and-core-solutions) [Azure Monitor voor VM's](../insights/vminsights-overview.md)en andere services zoals [Azure Security Center.](/azure/security-center/)

## <a name="costs"></a>Kosten
Er zijn geen kosten verbonden aan de Log Analytics-agent, maar er kunnen wel kosten in rekening worden gebracht voor de ingenomen gegevens. Controleer [Gebruik en kosten beheren met Azure Monitor-logboeken](manage-cost-storage.md) voor gedetailleerde informatie over de prijzen voor gegevens die zijn verzameld in een werkruimte log Analytics.

## <a name="data-collected"></a>Verzamelde gegevens
In de volgende tabel worden de typen gegevens weergegeven die u een Log Analytics-werkruimte configureren om te verzamelen van alle verbonden agents. Zie [Wat wordt gecontroleerd door Azure Monitor?](../monitor-reference.md) voor een lijst met inzichten, oplossingen en andere oplossingen die de Log Analytics-agent gebruiken om andere soorten gegevens te verzamelen.

| Gegevensbron | Beschrijving |
| --- | --- |
| [Logboeken voor Windows-gebeurtenissen](data-sources-windows-events.md) | Informatie die naar het Windows-gebeurtenislogboekregistratiesysteem wordt verzonden. |
| [Syslog](data-sources-syslog.md)                     | Informatie die naar het Linux-gebeurtenisregistratiesysteem wordt verzonden. |
| [Prestaties](data-sources-performance-counters.md)  | Numerieke waarden meten de prestaties van verschillende aspecten van het besturingssysteem en workloads. |
| [IIS-logboeken](data-sources-iis-logs.md)                 | Gebruiksinformatie voor IIS-websites die op het gastbesturingssysteem worden uitgevoerd. |
| [Aangepaste logboeken](data-sources-custom-logs.md)           | Gebeurtenissen van tekstbestanden op zowel Windows- als Linux-computers. |

## <a name="data-destinations"></a>Gegevensbestemmingen
De medewerker Logboekanalyse verzendt gegevens naar een Log Analytics-werkruimte in Azure Monitor. De Windows-agent kan worden multihomed om gegevens te verzenden naar meerdere werkruimten en System Center Operations Manager-beheergroepen. De Linux-agent kan slechts naar één bestemming verzenden.

## <a name="other-services"></a>Overige services
De agent voor Linux en Windows is niet alleen voor het verbinden met Azure Monitor, het ondersteunt ook Azure Automation om de hybride runbook-werkrol en andere services zoals [Change Tracking,](../../automation/change-tracking.md) [Update Management](../../automation/automation-update-management.md)en Azure [Security Center](../../security-center/security-center-intro.md)te hosten. Zie [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)voor meer informatie over de rol Hybride Runbook Worker voor hybride runbook .  

## <a name="installation-and-configuration"></a>Installatie en configuratie

Wanneer u de Log Analytics-agents gebruikt om gegevens te verzamelen, moet u het volgende begrijpen om de implementatie van uw agent te plannen:

* Als u gegevens van Windows-agents wilt verzamelen, u [elke agent configureren om te rapporteren aan een of meer werkruimten,](agent-windows.md)zelfs terwijl deze wordt gemeld aan een beheergroep van System Center Operations Manager. De Windows-agent kan maximaal vier werkruimten rapporteren.
* De Linux-agent ondersteunt geen multi-homing en kan alleen rapporteren aan één werkruimte.
* De Windows-agent ondersteunt de [FIPS 140-standaard,](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation)terwijl de Linux-agent deze niet ondersteunt.  

Als u System Center Operations Manager 2012 R2 of hoger gebruikt:

* Elke beheergroep operations manager kan slechts met één werkruimte worden [verbonden.](om-agents.md)
* Linux-computers die rapporteren aan een beheergroep, moeten zijn geconfigureerd om rechtstreeks te rapporteren aan een Log Analytics-werkruimte. Als uw Linux-computers al rechtstreeks naar een werkruimte rapporteren en u deze wilt controleren met Operations Manager, voert u deze stappen uit om te [rapporteren aan een beheergroep operations manager.](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)
* U de Windows-agent Log Analytics op de Windows-computer installeren en deze laten rapporteren aan zowel Operations Manager die is geïntegreerd met een werkruimte als een andere werkruimte.


Er zijn meerdere methoden om de Log Analytics-agent te installeren en uw machine aan te sluiten op Azure Monitor, afhankelijk van uw vereisten. In de volgende tabel wordt elke methode benadrukt om te bepalen welke het beste werkt in uw organisatie.

|Bron | Methode | Beschrijving|
|-------|-------------|-------------|
|Azure VM| [Handmatig vanuit de Azure-portal](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Geef VM's op die moeten worden geïmplementeerd vanuit de werkruimte Log Analytics. |
| | Vm-extensie log Analytics voor [Windows](../../virtual-machines/extensions/oms-windows.md) of [Linux](../../virtual-machines/extensions/oms-linux.md) met Azure CLI of met een Azure Resource Manager-sjabloon | De extensie installeert de Log Analytics-agent op virtuele Azure-machines en schrijft deze in in een bestaande Azure Monitor-werkruimte. |
| | [Azure Monitor voor virtuele machines](../insights/vminsights-enable-overview.md) | Wanneer u bewaking inschakelt met Azure Monitor voor VM's, worden de log analytics-extensie en -agent geïnstalleerd. |
| | [Automatische inrichting van Azure Security Center](../../security-center/security-center-enable-data-collection.md) | Azure Security Center kan de Log Analytics-agent inrichten op alle ondersteunde Azure VM's en alle nieuwe apparaten die worden gemaakt als u deze inschakelt om te controleren op beveiligingsproblemen en bedreigingen. Als deze is ingeschakeld, wordt elke nieuwe of bestaande VM zonder geïnstalleerde agent ingericht. |
| Hybride Windows-computer| [Handmatig installeren](agent-windows.md) | Installeer de Microsoft Monitoring-agent vanaf de opdrachtregel. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Automatiseer de installatie met Azure Automation DSC. |
| | [Resourcebeheersjabloon met Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | Gebruik een Azure Resource Manager-sjabloon als u Microsoft Azure Stack in uw datacenter hebt geïmplementeerd.| 
| Hybride Linux-computer| [Handmatig installeren](../../azure-monitor/learn/quick-collect-linux-computer.md)|Installeer de agent voor Linux-aanroepen een wrapper-script gehost op GitHub. | 
| System Center Operations Manager|[Operations Manager integreren met Log Analytics](../../azure-monitor/platform/om-agents.md) | Configureer de integratie tussen Operations Manager en Azure Monitor-logboeken om verzamelde gegevens van Windows-computers te rapporteren aan een beheergroep.|  


## <a name="supported-windows-operating-systems"></a>Ondersteunde Windows-besturingssystemen

De volgende versies van het Windows-besturingssysteem worden officieel ondersteund voor de Windows-agent:

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, versie 1709 en 1803
* Windows 7 SP1, Windows 8 Enterprise en Pro en Windows 10 Enterprise en Pro

>[!NOTE]
>Hoewel de Log Analytics-agent voor Windows is ontworpen om serverbewakingsscenario's te ondersteunen, realiseren we ons dat u windows-client uitvoeren om workloads te ondersteunen die zijn geconfigureerd en geoptimaliseerd voor het serverbesturingssysteem. De agent ondersteunt wel Windows-client, maar onze monitoringoplossingen richten zich niet op scenario's voor klantbewaking, tenzij expliciet vermeld.

## <a name="supported-linux-operating-systems"></a>Ondersteunde Linux-besturingssystemen

In deze sectie vindt u meer informatie over de ondersteunde Linux-distributies.

Te beginnen met versies die na augustus 2018 zijn uitgebracht, brengen we de volgende wijzigingen aan in ons ondersteuningsmodel:  

* Alleen de serverversies worden ondersteund, niet de client.  
* Focus ondersteuning op een van de [Azure Linux Endorsed distro's](../../virtual-machines/linux/endorsed-distros.md). Houd er rekening mee dat er enige vertraging kan optreden tussen een nieuwe distro/versie die Azure Linux wordt goedgekeurd en dat deze wordt ondersteund voor de Log Analytics Linux-agent.
* Alle kleine releases worden ondersteund voor elke belangrijke versie die wordt vermeld.
* Versies die de einddatum van de fabrikant hebben overschreden, worden niet ondersteund.  
* Nieuwe versies van AMI worden niet ondersteund.  
* Alleen versies met SSL 1.x worden standaard ondersteund.

>[!NOTE]
>Als u een distro of versie gebruikt die momenteel niet wordt ondersteund en niet wordt uitgelijnd op ons ondersteuningsmodel, raden we u aan deze repo te forken, erkennend dat Microsoft-ondersteuning geen hulp biedt bij versies van vorkagents.

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) en 7 (x64)  
* Oracle Linux 6 en 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) en 7 (x64)
* Debian GNU/Linux 8 en 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64), 16.04 LTS (x86/x64) en 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) en 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 wordt alleen ondersteund op x86_x64-platforms (64-bits) en OpenSSL eerder dan 1.x wordt op geen enkel platform ondersteund.
>

### <a name="agent-prerequisites"></a>Agentvereisten

In de volgende tabel worden de pakketten weergegeven die nodig zijn voor ondersteunde Linux-distro's waarop de agent wordt geïnstalleerd.

|Vereist pakket |Beschrijving |Minimale versie |
|-----------------|------------|----------------|
|Glibc |    GNU C-bibliotheek | 2.5-12 
|Openssl    | OpenSSL-bibliotheken | 1.0.x of 1.1.x |
|Curl | cURL-webclient | 7.15.5 |
|Python-ctypes | | 
|PAM | Pluggable Authentication Modules | | 

>[!NOTE]
>Rsyslog of syslog-ng zijn verplicht om syslog-berichten te verzamelen. De standaard syslog daemon op versie 5 van Red Hat Enterprise Linux, CentOS en Oracle Linux versie (sysklog) wordt niet ondersteund voor syslog event collection. Om sysloggegevens uit deze versie van deze distributies te verzamelen, moet de rsyslog daemon worden geïnstalleerd en geconfigureerd om sysklog te vervangen.

## <a name="tls-12-protocol"></a>TLS 1.2-protocol

Om de beveiliging van gegevens die onderweg zijn naar Azure Monitor-logboeken te garanderen, raden we u ten zeerste aan om de agent te configureren om ten minste TLS(1.2) (Transport Layer Security) te gebruiken. Oudere versies van TLS/Secure Sockets Layer (SSL) zijn kwetsbaar bevonden en hoewel ze momenteel nog werken om achterwaartse compatibiliteit mogelijk te maken, worden ze **niet aanbevolen.**  Voor meer informatie, controleer [het verzenden van gegevens veilig met TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 


## <a name="sha-2-code-signing-support-requirement-for-windows"></a>Ondersteuningsvereiste voor sha-2-code ondertekening voor Windows
De Windows-agent zal op 18 mei 2020 uitsluitend sha-2-ondertekening gebruiken. Deze wijziging heeft gevolgen voor klanten die de Log Analytics-agent gebruiken op een verouderd besturingssysteem als onderdeel van een Azure-service (Azure Monitor, Azure Automation, Azure Update Management, Azure Change Tracking, Azure Security Center, Azure Sentinel, Windows Defender ATP). Voor de wijziging is geen actie van de klant vereist, tenzij u de agent uitvoert op een verouderde osversie (Windows 7, Windows Server 2008 R2 en Windows Server 2008). Klanten die op een verouderde VERSIE van het BE draaien, moeten vóór 18 mei 2020 de volgende acties op hun machines uitvoeren of hun medewerkers stoppen met het verzenden van gegevens naar hun Log Analytics-werkruimten:

1. Installeer het nieuwste Service Pack voor uw besturingssysteem. De vereiste servicepackversies zijn:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Installeer de SHA-2-ondertekeningswindows-updates voor uw besturingssysteem zoals beschreven in [de ondersteuningsvereiste voor sha-2-code ondertekening in 2019 voor Windows en WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Update naar de nieuwste versie van de Windows-agent (versie 10.20.18029).
4. Aanbevolen om de agent te configureren om [TLS 1.2](agent-windows.md#configure-agent-to-use-tls-12)te gebruiken. 


## <a name="network-requirements"></a>Netwerkvereisten
De agent voor Linux en Windows communiceert outbound naar de Azure Monitor-service via TCP-poort 443 en als de machine verbinding maakt via een firewall of proxyserver om via internet te communiceren, controleert u onderstaande vereisten om de netwerkconfiguratie te begrijpen Vereist. Als in uw IT-beveiligingsbeleid computers in het netwerk geen verbinding kunnen worden gemaakt met internet, u een [Logboekanalysegateway](gateway.md) instellen en vervolgens de agent configureren om verbinding te maken via de gateway naar Azure Monitor-logboeken. De agent kan vervolgens configuratie-informatie ontvangen en gegevens verzenden die zijn verzameld, afhankelijk van welke regels voor het verzamelen van gegevens en bewakingsoplossingen u in uw werkruimte hebt ingeschakeld.

![Communicatiediagram voor log-analyses-agent](./media/log-analytics-agent/log-analytics-agent-01.png)


## <a name="network-firewall-requirements"></a>Vereisten voor netwerkfirewall
In de onderstaande informatie worden de proxy- en firewallconfiguratiegegevens vermeld die nodig zijn voor de Linux- en Windows-agent om te communiceren met Azure Monitor-logboeken.  

|Agentresource|Poorten |Richting |HTTPS-controle overslaan|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Poort 443 |Uitgaand|Ja |  
|*.oms.opinsights.azure.com |Poort 443 |Uitgaand|Ja |  
|*.blob.core.windows.net |Poort 443 |Uitgaand|Ja |  

Zie [Azure Government Management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)voor firewallgegevens die vereist zijn voor Azure Government. 

Als u van plan bent de Azure Automation Hybrid Runbook Worker te gebruiken om verbinding te maken met en te registreren bij de automatiseringsservice om runbooks of beheeroplossingen in uw omgeving te gebruiken, moet deze toegang hebben tot het poortnummer en de URL's die zijn beschreven in [Uw netwerk configureren voor de hybride runbookworker.](../../automation/automation-hybrid-runbook-worker.md#network-planning) 

De Windows- en Linux-agent ondersteunt communiceren via een proxyserver of Log Analytics-gateway naar Azure Monitor met behulp van het HTTPS-protocol.  Zowel anonieme als basisverificatie (gebruikersnaam/wachtwoord) worden ondersteund.  Voor de Windows-agent die rechtstreeks met de service is verbonden, wordt de proxyconfiguratie opgegeven tijdens de installatie of [na implementatie](agent-manage.md#update-proxy-settings) vanuit het Configuratiescherm of met PowerShell.  

Voor de Linux-agent wordt de proxyserver opgegeven tijdens de installatie of [na installatie](agent-manage.md#update-proxy-settings) door het configuratiebestand proxy.conf te wijzigen.  De configuratiewaarde van de Linux-agentproxy heeft de volgende syntaxis:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Als u niet moet verifiëren voor uw proxyserver, moet u nog steeds een pseudo-gebruiker/wachtwoord opgeven. Dit kan elke gebruikersnaam of wachtwoord zijn.

|Eigenschap| Beschrijving |
|--------|-------------|
|Protocol | https |
|gebruiker | Optionele gebruikersnaam voor proxyverificatie |
|wachtwoord | Optioneel wachtwoord voor proxyverificatie |
|proxyhost | Adres of FQDN van de proxyserver/Log Analytics-gateway |
|poort | Optioneel poortnummer voor de proxyserver/Log Analytics-gateway |

Bijvoorbeeld: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Als u speciale tekens\@gebruikt, zoals " " in uw wachtwoord, ontvangt u een fout in de proxy-verbinding omdat de waarde onjuist wordt ontleed.  Als u dit probleem wilt oplossen, codeert u het wachtwoord in de URL met behulp van een hulpprogramma zoals [URLDecode](https://www.urldecoder.org/).  



## <a name="next-steps"></a>Volgende stappen

* Bekijk [gegevensbronnen](agent-data-sources.md) om inzicht te krijgen in de gegevensbronnen die beschikbaar zijn om gegevens van uw Windows- of Linux-systeem te verzamelen. 
* Meer informatie over [logboekquery's](../log-query/log-query-overview.md) om de gegevens te analyseren die zijn verzameld uit gegevensbronnen en -oplossingen. 
* Meer informatie over [bewakingsoplossingen](../insights/solutions.md) die functionaliteit toevoegen aan Azure Monitor en ook gegevens verzamelen in de werkruimte Log Analytics.

