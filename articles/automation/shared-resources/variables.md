---
title: Variabele-assets in Azure Automation
description: Variabele elementen zijn waarden die beschikbaar zijn voor alle runbooks en DSC-configuraties in Azure Automation.  In dit artikel worden de details van variabelen uitgelegd en hoe u ermee werken in zowel tekstuele als grafische ontwerpen.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d4a4a92feb3e1b400c0f40076148f7898c4bdef1
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365827"
---
# <a name="variable-assets-in-azure-automation"></a>Variabele-assets in Azure Automation

Variabele elementen zijn waarden die beschikbaar zijn voor alle runbooks en DSC-configuraties in uw Automatiseringsaccount. U ze beheren vanuit de Azure-portal, vanuit PowerShell, binnen een runbook of in een DSC-configuratie.

Automatiseringsvariabelen zijn handig voor de volgende scenario's:

- Een waarde delen tussen meerdere runbooks of DSC-configuraties.

- Een waarde delen tussen meerdere taken uit dezelfde runbook- of DSC-configuratie.

- Een waarde beheren die wordt gebruikt door runbooks of DSC-configuraties vanaf de portal of vanaf de PowerShell-opdrachtregel. Een voorbeeld is een set algemene configuratie-items, zoals een specifieke lijst met VM-namen, een specifieke brongroep, een AD-domeinnaam en meer.  

Azure Automation blijft variabelen gebruiken en maakt ze beschikbaar, zelfs als een runbook- of DSC-configuratie mislukt. Met dit gedrag kan één runbook- of DSC-configuratie een waarde instellen die vervolgens wordt gebruikt door een ander runbook, of door dezelfde runbook- of DSC-configuratie de volgende keer dat deze wordt uitgevoerd.

Azure Automation slaat elke versleutelde variabele veilig op. Wanneer u een variabele maakt, u de versleuteling en opslag ervan door Azure Automation opgeven als een beveiligd element. Andere beveiligde elementen zijn referenties, certificaten en verbindingen. Azure Automation versleutelt deze elementen en slaat ze op met behulp van een unieke sleutel die wordt gegenereerd voor elk Automatiseringsaccount. De sleutel wordt opgeslagen in een door het systeem beheerde Key Vault. Voordat u een beveiligde asset opslaat, laadt Azure Automation de sleutel uit de Key Vault en gebruikt deze vervolgens om het element te versleutelen. 

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](../automation-update-azure-modules.md)

## <a name="variable-types"></a>Variabele typen

Wanneer u een variabele maakt met de Azure-portal, moet u een gegevenstype opgeven in de vervolgkeuzelijst, zodat de portal het juiste besturingselement kan weergeven voor het invoeren van de variabele waarde. De volgende zijn variabele typen die beschikbaar zijn in Azure Automation:

* Tekenreeks
* Geheel getal
* DateTime
* Booleaans
* Null

De variabele is niet beperkt tot het aangewezen gegevenstype. U moet de variabele instellen met Windows PowerShell als u een waarde van een ander type wilt opgeven. Als u `Not defined`aangeeft, wordt de waarde van de variabele ingesteld op Null en moet u `Set-AutomationVariable` de waarde instellen met de cmdlet [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) of de activiteit.

U de Azure-portal niet gebruiken om de waarde voor een complex variabel type te maken of te wijzigen. U echter een waarde van elk type bieden met Windows PowerShell. Complexe typen worden opgehaald als [een PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

U meerdere waarden opslaan in één variabele door een array of hashtable te maken en deze op te slaan in de variabele.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>PowerShell-cmdlets die variabele activa maken en beheren

Voor de Az-module worden de cmdlets in de volgende tabel gebruikt om variabele assets voor automatisering te maken en te beheren met Windows PowerShell. Ze worden verzonden als onderdeel van de [Az.Automation-module](/powershell/azure/overview), die beschikbaar is voor gebruik in automation runbooks en DSC-configuraties.

| Cmdlet | Beschrijving |
|:---|:---|
|[Get-AzAutomationVariabele](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Hiermee haalt u de waarde van een bestaande variabele op. U deze cmdlet niet gebruiken om de waarde van een gecodeerde variabele op te halen. De enige manier om dit `Get-AutomationVariable` te doen is door de activiteit te gebruiken in een runbook- of DSC-configuratie. |
|[Nieuw-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Hiermee maakt u een nieuwe variabele en stelt u de waarde ervan in.|
|[Variabele voor het verwijderen van azautomation](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Hiermee verwijdert u een bestaande variabele.|
|[Set-azautomationvariabele](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Hiermee stelt u de waarde voor een bestaande variabele in. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Activiteiten om toegang te krijgen tot variabelen in runbooks en DSC-configuraties

De activiteiten in de volgende tabel worden gebruikt om toegang te krijgen tot variabelen in runbooks en DSC-configuraties. De cmdlets voor deze activiteiten `Orchestrator.AssetManagement.Cmdlets`worden geleverd met de globale module.

| Activiteit | Beschrijving |
|:---|:---|
|`Get-AutomationVariable`|Hiermee haalt u de waarde van een bestaande variabele op.|
|`Set-AutomationVariable`|Hiermee stelt u de waarde voor een bestaande variabele in.|

> [!NOTE]
> Vermijd het gebruik `Name` van `Get-AutomationVariable` variabelen in de parameter van in een runbook- of DSC-configuratie. Het gebruik van deze parameter kan het ontdekken van afhankelijkheden tussen runbooks of DSC-configuraties en automatiseringsvariabelen tijdens het ontwerp bemoeilijkt.

Houd `Get-AutomationVariable` er rekening mee dat dit niet werkt in PowerShell, maar alleen in een runbook- of DSC-configuratie. Als u bijvoorbeeld de waarde van een versleutelde variabele wilt zien, u een runbook maken om de variabele te krijgen en deze vervolgens naar de uitvoerstroom te schrijven:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>Functies voor toegang tot variabelen in Python 2 runbooks

De functies in de volgende tabel worden gebruikt om toegang te krijgen tot variabelen in een Python 2-runbook.

|Python 2- functies|Beschrijving|
|:---|:---|
|`automationassets.get_automation_variable`|Hiermee haalt u de waarde van een bestaande variabele op. |
|`automationassets.set_automation_variable`|Hiermee stelt u de waarde voor een bestaande variabele in. |

> [!NOTE]
> U moet `automationassets` de module boven aan uw Python-runbook importeren om toegang te krijgen tot de assetfuncties.

## <a name="working-with-automation-variables"></a>Werken met automatiseringsvariabelen

>[!NOTE]
>Als u de versleuteling voor een variabele wilt verwijderen, moet u de variabele verwijderen en opnieuw maken als onversleuteld.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Een nieuwe variabele maken met de Azure-portal

1. Klik in uw automatiseringsaccount op de tegel **Activa** en vervolgens op het blad **Activa** en selecteer **Variabelen**.
2. Selecteer op de tegel **Variabelen** de optie **Een variabele toevoegen**.
3. Voltooi de opties op het nieuwe **variabele** blad en klik op **Maken** om de nieuwe variabele op te slaan.

> [!NOTE]
> Zodra u een versleutelde variabele hebt opgeslagen, kan deze niet meer worden bekeken in de portal. Het kan alleen worden bijgewerkt.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Een variabele maken en gebruiken in Windows PowerShell

Een PowerShell-script `New-AzAutomationVariable` gebruikt de cmdlet of het equivalent van de AzureRM-module om een nieuwe variabele te maken en de oorspronkelijke waarde in te stellen. Als de variabele is versleuteld, moet `Encrypted` de aanroep de parameter gebruiken.

Het script haalt de waarde van de variabele op met [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0). Als de waarde een eenvoudig type is, haalt de cmdlet hetzelfde type op. Als het een complex type `PSCustomObject` is, wordt een type opgehaald.

>[!NOTE]
>Een PowerShell-script kan geen versleutelde waarde ophalen. De enige manier om dit `Get-AutomationVariable` te doen is door een activiteit te gebruiken in een runbook- of DSC-configuratie.

In het volgende voorbeeld ziet u hoe u een variabele van type Tekenreeks maakt en de waarde ervan retourneert.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

In het volgende voorbeeld ziet u hoe u een variabele maakt met een complex type en vervolgens de eigenschappen ervan ophaalt. In dit geval wordt een virtueel machineobject van [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) gebruikt.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Een variabele maken en gebruiken in een runbook- of DSC-configuratie

De enige manier om een nieuwe variabele te maken vanuit een `New-AzAutomationVariable` runbook- of DSC-configuratie is door de cmdlet of het equivalent van de AzureRM-module te gebruiken. Het script gebruikt deze cmdlet om de initiële waarde van de variabele in te stellen. Het script kan vervolgens `Get-AzAutomationVariable`de waarde ophalen met behulp van . Als de waarde een eenvoudig type is, wordt datzelfde type opgehaald. Als het een complex type `PSCustomObject` is, wordt een type opgehaald.

>[!NOTE]
>De enige manier om een versleutelde waarde `Get-AutomationVariable` op te halen, is door de activiteit in de runbook- of DSC-configuratie te gebruiken. 

### <a name="textual-runbook-samples"></a>Tekstuele runbook-voorbeelden

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Een eenvoudige waarde uit een variabele instellen en ophalen

In de volgende voorbeeldopdrachten wordt uitgelegd hoe u een variabele in een tekstueel runbook instelt en ophaalt. In dit voorbeeld wordt uitgegaan van `NumberOfIterations` `NumberOfRunnings` de creatie van `SampleMessage`gehele variabelen met de naam en een tekenreeksvariabele met de naam .

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Een variabele instellen en ophalen in een Python 2-runbook

In het volgende voorbeeld ziet u hoe u een variabele gebruikt, een variabele instelt en een uitzondering verwerkt voor een niet-bestaande variabele in een Python 2-runbook.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Grafische runbook-voorbeelden

In een grafisch runbook kunt `Get-AutomationVariable` `Set-AutomationVariable` u de of activiteit toevoegen. Klik met de rechtermuisknop op de variabele in het deelvenster Bibliotheek van de grafische editor en selecteer de gewenste activiteit.

![Variabele toevoegen aan canvas](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Waarden instellen in een variabele

In de volgende afbeelding ziet u voorbeeldactiviteiten om een variabele bij te werken met een eenvoudige waarde in een grafisch runbook. Haalt in `Get-AzVM` dit voorbeeld één virtuele Azure-machine op en slaat de computernaam op in een bestaande variabele van de tekenreeks automatisering. Het maakt niet uit of de [koppeling een pijplijn of reeks is,](../automation-graphical-authoring-intro.md#links-and-workflow) omdat de code slechts één object in de uitvoer verwacht.

![Eenvoudige variabele instellen](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [Koppelingen in grafische ontwerpen](../automation-graphical-authoring-intro.md#links-and-workflow)voor meer informatie over het verbinden van activiteiten in grafische ontwerpen.
- Zie Mijn eerste grafische runbook om aan de slag te gaan met grafische [runbooks.](../automation-first-runbook-graphical.md)
