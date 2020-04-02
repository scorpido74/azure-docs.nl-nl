---
title: Modules beheren in Azure Automation
description: In dit artikel wordt beschreven hoe u modules beheert in Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 859eea66d10e07a3503e33166bc77c8a97577acd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548938"
---
# <a name="manage-modules-in-azure-automation"></a>Modules beheren in Azure Automation

Azure Automation maakt het mogelijk om PowerShell-modules te importeren in uw Automation-account en kan worden gebruikt door op PowerShell gebaseerde runbooks. Deze modules kunnen aangepaste modules zijn die u hebt gemaakt, modules uit de PowerShell Gallery of de AzureRM- en Az-modules voor Azure. Wanneer u een Automatiseringsaccount maakt, worden sommige modules standaard geïmporteerd.

## <a name="import-modules"></a>Modules importeren

Er zijn meerdere manieren waarop u een module importeren in uw Automatiseringsaccount. In de volgende secties worden de verschillende manieren weergegeven om een module te importeren.

> [!NOTE]
> De maximale padgrootte voor een bestand in een module die wordt gebruikt in Azure Automation is 140 tekens. Automatisering kan een bestand met een padgrootte van meer dan `Import-Module`140 tekens niet importeren in de PowerShell-sessie met .

### <a name="powershell"></a>PowerShell

U de cmdlet [Nieuw-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) gebruiken om een module in uw Automatiseringsaccount te importeren. De cmdlet neemt een URL voor een module .zip-pakket.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

U dezelfde cmdlet ook gebruiken om een module rechtstreeks uit PowerShell Gallery te importeren. Zorg ervoor `ModuleName` dat `ModuleVersion` u grijpen en van [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Azure Portal

Ga als lid van de module naar een azure-portal:

1. Navigeer naar uw Automatiseringsaccount.
2. Selecteer **modules** onder **Gedeelde bronnen**.
3. Klik **op Een module toevoegen**. 
4. Selecteer het **.zip-bestand** dat de module bevat.
5. Klik op **OK** om het proces te starten.

### <a name="powershell-gallery"></a>PowerShell Gallery

U [PowerShell Gallery-modules](https://www.powershellgallery.com) rechtstreeks vanuit de galerie of vanuit uw Automation-account importeren.

Ga als lid direct van de PowerShell-galerie:

1. Ga https://www.powershellgallery.com naar en zoek naar de module om te importeren.
2. Klik **op Implementeren voor Azure Automation** op het tabblad Azure **Automation** onder **Installatieopties**. Met deze actie wordt de Azure-portal geopend. 
3. Selecteer op de pagina Importeren uw automatiseringsaccount en klik op **OK**.

![Importmodule PowerShell Gallery](../media/modules/powershell-gallery.png)

Ga als u een PowerShell Gallery-module rechtstreeks vanuit uw Automation-account importeren:

1. Selecteer **modules** onder **Gedeelde bronnen**. 
2. Klik op de pagina Modules op **Galerie bladeren**en zoek in de galerie naar een module. 
3. Selecteer de module die u wilt importeren en klik op **Importeren**. 
4. Klik op de pagina Importeren op **OK** om het importproces te starten.

![PowerShell-galerie importeren uit Azure-portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Modules verwijderen

Als u problemen hebt met een module of als u moet terugdraaien naar een vorige versie van een module, u deze verwijderen uit uw Automatiseringsaccount. U de oorspronkelijke versies van de [standaardmodules](#default-modules) die worden geïmporteerd wanneer u een Automatiseringsaccount maakt, niet verwijderen. Als de module die moet worden verwijderd een nieuwere versie van een van de [standaardmodules](#default-modules)is, rolt deze terug naar de versie die is geïnstalleerd met uw Automation-account. Anders wordt elke module die u uit uw Automatiseringsaccount verwijdert, verwijderd.

### <a name="azure-portal"></a>Azure Portal

Ga als lid van het werk om een module in de Azure-portal te verwijderen:

1. Navigeer naar uw automatiseringsaccount en selecteer **Modules** onder **Gedeelde bronnen**. 
2. Selecteer de module die u wilt verwijderen. 
3. Selecteer **verwijderen**op de pagina **Module** . Als deze module een van de [standaardmodules](#default-modules)is, rolt deze terug naar de versie die bestond toen het automatiseringsaccount werd gemaakt.

### <a name="powershell"></a>PowerShell

Voer de volgende opdracht uit om een module via PowerShell te verwijderen:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Interne cmdlets

De onderstaande tabel bevat cmdlets in de interne `Orchestrator.AssetManagement.Cmdlets` module die in elk automatiseringsaccount wordt geïmporteerd. Deze cmdlets zijn toegankelijk in uw runbooks en DSC-configuraties en stellen u in staat om te communiceren met assets binnen uw Automation-account. Bovendien u met de interne cmdlets geheimen ophalen uit versleutelde variabelen, referenties en versleutelde verbindingen. De Azure PowerShell-cmdlets kunnen deze geheimen niet ophalen. Deze cmdlets vereisen niet dat u impliciet verbinding maakt met Azure wanneer u ze gebruikt, zoals bij het gebruik van een Run As-account om te verifiëren naar Azure.

>[!NOTE]
>Deze interne cmdlets zijn beschikbaar op een Windows Hybrid Runbook Worker, maar niet op een Linux Hybrid Runbook Worker. Gebruik de bijbehorende [AzureRM.Automation-](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) of [Az-module-cmdlets](../az-modules.md) voor runbooks die rechtstreeks op de computer worden uitgevoerd of tegen resources in uw omgeving. 

|Name|Beschrijving|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Een verbindingstype toevoegen aan uw module

U een aangepast [verbindingstype](../automation-connections.md) bieden dat u in uw Automatiseringsaccount gebruiken door een optioneel metagegevensbestand toe te voegen aan uw module. In dit bestand wordt een Azure Automation-verbindingstype opgegeven dat moet worden gebruikt met de cmdlets van de module in uw Automatiseringsaccount. Om dit te bereiken, moet u eerst weten hoe u een PowerShell-module moet maken. Zie [hoe u een PowerShell Script-module schrijft](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Een aangepaste verbinding gebruiken in de Azure-portal](../media/modules/connection-create-new.png)

Het bestand met eigenschappen van verbindingstype heeft de naam ** &lt;ModuleName&gt;-Automation.json** en is te vinden in de modulemap van het gecomprimeerde **zip-bestand.** Dit bestand bevat de velden van een verbinding die nodig zijn om verbinding te maken met het systeem of de service die de module vertegenwoordigt. De configuratie maakt het mogelijk een verbindingstype te maken in Azure Automation. Met dit bestand u de veldnamen, -typen en -typen instellen of de velden zijn versleuteld of optioneel voor het verbindingstype van de module. Het volgende voorbeeld is een sjabloon in de **.json-bestandsindeling** die de eigenschappen van gebruikersnaam en wachtwoord definieert:

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

U PowerShell-modules importeren in Azure Automation om hun cmdlets beschikbaar te maken binnen runbooks en hun DSC-resources binnen DSC-configuraties. Achter de schermen slaat Azure Automation deze modules op. Bij runbook-taak en DSC-taakuitvoeringstijd laadt Automation ze in de Azure Automation-sandboxes waar runbooks worden uitgevoerd en DSC-configuraties compileren. Alle DSC-bronnen in modules worden ook automatisch op de Automation DSC pull-server geplaatst. Machines kunnen ze trekken wanneer ze DSC-configuraties toepassen.

We raden u aan het volgende in overweging te nemen wanneer u een PowerShell-module maakt voor gebruik in Azure Automation:

* Neem geen versiemap op in het **.zip-pakket.**  Dit probleem is minder een punt van zorg voor runbooks, maar veroorzaakt wel een probleem met de statusconfiguratieservice. Azure Automation maakt de versiemap automatisch wanneer de module wordt gedistribueerd naar knooppunten die door DSC worden beheerd. Als er een versiemap bestaat, komt u uit op twee instanties. Hier is een voorbeeldmapstructuur voor een DSC-module:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Neem een samenvatting, beschrijving en Help-URI op voor elke cmdlet in de module. In PowerShell u help-informatie voor `Get-Help` cmdlets definiëren met behulp van de cmdlet. In het volgende voorbeeld ziet u hoe u een synopsis definieert en URI helpt in een **PSM1-modulebestand:**

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

  Het verstrekken van deze `Get-Help` informatie toont help tekst via de cmdlet in de PowerShell console. Deze tekst wordt ook weergegeven in de Azure-portal.

  ![Help bij integratiemodule](../media/modules/module-activity-description.png)

* Als de module verbinding maakt met een externe service, definieert u een [verbindingstype](#add-a-connection-type-to-your-module). Elke cmdlet in de module moet een verbindingsobject (een instantie van dat verbindingstype) als parameter accepteren. Gebruikers brengen parameters van het verbindingselement in kaart met de overeenkomstige parameters van de cmdlet telkens wanneer ze een cmdlet aanroepen. Op basis van het bovenstaande voorbeeld van runbook wordt `ContosoConnection` een voorbeeld van Contoso-verbindingsasset gebruikt dat wordt aangeroepen om toegang te krijgen tot Contoso-bronnen en gegevens van de externe service te retourneren.

  In het volgende voorbeeld worden de `UserName` velden `Password` toegewezen `PSCredential` aan de eigenschappen en eigenschappen van een object en vervolgens doorgegeven aan de cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Een eenvoudigere en betere manier om dit gedrag te benaderen is het verbindingsobject rechtstreeks door te geven aan de cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  U vergelijkbaar gedrag voor uw cmdlets inschakelen door ze toe te staan een verbindingsobject rechtstreeks als parameter te accepteren, in plaats van alleen verbindingsvelden voor parameters. Meestal wilt u een parameterset voor elk, zodat een gebruiker die Azure Automation niet gebruikt, uw cmdlets kan aanroepen zonder een hashtable te maken om als verbindingsobject te fungeren. De parameterset `UserAccount`wordt gebruikt om de eigenschappen van het verbindingsveld door te geven. `ConnectionObject`hiermee u de verbinding dwars door.

* Definieer het uitvoertype voor alle cmdlets in de module. Als u een uitvoertype voor een cmdlet definieert, kan IntelliSense u tijdens het ontwerpen helpen bij het bepalen van de uitvoereigenschappen van de cmdlet, voor gebruik tijdens het ontwerpen. Het is vooral handig tijdens automation runbook graphical authoring, waar kennis van ontwerptijd de sleutel is tot een eenvoudige gebruikerservaring met uw module.

Voeg `[OutputType([<MyOutputType>])]` toe waar MyOutputType een geldig type is. Zie [Functiefunctie Functietypeattribuut voor](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)meer informatie over OutputType . De volgende code is `OutputType` een voorbeeld van het toevoegen aan een cmdlet:

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

  Dit gedrag is vergelijkbaar met de "type ahead" functionaliteit van de uitvoer van een cmdlet in PowerShell ISE zonder het uit te voeren.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* Maak alle cmdlets in de module staatloos. Meerdere runbook-taken kunnen tegelijkertijd worden uitgevoerd in hetzelfde AppDomain en hetzelfde proces en sandbox. Als er een status wordt gedeeld op deze niveaus, kunnen taken elkaar beïnvloeden. Dit gedrag kan leiden tot intermitterende en moeilijk te diagnosticeren problemen.  Hier ziet u een voorbeeld van wat u niet moet doen.

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

* De module moet volledig zijn opgenomen in een xcopy-able pakket. Azure Automation-modules worden gedistribueerd naar de automatiseringssandboxes wanneer runbooks moeten worden uitgevoerd. De modules moeten onafhankelijk van de host werken waarop ze draaien. U moet in staat zijn om een modulepakket dicht te ritsen en te verplaatsen en het als normaal te laten functioneren wanneer het wordt geïmporteerd in de PowerShell-omgeving van een andere host. Om dit te laten gebeuren, mag de module niet afhankelijk zijn van bestanden buiten de modulemap die wordt opritsen wanneer de module wordt geïmporteerd in Azure Automation. De module mag ook niet afhankelijk zijn van unieke registerinstellingen op een host, zoals de instellingen die zijn ingesteld wanneer een product is geïnstalleerd. Alle bestanden in de module moeten een pad hebben van minder dan 140 tekens. Paden van meer dan 140 tekens veroorzaken problemen met het importeren van uw runbook. Als u deze aanbevolen procedures niet volgt, is de module niet bruikbaar in Azure Automation.  

* Als u verwijst naar de [Azure PowerShell Az-module](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) in uw module, moet u ervoor zorgen dat u niet ook verwijst naar `AzureRM`. U de `Az` module niet gebruiken `AzureRM` in combinatie met de module. `Az`wordt ondersteund in runbooks, maar wordt niet standaard geïmporteerd. Zie [Az-moduleondersteuning in Azure Automation](../az-modules.md)voor meer informatie over de `Az` module en overwegingen om rekening mee te houden.

## <a name="default-modules"></a>Standaardmodules

In de volgende tabel worden modules weergegeven die standaard worden geïmporteerd wanneer een automatiseringsaccount wordt gemaakt. Automatisering kan nieuwere versies van deze modules importeren. U de oorspronkelijke versie echter niet uit uw Automatiseringsaccount verwijderen, zelfs niet als u een nieuwere versie verwijdert.

|Modulenaam|Versie|
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
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Beheer |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets Orchestrator.AssetManagement.Cmdlets Orchestrator.AssetManagement.Cmdlets Orchestrat | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>Volgende stappen

* Zie [Een Windows PowerShell-module schrijven](/powershell/scripting/developer/windows-powershell)voor meer informatie over het maken van PowerShell-modules.
