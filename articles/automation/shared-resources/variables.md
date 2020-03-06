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
ms.openlocfilehash: 4ce56b64502904308f45c74a5471447d93419452
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303049"
---
# <a name="variable-assets-in-azure-automation"></a>Variabele assets in Azure Automation

Variabele assets zijn waarden die beschikbaar zijn voor alle runbooks en DSC-configuraties in uw Automation-account. U kunt deze beheren vanuit het Azure Portal, vanuit Power shell, binnen een runbook of in een DSC-configuratie.

Automation-variabelen zijn handig voor de volgende scenario's:

- Het delen van een waarde tussen meerdere runbooks of DSC-configuraties.

- Het delen van een waarde tussen meerdere taken vanuit hetzelfde runbook of de DSC-configuratie.

- Het beheren van een waarde die wordt gebruikt door runbooks of DSC-configuraties vanuit de portal of vanaf de Power shell-opdracht regel. Een voor beeld is een set algemene configuratie-items, zoals een specifieke lijst met VM-namen, een specifieke resource groep, een AD-domein naam en meer.  

Omdat Automation-variabelen persistent zijn, zijn ze beschikbaar, zelfs als het runbook of de DSC-configuratie mislukt. Dit gedrag maakt het mogelijk dat een runbook of DSC-configuratie een waarde instelt die vervolgens wordt gebruikt door een ander runbook of door hetzelfde runbook of de DSC-configuratie de volgende keer dat deze wordt uitgevoerd.

Wanneer u een variabele maakt, kunt u de versleuteling en opslag opgeven door Azure Automation als een beveiligd activum. Andere beveiligde assets zijn referenties, certificaten en verbindingen. Azure Automation versleutelt deze assets en slaat ze op met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. De sleutel wordt opgeslagen in een door het systeem beheerde Key Vault. Voordat u een beveiligde Asset opslaat, wordt Azure Automation de sleutel uit de Key Vault geladen en wordt deze vervolgens gebruikt om de Asset te versleutelen. 

Azure Automation elke versleutelde variabele veilig op te slaan. De waarde kan niet worden opgehaald met de cmdlet [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) die wordt geleverd als onderdeel van de module Azure PowerShell. De enige manier om een versleutelde waarde op te halen, is met behulp van de **Get-AutomationVariable-** activiteit in een RUNBOOK of DSC-configuratie.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Typen variabelen

Wanneer u een variabele met de Azure Portal maakt, moet u een gegevens type in de vervolg keuzelijst opgeven, zodat de portal het juiste besturings element kan weer geven voor het invoeren van de waarde van de variabele. Hieronder ziet u de beschik bare variabele typen in Azure Automation:

* Tekenreeks
* Geheel getal
* DateTime
* Booleaans
* Null

De variabele is niet beperkt tot het aangewezen gegevens type. U moet de variabele instellen met behulp van Windows Power shell als u een waarde van een ander type wilt opgeven. Als u **niet gedefinieerd**opgeeft, wordt de waarde van de variabele ingesteld op **Null**en moet u de waarde instellen met de cmdlet [set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) of de activiteit **set-AutomationVariable** .

U kunt de portal niet gebruiken om de waarde voor een complex type variabele te maken of te wijzigen. U kunt echter een waarde van elk type opgeven met behulp van Windows Power shell. Complexe typen worden opgehaald als een [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

U kunt meerdere waarden opslaan voor één variabele door een matrix of hashtabel te maken en deze op te slaan in een variabele.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Power shell-cmdlets waarmee variabele assets worden gemaakt en beheerd

Voor de module AZ worden de cmdlets in de volgende tabel gebruikt voor het maken en beheren van variabele-automatiserings elementen met Windows Power shell. Ze worden geleverd als onderdeel van de [module AZ. Automation](/powershell/azure/overview), die beschikbaar is voor gebruik in Automation-RUNBOOKS en DSC-configuraties.

| Cmdlet | Beschrijving |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | De waarde van een bestaande variabele opgehaald.|
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Hiermee maakt u een nieuwe variabele en stelt u de waarde ervan in.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Hiermee verwijdert u een bestaande variabele.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Hiermee stelt u de waarde voor een bestaande variabele.|

## <a name="activities-to-access-variables"></a>Activiteiten voor toegang tot variabelen

De activiteiten in de volgende tabel worden gebruikt voor toegang tot variabelen in runbooks en DSC-configuraties. Het verschil tussen **Get-AzAutomationVariable** en **Get-AutomationVariable** wordt in het begin van dit artikel uitgelegd op versleutelde variabelen.

| Activiteit | Beschrijving |
|:---|:---|
|**Get-AutomationVariable**|De waarde van een bestaande variabele opgehaald.|
|**Set-AutomationVariable**|Hiermee stelt u de waarde voor een bestaande variabele.|

> [!NOTE]
> Vermijd het gebruik van variabelen in de para meter *name* van **Get-AutomationVariable** in een runbook-of DSC-configuratie. Het gebruik van deze para meter kan de detectie van afhankelijkheden tussen runbooks of DSC-configuraties en Automation-variabelen tijdens ontwerp tijd bemoeilijken.

De functies in de volgende tabel worden gebruikt om variabelen in een Python2-runbook te openen en op te halen.

|Python2-functies|Beschrijving|
|:---|:---|
|automationassets.get_automation_variable|De waarde van een bestaande variabele opgehaald. |
|automationassets.set_automation_variable|Hiermee stelt u de waarde voor een bestaande variabele. |

> [!NOTE]
> U moet de **automationassets** -module boven aan uw python-runbook importeren om toegang te krijgen tot de Asset-functies.

## <a name="creating-a-new-automation-variable"></a>Een nieuwe Automation-variabele maken

### <a name="create-a-new-variable-using-the-azure-portal"></a>Een nieuwe variabele maken met behulp van de Azure Portal

1. Klik vanuit uw Automation-account op de tegel **assets** en selecteer vervolgens op de Blade **assets** de optie **variabelen**.
2. Selecteer **een variabele toevoegen**op de tegel **variabelen** .
3. Voltooi de opties op de Blade **nieuwe variabele** en klik vervolgens op **maken** om de nieuwe variabele op te slaan.

>[!NOTE]
>Als u de versleuteling voor een variabele wilt verwijderen, moet u de variabele verwijderen en opnieuw maken als niet-versleuteld.

### <a name="create-a-new-variable-with-windows-powershell"></a>Een nieuwe variabele maken met Windows Power shell

Het script maakt gebruik van de cmdlet **New-AzAutomationVariable** voor het maken van een nieuwe variabele en het instellen van de oorspronkelijke waarde. De waarde kan vervolgens worden opgehaald met **Get-AzAutomationVariable**. Als de waarde een eenvoudig type is, wordt hetzelfde type opgehaald. Als het een complex type is, wordt een **PSCustomObject** -type opgehaald.

In het volgende voor beeld ziet u hoe u een variabele van het type teken reeks maakt en de waarde ervan als resultaat geeft.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

In het volgende voor beeld ziet u hoe u een variabele met een complex type maakt en vervolgens de eigenschappen ervan ophaalt. In dit geval wordt een object van de virtuele machine uit [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) gebruikt.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Een variabele gebruiken in een runbook-of DSC-configuratie

Gebruik de activiteit **set-AutomationVariable** om de waarde van een Automation-variabele in een Power shell-RUNBOOK of DSC-configuratie in te stellen en haal de **AutomationVariable** op om deze op te halen. U mag de cmdlets **set-AzAutomationVariable** en **Get-AzAutomationVariable** of de bijbehorende AzureRM-module niet gebruiken in een runbook of DSC-configuratie, omdat deze minder efficiënt zijn dan de werk stroom activiteiten. 

Houd er rekening mee dat u de waarde van een beveiligde variabele niet kunt ophalen met **Get-AzAutomationVariable** of de bijbehorende AzureRM-module equivalent. 

De enige manier om vanuit een runbook of DSC-configuratie een nieuwe variabele te maken, is met behulp van de cmdlet **New-AzAutomationVariable** .

### <a name="textual-runbook-samples"></a>Tekstuele runbook-voor beelden

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Een eenvoudige waarde van een variabele instellen en ophalen

De volgende voorbeeld opdrachten laten zien hoe u een variabele kunt instellen en ophalen in een tekst runbook. In dit voor beeld wordt ervan uitgegaan dat het maken van geheeltallige variabelen met de naam *NumberOfIterations* en *NumberOfRunnings* en een teken reeks variabele met de naam *SampleMessage*.

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

#### <a name="set-and-retrieve-a-variable-in-a-python2-runbook"></a>Een variabele in een Python2-runbook instellen en ophalen

Het volgende voor beeld laat zien hoe u een variabele kunt gebruiken, een variabele kunt instellen en een uitzonde ring kunt afhandelen voor een niet-bestaande variabele in een Python2-runbook.

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

### <a name="graphical-runbook-samples"></a>Voor beelden van grafische runbook

In een grafisch runbook kunt u de activiteit **Get-AutomationVariable** of **set-AutomationVariable** toevoegen. Klik met de rechter muisknop op de variabele in het deel venster Bibliotheek van de grafische editor en selecteer de gewenste activiteit.

![Variabele toevoegen aan canvas](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Waarden in een variabele instellen

De volgende afbeelding toont voorbeeld activiteiten om een variabele met een eenvoudige waarde in een grafisch runbook bij te werken. In dit voor beeld haalt **Get-AzVM** één virtuele machine van Azure op en slaat de computer naam op in een bestaande Automation-variabele met een type teken reeks. Het maakt niet uit of de [koppeling een pijp lijn of sequencer is](../automation-graphical-authoring-intro.md#links-and-workflow) , omdat de code slechts één object in de uitvoer verwacht.

![Eenvoudige variabele instellen](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [koppelingen in grafische ontwerpen](../automation-graphical-authoring-intro.md#links-and-workflow)voor meer informatie over het koppelen van activiteiten in grafische ontwerpen.
- Zie [mijn eerste grafische runbook](../automation-first-runbook-graphical.md)om aan de slag te gaan met grafische runbooks.
