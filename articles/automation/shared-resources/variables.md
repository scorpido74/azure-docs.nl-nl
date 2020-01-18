---
title: Variabele assets in Azure Automation
description: Variabele assets zijn waarden die beschikbaar zijn voor alle runbooks en DSC-configuraties in Azure Automation.  In dit artikel worden de details van variabelen beschreven en wordt uitgelegd hoe u deze kunt gebruiken in tekstuele en grafische ontwerpen.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ac2c276f051155d7ba18ee91e4ca27acb0b35192
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76167994"
---
# <a name="variable-assets-in-azure-automation"></a>Variabele assets in Azure Automation

Variabele assets zijn waarden die beschikbaar zijn voor alle runbooks en DSC-configuraties in uw Automation-account. Ze kunnen worden beheerd vanuit de Azure Portal, Power shell, binnen een runbook of DSC-configuratie. Automation-variabelen zijn handig voor de volgende scenario's:

- Deel een waarde tussen meerdere runbooks of DSC-configuraties.

- Een waarde delen tussen meerdere taken van hetzelfde runbook of de DSC-configuratie.

- Een waarde beheren vanuit de portal of vanaf de Power shell-opdracht regel die wordt gebruikt door runbooks of DSC-configuraties, zoals een set algemene configuratie-items, zoals een specifieke lijst met VM-namen, een specifieke resource groep, een AD-domein naam en meer.  

Omdat Automation-variabelen persistent zijn, zijn ze beschikbaar, zelfs als het runbook of de DSC-configuratie mislukt. Met dit gedrag kan een waarde worden ingesteld met één runbook dat vervolgens wordt gebruikt door een andere, of wordt gebruikt door hetzelfde runbook of de DSC-configuratie de volgende keer dat deze wordt uitgevoerd.

Wanneer een variabele is gemaakt, kunt u opgeven dat deze is versleuteld. Versleutelde variabelen worden veilig opgeslagen in Azure Automation en de waarde ervan kan niet worden opgehaald uit de cmdlet [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) die wordt geleverd als onderdeel van de Azure PowerShell-module. De enige manier waarop een versleutelde waarde kan worden opgehaald, is afkomstig uit de **Get-AutomationVariable-** activiteit in een RUNBOOK of DSC-configuratie. Als u een versleutelde variabele wilt wijzigen in niet-versleuteld, moet u de variabele verwijderen en opnieuw maken als niet-versleuteld.

>[!NOTE]
>Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen bevatten. Deze assets worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. Deze sleutel wordt opgeslagen in een door het systeem beheerde Key Vault. Voordat u een beveiligde Asset opslaat, wordt de sleutel geladen uit Key Vault en vervolgens gebruikt om de Asset te versleutelen. Dit proces wordt beheerd door Azure Automation.

## <a name="variable-types"></a>Typen variabelen

Wanneer u een variabele met de Azure Portal maakt, moet u een gegevens type opgeven in de vervolg keuzelijst, zodat de portal het juiste besturings element kan weer geven voor het invoeren van de waarde van de variabele. De variabele is niet beperkt tot dit gegevens type. U moet de variabele instellen met behulp van Windows Power shell als u een waarde van een ander type wilt opgeven. Als u **niet gedefinieerd**opgeeft, wordt de waarde van de variabele ingesteld op **$Null**en moet u de waarde instellen met de cmdlet [set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) of de activiteit **set-AutomationVariable** . U kunt de waarde voor een complex type variabele niet maken of wijzigen in de portal, maar u hebt een waarde van elk type met behulp van Windows Power shell. Complexe typen worden geretourneerd als een [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

U kunt meerdere waarden opslaan voor één variabele door een matrix of hashtabel te maken en deze op te slaan in de variabele.

Hier volgt een lijst met beschik bare typen variabelen in Automation:

* Tekenreeks
* Geheel getal
* Datum/tijd
* Booleaans
* Null

## <a name="azurerm-powershell-cmdlets"></a>AzureRM Power shell-cmdlets

De cmdlets in de volgende tabel worden gebruikt voor AzureRM om Automation-referentie assets te maken en te beheren met Windows Power shell. Ze worden geleverd als onderdeel van de [AzureRM. Automation-module](/powershell/azure/overview), die beschikbaar is voor gebruik in Automation-RUNBOOKS en DSC-configuraties.

| Cmdlets | Beschrijving |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|De waarde van een bestaande variabele opgehaald.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Hiermee maakt u een nieuwe variabele en stelt u de waarde ervan in.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Hiermee verwijdert u een bestaande variabele.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Hiermee stelt u de waarde voor een bestaande variabele.|

## <a name="activities"></a>Activiteiten

De activiteiten in de volgende tabel worden gebruikt voor toegang tot variabelen in een runbook en DSC-configuraties. Het verschil tussen Get-AzureRmAutomationVariable en Get-AutomationVariable-cmdlets wordt boven aan het begin van dit document verduidelijkt.

| Activiteiten | Beschrijving |
|:---|:---|
|Get-AutomationVariable|De waarde van een bestaande variabele opgehaald.|
|Set-AutomationVariable|Hiermee stelt u de waarde voor een bestaande variabele.|

> [!NOTE]
> Vermijd het gebruik van variabelen in de para meter – name van **Get-AutomationVariable** in een runbook-of DSC-configuratie, omdat dit de detectie van afhankelijkheden tussen RUNBOOKS of DSC-configuratie en Automation-variabelen tijdens de ontwerp fase kan bemoeilijken.

De functies in de volgende tabel worden gebruikt om variabelen in een Python2-runbook te openen en op te halen.

|Python2-functies|Beschrijving|
|:---|:---|
|automationassets.get_automation_variable|De waarde van een bestaande variabele opgehaald. |
|automationassets.set_automation_variable|Hiermee stelt u de waarde voor een bestaande variabele. |

> [!NOTE]
> U moet de module ' automationassets ' boven aan uw python-runbook importeren om toegang te krijgen tot de Asset-functies.

## <a name="creating-a-new-automation-variable"></a>Een nieuwe Automation-variabele maken

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Een nieuwe variabele maken met de Azure Portal

1. Klik vanuit uw Automation-account op de tegel **assets** en selecteer vervolgens op de Blade **assets** de optie **variabelen**.
2. Selecteer **een variabele toevoegen**op de tegel **variabelen** .
3. Voltooi de opties op de Blade **nieuwe variabele** en **Klik op** de nieuwe variabele opslaan.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Een nieuwe variabele maken met Windows PowerShell

Met de cmdlet [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) maakt u een nieuwe variabele en stelt u de oorspronkelijke waarde in. U kunt de waarde ophalen met behulp van [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Als de waarde een eenvoudig type is, wordt hetzelfde type geretourneerd. Als het een complex type is, wordt er een **PSCustomObject** geretourneerd.

De volgende voorbeeld opdrachten laten zien hoe u een variabele van het type teken reeks maakt en de waarde ervan als resultaat geeft.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

De volgende voorbeeld opdrachten laten zien hoe u een variabele met een complex type maakt en vervolgens de eigenschappen ervan retourneert. In dit geval wordt een object van de virtuele machine uit **Get-AzureRmVm** gebruikt.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Een variabele gebruiken in een runbook-of DSC-configuratie

Gebruik de activiteit **set-AutomationVariable** om de waarde van een Automation-variabele in een Power shell-RUNBOOK of DSC-configuratie in te stellen en haal de **AutomationVariable** op om deze op te halen. U moet de cmdlets **set-AzureRMAutomationVariable** en **Get-AzureRMAutomationVariable** niet gebruiken in een runbook of DSC-configuratie, omdat deze minder efficiënt zijn dan de werk stroom activiteiten. U kunt ook de waarde van beveiligde variabelen niet ophalen met **Get-AzureRMAutomationVariable**. De enige manier om vanuit een runbook of DSC-configuratie een nieuwe variabele te maken, is met behulp van de cmdlet [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) .

### <a name="textual-runbook-samples"></a>Tekstuele runbook-voor beelden

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Een eenvoudige waarde van een variabele instellen en ophalen

De volgende voorbeeld opdrachten laten zien hoe u een variabele kunt instellen en ophalen in een tekst runbook. In dit voor beeld wordt ervan uitgegaan dat variabelen van het type geheel getal met de naam *NumberOfIterations* en *NumberOfRunnings* en een variabele van het type teken reeks met de naam *SampleMessage* zijn gemaakt.

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Een variabele instellen en ophalen in Python2

De volgende voorbeeld code laat zien hoe u een variabele gebruikt, een variabele instelt en een uitzonde ring afhandelt voor een niet-bestaande variabele in een Python2-runbook.

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
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Voor beelden van grafische runbook

In een grafisch runbook voegt u de **Get-AutomationVariable** -of **set-AutomationVariable** toe door met de rechter muisknop op de variabele in het deel venster Bibliotheek van de grafische editor te klikken en de gewenste activiteit te selecteren.

![Variabele toevoegen aan canvas](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Waarden instellen in een variabele

De volgende afbeelding toont voorbeeld activiteiten om een variabele met een eenvoudige waarde in een grafisch runbook bij te werken. In dit voor beeld haalt **Get-AzureRmVM** één virtuele machine van Azure op en wordt de computer naam opgeslagen in een bestaande automatiserings variabele met een type teken reeks. Het maakt niet uit of de [koppeling een pijp lijn of sequencer is](../automation-graphical-authoring-intro.md#links-and-workflow) , omdat u slechts één object in de uitvoer verwacht.

![Eenvoudige variabele instellen](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het koppelen van activiteiten in grafische ontwerpen [koppelingen in grafische ontwerpen](../automation-graphical-authoring-intro.md#links-and-workflow)
- Zie [Mijn eerste grafische runbook](../automation-first-runbook-graphical.md) om aan de slag te gaan met grafische runbooks
