---
title: Planningen in Azure Automation
description: Automatiseringsschema's worden gebruikt om runbooks in Azure Automation automatisch te starten. Beschrijft hoe u een planning maken en beheren, zodat u een runbook automatisch starten op een bepaald tijdstip of op een terugkerend schema.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4898ba62abdc42d95b77b9a77387bfe71fb4771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252660"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Een runbook in Azure Automation plannen

Als u wilt dat een runbook in Azure Automation op een bepaald tijdstip wordt gestart, koppelt u deze aan een of meer schema's. Een planning kan worden geconfigureerd om één keer uit te voeren of volgens een terugkerend uur- of dagschema voor runbooks in de Azure-portal. U ze ook plannen voor wekelijkse, maandelijkse, specifieke dagen van de week of dagen van de maand, of een bepaalde dag van de maand. Een runbook kan worden gekoppeld aan meerdere planningen en er kunnen meerdere runbooks aan een planning worden gekoppeld.

> [!NOTE]
> Schema's ondersteunen momenteel geen Azure Automation DSC-configuraties.

## <a name="powershell-cmdlets"></a>PowerShell-cmdlets

De cmdlets in de volgende tabel worden gebruikt om schema's te maken en te beheren met PowerShell in Azure Automation. Ze worden verzonden als onderdeel van de [Azure PowerShell-module.](/powershell/azure/overview)

| Cmdlets | Beschrijving |
|:--- |:--- |
| [Get-AzurermAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Hiermee haalt u een schema op. |
| [Nieuw-AzurermAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Hiermee maakt u een nieuw schema. |
| [Remove-AzurermAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Hiermee verwijdert u een schema. |
| [Set-AzurermAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Hiermee stelt u de eigenschappen in voor een bestaand schema. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Hiermee haalt u geplande runbooks op. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associeert een runbook met een schema. |
| [Kassa-AzureRmAutomationScheduledRunbook uitschrijven](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Scheidt een runbook van een schema. |

## <a name="creating-a-schedule"></a>Een planning maken

U een nieuw schema maken voor runbooks in de Azure-portal of met PowerShell.

> [!NOTE]
> Azure Automation gebruikt de nieuwste modules in uw Automatiseringsaccount wanneer een nieuwe geplande taak wordt uitgevoerd.  Om te voorkomen dat uw runbooks en de processen die ze automatiseren van invloed zijn, moet u eerst alle runbooks testen die schema's hebben gekoppeld aan een Automation-account dat is toegewezen voor het testen.  Dit valideert dat uw geplande runbooks correct blijven werken en zo niet, dan u eventuele wijzigingen die nodig zijn verder oplossen en toepassen voordat u de bijgewerkte runbook-versie naar de productie migreert.
> Uw Automatiseringsaccount ontvangt niet automatisch nieuwe versies van modules, tenzij u deze handmatig hebt bijgewerkt door de optie [Azure-modules bijwerken](../automation-update-azure-modules.md) in de **modules**te selecteren.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Een nieuwe planning maken in de Azure-portal

1. Selecteer In de Azure-portal in uw automatiseringsaccount de optie **Planningen** onder de sectie **Gedeelde resources** aan de linkerkant.
2. Klik boven aan de pagina op **Een schema toevoegen.**
3. Typ in het deelvenster **Nieuwe planning** een **naam** en eventueel een **beschrijving** voor de nieuwe planning.
4. Selecteer of de planning één keer wordt uitgevoerd of in een terugkerend schema door **Eenmaal** of **Terugkerend te**selecteren. Als u **Eenmaal** een **begintijd**opgeeft en vervolgens op **Maken**klikt. Als u **Terugkerend**selecteert, geeft u een **begintijd** op en voor **Elke Recur**, selecteert u de frequentie voor hoe vaak u wilt dat het runbook wordt herhaald - per **uur,** **dag,** **week**of **per maand**.
    1. Als u **week**selecteert, krijgt u een lijst met de dagen van de week om uit te kiezen. Selecteer zoveel dagen als u wilt. De eerste run van uw schema vindt plaats op de eerste dag die is geselecteerd na de begintijd. Als u bijvoorbeeld een weekendschema wilt kiezen, kiest u **zaterdag** en **zondag.**

       ![Een terugkerend weekendschema instellen](../media/schedules/week-end-weekly-recurrence.png)

    2. Als u **maand**selecteert, krijgt u verschillende opties. Selecteer **maanddagen** of **weekdagen**voor de optie **Maandelijkse gebeurtenissen** . Als u **maanddagen**kiest, wordt een agenda weergegeven waarmee u zoveel dagen kiezen als u wilt. Als u een datum kiest zoals de 31e die niet in de huidige maand plaatsvindt, wordt de planning niet uitgevoerd. Als u wilt dat het schema op de laatste dag wordt uitgevoerd, kiest u **Ja** onder **Uitvoeren op de laatste dag van de maand**. Als u **weekdagen**kiest, wordt elke optie **opnieuw** ontvangen. Kies **Eerste**, **Tweede,** **Derde**, **Vierde**of **Laatste**. Kies ten slotte een dag om op te herhalen.

       ![Maandelijks schema op de eerste, vijftiende en laatste dag van de maand](../media/schedules/monthly-first-fifteenth-last.png)

5. Klik op **Maken**als u klaar bent.

### <a name="to-create-a-new-schedule-with-powershell"></a>Een nieuwe planning maken met PowerShell

U gebruikt de cmdlet [Nieuw-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) om schema's te maken. U geeft de begintijd voor het schema en de frequentie op die moet worden uitgevoerd. In de volgende voorbeelden ziet u hoe u veel verschillende planningsscenario's maakt.

#### <a name="create-a-one-time-schedule"></a>Een eenmalig tijdsschema maken

Met de volgende voorbeeldopdrachten wordt een eenmalig schema gemaakt.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Een terugkerend schema maken

In de volgende voorbeeldopdrachten wordt uitgelegd hoe u een jaar lang elke dag om 13:00 uur een terugkerend schema maken.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Een wekelijks terugkerend schema maken

In de volgende voorbeeldopdrachten wordt uitgelegd hoe u een wekelijks schema maakt dat alleen op weekdagen wordt uitgevoerd.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Een wekelijks terugkerend schema voor het weekend maken

In de volgende voorbeeldopdrachten wordt uitgelegd hoe u een wekelijks schema maakt dat alleen in het weekend wordt uitgevoerd.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Een terugkerend schema maken voor de eerste, 15e en laatste dagen van de maand

In de volgende voorbeeldopdrachten wordt uitgelegd hoe u een terugkerend schema maakt dat wordt uitgevoerd op de 1e, 15e en laatste dag van een maand.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Een planning koppelen aan een runbook

Een runbook kan worden gekoppeld aan meerdere planningen en er kunnen meerdere runbooks aan een planning worden gekoppeld. Als een runbook parameters heeft, u er waarden voor opgeven. U moet waarden opgeven voor alle verplichte parameters en waarden opgeven voor eventuele optionele parameters. Deze waarden worden gebruikt telkens wanneer het runbook door dit schema wordt gestart. U hetzelfde runbook aan een ander schema koppelen en verschillende parameterwaarden opgeven.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Een planning koppelen aan een runbook met de Azure-portal

1. Selecteer in de Azure-portal vanuit uw automatiseringsaccount **Runbooks** onder de sectie **Procesautomatisering** aan de linkerkant.
2. Klik op de naam van het runbook dat u wilt plannen.
3. Als het runbook momenteel niet is gekoppeld aan een planning, krijgt u de optie om een nieuwe planning of koppeling naar een bestaand schema te maken.
4. Als de runbook parameters heeft, u de optie **Run-instellingen wijzigen (Standaard:Azure)** selecteren en wordt het deelvenster **Parameters** weergegeven waar u de gegevens invoeren.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Een planning koppelen aan een runbook met PowerShell

U de cmdlet [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) gebruiken om een planning te koppelen. U kunt waarden voor de parameters van het runbook opgeven met de parameter Parameters . Zie [Een runbook starten in Azure Automation](../automation-starting-a-runbook.md)voor meer informatie over het opgeven van parameterwaarden.
In de volgende voorbeeldopdrachten wordt uitgelegd hoe u een planning koppelt aan een runbook met behulp van een Azure Resource Manager-cmdlet met parameters.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Runbooks vaker plannen

Het meest voorkomende interval waarvoor een planning in Azure Automation kan worden geconfigureerd, is een uur. Als u wilt dat schema's vaker worden uitgevoerd dan dat, zijn er twee opties:

* Maak een [webhook](../automation-webhooks.md) voor de runbook en gebruik [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) om de webhook aan te roepen. Azure Logic Apps biedt meer fijnkorrelige granulariteit bij het definiëren van een planning.

* Maak vier schema's die allemaal binnen 15 minuten na elkaar lopen eenmaal per uur. Met dit scenario kan het runbook elke 15 minuten worden uitgevoerd met de verschillende schema's.

## <a name="disabling-a-schedule"></a>Een schema uitschakelen

Wanneer u een planning uitschakelt, wordt elk runbook dat eraan is gekoppeld, niet meer volgens dat schema uitgevoerd. U handmatig een planning uitschakelen of een vervaldatum instellen voor planningen met een frequentie wanneer u ze maakt. Zodra de vervaldatum is bereikt, is het schema uitgeschakeld.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Een planning uitschakelen vanuit de Azure-portal

1. Selecteer In de Azure-portal in uw Automatiseringsaccount de optie **Planningen** onder de sectie **Gedeelde resources** aan de linkerkant.
2. Klik op de naam van een schema om het detailvenster te openen.
3. **Wijzigen ingeschakeld in** **nee**.

> [!NOTE]
> Als u een planning wilt uitschakelen die in het verleden een begintijd heeft, moet u de begindatum wijzigen in een tijdstip in de toekomst voordat u deze opslaat.

### <a name="to-disable-a-schedule-with-powershell"></a>Een planning uitschakelen met PowerShell

U de cmdlet [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) gebruiken om de eigenschappen van een bestaand schema te wijzigen. Als u de planning wilt uitschakelen, geeft u **false** op voor de parameter **IsEnabled.**

In de volgende voorbeeldopdrachten wordt uitgelegd hoe u een planning voor een runbook uitschakelt met een Azure Resource Manager-cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Een runbook starten in Azure Automation](../automation-starting-a-runbook.md) voor aan de slag met runbooks in Azure Automation

