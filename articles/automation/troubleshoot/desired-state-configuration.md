---
title: Problemen met Azure Automation State Configuration (DSC) oplossen
description: In dit artikel vindt u informatie over het oplossen van problemen met Azure Automation State Configuration (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679309"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>Problemen met Azure Automation State Configuration (DSC) oplossen

In dit artikel vindt u informatie over probleemoplossingsproblemen die zich voordoen bij het samenstellen of implementeren van configuraties in Azure Automation State Configuration (DSC).

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](../automation-update-azure-modules.md)

## <a name="diagnosing-an-issue"></a>Een probleem diagnosticeren

Wanneer u een compilatie- of implementatiefout voor de configuratie ontvangt, zijn hier een paar stappen om u te helpen het probleem te diagnosticeren.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Zorg ervoor dat uw configuratie met succes wordt gecompileerd op de lokale machine

Azure Automation State Configuration (DSC) is gebouwd op PowerShell Desired State Configuration (DSC). U vindt de documentatie voor de DSC-taal en -syntaxis in de [PowerShell DSC-documenten](https://docs.microsoft.com/powershell/scripting/overview).

Door een DSC-configuratie op uw lokale machine samen te stellen, u veelvoorkomende fouten ontdekken en oplossen, zoals:

   - Ontbrekende modules
   - Syntaxisfouten
   - Logische fouten

### <a name="2-view-dsc-logs-on-your-node"></a>2. DSC-logboeken bekijken op uw knooppunt

Als uw configuratie met succes wordt gecompileerd, maar mislukt wanneer deze wordt toegepast op een knooppunt, u gedetailleerde informatie vinden in de DSC-logboeken. Zie [Waar zijn de DSC-gebeurtenislogboeken](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)voor informatie over waar u deze logboeken vinden.

De [xDscDiagnostics-module](https://github.com/PowerShell/xDscDiagnostics) kan u helpen bij het ontwijsmaken van gedetailleerde informatie uit de DSC-logboeken. Als u contact opneemt met ondersteuning, moeten deze logboeken uw probleem diagnosticeren.

U `xDscDiagnostics` de module op uw lokale machine installeren met behulp van de instructies in [De stabiele versiemodule installeren.](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)

Als u `xDscDiagnostics` de module op uw Azure-machine wilt installeren, gebruikt u [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). U de **opdrachtoptie Uitvoeren** ook gebruiken in de Azure-portal door de stappen te volgen in [PowerShell-scripts uitvoeren in uw Windows VM met Opdracht Uitvoeren.](../../virtual-machines/windows/run-command.md)

Zie [XDscDiagnostics gebruiken om DSC-logboeken te analyseren voor](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)informatie over het gebruik van **xDscDiagnostics.** Zie ook [xDscDiagnostics Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Zorg ervoor dat knooppunten en de werkruimte Automatisering modules nodig hebben

DSC is afhankelijk van modules die op het knooppunt zijn geïnstalleerd. Wanneer u Azure Automation State Configuration gebruikt, importeert u alle vereiste modules in uw automatiseringsaccount met behulp van de stappen in [Importmodules.](../shared-resources/modules.md#importing-modules) Configuraties kunnen ook afhankelijk zijn van specifieke versies van modules. Zie [Modules oplossen voor](shared-resources.md#modules)meer informatie .

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenario: Een configuratie met speciale tekens kan niet worden verwijderd uit de portal

### <a name="issue"></a>Probleem

Wanneer u een DSC-configuratie uit de portal probeert te verwijderen, ziet u de volgende fout:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Oorzaak

Deze fout is een tijdelijk probleem dat is gepland om te worden opgelost.

### <a name="resolution"></a>Oplossing

Gebruik de cmdlet [Remove-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 cmdlet om de configuratie te verwijderen.

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenario: Kan Dsc-agent niet registreren

### <a name="issue"></a>Probleem

Wanneer [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) of een andere DSC-cmdlet wordt ingesteld, ontvangt u de fout:

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

Deze fout wordt normaal gesproken veroorzaakt door een firewall, waarbij de machine achter een proxyserver zit of andere netwerkfouten.

### <a name="resolution"></a>Oplossing

Controleer of uw machine toegang heeft tot de juiste eindpunten voor DSC en probeer het opnieuw. Zie [netwerkplanning](../automation-dsc-overview.md#network-planning) voor een lijst met benodigde poorten en adressen

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenario: Statusrapporten retourreactiecode ongeautoriseerd

### <a name="issue"></a>Probleem

Wanneer u een knooppunt registreert bij Azure Automation State Configuration, ontvangt u een van de volgende foutberichten:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Oorzaak

Dit probleem wordt veroorzaakt door een slecht of verlopen certificaat. Zie [Certificaat verlopen en opnieuw registreren](../automation-dsc-onboarding.md#re-registering-a-node).

Dit probleem kan ook worden veroorzaakt door een proxyconfiguratie die geen toegang biedt tot ***azure-automation.net**. Zie [Configuratie van privénetwerken](../automation-dsc-overview.md#network-planning)voor meer informatie. 

### <a name="resolution"></a>Oplossing

Gebruik de onderstaande stappen om het falende DSC-knooppunt opnieuw te registreren.

Stap 1 - Meld het knooppunt uit.

1. Navigeer in Azure-portal naar -> **Domotica-accounts** -> (uw automatiseringsaccount) -> **State-configuratie (DSC).** **Home**
2. Selecteer **Knooppunten**en klik op het knooppunt met problemen.
3. Klik **op Aanmelden om** het knooppunt uit te schrijven.

Stap 2 - Verwijder de DSC-extensie van het knooppunt.

1. Navigeer in Azure-portal naar **Home** -> **Virtual Machine** -> (mislukt knooppunt) **>-extensies**.
2. Selecteer **Microsoft.Powershell.DSC**, de PowerShell DSC-extensie.
3. Klik **op Verwijderen** om de extensie te verwijderen.

Stap 3 - Verwijder alle slechte of verlopen certificaten van het knooppunt.

Voer op het falende knooppunt van een verhoogde PowerShell-prompt de volgende opdrachten uit:

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

Stap 4 - Registreer het falende knooppunt opnieuw.

1. Navigeer in de Azure-portal naar -> **Domotica-accounts** -> (uw automatiseringsaccount) > **Staatsconfiguratie (DSC)** **Home**
2. Selecteer **Knooppunten**.
3. Klik op **Add**.
4. Selecteer het falende knooppunt.
5. Klik **op Verbinden** en selecteer de gewenste opties.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenario: Knooppunt heeft de status mislukt met een fout 'Niet gevonden'

### <a name="issue"></a>Probleem

Het knooppunt heeft een rapport met de status Mislukt en bevat de fout:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Oorzaak

Deze fout treedt meestal op wanneer het knooppunt is toegewezen aan een configuratienaam, bijvoorbeeld **ABC,** in plaats van een naam van een knooppuntconfiguratie (MOF-bestand), bijvoorbeeld **ABC. WebServer**.

### <a name="resolution"></a>Oplossing

* Zorg ervoor dat u het knooppunt toekent met de naam van de nodeconfiguratie en niet met de configuratienaam.
* U een knooppuntconfiguratie toewijzen aan een knooppunt via azure-portal of met een PowerShell-cmdlet.

  * Navigeer in de Azure-portal naar -> **Domotica-accounts** -> (uw automatiseringsaccount) - > **Staatsconfiguratie (DSC)** en selecteer vervolgens een knooppunt en klik op **Knooppuntconfiguratie toewijzen**. **Home**
  * Gebruik de [cmdlet Set-AzAutomationDscNode.](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0)

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scenario: No no node configurations (MOF-bestanden) werden geproduceerd toen een configuratie werd gecompileerd

### <a name="issue"></a>Probleem

Uw DSC-compilatietaak wordt onderbroken met de fout:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Oorzaak

Wanneer de expressie `Node` na het trefwoord in `$null`de DSC-configuratie evalueert tot , worden geen knooppuntconfiguraties geproduceerd.

### <a name="resolution"></a>Oplossing

Gebruik een van de volgende oplossingen om het probleem op te lossen:

* Zorg ervoor dat de `Node` expressie naast het zoekwoord in de configuratiedefinitie niet wordt geëvalueerd op Null.
* Als u [ConfigurationData](../automation-dsc-compile.md) doorgeeft bij het samenstellen van de configuratie, moet u ervoor zorgen dat u de waarden doorgeeft die de configuratie verwacht van de configuratiegegevens.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scenario: Het rapport van het DSC-knooppunt wordt vastgelopen in de status In uitvoering

### <a name="issue"></a>Probleem

De dsc-agentuitgangen:

```error
No instance found with given property values
```

### <a name="cause"></a>Oorzaak

U hebt uw WMF-versie (Windows Management Framework) geüpgraded en windows managementinstrumentatie (WMI) beschadigd.

### <a name="resolution"></a>Oplossing

Volg instructies in [DSC bekende problemen en beperkingen](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenario: kan geen referentie gebruiken in een DSC-configuratie

### <a name="issue"></a>Probleem

Uw DSC-compilatietaak is opgeschort met de fout:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Oorzaak

U hebt een referentie in een configuratie gebruikt, maar hebt niet de juiste informatie `ConfigurationData` verstrekt om voor elke knooppuntconfiguratie in te stellen. `PSDscAllowPlainTextPassword`

### <a name="resolution"></a>Oplossing

Zorg ervoor dat u `ConfigurationData` de `PSDscAllowPlainTextPassword` juiste om in te stellen op true voor elke knooppunt configuratie die wordt vermeld in de configuratie. Zie [DSC-configuraties compileren in Azure Automation State Configuration](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>Scenario: fout "Fout verwerking extensie" bij onboarding van DSC extensie

### <a name="issue"></a>Probleem

Bij onboarding met een DSC-extensie treedt een fout op met de fout:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Oorzaak

Deze fout treedt meestal op wanneer aan het knooppunt een knooppuntconfiguratienaam wordt toegewezen die niet in de service bestaat.

### <a name="resolution"></a>Oplossing

* Zorg ervoor dat u het knooppunt toekent met een naam die precies overeenkomt met de naam in de service.
* U ervoor kiezen om de naam van de knooppuntconfiguratie niet op te nemen, wat resulteert in het inwerken van het knooppunt, maar niet het toewijzen van een knooppuntconfiguratie.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>Scenario: fout "Er zijn een of meer fouten opgetreden" bij het registreren van een knooppunt met PowerShell

### <a name="issue"></a>Probleem

Wanneer u een knooppunt registreert met [Register-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) of [Register-AzureRMAutomationDSCNode,](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)ontvangt u de volgende fout:

```error
One or more errors occurred.
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een knooppunt probeert te registreren in een afzonderlijk abonnement dan het door het automatiseringsaccount gebruikte abonnement.

### <a name="resolution"></a>Oplossing

Behandel het knooppunt voor abonnementen alsof het is gedefinieerd voor een afzonderlijke cloud of on-premises. Registreer het knooppunt met een van deze onboarding-opties:

* Windows - [Fysieke/virtuele Windows-machines on-premises of in een andere cloud dan Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines).
* Linux - [Fysieke/virtuele Linux-machines on-premises of in een andere cloud dan Azure.](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines)

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Scenario: Foutbericht - 'Inrichten is mislukt'

### <a name="issue"></a>Probleem

Bij het registreren van een knooppunt ziet u de fout:

```error
Provisioning has failed
```

### <a name="cause"></a>Oorzaak

Dit bericht treedt op wanneer er een probleem is met de verbinding tussen het knooppunt en Azure.

### <a name="resolution"></a>Oplossing

Bepaal of uw knooppunt zich in een VPN (virtual private network) bevindt of dat er andere problemen zijn die verbinding maken met Azure. Zie [Fouten oplossen bij onboarding-oplossingen](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scenario: Fout met een algemene fout bij het toepassen van een configuratie in Linux

### <a name="issue"></a>Probleem

Bij het toepassen van een configuratie in Linux treedt een fout op met de fout:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Oorzaak

Als de **/tmp-locatie** is ingesteld op `noexec`, past de huidige versie van DSC geen configuraties toe.

### <a name="resolution"></a>Oplossing

Verwijder `noexec` de optie van de **/tmp-locatie.**

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenario: Knooppuntconfiguratienamen die elkaar overlappen, kunnen leiden tot een slechte release

### <a name="issue"></a>Probleem

Wanneer u één configuratiescript gebruikt om meerdere knooppuntconfiguraties te genereren en sommige knooppuntconfiguratienamen subsets van andere namen zijn, kan de compilatieservice uiteindelijk de verkeerde configuratie toewijzen. Dit probleem treedt alleen op wanneer één script wordt gebruikt om configuraties te genereren met configuratiegegevens per knooppunt en alleen wanneer de naamoverlap optreedt aan het begin van de tekenreeks. Een voorbeeld is een enkel configuratiescript dat wordt gebruikt om configuraties te genereren op basis van knooppuntgegevens die als hashtabel worden doorgegeven met behulp van cmdlets, en de knooppuntgegevens bevatten servers met de naam **server** en **1server.**

### <a name="cause"></a>Oorzaak

Dit is een bekend probleem met de compilatieservice.

### <a name="resolution"></a>Oplossing

De beste tijdelijke oplossing is om lokaal of in een CI/CD-pijplijn samen te stellen en de Knooppuntconfiguratie MOF-bestanden rechtstreeks naar de service te uploaden. Als compilatie in de service een vereiste is, is de volgende beste tijdelijke oplossing het splitsen van de compilatietaken, zodat er geen overlap is in namen.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenario: Fout in time-out gateway op Upload van DSC-configuratie

#### <a name="issue"></a>Probleem

U ontvangt `GatewayTimeout` een foutmelding bij het uploaden van een DSC-configuratie. 

### <a name="cause"></a>Oorzaak

DSC-configuraties die lang duren om te compileren, kunnen deze fout veroorzaken.

### <a name="resolution"></a>Oplossing

U uw DSC-configuraties sneller laten ontlopen door de `ModuleName` parameter voor [import-DSCResource-aanroepen](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1) expliciet op te nemen.

## <a name="next-steps"></a>Volgende stappen

Als je het probleem hierboven niet ziet of het probleem niet oplossen, probeer je een van de volgende kanalen voor extra ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport)verbinding met het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.