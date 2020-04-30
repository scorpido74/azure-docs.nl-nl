---
title: Problemen met gedeelde resources in Azure Automation oplossen
description: Meer informatie over het oplossen van problemen met Azure Automation gedeelde bronnen.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733578"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Problemen met gedeelde resources in Azure Automation oplossen

In dit artikel worden oplossingen beschreven voor problemen die u kunt uitvoeren wanneer u [gedeelde resources](../automation-intro.md#shared-resources) gebruikt in azure Automation.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](../automation-update-azure-modules.md).

## <a name="modules"></a>Modules

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scenario: een module is vastgelopen tijdens het importeren

#### <a name="issue"></a>Probleem

Wanneer u uw Azure Automation-modules importeert of bijwerkt, is een module vastgelopen in de import status.

#### <a name="cause"></a>Oorzaak

Omdat het importeren van Power shell-modules een complexe proces voor meerdere stappen is, is het mogelijk dat een module niet correct kan worden geïmporteerd en in een tijdelijke status kan worden vastgelopen. Zie [een Power shell-module importeren](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)voor meer informatie over het import proces.

#### <a name="resolution"></a>Oplossing

Om dit probleem op te lossen, moet u de module die in de import status zit, verwijderen met de cmdlet [Remove-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) . U kunt de module vervolgens opnieuw importeren.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scenario: AzureRM-modules blijven hangen tijdens het importeren na een update poging

#### <a name="issue"></a>Probleem

Een banner met het volgende bericht blijft in uw account na het bijwerken van uw AzureRM-modules:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Oorzaak

Er is een bekend probleem met het bijwerken van de AzureRM-modules in een Automation-account in een resource groep met een numerieke naam die begint met 0.

#### <a name="resolution"></a>Oplossing

Als u uw AzureRM-modules in uw Automation-account wilt bijwerken, moet het account zich in een resource groep met een alfanumerieke naam be. Resource groepen met een numerieke naam die begint met 0, kunnen op dit moment geen AzureRM-modules bijwerken.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenario: de module kan niet worden geïmporteerd of cmdlets kunnen niet worden uitgevoerd na het importeren

#### <a name="issue"></a>Probleem

Een module kan niet worden geïmporteerd of geïmporteerd, maar er worden geen cmdlets geëxtraheerd.

#### <a name="cause"></a>Oorzaak

Enkele veelvoorkomende redenen waarom een module mogelijk niet met succes kan worden geïmporteerd naar Azure Automation zijn:

* De structuur komt niet overeen met de structuur die nodig is voor automatisering.
* De module is afhankelijk van een andere module die niet is geïmplementeerd in uw Automation-account.
* De afhankelijkheden van de module ontbreken in de map.
* De cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) wordt gebruikt om de module te uploaden, en u hebt niet het volledige opslagpad opgegeven of u hebt de module niet geladen met behulp van een openbaar toegankelijke URL.

#### <a name="resolution"></a>Oplossing

Gebruik een van deze oplossingen om het probleem op te lossen.

* Zorg ervoor dat de module de indeling: module naam. zip-> module of versie nummer-> (module naam. psm1, module naam. psd1) volgt.
* Open het **. psd1** -bestand en controleer of de module afhankelijkheden heeft. Als dit het geval is, uploadt u deze modules naar het Automation-account.
* Zorg ervoor dat alle **. dll** -bestanden waarnaar wordt verwezen, aanwezig zijn in de module map.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenario: Update-AzureModule. ps1 wordt onderbroken bij het bijwerken van modules

#### <a name="issue"></a>Probleem

Wanneer u het runbook [Update-AzureModule. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) gebruikt om uw Azure-modules bij te werken, wordt het update proces van de module onderbroken.

#### <a name="cause"></a>Oorzaak

De standaard instelling om te bepalen hoeveel modules tegelijk worden bijgewerkt, is 10 bij gebruik van **Update-AzureModule. ps1**. Het update proces is gevoelig voor fouten wanneer te veel modules tegelijk worden bijgewerkt.

#### <a name="resolution"></a>Oplossing

Het is niet gebruikelijk dat alle AzureRM-of AZ-modules zijn vereist in hetzelfde Automation-account. Het is raadzaam om alleen de specifieke modules te importeren die u nodig hebt.

> [!NOTE]
> Vermijd het importeren van `Az.Automation` het `AzureRM.Automation` hele of-module, waardoor alle opgenomen modules worden geïmporteerd.

Als het update proces wordt onderbroken, voegt u `SimultaneousModuleImportJobCount` de para meter toe aan het script **Update-AzureModules. ps1** en levert u een lagere waarde dan de standaard instelling van 10. Als u deze logica implementeert, is het raadzaam om te beginnen met een waarde van 3 of 5. `SimultaneousModuleImportJobCount`is een para meter van het runbook **Update-AutomationAzureModulesForAccount** System dat wordt gebruikt om Azure-modules bij te werken. Als u deze aanpassing aanbrengt, wordt het update proces langer uitgevoerd, maar heeft het een grotere kans om te volt ooien. In het volgende voor beeld ziet u de para meter en waar u deze in het runbook plaatst:

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Scenario: u kunt een uitvoeren als-account niet maken of bijwerken

#### <a name="issue"></a>Probleem

Wanneer u een uitvoeren als-account probeert te maken of bij te werken, wordt een fout bericht van de volgende strekking weer gegeven:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Oorzaak

U hebt niet de machtigingen die u nodig hebt om het uitvoeren als-account te maken of bij te werken, of de resource is vergrendeld op het niveau van een resource groep.

#### <a name="resolution"></a>Oplossing

Als u een uitvoeren als-account wilt maken of bijwerken, moet u de juiste [machtigingen](../manage-runas-account.md#permissions) hebben voor de verschillende resources die worden gebruikt door het run as-account. 

Als het probleem wordt veroorzaakt door een vergren deling, controleert u of de vergren deling kan worden verwijderd. Ga vervolgens naar de resource die in Azure Portal is vergrendeld, klik met de rechter muisknop op de vergren deling en klik op **verwijderen**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenario: u ontvangt de fout ' kan geen ingangs punt vinden met de naam ' GetPerAdapterInfo ' in DLL ' iplpapi. dll ' ' tijdens het uitvoeren van een runbook

#### <a name="issue"></a>Probleem

Wanneer u een runbook uitvoert, wordt de volgende uitzonde ring weer gegeven:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Oorzaak

Deze fout wordt waarschijnlijk veroorzaakt door een onjuist geconfigureerd [uitvoeren als-account](../manage-runas-account.md).

#### <a name="resolution"></a>Oplossing

Zorg ervoor dat het uitvoeren als-account correct is geconfigureerd. Controleer vervolgens of u de juiste code in uw runbook hebt om te verifiëren met Azure. In het volgende voor beeld ziet u een code fragment voor het verifiëren van Azure in een runbook met behulp van een run as-account.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Volgende stappen

Als uw probleem niet hierboven wordt weer geven of als u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport)met, het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
