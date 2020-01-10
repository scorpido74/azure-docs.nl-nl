---
title: Fouten met Azure Automation gedeelde resources oplossen
description: Meer informatie over het oplossen van problemen met Azure Automation gedeelde bronnen die runbooks ondersteunen.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769860"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Fouten met gedeelde resources oplossen

In dit artikel worden oplossingen beschreven om problemen op te lossen die u kunt uitvoeren wanneer u de gedeelde resources in Azure Automation gebruikt.

## <a name="modules"></a>Modules

### <a name="module-stuck-importing"></a>Scenario: een module is blijven importeren

#### <a name="issue"></a>Probleem

Wanneer u uw modules in azure Automation importeert of bijwerkt, wordt een module vastzitten **in de import** status.

#### <a name="cause"></a>Oorzaak

Het importeren van Power shell-modules is een complexe proces voor meerdere stappen. Dit proces introduceert de mogelijkheid van een module die niet correct wordt geïmporteerd. Als dit probleem zich voordoet, kan de module die u wilt importeren vastzitten in een tijdelijke status. Zie [een Power shell-module importeren](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)voor meer informatie over dit proces.

#### <a name="resolution"></a>Resolutie

Om dit probleem op te lossen, moet u de module die in de **import** status zit, verwijderen met de cmdlet [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) . U kunt de module vervolgens opnieuw importeren.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>Scenario: AzureRM-modules zijn na het bijwerken blijvend geïmporteerd

#### <a name="issue"></a>Probleem

Een banner met het volgende bericht blijft in uw account na het bijwerken van uw AzureRM-modules:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Oorzaak

Er is een bekend probleem met het bijwerken van de AzureRM-modules in een Automation-account dat zich in een resource groep bevindt met een numerieke naam die begint met 0.

#### <a name="resolution"></a>Resolutie

Als u uw Azure-modules in uw Automation-account wilt bijwerken, moet deze zich in een resource groep betreden die een alfanumerieke naam heeft. Resource groepen met een numerieke naam die begint met 0, kunnen op dit moment geen AzureRM-modules bijwerken.

### <a name="module-fails-to-import"></a>Scenario: de module kan niet worden geïmporteerd of cmdlets kunnen niet worden uitgevoerd na het importeren

#### <a name="issue"></a>Probleem

Een module kan niet worden geïmporteerd of geïmporteerd, maar er worden geen cmdlets geëxtraheerd.

#### <a name="cause"></a>Oorzaak

Enkele veelvoorkomende redenen waarom een module mogelijk niet met succes kan worden geïmporteerd naar Azure Automation zijn:

* De structuur komt niet overeen met de structuur waarin de automatisering zich moet bevinden.
* De module is afhankelijk van een andere module die niet is geïmplementeerd in uw Automation-account.
* De afhankelijkheden van de module ontbreken in de map.
* De cmdlet `New-AzureRmAutomationModule` wordt gebruikt om de module te uploaden en u hebt niet het volledige opslagpad opgegeven of u hebt de module niet geladen met behulp van een openbaar toegankelijke URL.

#### <a name="resolution"></a>Resolutie

Het probleem wordt opgelost met een van de volgende oplossingen:

* Zorg ervoor dat de module de volgende indeling heeft: module naam. zip **->** module of versie nummer **->** (module naam. psm1, module naam. psd1)
* Open het. psd1-bestand en controleer of de module afhankelijkheden heeft. Als dit het geval is, uploadt u deze modules naar het Automation-account.
* Zorg ervoor dat alle verwijzingen. dll-bestanden aanwezig zijn in de map module.

### <a name="all-modules-suspended"></a>Scenario: Update-AzureModule. ps1 wordt onderbroken bij het bijwerken van modules

#### <a name="issue"></a>Probleem

Wanneer u het runbook [Update-AzureModule. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) gebruikt om uw Azure-modules bij te werken, wordt het update proces door de module bijgewerkt.

#### <a name="cause"></a>Oorzaak

De standaard instelling om te bepalen hoeveel modules tegelijk worden bijgewerkt, is 10 wanneer u het `Update-AzureModule.ps1` script gebruikt. Het update proces is gevoelig voor fouten wanneer te veel modules tegelijk worden bijgewerkt.

#### <a name="resolution"></a>Resolutie

Het is niet gebruikelijk dat alle AzureRM-modules in hetzelfde Automation-account zijn vereist. Het is raadzaam om alleen de AzureRM-modules te importeren die u nodig hebt.

> [!NOTE]
> Vermijd het importeren van de **AzureRM** -module. Als u de **AzureRM** -modules importeert, worden alle **AzureRM.\*** -modules geïmporteerd. Dit is niet kunt.

Als het update proces wordt onderbroken, moet u de para meter `SimultaneousModuleImportJobCount` toevoegen aan het `Update-AzureModules.ps1` script en een lagere waarde opgeven dan de standaard die 10 is. Het wordt aanbevolen als u deze logica implementeert om te beginnen met een waarde van 3 of 5. `SimultaneousModuleImportJobCount` is een para meter van het `Update-AutomationAzureModulesForAccount` systeem-runbook dat wordt gebruikt om Azure-modules bij te werken. Met deze wijziging wordt het proces langer uitgevoerd, maar heeft het een grotere kans om te volt ooien. In het volgende voor beeld ziet u de para meter en waar u deze in het runbook plaatst:

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

### <a name="unable-create-update"></a>Scenario: u kunt een uitvoeren als-account niet maken of bijwerken

#### <a name="issue"></a>Probleem

Wanneer u een uitvoeren als-account probeert te maken of bij te werken, wordt een fout bericht van de volgende strekking weer gegeven:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Oorzaak

U hebt niet de machtigingen die u nodig hebt om het uitvoeren als-account te maken of bij te werken, of de resource is vergrendeld op het niveau van een resource groep.

#### <a name="resolution"></a>Resolutie

Als u een uitvoeren als-account wilt maken of bijwerken, moet u de juiste machtigingen hebben voor de verschillende resources die worden gebruikt door het run as-account. Zie [Run as-account machtigingen](../manage-runas-account.md#permissions)voor meer informatie over de machtigingen die nodig zijn om een uitvoeren als-account te maken of bij te werken.

Als het probleem wordt veroorzaakt door een vergren deling, controleert u of de vergren deling is OK om deze te verwijderen. Ga vervolgens naar de resource die is vergrendeld, klik met de rechter muisknop op de vergren deling en kies **verwijderen** om de vergren deling te verwijderen.

### <a name="iphelper"></a>Scenario: u ontvangt de fout ' kan geen ingangs punt vinden met de naam ' GetPerAdapterInfo ' in DLL ' iplpapi. dll ' ' tijdens het uitvoeren van een runbook.

#### <a name="issue"></a>Probleem

Wanneer u een runbook uitvoert, wordt de volgende uitzonde ring weer gegeven:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Oorzaak

Deze fout wordt waarschijnlijk veroorzaakt door een onjuist geconfigureerd [uitvoeren als-account](../manage-runas-account.md).

#### <a name="resolution"></a>Resolutie

Zorg ervoor dat het [uitvoeren als-account](../manage-runas-account.md) correct is geconfigureerd. Zodra de configuratie correct is geconfigureerd, controleert u of u de juiste code in uw runbook hebt om te verifiëren met Azure. In het volgende voor beeld ziet u een code fragment voor het verifiëren van Azure in een runbook met behulp van een run as-account.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts op [Azure-Forums](https://azure.microsoft.com/support/forums/)
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport), het officiële Microsoft Azure-account voor het verbeteren van de gebruikerservaring door de Azure-community in contact te brengen met de juiste resources: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, kunt u een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
