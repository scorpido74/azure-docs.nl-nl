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
ms.openlocfilehash: 2a567b156c3a7e0c48e2c2f8db2d268e0eb3307c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770950"
---
# <a name="manage-modules-in-azure-automation"></a>Modules beheren in Azure Automation

Azure Automation stelt u in staat PowerShell-modules te importeren om cmdlets in runbooks en DSC-resources in DSC-configuraties in te schakelen. Modules die worden gebruikt in Azure Automation zijn:

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM.Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Interne `Orchestrator.AssetManagement.Cmdlets` module voor de log-analyse-agent voor Windows
* [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)
* Andere PowerShell-modules
* Aangepaste modules die u maakt 

Wanneer u een Automatiseringsaccount maakt, importeert Azure Automation standaard enkele modules. Zie [Standaardmodules](#default-modules).

Wanneer Azure Automation runbook- en DSC-compilatietaken uitvoert, worden de modules geladen in zandbakken waar de runbooks kunnen worden uitgevoerd en de DSC-configuraties kunnen compileren. Automatisering plaatst ook automatisch DSC-resources in modules op de DSC pull-server. Machines kunnen de resources opvragen wanneer ze de DSC-configuraties toepassen.

>[!NOTE]
>Zorg ervoor dat u alleen de modules importeert die uw runbooks en DSC-configuraties daadwerkelijk nodig hebben. Importeer de rollupmodule in ieder geval niet, bijvoorbeeld Az.Automation.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](../automation-update-azure-modules.md)

## <a name="default-modules"></a>Standaardmodules

In de volgende tabel worden modules weergegeven die Azure Automation standaard importeert wanneer u uw Automatiseringsaccount maakt. Automatisering kan nieuwere versies van deze modules importeren. U de oorspronkelijke versie echter niet uit uw Automatiseringsaccount verwijderen, zelfs niet als u een nieuwere versie verwijdert. Houd er rekening mee dat deze standaardmodules verschillende AzureRM-modules bevatten. 

> [!NOTE]
> We raden u aan modules en runbooks te wijzigen in automatiseringsaccounts die oplossingen bevatten. 

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


Az.Automation modules hebben de voorkeur in uw runbooks en DSC configuraties. Azure Automation importeert de rollup Az-module echter niet automatisch in nieuwe of bestaande automatiseringsaccounts. Zie [Migreren naar Az-modules voor](#migrating-to-az-modules)meer informatie over het werken met deze modules.

## <a name="internal-cmdlets"></a>Interne cmdlets

In de volgende tabel worden de interne `Orchestrator.AssetManagement.Cmdlets` cmdlets gedefinieerd die door de module worden ondersteund. Gebruik deze in uw runbooks en DSC-configuraties om te communiceren met automatiseringselementen binnen het Automatiseringsaccount. De cmdlets zijn ontworpen om geheimen op te halen uit versleutelde variabelen, referenties en versleutelde verbindingen. 

> [!NOTE]
> Azure PowerShell-cmdlets kunnen niet de assetgeheimen verkrijgen die de interne cmdlets kunnen ophalen. 

|Naam|Beschrijving|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Deze interne cmdlets zijn beschikbaar op een Windows Hybrid Runbook Worker, maar niet op een Linux Hybrid Runbook Worker. Ze worden uitgevoerd vanuit een Orchestrator sandbox, die wordt gebruikt door de hybride werknemer.  Het gebruik ervan vereist geen impliciete verbinding met Azure, zoals bij het gebruik van een Run As-account voor verificatie.

In plaats van de interne cmdlets te gebruiken, gebruikt u de cmdlets van Az of AzureRM voor runbooks en -configuraties die rechtstreeks op de computer of tegen resources in uw omgeving worden uitgevoerd. In deze gevallen moet u impliciet verbinding maken met Azure bij het gebruik van de cmdlets, zoals bij het gebruik van een Run As-account om te verifiëren naar Azure. 

## <a name="modules-supporting-get-automationpscredential"></a>Modules die Get-AutomationPSCredential ondersteunen

Voor lokale ontwikkeling met behulp van `Get-AutomationPSCredential` de Azure Automation Authoring Toolkit maakt de cmdlet deel uit van de assemblage [azureautomationAuthoringToolkit.](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9) Voor Azure die met de context Automatisering `Orchestrator.AssetManagement.Cmdlets`werkt, bevindt de cmdlet zich in . Zie [Credential assets in Azure Automation](credentials.md)voor meer informatie over het gebruik van referenties in Azure Automation.

## <a name="migrating-to-az-modules"></a>Migreren naar Az-modules

Er zijn verschillende dingen waarmee u rekening moet houden bij het gebruik van de Az-modules in Azure Automation:

* Oplossingen op een hoger niveau in uw Automation-account kunnen runbooks en modules gebruiken. Daarom kan het bewerken van runbooks of het upgraden van modules mogelijk problemen veroorzaken met uw oplossingen. U moet alle runbooks en oplossingen zorgvuldig testen in een apart Automation-account voordat u de Az-modules importeert. 

* Wijzigingen aan modules kunnen een negatieve invloed hebben op de [Start/Stop VM's tijdens de oplossing buiten kantooruren.](../automation-solution-vm-management.md) 

* Als u een Az-module importeert in uw Automatiseringsaccount, wordt de module niet automatisch geïmporteerd in de PowerShell-sessie die boeken gebruiken. Modules worden in de volgende situaties in de PowerShell-sessie geïmporteerd:

    * Wanneer een runbook een cmdlet uit een module aanroept
    * Wanneer een runbook de module `Import-Module` expliciet importeert met de cmdlet
    * Wanneer een runbook een andere afhankelijke module importeert

Nadat u de migratie van uw modules hebt voltooid, probeert u geen runbooks te starten met AzureRM-modules op het automatiseringsaccount. Het wordt ook aanbevolen om AzureRM-modules in het account niet te importeren of bij te werken. Overweeg het account gemigreerd naar Az.Automation, en werken met Az modules alleen. 

>[!IMPORTANT]
>Wanneer u een nieuw Automatiseringsaccount maakt, installeert Azure Automation de AzureRM-modules standaard. U de runbooks van de zelfstudie nog steeds bijwerken met AzureRM-cmdlets. U moet deze runbooks echter niet uitvoeren.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Alle runbooks met AzureRM-modules stoppen en uitplannen en uitplannen

Als u ervoor wilt zorgen dat u geen bestaande runbooks uitvoert die AzureRM-modules gebruiken, stopt en maakt u alle betreffende runbooks niet meer. U zien welke schema's er bestaan en welke schema's moeten worden verwijderd door code uit te voeren die vergelijkbaar is met dit voorbeeld:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Het is belangrijk om elk schema afzonderlijk te bekijken om ervoor te zorgen dat u het in de toekomst opnieuw plannen voor uw runbooks, indien nodig.

### <a name="import-the-az-modules"></a>De Az-modules importeren

In dit gedeelte wordt uitgelegd hoe u de Az-modules in de Azure-portal importeren. Vergeet niet om alleen de Az-modules te importeren die u nodig hebt, niet de gehele Az.Automation-module. Aangezien [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) een afhankelijkheid is voor de andere Az-modules, moet u deze module vóór andere modules importeren.

1. Selecteer **modules** onder Gedeelde **bronnen**in uw automatiseringsaccount. 
2. Klik **op Galerie bladeren** om de pagina Bladeren door de galerij te openen.  
3. Voer in de zoekbalk bijvoorbeeld de `Az.Accounts`modulenaam in. 
4. Klik op de pagina PowerShell-module op **Importeren** om de module te importeren in uw automatiseringsaccount.

    ![Modules importeren in automatiseringsaccount](../media/modules/import-module.png)

Dit importproces kan ook via de [PowerShell Gallery](https://www.powershellgallery.com) worden uitgevoerd door te zoeken naar de module die u wilt importeren. Zodra u de module hebt gevonden, selecteert u deze, kiest u het tabblad **Azure Automation** en klikt u op **Implementeren voor Azure Automation**.

![Modules rechtstreeks importeren vanuit galerie](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Uw runbooks testen

Zodra u de Az-modules hebt geïmporteerd in het automatiseringsaccount, u beginnen met het bewerken van de runbooks om de nieuwe modules te gebruiken. De meeste cmdlets hebben dezelfde namen als voor de AzureRM-modules, behalve dat het AzureRM-voorvoegsel (of AzureRm) is gewijzigd in Az. Zie lijst met [uitzonderingen](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)voor een lijst met modules die deze naamgevingsconventie niet volgen.

Een manier om de wijziging van een runbook te testen `Enable-AzureRmAlias -Scope Process` om de nieuwe cmdlets te gebruiken is door gebruik te maken van aan het begin van het runbook. Door deze opdracht toe te voegen aan uw runbook, kan het script zonder wijzigingen worden uitgevoerd.

## <a name="authoring-modules"></a>Ontwerpmodules

We raden u aan de overwegingen in deze sectie te volgen wanneer u een PowerShell-module maakt voor gebruik in Azure Automation.

### <a name="references-to-azurerm-and-az"></a>Verwijzingen naar AzureRM en Az

Als u verwijst naar de Az-modules in uw module, moet u ervoor zorgen dat u niet ook verwijst naar de AzureRM-modules. U beide sets modules niet tegelijkertijd gebruiken. 

### <a name="version-folder"></a>Versiemap

Neem geen versiemap op in het **.zip-pakket** voor uw module.  Dit probleem is minder een punt van zorg voor runbooks, maar veroorzaakt wel een probleem met de DSC-service (State Configuration). Azure Automation maakt de versiemap automatisch wanneer de module wordt gedistribueerd naar knooppunten die door DSC worden beheerd. Als er een versiemap bestaat, komt u uit op twee instanties. Hier is een voorbeeldmapstructuur voor een DSC-module:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Help-informatie

Voeg een samenvatting, beschrijving en help URI toe voor elke cmdlet in uw module. In PowerShell u help-informatie voor `Get-Help` cmdlets definiëren met behulp van de cmdlet. In het volgende voorbeeld ziet u hoe u een synopsis definieert en URI helpt in een **PSM1-modulebestand.**

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

### <a name="connection-type"></a>Type verbinding

Als de module verbinding maakt met een externe service, definieert u een [verbindingstype](#adding-a-connection-type-to-your-module). Elke cmdlet in de module moet een verbindingsobject (een instantie van dat verbindingstype) als parameter accepteren. Gebruikers brengen parameters van het verbindingselement in kaart met de overeenkomstige parameters van de cmdlet telkens wanneer ze een cmdlet aanroepen. In het volgende voorbeeld van runbook, gebaseerd op het voorbeeld in `ContosoConnection` de vorige sectie, wordt een Contoso-verbindingselement gebruikt dat is aangeroepen om toegang te krijgen tot Contoso-bronnen en gegevens van de externe service te retourneren. In dit voorbeeld worden de velden `UserName` `Password` toegewezen aan `PSCredential` de eigenschappen en eigenschappen van een object en vervolgens doorgegeven aan de cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Een eenvoudigere en betere manier om dit gedrag te benaderen is door het verbindingsobject rechtstreeks door te geven aan de cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  U vergelijkbaar gedrag voor uw cmdlets inschakelen door ze toe te staan een verbindingsobject rechtstreeks als parameter te accepteren, in plaats van alleen verbindingsvelden voor parameters. Meestal wilt u een parameterset voor elk, zodat een gebruiker die Azure Automation niet gebruikt, uw cmdlets kan aanroepen zonder een hashtable te maken om als verbindingsobject te fungeren. De parameterset `UserAccount` wordt gebruikt om de eigenschappen van het verbindingsveld door te geven. `ConnectionObject`hiermee u de verbinding dwars door.

### <a name="output-type"></a>Uitvoertype

Definieer het uitvoertype voor alle cmdlets in uw module. Als u een uitvoertype voor een cmdlet definieert, u intelliSense met ontwerptijd helpen bij het bepalen van de uitvoereigenschappen van de cmdlet tijdens het ontwerpen. Deze praktijk is vooral handig tijdens het schrijven van grafische runbook, waarvoor design-time kennis de sleutel is tot een eenvoudige gebruikerservaring met uw module.

Voeg `[OutputType([<MyOutputType>])]` `MyOutputType` waar is een geldig type. Zie `OutputType` [Functiefunctie Functietypeattribuut](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)voor meer informatie. De volgende code is `OutputType` een voorbeeld van het toevoegen aan een cmdlet:

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

### <a name="cmdlet-state"></a>Cmdlet-status

Maak alle cmdlets in uw module stateloos. Meerdere runbook-taken kunnen tegelijkertijd `AppDomain` in hetzelfde en hetzelfde proces en sandbox worden uitgevoerd. Als er een status wordt gedeeld op deze niveaus, kunnen taken elkaar beïnvloeden. Dit gedrag kan leiden tot intermitterende en moeilijk te diagnosticeren problemen. Hier is een voorbeeld van wat niet te doen:

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

### <a name="module-dependency"></a>Afhankelijk van de module

Zorg ervoor dat de module volledig is opgenomen in een pakket dat kan worden gekopieerd met behulp van xcopy. Azure Automation-modules worden gedistribueerd naar de sandboxes voor automatisering wanneer runbooks worden uitgevoerd. De modules moeten onafhankelijk van de host werken die ze uitvoert. 

U moet in staat zijn om een modulepakket dicht te ritsen en te verplaatsen en het als normaal te laten functioneren wanneer het wordt geïmporteerd in de PowerShell-omgeving van een andere host. Zorg ervoor dat de module niet afhankelijk is van bestanden buiten de modulemap die wordt opritsen wanneer de module wordt geïmporteerd in Azure Automation. 

Uw module mag niet afhankelijk zijn van unieke registerinstellingen op een host. Voorbeelden zijn de instellingen die worden gemaakt wanneer een product wordt geïnstalleerd. 

### <a name="module-file-paths"></a>Bestandspaden module

Zorg ervoor dat alle bestanden in de module paden hebben met minder dan 140 tekens. Paden met een lengte van meer dan 140 tekens veroorzaken problemen met het importeren van runbooks. Azure Automation kan een bestand met een padgrootte van meer dan `Import-Module`140 tekens niet importeren in de PowerShell-sessie met .

## <a name="importing-modules"></a>Modules importeren

In deze sectie worden verschillende manieren gedefinieerd waarop u een module importeren in uw Automatiseringsaccount. 

### <a name="import-modules-in-azure-portal"></a>Modules importeren in Azure-portal

Ga als lid van de module naar een azure-portal:

1. Navigeer naar uw Automatiseringsaccount.
2. Selecteer **modules** onder **Gedeelde bronnen**.
3. Klik **op Een module toevoegen**. 
4. Selecteer het **.zip-bestand** dat de module bevat.
5. Klik op **OK** om het proces te starten.

### <a name="import-modules-using-powershell"></a>Modules importeren met PowerShell

U de cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) gebruiken om een module in uw Automation-account te importeren. De cmdlet neemt een URL voor een module .zip-pakket.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

U dezelfde cmdlet ook gebruiken om een module rechtstreeks uit PowerShell Gallery te importeren. Zorg ervoor `ModuleName` dat `ModuleVersion` u grijpen en van [PowerShell Gallery](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Modules importeren uit PowerShell Gallery

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

## <a name="deleting-modules"></a>Modules verwijderen

Als u problemen hebt met een module of als u moet terugdraaien naar een vorige versie van een module, u deze verwijderen uit uw Automatiseringsaccount. U de oorspronkelijke versies van de [standaardmodules](#default-modules) die worden geïmporteerd wanneer u een Automatiseringsaccount maakt, niet verwijderen. Als de module die moet worden verwijderd een nieuwere versie van een van de [standaardmodules](#default-modules)is, rolt deze terug naar de versie die is geïnstalleerd met uw Automation-account. Anders wordt elke module die u uit uw Automatiseringsaccount verwijdert, verwijderd.

### <a name="delete-modules-in-azure-portal"></a>Modules verwijderen in Azure-portal

Ga als lid van het werk om een module in de Azure-portal te verwijderen:

1. Navigeer naar uw automatiseringsaccount en selecteer **Modules** onder **Gedeelde bronnen**. 
2. Selecteer de module die u wilt verwijderen. 
3. Selecteer **verwijderen**op de pagina **Module** . Als deze module een van de [standaardmodules](#default-modules)is, rolt deze terug naar de versie die bestond toen het automatiseringsaccount werd gemaakt.

### <a name="delete-modules-using-powershell"></a>Modules verwijderen met PowerShell

Voer de volgende opdracht uit om een module via PowerShell te verwijderen:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Een verbindingstype toevoegen aan uw module

U een aangepast [verbindingstype](../automation-connections.md) bieden dat u in uw Automatiseringsaccount gebruiken door een optioneel metagegevensbestand toe te voegen aan uw module. In dit bestand wordt een Azure Automation-verbindingstype opgegeven dat moet worden gebruikt met de cmdlets van de module in uw Automatiseringsaccount. Zie [Een PowerShell Script Module schrijven voor](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7)meer informatie over het schrijven van een PowerShell-module.

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

## <a name="next-steps"></a>Volgende stappen

* Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0)voor meer informatie over het gebruik van Azure PowerShell-modules.
* Zie [Een Windows PowerShell-module schrijven](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7)voor meer informatie over het maken van PowerShell-modules.
