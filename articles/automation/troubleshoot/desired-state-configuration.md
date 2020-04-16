---
title: Problemen met azure automation desired state configuration (DSC) oplossen
description: In dit artikel vindt u informatie over het oplossen van problemen met de gewenste statusconfiguratie (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: aeffa0bb736f03403bf483b22775ef468bbcb2bd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405470"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Problemen met Azure Automation Desired State Configuration (DSC) oplossen

In dit artikel vindt u informatie over het oplossen van problemen met de gewenste statusconfiguratie (DSC).

## <a name="diagnosing-an-issue"></a>Een probleem diagnosticeren

Wanneer u fouten hebt tijdens het compileren of implementeren van configuraties in Azure State Configuration, volgen hier een paar stappen om u te helpen het probleem te diagnosticeren.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Zorg ervoor dat uw configuratie met succes wordt gecompileerd op de lokale machine

Azure State Configuration is gebouwd op PowerShell DSC. U vindt de documentatie voor de DSC-taal en -syntaxis in de [PowerShell DSC-documenten](https://docs.microsoft.com/powershell/scripting/overview).

Door uw DSC-configuratie op uw lokale machine samen te stellen, u veelvoorkomende fouten ontdekken en oplossen, zoals:

   - Ontbrekende modules
   - Syntaxisfouten
   - Logische fouten

### <a name="2-view-dsc-logs-on-your-node"></a>2. DSC-logboeken bekijken op uw knooppunt

Als uw configuratie met succes wordt gecompileerd, maar mislukt wanneer deze wordt toegepast op een knooppunt, u gedetailleerde informatie vinden in de DSC-logboeken. Zie [Waar zijn de DSC-gebeurtenislogboeken](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)voor informatie over waar u deze logboeken vinden.

De [xDscDiagnostics-module](https://github.com/PowerShell/xDscDiagnostics) kan u helpen bij het ontwijsmaken van gedetailleerde informatie uit de DSC-logboeken. Als u contact opneemt met ondersteuning, moeten deze logboeken uw probleem diagnosticeren.

U de xDscDiagnostics-module op uw lokale machine installeren met behulp van de instructies in [De stabiele versiemodule installeren.](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)

Als u de xDscDiagnostics-module op uw Azure-machine wilt installeren, gebruikt u [Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). U de **opdrachtoptie Uitvoeren** ook gebruiken vanuit de portal door de stappen te volgen in [PowerShell-scripts uitvoeren in uw Windows VM met Opdracht uitvoeren.](../../virtual-machines/windows/run-command.md)

Zie [XDscDiagnostics gebruiken om DSC-logboeken te analyseren voor](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)informatie over het gebruik van xDscDiagnostics. Zie ook [xDscDiagnostics Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Zorg ervoor dat knooppunten en de werkruimte Automatisering modules nodig hebben

DSC is afhankelijk van modules die op het knooppunt zijn geïnstalleerd. Wanneer u Azure Automation State Configuration gebruikt, importeert u alle vereiste modules in uw Automatiseringsaccount met behulp van de stappen die worden vermeld in [Importmodules.](../shared-resources/modules.md#importing-modules) Configuraties kunnen ook afhankelijk zijn van specifieke versies van modules. Zie [Modules oplossen voor](shared-resources.md#modules)meer informatie .

## <a name="common-errors-when-working-with-dsc"></a>Veelvoorkomende fouten bij het werken met DSC

### <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenario: Een configuratie met speciale tekens kan niet worden verwijderd uit de portal

#### <a name="issue"></a>Probleem

Wanneer u een DSC-configuratie uit de portal probeert te verwijderen, ziet u de volgende fout:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Oorzaak

Deze fout is een tijdelijk probleem dat is gepland om te worden opgelost.

#### <a name="resolution"></a>Oplossing

* Gebruik de Az Cmdlet "Remove-AzAutomationDscConfiguration" om de configuratie te verwijderen.
* De documentatie voor deze cmdlet is nog niet bijgewerkt.  Tot die tijd raadpleegt u de documentatie voor de AzureRM-module.
  * [Remove-AzurermAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenario: Kan Dsc-agent niet registreren

#### <a name="issue"></a>Probleem

Wanneer u `Set-DscLocalConfigurationManager` probeert uit te voeren of een andere DSC-cmdlet ontvangt u de fout:

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

#### <a name="cause"></a>Oorzaak

Deze fout wordt normaal gesproken veroorzaakt door een firewall, waarbij de machine achter een proxyserver zit of andere netwerkfouten.

#### <a name="resolution"></a>Oplossing

Controleer of uw machine toegang heeft tot de juiste eindpunten voor Azure Automation DSC en probeer het opnieuw. Zie [netwerkplanning](../automation-dsc-overview.md#network-planning) voor een lijst met benodigde poorten en adressen

### <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenario: Statusrapporten retourreactiecode 'Ongeautoriseerd'

#### <a name="issue"></a>Probleem

Wanneer u een knooppunt registreert bij De Configuratie van de staat (DSC) ontvangt u een van de volgende foutmeldingen:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Oorzaak

Dit probleem wordt veroorzaakt door een slecht of verlopen certificaat.  Zie [Certificaat verlopen en opnieuw registreren](../automation-dsc-onboarding.md#re-register-a-node)voor meer informatie.

Dit probleem kan ook worden veroorzaakt door een proxyconfiguratie die geen toegang biedt tot ***.azure-automation.net**. Zie [Configuratie van privénetwerken](../automation-dsc-overview.md#network-planning)voor meer informatie. 

### <a name="resolution"></a>Oplossing

Volg de onderstaande stappen om het falende DSC-knooppunt opnieuw te registreren.

Registreer eerst het knooppunt met de volgende stappen.

1. Vanuit de Azure-portal onder -> **Domotica-accounts**-> {Uw automatiseringsaccount} -> **Staatsconfiguratie (DSC)** **Home**
2. Klik op 'Knooppunten' en klik op het knooppunt met problemen.
3. Klik op 'Registreren uitschrijven' om het knooppunt niet te registreren.

Ten tweede, verwijder de DSC-extensie van het knooppunt.

1. Vanuit de Azure-portal onder **Home** -> **Virtual Machine** -> {Failing node} -> **Extensies**
2. Klik op "Microsoft.Powershell.DSC".
3. Klik op 'Verwijderen', om de PowerShell DSC-extensie te verwijderen.

Ten derde, verwijder alle slechte of verlopen certificaten van het knooppunt.

Voer op het falende knooppunt van een verhoogde Powershell Prompt het volgende uit:

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

Registreer ten slotte het falende knooppunt opnieuw met behulp van de volgende stappen.

1. Vanuit de Azure-portal onder -> **Domotica-accounts** -> {Uw automatiseringsaccount} -> **Staatsconfiguratie (DSC)** **Home**
2. Klik op 'Knooppunten'.
3. Klik op de knop Toevoegen.
4. Selecteer het falende knooppunt.
5. Klik op 'Verbinding maken' en selecteer de gewenste opties.

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenario: Knooppunt heeft de status mislukt met een fout 'Niet gevonden'

#### <a name="issue"></a>Probleem

Het knooppunt heeft een rapport met de status **Mislukt** en bevat de fout:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt meestal op wanneer het knooppunt is toegewezen aan een configuratienaam (bijvoorbeeld ABC) in plaats van een knooppuntconfiguratienaam (bijvoorbeeld ABC. WebServer).

#### <a name="resolution"></a>Oplossing

* Zorg ervoor dat u het knooppunt toekent met de naam 'knooppuntconfiguratie' en niet met de 'configuratienaam'.
* U een knooppuntconfiguratie toewijzen aan een knooppunt via azure-portal of met een PowerShell-cmdlet.

  * Als u een knooppuntconfiguratie wilt toewijzen aan een knooppunt met Azure-portal, opent u de pagina **DSC-knooppunten,** selecteert u een knooppunt en klikt u op **knooppuntconfiguratieknop toewijzen.**
  * Als u een knooppuntconfiguratie wilt toewijzen aan een knooppunt met PowerShell-cmdlet, gebruikt u de cmdlet **Set-AzureRmAutomationDscNode**

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a><a name="no-mof-files"></a>Scenario: No no node configurations (MOF-bestanden) zijn geproduceerd wanneer een configuratie wordt gecompileerd

#### <a name="issue"></a>Probleem

Uw DSC-compilatietaak wordt onderbroken met de fout:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Oorzaak

Wanneer de expressie na het trefwoord **Knooppunt** in `$null`de DSC-configuratie evalueert tot , worden knooppuntconfiguraties geproduceerd.

#### <a name="resolution"></a>Oplossing

Een van de volgende oplossingen lost het probleem op:

* Zorg ervoor dat de expressie naast het trefwoord **Knooppunt** in de configuratiedefinitie niet wordt geëvalueerd om $null.
* Als u ConfigurationData doorgeeft bij het samenstellen van de configuratie, moet u ervoor zorgen dat u de verwachte waarden doorgeeft die de configuratie van [ConfigurationData](../automation-dsc-compile.md)vereist.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a><a name="dsc-in-progress"></a>Scenario: Het dsc-knooppuntrapport blijft 'in progress'-status steken

#### <a name="issue"></a>Probleem

De dsc-agentuitgangen:

```error
No instance found with given property values
```

#### <a name="cause"></a>Oorzaak

U hebt uw WMF-versie geüpgraded en WMI beschadigd.

#### <a name="resolution"></a>Oplossing

Volg de instructies in het [dsc-artikel](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) om het probleem op te lossen.

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenario: kan geen referentie gebruiken in een DSC-configuratie

#### <a name="issue"></a>Probleem

Uw DSC-compilatietaak is opgeschort met de fout:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Oorzaak

U hebt een referentie in een configuratie gebruikt, maar hebt geen juiste **configurationdata verstrekt** om **PSDscAllowPlainTextPassword** in te stellen op true voor elke knooppuntconfiguratie.

#### <a name="resolution"></a>Oplossing

* Zorg ervoor dat u de juiste **ConfigurationData** invoert om **PSDscAllowPlainTextPassword** in te stellen op true voor elke knooppuntconfiguratie die in de configuratie wordt vermeld. Zie [DSC-configuraties compileren in Azure Automation State Configuration](../automation-dsc-compile.md)voor meer informatie.

### <a name="scenario-onboarding-from-dsc-extension-failure-processing-extension-error"></a><a name="failure-processing-extension"></a>Scenario: Onboarding van dsc-extensie, fout 'Foutverwerking extensie'

#### <a name="issue"></a>Probleem

Bij onboarding met DSC-extensie treedt een fout op met de fout:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt meestal op wanneer aan het knooppunt een knooppuntconfiguratienaam wordt toegewezen die niet in de service bestaat.

#### <a name="resolution"></a>Oplossing

* Zorg ervoor dat u het knooppunt toekent met een knooppuntconfiguratienaam die precies overeenkomt met de naam in de service.
* U ervoor kiezen om de naam van de nodeconfiguratie niet op te nemen, wat resulteert in het inwerken van het knooppunt, maar niet het toewijzen van een knooppuntconfiguratie

### <a name="scenario-registering-a-node-with-powershell-returns-the-error-one-or-more-errors-occurred"></a><a name="cross-subscription"></a>Scenario: Het registreren van een knooppunt bij PowerShell retourneert de fout "Een of meer fouten zijn opgetreden"

#### <a name="issue"></a>Probleem

Bij het registreren `Register-AzAutomationDSCNode` van `Register-AzureRMAutomationDSCNode`een knooppunt met of , ontvangt u de volgende fout.

```error
One or more errors occurred.
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een knooppunt probeert te registreren dat in een afzonderlijk abonnement woont dan het automatiseringsaccount.

#### <a name="resolution"></a>Oplossing

Behandel het knooppunt met abonnementen alsof het in een aparte cloud of on-premise leeft.

Volg de onderstaande stappen om het knooppunt te registreren.

* Windows - [Fysieke/virtuele Windows-machines on-premises of in een andere cloud dan Azure/AWS](../automation-dsc-onboarding.md#onboard-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure).
* Linux - [Fysieke/virtuele Linux-machines on-premises of in een andere cloud dan Azure.](../automation-dsc-onboarding.md#onboard-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure)

### <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Scenario: Foutbericht - 'Inrichten is mislukt'

#### <a name="issue"></a>Probleem

Bij het registreren van een knooppunt ziet u de fout:

```error
Provisioning has failed
```

#### <a name="cause"></a>Oorzaak

Dit bericht treedt op wanneer er een verbindingsprobleem is tussen het knooppunt en Azure.

#### <a name="resolution"></a>Oplossing

Bepaal of uw knooppunt zich in een virtueel privénetwerk bevindt of dat er andere problemen zijn die verbinding maken met Azure.

Zie [Fouten oplossen bij onboarding-oplossingen](onboarding.md)voor meer informatie.

### <a name="scenario-applying-a-configuration-in-linux-a-failure-occurs-with-a-general-error"></a><a name="failure-linux-temp-noexec"></a>Scenario: Het toepassen van een configuratie in Linux, een fout optreedt met een algemene fout

#### <a name="issue"></a>Probleem

Bij het toepassen van een configuratie in Linux treedt een fout op met de fout:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Oorzaak

Klanten hebben vastgesteld dat `/tmp` als de `noexec`locatie is ingesteld op , de huidige versie van DSC geen configuraties zal toepassen.

#### <a name="resolution"></a>Oplossing

* Verwijder `noexec` de optie `/tmp` van de locatie.

### <a name="scenario-node-configuration-names-that-overlap-could-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenario: Knooppuntconfiguratienamen die elkaar overlappen, kunnen leiden tot een slechte release

#### <a name="issue"></a>Probleem

Als één configuratiescript wordt gebruikt om meerdere knooppuntconfiguraties te genereren en sommige nodeconfiguraties een naam hebben die een subset van anderen is, kan een probleem in de compilatieservice resulteren in het toewijzen van de verkeerde configuratie.  Dit gebeurt alleen wanneer één script wordt gebruikt om configuraties te genereren met configuratiegegevens per knooppunt, en alleen wanneer de naamoverlap plaatsvindt aan het begin van de tekenreeks.

Als een enkel configuratiescript wordt gebruikt om configuraties te genereren op basis van knooppuntgegevens die als hashtabel worden doorgegeven met behulp van cmdlets, en de knooppuntgegevens een server met de naam "server" en "1server".

#### <a name="cause"></a>Oorzaak

Bekend probleem met de compilatieservice.

#### <a name="resolution"></a>Oplossing

De beste oplossing zou zijn om lokaal of in een CI / CD-pijplijn samen te stellen en de MOF-bestanden rechtstreeks naar de service te uploaden.  Als compilatie in de service een vereiste is, is de volgende beste oplossing het splitsen van de compilatietaken, zodat er geen overlap is in namen.

### <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenario: Fout in time-out gateway op Upload van DSC-configuratie

#### <a name="issue"></a>Probleem

U ontvangt `GatewayTimeout` een foutmelding bij het uploaden van een DSC-configuratie. 

#### <a name="cause"></a>Oorzaak

DSC-configuraties die lang duren om te compileren, kunnen deze fout veroorzaken.

#### <a name="resolution"></a>Oplossing

U uw DSC-configuraties sneller laten ontlopen door de `ModuleName` parameter voor oproepen `Import-DscResource` expliciet op te nemen. Zie [Import-DSCResource gebruiken](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met – het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, u een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
