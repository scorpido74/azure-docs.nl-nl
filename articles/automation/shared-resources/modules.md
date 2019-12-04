---
title: Modules in Azure Automation beheren
description: In dit artikel wordt beschreven hoe u modules beheert in Azure Automation
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 65759b32889f9a99b0322823bb8a4924788e8c09
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786466"
---
# <a name="manage-modules-in-azure-automation"></a>Modules in Azure Automation beheren

Azure Automation biedt de mogelijkheid om Power shell-modules te importeren in uw Automation-account dat moet worden gebruikt door de op Power shell gebaseerde runbooks. Deze modules kunnen aangepaste modules zijn die u hebt gemaakt, van de PowerShell Gallery, of de AzureRM-en AZ-modules voor Azure. Wanneer u een Automation-account maakt, worden er standaard sommige modules geïmporteerd.

## <a name="import-modules"></a>Modules importeren

Er zijn meerdere manieren waarop u een module kunt importeren in uw Automation-account. In de volgende secties ziet u de verschillende manieren om een module te importeren.

> [!NOTE]
> Het maximum pad van een bestand in een module dat moet worden gebruikt in Azure Automation is 140 tekens. Elk pad dat langer is dan 140 tekens, kan niet worden geïmporteerd in de Power shell-sessie met `Import-Module`.

### <a name="powershell"></a>PowerShell

U kunt de [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) gebruiken om een module te importeren in uw Automation-account. De cmdlet maakt een URL naar een module zip-pakket.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

U kunt ook dezelfde cmdlet gebruiken om rechtstreeks een module te importeren vanuit PowerShell Gallery. Zorg ervoor dat u de **module** naam en **ModuleVersion** van [PowerShell Gallery](https://www.powershellgallery.com)ophaalt.

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Azure Portal

Navigeer in het Azure Portal naar uw Automation-account en selecteer **modules** onder **gedeelde bronnen**. Klik op **+ een module toevoegen**. Selecteer een **zip** -bestand dat de module bevat en klik op **OK** om te beginnen met het importeren van het proces.

### <a name="powershell-gallery"></a>PowerShell Gallery

Modules uit de Power shell Gallery kunnen vanuit het [PowerShell Gallery](https://www.powershellgallery.com) rechtstreeks of vanuit uw Automation-account worden geïmporteerd.

Als u een module wilt importeren vanuit de PowerShell Gallery, gaat u naar https://www.powershellgallery.com en zoekt u naar de module die u wilt importeren. Klik op **implementeren naar Azure Automation** op het tabblad **Azure Automation** onder **installatie opties**. Met deze actie wordt de Azure Portal geopend. Selecteer uw Automation-account op de pagina **importeren** en klik op **OK**.

![Module voor PowerShell Gallery importeren](../media/modules/powershell-gallery.png)

U kunt ook modules vanuit het PowerShell Gallery rechtstreeks vanuit uw Automation-account importeren. Selecteer in uw Automation-account **modules** onder **gedeelde bronnen**. Klik op de pagina modules op **Browse Gallery**en zoek de PowerShell Gallery voor een module. Selecteer de module die u wilt importeren en klik op **importeren**. Klik op de pagina **importeren** op **OK** om het import proces te starten.

![PowerShell Gallery importeren uit Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Modules verwijderen

Als u problemen ondervindt met een module of als u een eerdere versie van een module wilt herstellen, kunt u deze verwijderen uit uw Automation-account. U kunt de oorspronkelijke versie van de [standaard modules](#default-modules) die worden geïmporteerd niet verwijderen wanneer u een Automation-account maakt. Als de module die u wilt verwijderen een nieuwere versie is van een van de [standaard modules](#default-modules) die zijn geïnstalleerd, wordt de versie teruggezet die is geïnstalleerd met uw Automation-account. Anders wordt elke module verwijderd die u uit uw Automation-account verwijdert.

### <a name="azure-portal"></a>Azure Portal

Navigeer in het Azure Portal naar uw Automation-account en selecteer **modules** onder **gedeelde bronnen**. Selecteer de module die u wilt verwijderen. Op de pagina **module** , clcick **verwijderen**. Als deze module een van de [standaard modules](#default-modules)is, wordt deze teruggedraaid naar de versie die aanwezig was tijdens het maken van het Automation-account.

### <a name="powershell"></a>PowerShell

Als u een module wilt verwijderen via Power shell, voert u de volgende opdracht uit:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Interne cmdlets

Hier volgt een lijst met cmdlets in de module interne `Orchestrator.AssetManagement.Cmdlets` die in elk Automation-account wordt geïmporteerd. Deze cmdlets zijn toegankelijk in uw runbooks en DSC-configuraties en bieden u de mogelijkheid om te communiceren met uw assets in uw Automation-account. Daarnaast kunt u met de interne cmdlets geheimen ophalen van versleutelde **variabelen** waarden, **referenties**en versleutelde **verbindings** velden. De Azure PowerShell-cmdlets kunnen deze geheimen niet ophalen. Voor deze cmdlets hoeft u niet impliciet verbinding te maken met Azure wanneer u deze gebruikt, zoals het gebruik van een uitvoeren als-account om te verifiëren bij Azure.

>[!NOTE]
>Deze interne cmdlets zijn niet beschikbaar op een Hybrid Runbook Worker, ze zijn alleen toegankelijk vanuit runbooks die worden uitgevoerd in Azure. Gebruik de bijbehorende modules [AzureRM. Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) of [AZ](../az-modules.md) voor runbooks die rechtstreeks op de computer of op basis van resources in uw omgeving worden uitgevoerd. 
>

|Naam|Beschrijving|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Een verbindings type toevoegen aan uw module

U kunt een aangepast [verbindings type](../automation-connections.md) opgeven dat u wilt gebruiken in uw Automation-account door een optioneel bestand aan uw module toe te voegen. Dit bestand is een meta gegevensbestand waarin een Azure Automation verbindings type wordt opgegeven dat moet worden gebruikt met de cmdlets van de module in uw Automation-account. Als u dit wilt doen, moet u eerst weten hoe u een Power shell-module ontwerpt. Zie [een Power shell-script module schrijven](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)voor meer informatie over het ontwerpen van een module.

![Gebruik een aangepaste verbinding in het Azure Portal](../media/modules/connection-create-new.png)

Als u een Azure Automation verbindings type wilt toevoegen, moet uw module een bestand bevatten met de naam `<ModuleName>-Automation.json` die de eigenschappen van het verbindings type specificeert. Het JSON-bestand wordt opgeslagen in de map module van het gecomprimeerde zip-bestand. Dit bestand bevat de velden van een verbinding die is vereist om verbinding te maken met het systeem of de service die door de module wordt vertegenwoordigd. De configuratie eindigt op het maken van een verbindings type in Azure Automation. Met dit bestand kunt u de veld namen, typen en opgeven of de velden moeten worden versleuteld of optioneel, voor het verbindings type van de module. Het volgende voor beeld is een sjabloon in de JSON-bestands indeling die een gebruikers naam-en wachtwoord eigenschap definieert:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="module-best-practices"></a>Aanbevolen procedures voor modules

Power shell-modules kunnen worden geïmporteerd in Azure Automation om de cmdlets beschikbaar te maken voor gebruik in runbooks en hun DSC-resources die beschikbaar zijn voor gebruik in DSC-configuraties. Achter de schermen worden deze modules door Azure Automation opgeslagen in de runbook-taak en de uitvoerings tijd van de DSC-compilatie taak, worden deze in de Azure Automation sandboxes geladen waar runbooks worden uitgevoerd en DSC-configuraties worden gecompileerd. DSC-resources in modules worden ook automatisch geplaatst op de Automation DSC pull-server. Ze kunnen worden opgehaald door machines wanneer ze DSC-configuraties Toep assen.

U wordt aangeraden het volgende te overwegen wanneer u een Power shell-module ontwerpt voor gebruik in Azure Automation:

* Neem geen versie-map op in het zip-pakket.  Dit probleem is minder belang rijk voor runbooks, maar veroorzaakt een probleem met de status configuratie service.  Azure Automation maakt de map versie automatisch wanneer de module wordt gedistribueerd naar knoop punten die worden beheerd door DSC, en als er een versie-map bestaat, kunt u twee exemplaren beëindigen.  Voor beeld van een mapstructuur voor een DSC-module:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Neem een samenvatting, beschrijving en Help-URI op voor elke cmdlet in de module. In PowerShell kunt u bepaalde Help-informatie voor cmdlets definiëren, zodat de gebruiker hulp ontvangt bij het gebruik van cmdlets met de cmdlet **Get-Help**. In het volgende voor beeld ziet u hoe u een samen vatting en Help-URI definieert voor in een. psm1-module bestand:

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Als u deze informatie opgeeft, wordt deze Help weer gegeven met behulp van de cmdlet **Get-Help** in de Power shell-console. Deze beschrijving wordt ook weer gegeven in de Azure Portal.

  ![Help bij integratiemodule](../media/modules/module-activity-description.png)

* Als de module verbinding maakt met een externe service, moet deze een [verbindings type](#add-a-connection-type-to-your-module)bevatten. Elke cmdlet in de module moet een verbindingsobject (een exemplaar van dat verbindingstype) kunnen bevatten als een parameter. Gebruikers wijzen alle para meters van de verbindings Asset toe aan de bijbehorende para meters telkens wanneer ze een cmdlet aanroepen. Op basis van het bovenstaande runbook-voor beeld gebruikt het een voor beeld van een contoso-verbindings element met de naam ContosoConnection om toegang te krijgen tot contoso-resources en gegevens te retour neren van de externe service.

  In het volgende voor beeld worden de velden toegewezen aan de eigenschappen van de gebruikers naam en het wacht woord van een `PSCredential`-object en vervolgens door gegeven aan de cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Een eenvoudiger en betere manier om dit gedrag te benaderen, is het direct door geven van het verbindings object aan de cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  U kunt gedrag zoals het vorige voor beeld voor uw cmdlets inschakelen door hen toe te staan een verbindings object rechtstreeks als para meter te accepteren, in plaats van alleen verbindings velden voor para meters. Normaal gesp roken wilt u voor elke para meter instellen, zodat een gebruiker die geen gebruikmaakt van Azure Automation, uw cmdlets kan aanroepen zonder een hashtabel te maken die als verbindings object kan fungeren. De para meter set `UserAccount`wordt gebruikt om de eigenschappen van het verbindings veld door te geven. met `ConnectionObject` kunt u de verbinding direct door geven.

* Definieer het uitvoer type voor alle cmdlets in de module. Als u een uitvoertype voor een cmdlet definieert, kan IntelliSense u tijdens het ontwerpen helpen bij het bepalen van de uitvoereigenschappen van de cmdlet, voor gebruik tijdens het ontwerpen. Het is vooral handig tijdens het ontwerpen van een automatiserings-runbook, waarbij kennis van ontwerp tijd essentieel is voor een gemakkelijke gebruikers ervaring met uw module.

Voeg `[OutputType([<MyOutputType>])]` toe waarbij MyOutputType een geldig type is. Zie [about functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)(Engelstalig) voor meer informatie over output type. De volgende code is een voor beeld van het toevoegen van `OutputType` aan een cmdlet:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Uitvoertype grafisch runbook](../media/modules/runbook-graphical-module-output-type.png)

  Dit gedrag is vergelijkbaar met de functionaliteit ' type vooruit ' van de uitvoer van een cmdlet in Power shell ISE zonder dat u deze hoeft uit te voeren.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* Maak alle cmdlets in de module staatloos. Meerdere runbook-taken kunnen gelijktijdig worden uitgevoerd in hetzelfde AppDomain en hetzelfde proces en dezelfde sandbox. Als er een status op die niveaus wordt gedeeld, kunnen de taken van invloed zijn op elkaar. Dit gedrag kan leiden tot regel matig en moeilijk om problemen op te sporen.  Hier ziet u een voorbeeld van wat u niet moet doen.

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

* De module moet volledig zijn opgenomen in een Xcopy-toegestaan pakket. Azure Automation-modules worden gedistribueerd naar de Automation-sandboxs wanneer runbooks moeten worden uitgevoerd. De modules moeten onafhankelijk van de host worden gebruikt waarop ze worden uitgevoerd. U moet een module pakket kunnen opwaarderen en verplaatsen en het als normaal laten functioneren wanneer het wordt geïmporteerd in de Power shell-omgeving van een andere host. Om dat te doen, mag de module niet afhankelijk zijn van bestanden buiten de module map. Deze map is de map die wordt ingepakt wanneer de module in Azure Automation wordt geïmporteerd. De module moet ook niet afhankelijk zijn van unieke register instellingen op een host, zoals de instellingen die zijn ingesteld wanneer een product is geïnstalleerd. Alle bestanden in de module moeten een pad hebben dat kleiner is dan 140 tekens. Alle paden ten opzichte van 140 tekens veroorzaken problemen bij het importeren van uw runbook. Als dit best practice niet wordt gevolgd, is de module niet bruikbaar in Azure Automation.  

* Als u verwijst naar [Azure Power shell AZ-modules](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) in uw module, zorgt u ervoor dat u niet ook verwijst naar `AzureRM`. De module `Az` kan niet worden gebruikt in combi natie met de `AzureRM`-modules. `Az` wordt ondersteund in runbooks, maar wordt niet standaard geïmporteerd. Zie [AZ module support in azure Automation](../az-modules.md)voor meer informatie over de `Az` modules en overwegingen om rekening mee te houden.

## <a name="default-modules"></a>Standaard modules

De volgende tabel bevat de modules die standaard worden geïmporteerd wanneer een Automation-account wordt gemaakt. In de onderstaande modules kunnen nieuwere versies van deze onderdelen worden geïmporteerd, maar de oorspronkelijke versie kan niet worden verwijderd uit uw Automation-account, zelfs niet als u een nieuwere versie ervan verwijdert.

|Module naam|Version|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Micro soft. Power shell. core | 0 |
| Micro soft. Power shell. Diagnostics |  |
| Micro soft. Power shell. Management |  |
| Micro soft. Power shell. Security |  |
| Microsoft.PowerShell.Utility |  |
| Micro soft. WSMan. Management |  |
| Orchestrator. AssetManagement. cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>Volgende stappen

* Zie [Een Windows PowerShell-module schrijven](/powershell/scripting/developer/windows-powershell) voor meer informatie over het maken van PowerShell-modules.
