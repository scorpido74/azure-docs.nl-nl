---
title: Problemen met gedeelde resources in Azure Automation oplossen
description: Meer informatie over het oplossen en oplossen van problemen met gedeelde azure automation-bronnen.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733578"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Problemen met gedeelde resources in Azure Automation oplossen

In dit artikel worden oplossingen besproken voor problemen die u mogelijk tegenkomt bij het gebruik van [gedeelde resources](../automation-intro.md#shared-resources) in Azure Automation.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](../automation-update-azure-modules.md)

## <a name="modules"></a>Modules

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scenario: Een module zit vast tijdens het importeren

#### <a name="issue"></a>Probleem

Een module zit vast in de status Importeren wanneer u uw Azure Automation-modules importeert of bijwerkt.

#### <a name="cause"></a>Oorzaak

Aangezien het importeren van PowerShell-modules een complex proces met meerdere stappen is, kan een module mogelijk niet correct worden geïmporteerd en kan deze in een tijdelijke status worden vastgezet. Zie [Een PowerShell-module importeren](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)voor meer informatie over het importproces.

#### <a name="resolution"></a>Oplossing

Als u dit probleem wilt oplossen, moet u de module verwijderen die vastzit in de status Importeren met de cmdlet [Remove-AzAutomationModule.](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) U vervolgens opnieuw proberen de module te importeren.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scenario: AzureRM-modules blijven steken tijdens import na een updatepoging

#### <a name="issue"></a>Probleem

Een banner met het volgende bericht blijft in uw account staan nadat u uw AzureRM-modules hebt bijgewerkt:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Oorzaak

Er is een bekend probleem met het bijwerken van de AzureRM-modules in een Automatiseringsaccount dat zich in een resourcegroep bevindt met een numerieke naam die begint met 0.

#### <a name="resolution"></a>Oplossing

Als u uw AzureRM-modules wilt bijwerken in uw Automatiseringsaccount, moet het account zich in een brongroep met een alfanumerieke naam bevinden. Resourcegroepen met numerieke namen die met 0 beginnen, kunnen azurerm-modules op dit moment niet bijwerken.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenario: Module kan niet importeren of cmdlets kunnen niet worden uitgevoerd na het importeren

#### <a name="issue"></a>Probleem

Een module voert niet in of importeert niet met succes, maar er worden geen cmdlets geëxtraheerd.

#### <a name="cause"></a>Oorzaak

Enkele veelvoorkomende redenen waarom een module mogelijk niet wordt geïmporteerd naar Azure Automation zijn:

* De structuur komt niet overeen met de structuur die Automation nodig heeft.
* De module is afhankelijk van een andere module die niet is geïmplementeerd in uw Automatiseringsaccount.
* De module mist zijn afhankelijkheden in de map.
* De cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) wordt gebruikt om de module te uploaden en u hebt nog niet het volledige opslagpad gegeven of de module niet geladen met behulp van een openbaar toegankelijke URL.

#### <a name="resolution"></a>Oplossing

Gebruik een van deze oplossingen om het probleem op te lossen.

* Zorg ervoor dat de module de indeling volgt: ModuleName.zip -> ModuleName of Version Number -> (ModuleName.psm1, ModuleName.psd1).
* Open het **PSD1-bestand** en kijk of de module afhankelijkheden heeft. Upload deze modules dan naar het automatiseringsaccount.
* Zorg ervoor dat er naar **alle verwijzende .dll-bestanden** in de modulemap aanwezig zijn.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenario: Update-AzureModule.ps1 wordt onderbroken bij het bijwerken van modules

#### <a name="issue"></a>Probleem

Wanneer u de [runbook Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) gebruikt om uw Azure-modules bij te werken, wordt het proces voor het bijwerken van de module opgeschort.

#### <a name="cause"></a>Oorzaak

De standaardinstelling om te bepalen hoeveel modules tegelijkertijd worden bijgewerkt, is 10 wanneer u **Update-AzureModule.ps1**gebruikt. Het updateproces is gevoelig voor fouten wanneer te veel modules tegelijkertijd worden bijgewerkt.

#### <a name="resolution"></a>Oplossing

Het komt niet vaak voor dat alle AzureRM- of Az-modules vereist zijn in hetzelfde Automatiseringsaccount. Het wordt aanbevolen om alleen de specifieke modules te importeren die u nodig hebt.

> [!NOTE]
> Vermijd het importeren `Az.Automation` `AzureRM.Automation` van de gehele of module, die alle opgenomen modules importeert.

Als het updateproces wordt `SimultaneousModuleImportJobCount` onderbroken, voegt u de parameter toe aan het **script Update-AzureModules.ps1** en geeft u een lagere waarde dan de standaardwaarde van 10. Als u deze logica implementeert, wordt het aanbevolen om te beginnen met een waarde van 3 of 5. `SimultaneousModuleImportJobCount`is een parameter van de runbook **update-AutomationAzureModulesForAccount-systeem** die wordt gebruikt om Azure-modules bij te werken. Als u deze aanpassing uitvoert, wordt het updateproces langer uitgevoerd, maar heeft het een betere kans om te voltooien. In het volgende voorbeeld wordt de parameter weergegeven en waar u deze in het runbook moet plaatsen:

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

Als u een Run As-account wilt maken of bijwerken, moet u over de juiste [machtigingen](../manage-runas-account.md#permissions) beschikken voor de verschillende bronnen die worden gebruikt door het Run As-account. 

Als het probleem is vanwege een vergrendeling, controleert u of het slot kan worden verwijderd. Navigeer vervolgens naar de bron die is vergrendeld in de Azure-portal, klik met de rechtermuisknop op het slot en klik op **Verwijderen**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenario: U ontvangt de foutmelding 'Kan geen toegangspunt met de naam 'GetPerAdapterInfo' vinden in DLL 'iplpapi.dll'" bij het uitvoeren van een runbook

#### <a name="issue"></a>Probleem

Bij het uitvoeren van een runbook ontvangt u de volgende uitzondering:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Oorzaak

Deze fout wordt waarschijnlijk veroorzaakt door een onjuist geconfigureerde [run as-account](../manage-runas-account.md).

#### <a name="resolution"></a>Oplossing

Zorg ervoor dat uw Run As-account correct is geconfigureerd. Controleer vervolgens of u de juiste code in uw runbook hebt om te verifiëren met Azure. In het volgende voorbeeld wordt een codefragment weergegeven dat moet worden geverifieerd aan Azure in een runbook met een Run As-account.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Volgende stappen

Als je het probleem hierboven niet ziet of het probleem niet oplossen, probeer je een van de volgende kanalen voor extra ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport)verbinding met het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
