---
title: Modules beheren in Azure Automation
description: In dit artikel wordt beschreven hoe u modules beheert in Azure Automation
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278335"
---
# <a name="manage-modules-in-azure-automation"></a>Modules beheren in Azure Automation

Azure Automation biedt de mogelijkheid om PowerShell-modules te importeren in uw automatiseringsaccount om te worden gebruikt door de op PowerShell gebaseerde runbooks. Deze modules kunnen aangepaste modules zijn die u hebt gemaakt, van de PowerShell Gallery of de AzureRM- en Az-modules voor Azure. Wanneer u een automatiseringsaccount maakt, worden sommige modules standaard geïmporteerd.

## <a name="import-modules"></a>Modules importeren

Er zijn meerdere manieren waarop u een module importeren in uw automatiseringsaccount. In de volgende secties worden de verschillende manieren weergegeven om een module te importeren.

> [!NOTE]
> Het maximale pad van een bestand in een module die moet worden gebruikt in Azure Automation is 140 tekens. Elk pad van meer dan 140 tekens kan niet worden `Import-Module`geïmporteerd in de PowerShell-sessie met .

### <a name="powershell"></a>PowerShell

U de [Nieuw-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) gebruiken om een module te importeren in uw automatiseringsaccount. De cmdlet neemt een url naar een module zip pakket.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

U dezelfde cmdlet ook gebruiken om een module rechtstreeks uit PowerShell Gallery te importeren. Zorg ervoor dat je **ModuleName** en **ModuleVersion** uit [PowerShell Gallery pakt.](https://www.powershellgallery.com)

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Azure Portal

Navigeer in de Azure-portal naar uw automatiseringsaccount en selecteer **Modules** onder **Gedeelde resources**. Klik **op + Een module toevoegen**. Selecteer een **.zip-bestand** met de module en klik op **Ok** om te beginnen met het importeren van het proces.

### <a name="powershell-gallery"></a>PowerShell Gallery

Modules uit de PowerShell-galerie kunnen rechtstreeks of vanuit uw automatiseringsaccount uit de [PowerShell-galerie](https://www.powershellgallery.com) worden geïmporteerd.

Als u een module wilt importeren https://www.powershellgallery.com uit de PowerShell-galerie, gaat u naar en zoekt u naar de module die u wilt importeren. Klik **op Implementeren voor Azure Automation** op het tabblad Azure **Automation** onder **Installatieopties**. Met deze actie wordt de Azure-portal geopend. Selecteer **op de** pagina Importeren uw automatiseringsaccount en klik op **OK**.

![Importmodule PowerShell Gallery](../media/modules/powershell-gallery.png)

U ook modules uit de PowerShell-galerie rechtstreeks importeren vanuit uw automatiseringsaccount. Selecteer **modules** onder Gedeelde **resources**in uw automatiseringsaccount. Klik op de modulespagina op **Galerie bladeren**en zoek in de PowerShell-galerie naar een module. Selecteer de module die u wilt importeren en klik op **Importeren**. Klik **op** de pagina Importeren op **OK** om het importproces te starten.

![PowerShell-galerie importeren uit Azure-portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Modules verwijderen

Als u problemen hebt met een module of als u moet terugdraaien naar een vorige versie van een module, u deze verwijderen uit uw automatiseringsaccount. U de oorspronkelijke versie van de [standaardmodules](#default-modules) die worden geïmporteerd wanneer u een automatiseringsaccount maakt, niet verwijderen. Als de module die u wilt verwijderen een nieuwere versie is van een van de [standaardmodules](#default-modules) die zijn geïnstalleerd, wordt deze teruggedraaid naar de versie die is geïnstalleerd met uw automatiseringsaccount. Anders wordt elke module die u uit uw automatiseringsaccount verwijdert, verwijderd.

### <a name="azure-portal"></a>Azure Portal

Navigeer in de Azure-portal naar uw automatiseringsaccount en selecteer **Modules** onder **Gedeelde resources**. Selecteer de module die u wilt verwijderen. Selecteer **verwijderen**op de pagina **Module** . Als deze module een van de [standaardmodules](#default-modules)is, wordt deze teruggedraaid naar de versie die aanwezig was toen het automatiseringsaccount werd gemaakt.

### <a name="powershell"></a>PowerShell

Voer de volgende opdracht uit om een module via PowerShell te verwijderen:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Interne cmdlets

Het volgende is een lijst van `Orchestrator.AssetManagement.Cmdlets` cmdlets in de interne module die wordt geïmporteerd in elke automatiseringsaccount. Deze cmdlets zijn toegankelijk in uw runbooks en DSC-configuraties en stellen u in staat om te communiceren met uw assets binnen uw automatiseringsaccount. Bovendien u met de interne cmdlets geheimen ophalen uit versleutelde **variabele** waarden, **referenties**en versleutelde **verbindingsvelden.** De Azure PowerShell-cmdlets kunnen deze geheimen niet ophalen. Deze cmdlets vereisen niet dat u impliciet verbinding maakt met Azure wanneer u ze gebruikt, zoals het gebruik van een Run As-account om te verifiëren naar Azure.

>[!NOTE]
>Deze interne cmdlets zijn beschikbaar op een Windows Hybrid Runbook Worker, ze zijn niet beschikbaar op een Linux Hybrid Runbook Worker. Gebruik de bijbehorende [AzureRM.Automation-](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) of [Az-modules](../az-modules.md) voor runbooks die rechtstreeks op de computer worden uitgevoerd of tegen resources in uw omgeving. 
>

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

U een aangepast [verbindingstype](../automation-connections.md) voor u toevoegen in uw automatiseringsaccount door een optioneel bestand aan uw module toe te voegen. Dit bestand is een metagegevensbestand met een Azure Automation-verbindingstype dat moet worden gebruikt met de cmdlets van de module in uw automatiseringsaccount. Om dit te bereiken, moet u eerst weten hoe u een PowerShell-module moet maken. Zie [Een PowerShell Script-module schrijven voor](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)meer informatie over modulecreatie.

![Een aangepaste verbinding gebruiken in de Azure-portal](../media/modules/connection-create-new.png)

Als u een azure automation-verbindingstype wilt toevoegen, `<ModuleName>-Automation.json` moet de module een bestand bevatten met de naam die de eigenschappen van het verbindingstype opgeeft. Het json-bestand wordt in de modulemap van uw gecomprimeerde .zip-bestand geplaatst. Dit bestand bevat de velden van een verbinding die nodig is om verbinding te maken met het systeem of de service die de module vertegenwoordigt. De configuratie maakt uiteindelijk een verbindingstype in Azure Automation. Met dit bestand u de veldnamen, -typen en -typen instellen voor het verbindingstype van de module. Het volgende voorbeeld is een sjabloon in de json-bestandsindeling die een eigenschap gebruikersnaam en wachtwoord definieert:

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

PowerShell-modules kunnen worden geïmporteerd in Azure Automation om hun cmdlets beschikbaar te maken voor gebruik binnen runbooks en hun DSC-resources beschikbaar voor gebruik binnen DSC-configuraties. Achter de schermen slaat Azure Automation deze modules op en laadt azure automation deze op tijdens de uitvoeringstijd van runbook-taken en DSC-compilatietaken in de azure automation-sandboxes waar runbooks worden uitgevoerd en DSC-configuraties compileren. Alle DSC-bronnen in modules worden ook automatisch op de Automation DSC pull-server geplaatst. Ze kunnen worden getrokken door machines wanneer ze DSC-configuraties toepassen.

We raden u aan het volgende te overwegen wanneer u een PowerShell-module maakt voor gebruik in Azure Automation:

* Neem geen versiemap op in het .zip-pakket.  Dit probleem is minder een punt van zorg voor runbooks, maar zal een probleem veroorzaken met de state configuration service.  Azure Automation maakt de versiemap automatisch wanneer de module wordt gedistribueerd naar knooppunten die door DSC worden beheerd, en als er een versiemap bestaat, krijgt u twee exemplaren.  Voorbeeldmapstructuur voor een DSC-module:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Neem een samenvatting, beschrijving en Help-URI op voor elke cmdlet in de module. In PowerShell kunt u bepaalde Help-informatie voor cmdlets definiëren, zodat de gebruiker hulp ontvangt bij het gebruik van cmdlets met de cmdlet **Get-Help**. In het volgende voorbeeld ziet u hoe u een synopsis definieert en URI helpt in een PSM1-modulebestand:

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

  Het verstrekken van deze informatie toont deze hulp met behulp van de **Get-Help** cmdlet in de PowerShell-console. Deze beschrijving wordt ook weergegeven in de Azure-portal.

  ![Help bij integratiemodule](../media/modules/module-activity-description.png)

* Als de module verbinding maakt met een externe service, moet deze een [verbindingstype](#add-a-connection-type-to-your-module)bevatten. Elke cmdlet in de module moet een verbindingsobject (een exemplaar van dat verbindingstype) kunnen bevatten als een parameter. Gebruikers brengen parameters van het verbindingselement in kaart met de overeenkomstige parameters van de cmdlet telkens wanneer ze een cmdlet aanroepen. Op basis van het bovenstaande voorbeeld van runbook gebruikt het een voorbeeld van Contoso-verbindingselement ContosoConnection om toegang te krijgen tot Contoso-bronnen en gegevens van de externe service te retourneren.

  In het volgende voorbeeld worden de velden toegewezen aan de `PSCredential` eigenschappen Gebruikersnaam en Wachtwoord van een object en vervolgens doorgegeven aan de cmdlet.

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

  U gedrag zoals het voorgaande voorbeeld voor uw cmdlets inschakelen door ze toe te staan een verbindingsobject rechtstreeks als parameter te accepteren, in plaats van alleen verbindingsvelden voor parameters. Meestal wilt u een parameterset voor elk, zodat een gebruiker die Azure Automation niet gebruikt, uw cmdlets kan aanroepen zonder een hashtable te maken om als verbindingsobject te fungeren. De parameterset `UserAccount`wordt gebruikt om de eigenschappen van het verbindingsveld door te geven. `ConnectionObject`hiermee u de verbinding dwars door.

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

* De module moet volledig zijn opgenomen in een xcopy-able pakket. Azure Automation-modules worden gedistribueerd naar de automatiseringssandboxes wanneer runbooks moeten worden uitgevoerd. De modules moeten onafhankelijk van de host werken waarop ze draaien. U moet in staat zijn om zip-up en verplaats een module pakket en hebben het functioneren als normaal wanneer geïmporteerd in de PowerShell-omgeving van een andere host. Om dat te laten gebeuren, mag de module niet afhankelijk zijn van bestanden buiten de modulemap. Deze map is de map die wordt opritsen wanneer de module wordt geïmporteerd in Azure Automation. De module mag ook niet afhankelijk zijn van unieke registerinstellingen op een host, zoals de instellingen die zijn ingesteld wanneer een product is geïnstalleerd. Alle bestanden in de module moeten een pad hebben van minder dan 140 tekens. Paden van meer dan 140 tekens veroorzaken problemen met het importeren van uw runbook. Als deze aanbevolen procedure niet wordt gevolgd, is de module niet bruikbaar in Azure Automation.  

* Als u verwijst naar [Azure Powershell Az-modules](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) in uw module, moet u ervoor zorgen dat u niet ook verwijst naar `AzureRM`. De `Az` module kan niet worden gebruikt `AzureRM` in combinatie met de modules. `Az`wordt ondersteund in runbooks, maar worden niet standaard geïmporteerd. Zie [Az-moduleondersteuning in Azure Automation](../az-modules.md)voor meer informatie over de `Az` modules en overwegingen waarmee rekening moet worden gehouden.

## <a name="default-modules"></a>Standaardmodules

In de volgende tabel worden de modules weergegeven die standaard worden geïmporteerd wanneer een automatiseringsaccount wordt gemaakt. De onderstaande modules kunnen nieuwere versies van hen geïmporteerd hebben, maar de oorspronkelijke versie kan niet worden verwijderd uit uw automatiseringsaccount, zelfs als u een nieuwere versie ervan verwijdert.

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

* Zie [Een Windows PowerShell-module schrijven](/powershell/scripting/developer/windows-powershell) voor meer informatie over het maken van PowerShell-modules.
