---
title: Problemen met Azure Automation desired state Configuration (DSC) oplossen
description: In dit artikel vindt u informatie over het oplossen van problemen met desired state Configuration (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dcd0371d275c3a46fe9bf07c96516a2d0820abb7
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430530"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Problemen oplossen met Azure Automation desired state Configuration (DSC)

In dit artikel vindt u informatie over het oplossen van problemen met desired state Configuration (DSC).

## <a name="diagnosing-an-issue"></a>Een probleem vaststellen

Wanneer er fouten zijn opgetreden tijdens het compileren of implementeren van configuraties in de Azure-status configuratie, zijn hier enkele stappen beschikbaar om u te helpen bij het vaststellen van het probleem.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Controleer of de configuratie is gecompileerd op de lokale computer

De Azure-status configuratie is gebaseerd op Power shell DSC. U kunt de documentatie voor de DSC-taal en syntaxis vinden in de [Power shell DSC-documenten](https://docs.microsoft.com/powershell/scripting/overview).

Door de DSC-configuratie op uw lokale machine te compileren, kunt u veelvoorkomende fouten detecteren en oplossen, zoals:

   - Ontbrekende modules
   - Syntaxis fouten
   - Logische fouten

### <a name="2-view-dsc-logs-on-your-node"></a>2. DSC-logboeken weer geven op uw knoop punt

Als uw configuratie is gecompileerd, maar mislukt als deze wordt toegepast op een knoop punt, kunt u gedetailleerde informatie vinden in de DSC-Logboeken. Zie voor informatie over waar u deze logboeken kunt vinden, [waar de DSC-gebeurtenis logboeken zijn](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

De [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) -module kan u helpen bij het parseren van gedetailleerde informatie uit de DSC-Logboeken. Als u contact opneemt met ondersteuning, moeten deze logboeken uw probleem vaststellen.

U kunt de xDscDiagnostics-module op uw lokale machine installeren met behulp van de instructies in [de module stabiele versie installeren](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Als u de xDscDiagnostics-module op uw Azure-computer wilt installeren, gebruikt u [invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). U kunt ook de **opdracht optie uitvoeren** vanuit de portal gebruiken door de stappen uit te voeren die zijn gevonden in [Power shell-scripts uitvoeren in uw Windows-VM met de opdracht uitvoeren](../../virtual-machines/windows/run-command.md).

Zie voor meer informatie over het gebruik van xDscDiagnostics [DSC-logboeken analyseren met behulp van xDscDiagnostics](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Zie ook [xDscDiagnostics-cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Controleer of de knoop punten en de Automation-werk ruimte de vereiste modules hebben

DSC is afhankelijk van de modules die op het knoop punt zijn geïnstalleerd. Wanneer u Azure Automation status configuratie gebruikt, importeert u de benodigde modules in uw Automation-account met behulp van de stappen die worden beschreven in [modules importeren](../shared-resources/modules.md#import-modules). Configuraties kunnen ook afhankelijk zijn van specifieke versies van modules. Zie [problemen met modules oplossen](shared-resources.md#modules)voor meer informatie.

## <a name="common-errors-when-working-with-dsc"></a>Veelvoorkomende fouten bij het werken met DSC

### <a name="unsupported-characters"></a>Scenario: een configuratie met speciale tekens kan niet worden verwijderd uit de portal

#### <a name="issue"></a>Probleem

Wanneer u probeert een DSC-configuratie te verwijderen uit de portal, ziet u de volgende fout:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Oorzaak

Deze fout is een tijdelijk probleem dat is gepland om te worden opgelost.

#### <a name="resolution"></a>Oplossing

* Gebruik de AZ-cmdlet Remove-AzAutomationDscConfiguration om de configuratie te verwijderen.
* De documentatie voor deze cmdlet is nog niet bijgewerkt.  Tot slot raadpleegt u de documentatie voor de module AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Scenario: de DSC-agent kan niet worden geregistreerd

#### <a name="issue"></a>Probleem

Wanneer u probeert `Set-DscLocalConfigurationManager` of een andere DSC-cmdlet uit te voeren, wordt de volgende fout weer gegeven:

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

Deze fout wordt meestal veroorzaakt door een firewall, de computer zich achter een proxy server bevindt of andere netwerk fouten.

#### <a name="resolution"></a>Oplossing

Controleer of de computer toegang heeft tot de juiste eind punten voor Azure Automation DSC en probeer het opnieuw. Zie [netwerk planning](../automation-dsc-overview.md#network-planning) voor een lijst met poorten en adressen.

### <a name="a-nameunauthorizedascenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>scenario: status rapporten retour antwoord code "onbevoegd"

#### <a name="issue"></a>Probleem

Bij het registreren van een knoop punt met de status configuratie (DSC) wordt een van de volgende fout berichten weer gegeven:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Oorzaak

Dit probleem wordt veroorzaakt door een onjuist of verlopen certificaat.  Zie [verval datum en registratie van certificaten](../automation-dsc-onboarding.md#certificate-expiration-and-re-registration)voor meer informatie.

### <a name="resolution"></a>Oplossing

Volg de onderstaande stappen om het failed DSC-knoop punt opnieuw te registreren.

Verwijder eerst de registratie van het knoop punt met behulp van de volgende stappen.

1. Vanuit de Azure Portal, onder **Home** -> **Automation-accounts**: > {Your Automation-account}-> **State Configuration (DSC)**
2. Klik op knoop punten en klik op het knoop punt dat problemen ondervindt.
3. Klik op verwijderen om de registratie van het knoop punt ongedaan te maken.

Ten tweede verwijdert u de DSC-uitbrei ding van het knoop punt.

1. Vanuit de Azure Portal onder **Home** -> **virtuele machine** -> {knoop punt mislukt}-> **uitbreidingen**
2. Klik op micro soft. Power shell. DSC.
3. Klik op verwijderen om de Power shell DSC-uitbrei ding te verwijderen.

Ten derde verwijdert u alle beschadigde of verlopen certificaten van het knoop punt.

Voer het volgende uit op het knoop punt waarvoor een Power shell-prompt met verhoogde bevoegdheid is uitgevoerd:

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

Als laatste moet u het knoop punt met de fout opnieuw registreren door de volgende stappen uit te voeren.

1. Vanuit de Azure Portal, onder **Home** -> **Automation-accounts** : > {Your Automation-account}-> **State Configuration (DSC)**
2. Klik op knoop punten.
3. Klik op de knop toevoegen.
4. Selecteer het knoop punt dat niet werkt.
5. Klik op verbinden en selecteer de gewenste opties.

### <a name="failed-not-found"></a>Scenario: het knoop punt heeft de status mislukt met de fout ' niet gevonden '

#### <a name="issue"></a>Probleem

Het knoop punt heeft een rapport met de status **mislukt** en bevat de volgende fout:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt doorgaans op wanneer het knoop punt is toegewezen aan een configuratie naam (bijvoorbeeld ABC) in plaats van een knooppunt configuratie naam (bijvoorbeeld ABC. Webserver).

#### <a name="resolution"></a>Oplossing

* Zorg ervoor dat u het knoop punt met de naam ' knooppunt configuratie ' en niet de ' configuratie naam ' toewijst.
* U kunt een knooppunt configuratie toewijzen aan een knoop punt met behulp van Azure Portal of met een Power shell-cmdlet.

  * Als u een knooppunt configuratie wilt toewijzen aan een knoop punt met behulp van Azure Portal, opent u de pagina **DSC-knoop punten** , selecteert u een knoop punt en klikt u op de knop **knooppunt configuratie toewijzen** .
  * Met de cmdlet **set-AzureRmAutomationDscNode** kunt u een knooppunt configuratie toewijzen aan een knoop punt met behulp van de Power shell-cmdlet.

### <a name="no-mof-files"></a>Scenario: er zijn geen knooppunt configuraties (MOF-bestanden) geproduceerd bij het compileren van een configuratie

#### <a name="issue"></a>Probleem

Uw DSC-compilatie taak wordt onderbroken met de volgende fout:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Oorzaak

Wanneer de expressie die volgt op het sleutel woord van het **knoop punt** in de DSC-configuratie, wordt geëvalueerd als `$null`, worden er geen knooppunt configuraties geproduceerd.

#### <a name="resolution"></a>Oplossing

Het probleem wordt opgelost met een van de volgende oplossingen:

* Zorg ervoor dat de expressie naast het sleutel woord van het **knoop punt** in de configuratie definitie niet evalueert naar $null.
* Als u ConfigurationData door geven tijdens het compileren van de configuratie, moet u ervoor zorgen dat u de verwachte waarden doorgeeft die de configuratie vereist van [ConfigurationData](../automation-dsc-compile.md).

### <a name="dsc-in-progress"></a>Scenario: het DSC-knooppunt rapport wordt vastlopen de status wordt uitgevoerd

#### <a name="issue"></a>Probleem

De DSC-agent uitvoer:

```error
No instance found with given property values
```

#### <a name="cause"></a>Oorzaak

U hebt uw WMF-versie bijgewerkt en hebt WMI beschadigd.

#### <a name="resolution"></a>Oplossing

Volg de instructies in het artikel [bekende problemen en beperkingen van DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) om het probleem op te lossen.

### <a name="issue-using-credential"></a>Scenario: een referentie kan niet worden gebruikt in een DSC-configuratie

#### <a name="issue"></a>Probleem

Uw DSC-compilatie taak is onderbroken met de volgende fout:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Oorzaak

U hebt een referentie in een configuratie gebruikt, maar u hebt niet de juiste **ConfigurationData** om **PSDscAllowPlainTextPassword** in te stellen op True voor elke knooppunt configuratie.

#### <a name="resolution"></a>Oplossing

* Zorg ervoor dat u de juiste **ConfigurationData** geeft om **PSDscAllowPlainTextPassword** in te stellen op True voor elke knooppunt configuratie die wordt vermeld in de configuratie. Zie [DSC-configuraties compileren in azure Automation status configuratie](../automation-dsc-compile.md)voor meer informatie.

### <a name="failure-processing-extension"></a>Scenario: onboarding van de DSC-uitbrei ding ' fout bij het verwerken van de uitbrei ding '

#### <a name="issue"></a>Probleem

Bij onboarding met behulp van de DSC-extensie treedt er een fout op met de volgende fout:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt doorgaans op wanneer aan het knoop punt een knooppunt configuratie naam wordt toegewezen die niet voor komt in de service.

#### <a name="resolution"></a>Oplossing

* Zorg ervoor dat u het knoop punt toewijst met een knooppunt configuratie naam die exact overeenkomt met de naam in de service.
* U kunt ervoor kiezen om de naam van de configuratie van het knoop punt niet op te stellen, wat resulteert in het voorbereiden van het knoop punt, maar geen knooppunt configuratie toewijzen

### <a name="cross-subscription"></a>Scenario: een knoop punt registreren met Power shell retourneert de fout ' er zijn een of meer fouten opgetreden '

#### <a name="issue"></a>Probleem

Wanneer u een knoop punt registreert met behulp van `Register-AzAutomationDSCNode` of `Register-AzureRMAutomationDSCNode`, wordt de volgende fout weer gegeven.

```error
One or more errors occurred.
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een knoop punt wilt registreren dat zich in een ander abonnement bevindt dan het Automation-account.

#### <a name="resolution"></a>Oplossing

Het knoop punt voor meerdere abonnementen behandelen alsof het in een afzonderlijke Cloud of on-premises valt.

Volg de onderstaande stappen om het knoop punt te registreren.

* Windows- [fysieke en virtuele Windows-machines on-premises of in een andere Cloud dan Azure/AWS](../automation-dsc-onboarding.md#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances).
* Linux- [fysieke/virtuele Linux-machines on-premises of in een andere Cloud dan Azure](../automation-dsc-onboarding.md#physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure).

### <a name="agent-has-a-problem"></a>Scenario: fout bericht-' inrichting mislukt '

#### <a name="issue"></a>Probleem

Wanneer u een knoop punt registreert, ziet u de volgende fout:

```error
Provisioning has failed
```

#### <a name="cause"></a>Oorzaak

Dit bericht wordt weer gegeven wanneer er een connectiviteits probleem is tussen het knoop punt en Azure.

#### <a name="resolution"></a>Oplossing

Bepaal of uw knoop punt zich in een particulier virtueel netwerk bevindt of dat er andere problemen zijn die verbinding maken met Azure.

Zie [problemen oplossen bij onboarding van oplossingen](onboarding.md)voor meer informatie.

### <a name="failure-linux-temp-noexec"></a>Scenario: een configuratie Toep assen in Linux, treedt er een fout op met een algemene fout

#### <a name="issue"></a>Probleem

Bij het Toep assen van een configuratie in Linux treedt er een fout op met de volgende fout:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Oorzaak

Klanten hebben vastgesteld dat als de `/tmp` locatie is ingesteld op `noexec`, de huidige versie van DSC geen configuraties kan Toep assen.

#### <a name="resolution"></a>Oplossing

* Verwijder de optie `noexec` van de locatie van `/tmp`.

### <a name="compilation-node-name-overlap"></a>Scenario: namen van knooppunt configuraties die elkaar overlappen kunnen resulteren in slechte release

#### <a name="issue"></a>Probleem

Als één configuratie script wordt gebruikt om meerdere knooppunt configuraties te genereren en sommige knooppunt configuraties een naam hebben die een subset van andere is, kan een probleem in de compilatie service ertoe leiden dat de verkeerde configuratie wordt toegewezen.  Dit gebeurt alleen wanneer u één script gebruikt voor het genereren van configuraties met configuratie gegevens per knoop punt en alleen wanneer de naam overlapt aan het begin van de teken reeks.

Als er bijvoorbeeld één configuratie script wordt gebruikt om configuraties te genereren op basis van knooppunt gegevens die worden door gegeven als hash-tabel met behulp van cmdlets, en de knooppunt gegevens een server met de naam ' server ' en ' 1server ' bevatten.

#### <a name="cause"></a>Oorzaak

Bekend probleem met de compilatie service.

#### <a name="resolution"></a>Oplossing

De beste oplossing is om lokaal of in een CI/CD-pijp lijn te compileren en de MOF-bestanden rechtstreeks te uploaden naar de service.  Als de compilatie in de service een vereiste is, is de volgende beste tijdelijke oplossing om de compilatie taken te splitsen, zodat er geen overlap is in namen.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport), het officiële Microsoft Azure-account voor het verbeteren van de gebruikerservaring door de Azure-community in contact te brengen met de juiste resources: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, kunt u een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
