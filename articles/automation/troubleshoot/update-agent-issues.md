---
title: Diagnose Windows Hybrid Runbook Worker - Azure Update Management
description: Meer informatie over het oplossen en oplossen van problemen met de Azure Automation Hybrid Runbook Worker op Windows die Updatebeheer ondersteunt.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: ec35d11eba59ea21947e2c3cd5286bababa4eabb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153851"
---
# <a name="understand-and-resolve-windows-hybrid-runbook-worker-health-in-update-management"></a>De status van Windows Hybrid Runbook Worker begrijpen en oplossen in Updatebeheer

Er kunnen vele redenen zijn waarom uw machine niet **Ready** weergeeft in Updatebeheer. In Updatebeheer u de status van een hybride runbookworker-agent controleren om het onderliggende probleem te bepalen. In dit artikel wordt besproken hoe u de probleemoplosser voor Azure-machines uitvoeren vanuit de Azure-portal en niet-Azure-machines in het [offlinescenario.](#troubleshoot-offline)

De volgende lijst zijn de drie gereedheidstoestanden waarin een machine zich kan bevinden:

* **Klaar** - De hybride runbook worker is geïmplementeerd en is minder dan 1 uur geleden voor het laatst gezien.
* **Losgekoppeld** - De hybride runbookworker wordt geïmplementeerd en is meer dan 1 uur geleden voor het laatst gezien.
* **Niet geconfigureerd** - De hybride runbookworker is niet gevonden of is nog niet klaar met instappen.

> [!NOTE]
> Er kan een kleine vertraging optreden tussen wat de Azure-portal weergeeft en de huidige status van de machine.

## <a name="start-the-troubleshooter"></a>De probleemoplosser starten

Klik voor Azure-machines op de koppeling **Problemen oplossen** onder de kolom **Gereedheid bijwerken** in de portal en start de pagina Problemen met **updateagent.** Voor niet-Azure-machines brengt de koppeling u naar dit artikel. Zie de [offline instructies](#troubleshoot-offline) om problemen op te lossen voor een niet-Azure-machine.

![Lijst met beheer van virtuele machines bijwerken](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Als u de status van de hybride runbookworker wilt controleren, moet de vm worden uitgevoerd. Als de vm niet wordt uitgevoerd, wordt de knop **VM starten** weergegeven.

Selecteer op de pagina **Problemen met bijwerken agent** de optie Controles **uitvoeren** om de probleemoplosser te starten. De probleemoplosser gebruikt [Run Command](../../virtual-machines/windows/run-command.md) om een script op de machine uit te voeren om afhankelijkheden te verifiëren. Wanneer de probleemoplosser is voltooid, wordt het resultaat van de controles geretourneerd.

![Pagina Updateagent oplossen](../media/update-agent-issues/troubleshoot-page.png)

Resultaten worden weergegeven op de pagina wanneer ze klaar zijn. De controles secties tonen wat is opgenomen in elke controle.

![Update-agentcontroles oplossen](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Controles van vereisten

### <a name="operating-system"></a>Besturingssysteem

De controle van het besturingssysteem controleert of de hybride runbookworker een van de volgende besturingssystemen uitvoert:

|Besturingssysteem  |Opmerkingen  |
|---------|---------|
|Windows Server 2012 en hoger |.NET Framework 4.6 of hoger is vereist. ([Download het .NET Framework)](/dotnet/framework/install/guide-for-developers)<br/> Windows PowerShell 5.1 is vereist.  ([Windows Management Framework 5.1 downloaden](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET 4.6.2

De .NET Framework-controle controleert of het systeem minimaal [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) heeft geïnstalleerd.

### <a name="wmf-51"></a>WMF 5.1

De WMF-controle controleert of het systeem de vereiste versie van het Windows Management Framework (WMF) heeft - [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Deze controle bepaalt of u TLS 1.2 gebruikt om uw communicatie te versleutelen. TLS 1.0 wordt niet meer ondersteund door het platform. We raden klanten aan TLS 1.2 te gebruiken om te communiceren met Update Management.

## <a name="connectivity-checks"></a>Connectiviteitscontroles

### <a name="registration-endpoint"></a>Eindpunt voor registratie

Deze controle bepaalt of de agent goed kan communiceren met de agent service.

Proxy- en firewallconfiguraties moeten de medewerker Hybride Runbook Worker in staat stellen te communiceren met het registratieeindpunt. Zie [Netwerkplanning voor hybride werknemers voor](../automation-hybrid-runbook-worker.md#network-planning)een lijst met adressen en poorten die u wilt openen.

### <a name="operations-endpoint"></a>Eindpunt voor bewerkingen

Deze controle bepaalt of de agent goed kan communiceren met de Job Runtime Data Service.

Proxy- en firewallconfiguraties moeten de medewerker Hybride Runbook Worker in staat stellen te communiceren met de Job Runtime Data Service. Zie [Netwerkplanning voor hybride werknemers voor](../automation-hybrid-runbook-worker.md#network-planning)een lijst met adressen en poorten die u wilt openen.

## <a name="vm-service-health-checks"></a>VM-servicestatuscontroles

### <a name="monitoring-agent-service-status"></a>Status van bewakingsagentservice

Deze controle `HealthService`bepaalt of de Microsoft Monitoring Agent op de machine wordt uitgevoerd.

Zie [De Microsoft-bewakingsagent wordt niet uitgevoerd](hybrid-runbook-worker.md#mma-not-running)voor meer informatie over het oplossen van problemen met de service.

Zie [De Microsoft-bewakingsagent installeren en configureren](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)als u de Microsoft Monitoring Agent opnieuw wilt installeren en configureren.

### <a name="monitoring-agent-service-events"></a>Gebeurtenissen van de bewakingsagentservice

Met deze controle `4502` bepaalt u of gebeurtenissen in de afgelopen 24 uur worden weergegeven in het Azure Operations Manager-logboek op de machine.

Zie de [handleiding voor probleemoplossing](hybrid-runbook-worker.md#event-4502) voor deze gebeurtenis voor meer informatie over deze gebeurtenis.

## <a name="access-permissions-checks"></a>Controles toegangsmachtigingen

### <a name="machinekeys-folder-access"></a>Toegang tot de map MachineKeys

De toegangscontrole voor de cryptomap bepaalt of het lokale systeemaccount toegang heeft tot C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Problemen met offline oplossen

U de probleemoplosser voor een hybride runbookworker offline gebruiken door het script lokaal uit te voeren. U het script, [Troubleshoot-WindowsUpdateAgentRegistration,](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration)in de PowerShell Gallery krijgen. U moet WMF 4.0 of meer hebben geïnstalleerd om het script uit te voeren. Zie [Verschillende versies van PowerShell installeren](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)als u de nieuwste versie van PowerShell wilt downloaden.

De uitvoer van dit script ziet eruit als het volgende voorbeeld:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Volgende stappen

Zie Problemen met [hybride runbookworkers oplossen](hybrid-runbook-worker.md)als u meer problemen met uw hybride runbook-werknemers wilt oplossen.
