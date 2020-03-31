---
title: Fouten oplossen met gedeelde azure automation-bronnen
description: Meer informatie over het oplossen en oplossen van problemen met azure automation shared resources die runbooks ondersteunen.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278322"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Fouten oplossen met gedeelde bronnen

In dit artikel worden oplossingen besproken om problemen op te lossen die u tegenkomen bij het gebruik van de gedeelde resources in Azure Automation.

## <a name="modules"></a>Modules

### <a name="scenario-a-module-is-stuck-importing"></a><a name="module-stuck-importing"></a>Scenario: Een module zit vast aan importeren

#### <a name="issue"></a>Probleem

Een module zit vast in de **status Importeren** wanneer u uw modules importeert of bijwerkt in Azure-automatisering.

#### <a name="cause"></a>Oorzaak

Het importeren van PowerShell-modules is een complex proces in meerdere stappen. Dit proces introduceert de mogelijkheid van een module niet correct importeren. Als dit probleem optreedt, kan de module die u importeert, in een tijdelijke status worden geplakt. Zie [Een PowerShell-module importeren](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)voor meer informatie over dit proces.

#### <a name="resolution"></a>Oplossing

Als u dit probleem wilt oplossen, moet u de module verwijderen die vastzit in de **status Importeren** met de cmdlet [Remove-AzureRmAutomationModule.](/powershell/module/azurerm.automation/remove-azurermautomationmodule) U vervolgens opnieuw proberen de module te importeren.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-importing-after-trying-to-update-them"></a><a name="update-azure-modules-importing"></a>Scenario: AzureRM-modules blijven steken op importeren nadat ze zijn bijgewerkt

#### <a name="issue"></a>Probleem

Een banner met het volgende bericht blijft in uw account staan nadat u uw AzureRM-modules hebt bijgewerkt:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Oorzaak

Er is een bekend probleem met het bijwerken van de AzureRM-modules in een automatiseringsaccount dat zich in een resourcegroep bevindt met een numerieke naam die begint met 0.

#### <a name="resolution"></a>Oplossing

Als u uw Azure-modules wilt bijwerken in uw automatiseringsaccount, moet deze zich in een brongroep bevinden met een alfanumerieke naam. Resourcegroepen met numerieke namen die met 0 beginnen, kunnen azurerm-modules op dit moment niet bijwerken.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenario: Module kan niet importeren of cmdlets kunnen niet worden uitgevoerd na het importeren

#### <a name="issue"></a>Probleem

Een module voert geen invoer of invoer met succes uit, maar er worden geen cmdlets geëxtraheerd.

#### <a name="cause"></a>Oorzaak

Enkele veelvoorkomende redenen waarom een module mogelijk niet wordt geïmporteerd naar Azure Automation zijn:

* De structuur komt niet overeen met de structuur waarin Automation deze nodig heeft.
* De module is afhankelijk van een andere module die niet is geïmplementeerd in uw Automatiseringsaccount.
* De module mist zijn afhankelijkheden in de map.
* De `New-AzureRmAutomationModule` cmdlet wordt gebruikt om de module te uploaden en u hebt nog niet het volledige opslagpad gegeven of de module niet geladen met behulp van een openbaar toegankelijke URL.

#### <a name="resolution"></a>Oplossing

Een van de volgende oplossingen lost het probleem op:

* Zorg ervoor dat de module de volgende **->** indeling volgt: **->** ModuleName.Zip ModuleName of Versienummer (ModuleName.psm1, ModuleName.psd1)
* Open het PSD1-bestand en kijk of de module afhankelijkheden heeft. Upload deze modules dan naar het automatiseringsaccount.
* Zorg ervoor dat er naar de verwijzing .dlls in de modulemap wordt verwezen.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenario: Update-AzureModule.ps1 wordt onderbroken bij het bijwerken van modules

#### <a name="issue"></a>Probleem

Wanneer u de [runbook Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) gebruikt om uw Azure-modules bij te werken, wordt de module-update van het updateproces opgeschort.

#### <a name="cause"></a>Oorzaak

De standaardinstelling om te bepalen hoeveel modules tegelijkertijd worden `Update-AzureModule.ps1` bijgewerkt, is 10 wanneer u het script gebruikt. Het updateproces is gevoelig voor fouten wanneer te veel modules tegelijkertijd worden bijgewerkt.

#### <a name="resolution"></a>Oplossing

Het komt niet vaak voor dat alle AzureRM-modules vereist zijn in hetzelfde Automatiseringsaccount. Het wordt aanbevolen om alleen de AzureRM-modules te importeren die u nodig hebt.

> [!NOTE]
> Vermijd het importeren van de **AzureRM-module.** Als u de **AzureRM-modules** importeert, worden alle **\* AzureRM-modules** geïmporteerd, dit wordt niet aanbevolen.

Als het updateproces wordt onderbroken, `SimultaneousModuleImportJobCount` moet u `Update-AzureModules.ps1` de parameter aan het script toevoegen en een lagere waarde opgeven dan de standaardwaarde van 10. Het wordt aanbevolen als u deze logica implementeert, om te beginnen met een waarde van 3 of 5. `SimultaneousModuleImportJobCount`is een parameter `Update-AutomationAzureModulesForAccount` van de systeemrunbook die wordt gebruikt om Azure-modules bij te werken. Deze wijziging maakt het proces langer duren, maar heeft een betere kans op voltooiing. In het volgende voorbeeld wordt de parameter weergegeven en waar u deze in het runbook moet plaatsen:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Uitvoeren als-accounts

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Scenario: Je een Run As-account niet maken of bijwerken

#### <a name="issue"></a>Probleem

Wanneer u een Run As-account probeert aan te maken of bij te werken, ontvangt u een fout die vergelijkbaar is met het volgende foutbericht:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Oorzaak

U hebt niet de machtigingen die u nodig hebt om het Run As-account te maken of bij te werken of de resource is vergrendeld op het niveau van de resourcegroep.

#### <a name="resolution"></a>Oplossing

Als u een Run As-account wilt maken of bijwerken, moet u over de juiste machtigingen beschikken voor de verschillende bronnen die worden gebruikt door het Run As-account. Zie [Uitvoeren als accountmachtigingen](../manage-runas-account.md#permissions)voor meer informatie over de machtigingen die nodig zijn om een Run As-account te maken of bij te werken.

Als het probleem is vanwege een vergrendeling, controleer dan of het slot in orde is om het te verwijderen. Navigeer vervolgens naar de bron die is vergrendeld, klik met de rechtermuisknop op het slot en kies **Verwijderen** om het slot te verwijderen.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenario: U ontvangt de foutmelding 'Kan geen toegangspunt met de naam 'GetPerAdapterInfo' vinden in DLL 'iplpapi.dll'" bij het uitvoeren van een runbook.

#### <a name="issue"></a>Probleem

Bij het uitvoeren van een runbook ontvangt u de volgende uitzondering:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Oorzaak

Deze fout wordt waarschijnlijk veroorzaakt door een onjuist geconfigureerde [run as-account](../manage-runas-account.md).

#### <a name="resolution"></a>Oplossing

Zorg ervoor dat je [Run As-account](../manage-runas-account.md) goed is geconfigureerd. Zodra deze correct is geconfigureerd, moet u ervoor zorgen dat u de juiste code in uw runbook hebt om te verifiëren met Azure. In het volgende voorbeeld wordt een codefragment weergegeven dat moet worden geverifieerd aan Azure in een runbook met een Run As-account.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts op [Azure-Forums](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met – het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, u een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
