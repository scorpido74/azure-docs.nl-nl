---
title: Problemen met de configuratie van Azure Automation State (DSC) oplossen
description: Dit artikel bevat informatie over het oplossen van problemen met de configuratie van Azure Automation State (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679309"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>Problemen oplossen met de configuratie van Azure Automation State (DSC)

Dit artikel bevat informatie over het oplossen van problemen die zich voordoen tijdens het compileren of implementeren van configuraties in Azure Automation State Configuration (DSC).

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](../automation-update-azure-modules.md).

## <a name="diagnosing-an-issue"></a>Een probleem vaststellen

Wanneer u een compilatie-of implementatie fout voor de configuratie ontvangt, zijn hier enkele stappen waarmee u het probleem kunt vaststellen.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Controleer of de configuratie is gecompileerd op de lokale computer

Azure Automation State Configuration (DSC) is gebaseerd op Power shell desired state Configuration (DSC). U kunt de documentatie voor de DSC-taal en syntaxis vinden in de [Power shell DSC-documenten](https://docs.microsoft.com/powershell/scripting/overview).

Door een DSC-configuratie op uw lokale machine te compileren, kunt u veelvoorkomende fouten detecteren en oplossen, zoals:

   - Ontbrekende modules
   - Syntaxis fouten
   - Logische fouten

### <a name="2-view-dsc-logs-on-your-node"></a>2. DSC-logboeken weer geven op uw knoop punt

Als uw configuratie is gecompileerd, maar mislukt als deze wordt toegepast op een knoop punt, kunt u gedetailleerde informatie vinden in de DSC-Logboeken. Zie voor informatie over waar u deze logboeken kunt vinden, [waar de DSC-gebeurtenis logboeken zijn](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

De [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) -module kan u helpen bij het parseren van gedetailleerde informatie uit de DSC-Logboeken. Als u contact opneemt met ondersteuning, moeten deze logboeken uw probleem vaststellen.

U kunt de `xDscDiagnostics` module installeren op uw lokale computer met behulp van de instructies in [de module stabiele versie installeren](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Als u de `xDscDiagnostics` module op uw Azure-machine wilt installeren, gebruikt u [invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). U kunt ook de optie **opdracht uitvoeren** in de Azure Portal gebruiken door de stappen te volgen in [Power shell-scripts uitvoeren in uw Windows-VM met de opdracht uitvoeren](../../virtual-machines/windows/run-command.md).

Zie voor meer informatie over het gebruik van **XDSCDIAGNOSTICS** [DSC-logboeken analyseren met behulp van xDscDiagnostics](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Zie ook [xDscDiagnostics-cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Controleer of de knoop punten en de Automation-werk ruimte de vereiste modules hebben

DSC is afhankelijk van de modules die op het knoop punt zijn geïnstalleerd. Wanneer u Azure Automation status configuratie gebruikt, importeert u de benodigde modules in uw Automation-account met behulp van de stappen in [import modules](../shared-resources/modules.md#importing-modules). Configuraties kunnen ook afhankelijk zijn van specifieke versies van modules. Zie [problemen met modules oplossen](shared-resources.md#modules)voor meer informatie.

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenario: een configuratie met speciale tekens kan niet worden verwijderd uit de portal

### <a name="issue"></a>Probleem

Wanneer u probeert een DSC-configuratie te verwijderen uit de portal, ziet u de volgende fout:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Oorzaak

Deze fout is een tijdelijk probleem dat is gepland om te worden opgelost.

### <a name="resolution"></a>Oplossing

Gebruik de [Remove-AzAutomationDscConfiguration] (https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 cmdlet om de configuratie te verwijderen.

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenario: de DSC-agent kan niet worden geregistreerd

### <a name="issue"></a>Probleem

Wanneer u DscLocalConfigurationManager of een andere DSC-cmdlet hebt [ingesteld](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) , wordt de volgende fout weer gegeven:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>Oorzaak

Deze fout wordt meestal veroorzaakt door een firewall, de computer zich achter een proxy server bevindt of andere netwerk fouten.

### <a name="resolution"></a>Oplossing

Controleer of de computer toegang heeft tot de juiste eind punten voor DSC en probeer het opnieuw. Zie [netwerk planning](../automation-dsc-overview.md#network-planning) voor een lijst met poorten en adressen.

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenario: status rapporten retour reactie code niet toegestaan

### <a name="issue"></a>Probleem

Bij het registreren van een knoop punt met Azure Automation status configuratie wordt een van de volgende fout berichten weer gegeven:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Oorzaak

Dit probleem wordt veroorzaakt door een onjuist of verlopen certificaat. Zie het [verlopen en opnieuw registreren van certificaten](../automation-dsc-onboarding.md#re-registering-a-node).

Dit probleem kan ook worden veroorzaakt door een proxy configuratie waarmee geen toegang tot ***. Azure-Automation.net**wordt toegestaan. Zie [configuratie van particuliere netwerken](../automation-dsc-overview.md#network-planning)voor meer informatie. 

### <a name="resolution"></a>Oplossing

Gebruik de volgende stappen om het niet-werkende DSC-knoop punt opnieuw te registreren.

Stap 1: de registratie van het knoop punt ongedaan maken.

1. Ga in azure Portal naar **Start** -> **Automation-accounts** -> (uw Automation-account)-> **State Configuration (DSC)**.
2. Selecteer **knoop punten**en klik op het knoop punt met problemen.
3. Klik op **registratie ongedaan maken** om de registratie van het knoop punt op te heffen.

Stap 2: Verwijder de DSC-extensie van het knoop punt.

1. Ga in azure Portal naar de**virtuele machine** van de **thuis gebruiker** -> -> (knoop punt mislukt)->- **extensies**.
2. Selecteer **micro soft. Power shell. DSC**, de Power shell DSC-uitbrei ding.
3. Klik op **verwijderen** om de extensie te verwijderen.

Stap 3: Verwijder alle beschadigde of verlopen certificaten van het knoop punt.

Voer de volgende opdrachten uit op het knoop punt waarvoor een verhoogde Power shell-prompt is uitgevoerd:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

Stap 4: het knoop punt waarvoor een fout optreedt, opnieuw registreren.

1. In de Azure portal gaat u naar **Start** -> **Automation-accounts** -> (uw Automation-account)-> **State Configuration (DSC)**
2. **Knoop punten**selecteren.
3. Klik op **Add**.
4. Selecteer het knoop punt dat niet werkt.
5. Klik op **verbinden** en selecteer de gewenste opties.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenario: het knoop punt heeft de status mislukt met de fout ' niet gevonden '

### <a name="issue"></a>Probleem

Het knoop punt heeft een rapport met de status mislukt en bevat de volgende fout:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Oorzaak

Deze fout treedt doorgaans op wanneer het knoop punt is toegewezen aan een configuratie naam, bijvoorbeeld **ABC**, in plaats van de naam van een knooppunt configuratie (MOF-bestand), bijvoorbeeld **ABC. Webserver**.

### <a name="resolution"></a>Oplossing

* Zorg ervoor dat u het knoop punt toewijst met de naam van de knooppunt configuratie en niet de configuratie naam.
* U kunt een knooppunt configuratie toewijzen aan een knoop punt met behulp van Azure Portal of met een Power shell-cmdlet.

  * In de Azure portal gaat u naar **Start** -> **Automation-accounts** -> (uw Automation-account)-> **State Configuration (DSC)**, selecteert u een knoop punt en klikt u op **knooppunt configuratie toewijzen**.
  * Gebruik de cmdlet [set-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) .

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scenario: er zijn geen knooppunt configuraties (MOF-bestanden) geproduceerd tijdens het compileren van een configuratie

### <a name="issue"></a>Probleem

Uw DSC-compilatie taak wordt onderbroken met de volgende fout:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Oorzaak

Wanneer de expressie die volgt `Node` op het sleutel woord in de DSC- `$null`configuratie, wordt geëvalueerd naar, worden er geen knooppunt configuraties geproduceerd.

### <a name="resolution"></a>Oplossing

Gebruik een van de volgende oplossingen om het probleem op te lossen:

* Zorg ervoor dat de expressie naast het `Node` sleutel woord in de configuratie definitie niet wordt geëvalueerd naar null.
* Als u [ConfigurationData](../automation-dsc-compile.md) door geven tijdens het compileren van de configuratie, moet u ervoor zorgen dat u de waarden doorgeeft die de configuratie verwacht van de configuratie gegevens.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scenario: het DSC-knooppunt rapport wordt vastgelopen in de status in uitvoering

### <a name="issue"></a>Probleem

De DSC-agent uitvoer:

```error
No instance found with given property values
```

### <a name="cause"></a>Oorzaak

U hebt uw Windows Management Framework-versie (WMF) bijgewerkt en hebt Windows Management Instrumentation (WMI) beschadigd.

### <a name="resolution"></a>Oplossing

Volg de instructies in [bekende problemen en beperkingen van DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenario: een referentie kan niet worden gebruikt in een DSC-configuratie

### <a name="issue"></a>Probleem

De DSC-compilatie taak is onderbroken met de volgende fout:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Oorzaak

U hebt een referentie in een configuratie gebruikt, maar u beschikt `ConfigurationData` niet over `PSDscAllowPlainTextPassword` de juiste waarde voor elke knooppunt configuratie.

### <a name="resolution"></a>Oplossing

Zorg ervoor dat de juiste `ConfigurationData` waarde wordt ingesteld `PSDscAllowPlainTextPassword` op True voor elke knooppunt configuratie die wordt vermeld in de configuratie. Zie [DSC-configuraties compileren in de configuratie van de Azure Automation-status](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>Scenario: ' fout bij het verwerken van de extensie ' tijdens het voorbereiden van de DSC-uitbrei ding

### <a name="issue"></a>Probleem

Bij onboarding met een DSC-extensie treedt er een fout op met de volgende fout:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Oorzaak

Deze fout treedt doorgaans op wanneer aan het knoop punt een knooppunt configuratie naam wordt toegewezen die niet voor komt in de service.

### <a name="resolution"></a>Oplossing

* Zorg ervoor dat u het knoop punt toewijst met een naam die exact overeenkomt met de naam in de service.
* U kunt ervoor kiezen om de naam van de configuratie van het knoop punt niet op te stellen, wat resulteert in het onboarding van het knoop punt, maar geen knooppunt configuratie toewijst.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>Scenario: fout ' er zijn een of meer fouten opgetreden ' bij het registreren van een knoop punt met Power shell

### <a name="issue"></a>Probleem

Wanneer u een knoop punt registreert met behulp [van Regi ster-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) of [REGI ster-AzureRMAutomationDSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0), wordt de volgende fout weer gegeven:

```error
One or more errors occurred.
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een knoop punt probeert te registreren in een afzonderlijk abonnement dat wordt gebruikt door het Automation-account.

### <a name="resolution"></a>Oplossing

Het knoop punt voor meerdere abonnementen behandelen alsof het is gedefinieerd voor een afzonderlijke Cloud, of on-premises. Registreer het knoop punt met een van de volgende onboarding-opties:

* Windows- [fysieke en virtuele Windows-machines on-premises of in een andere Cloud dan Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines).
* Linux- [fysieke/virtuele Linux-machines on-premises of in een andere Cloud dan Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines).

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Scenario: fout bericht-' inrichting mislukt '

### <a name="issue"></a>Probleem

Wanneer u een knoop punt registreert, ziet u de volgende fout:

```error
Provisioning has failed
```

### <a name="cause"></a>Oorzaak

Dit bericht wordt weer gegeven wanneer er een probleem is met de verbinding tussen het knoop punt en Azure.

### <a name="resolution"></a>Oplossing

Bepaal of het knoop punt zich in een virtueel particulier netwerk (VPN) bevindt of dat er andere problemen zijn die verbinding maken met Azure. Raadpleeg [problemen oplossen bij het onboarden van oplossingen](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scenario: fout met een algemene fout bij het Toep assen van een configuratie in Linux

### <a name="issue"></a>Probleem

Bij het Toep assen van een configuratie in Linux treedt er een fout op met de volgende fout:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Oorzaak

Als de locatie van de **map/tmp** is `noexec`ingesteld op, kan de huidige versie van DSC geen configuraties Toep assen.

### <a name="resolution"></a>Oplossing

Verwijder de `noexec` optie uit de locatie **map/tmp** .

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenario: namen van knooppunt configuraties die elkaar overlappen kunnen resulteren in slechte release

### <a name="issue"></a>Probleem

Wanneer u één configuratie script gebruikt om meerdere knooppunt configuraties te genereren en sommige knooppunt configuratie namen subsets van andere namen zijn, kan de compilatie service uiteindelijk de verkeerde configuratie toewijzen. Dit probleem treedt alleen op wanneer u één script gebruikt voor het genereren van configuraties met configuratie gegevens per knoop punt en alleen wanneer de naam overlapt aan het begin van de teken reeks. Een voor beeld is een configuratie script dat wordt gebruikt voor het genereren van configuraties op basis van knooppunt gegevens die worden door gegeven als hashes met behulp van cmdlets en de knooppunt gegevens bevatten servers met de naam **Server** en **1server**.

### <a name="cause"></a>Oorzaak

Dit is een bekend probleem met de compilatie service.

### <a name="resolution"></a>Oplossing

De beste oplossing is lokaal of in een CI/CD-pijp lijn te compileren en de knooppunt configuratie-MOF-bestanden rechtstreeks te uploaden naar de service. Als de compilatie in de service een vereiste is, is de volgende beste oplossing om de compilatie taken te splitsen zodat er geen overlap is in namen.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenario: time-out van gateway bij DSC-configuratie uploaden

#### <a name="issue"></a>Probleem

Er wordt een `GatewayTimeout` fout bericht weer gegeven bij het uploaden van een DSC-configuratie. 

### <a name="cause"></a>Oorzaak

DSC-configuraties die lange tijd duren om te compileren, kunnen deze fout veroorzaken.

### <a name="resolution"></a>Oplossing

U kunt ervoor zorgen dat uw DSC-configuraties sneller worden geparseerd door expliciet de para meter op te nemen voor de aanroepen van de `ModuleName` [import-dscresource bieden](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1) .

## <a name="next-steps"></a>Volgende stappen

Als uw probleem niet hierboven wordt weer geven of als u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport)met, het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.