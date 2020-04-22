---
title: Schema's beheren in Azure Automation
description: Automatiseringsschema's worden gebruikt om runbooks in Azure Automation automatisch te starten. Beschrijft hoe u een planning maken en beheren, zodat u een runbook automatisch starten op een bepaald tijdstip of op een terugkerend schema.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3603e76186ce30fb491d829d3a804837f4ac2e6d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732800"
---
# <a name="manage-schedules-in-azure-automation"></a>Schema's beheren in Azure Automation

Als u wilt dat een runbook in Azure Automation op een bepaald tijdstip wordt gestart, koppelt u deze aan een of meer schema's. Een planning kan worden geconfigureerd om één keer uit te voeren of volgens een terugkerend uur- of dagschema voor runbooks in de Azure-portal. U ze ook plannen voor wekelijkse, maandelijkse, specifieke dagen van de week of dagen van de maand, of een bepaalde dag van de maand. Een runbook kan worden gekoppeld aan meerdere planningen en er kunnen meerdere runbooks aan een planning worden gekoppeld.

> [!NOTE]
> Schema's ondersteunen momenteel geen Azure Automation DSC-configuraties.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](../automation-update-azure-modules.md)

## <a name="powershell-cmdlets"></a>PowerShell-cmdlets

De cmdlets in de volgende tabel worden gebruikt om schema's te maken en te beheren met PowerShell in Azure Automation. Ze worden verzonden als onderdeel van de [Azure PowerShell-module.](/powershell/azure/overview)

| Cmdlets | Beschrijving |
|:--- |:--- |
| [Get-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Hiermee haalt u een schema op. |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Hiermee haalt u geplande runbooks op. |
| [Nieuwe azautomationschedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Hiermee maakt u een nieuw schema. |
| [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Associeert een runbook met een schema. |
| [Verwijder-azautomationschedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Hiermee verwijdert u een schema. |
| [Set-azautomationschema](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Hiermee stelt u de eigenschappen in voor een bestaand schema. |
| [Kassa-AzAutomationScheduledRunbook uitschrijven](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Scheidt een runbook van een schema. |

## <a name="creating-a-schedule"></a>Een planning maken

U een nieuw schema maken voor runbooks in de Azure-portal of met PowerShell.

> [!NOTE]
> Azure Automation gebruikt de nieuwste modules in uw Automatiseringsaccount wanneer een nieuwe geplande taak wordt uitgevoerd.  Om te voorkomen dat uw runbooks en de processen die ze automatiseren van invloed zijn, moet u eerst alle runbooks testen die schema's hebben gekoppeld aan een Automation-account dat is toegewezen voor het testen.  Dit valideert dat uw geplande runbooks correct blijven werken en zo niet, dan u eventuele wijzigingen die nodig zijn verder oplossen en toepassen voordat u de bijgewerkte runbook-versie naar de productie migreert.
> Uw Automatiseringsaccount ontvangt niet automatisch nieuwe versies van modules, tenzij u deze handmatig hebt bijgewerkt door de optie [Azure-modules bijwerken](../automation-update-azure-modules.md) in de **modules**te selecteren.

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Een nieuwe planning maken in de Azure-portal

1. Selecteer In de Azure-portal in uw automatiseringsaccount de optie **Planningen** onder de sectie **Gedeelde resources** aan de linkerkant.
2. Klik boven aan de pagina op **Een schema toevoegen.**
3. Typ in het deelvenster Nieuwe planning een naam en eventueel een beschrijving voor de nieuwe planning.
4. Selecteer of de planning één keer wordt uitgevoerd of in een terugkerend schema door **Eenmaal** of **Terugkerend te**selecteren. Als u **Eenmaal**selecteert, geeft u een begintijd op en klikt u op **Maken**. Als u **Terugkerend**selecteert, geeft u een begintijd op. Selecteer **voor Elke recur**hoe vaak u het runbook moet herhalen - per uur, dag, week of maand.
    1. Als u **week**selecteert, worden de dagen van de week gepresenteerd waaruit u kiezen. Selecteer zoveel dagen als u wilt. De eerste run van uw schema vindt plaats op de eerste dag die is geselecteerd na de begintijd. Als u bijvoorbeeld een weekendschema wilt kiezen, selecteert u zaterdag en zondag. 
    
       ![Een terugkerend weekendschema instellen](../media/schedules/week-end-weekly-recurrence.png)

    2. Als u **maand**selecteert, krijgt u verschillende opties. Selecteer **maanddagen** of **weekdagen**voor de optie **Maandelijkse gebeurtenissen** . Als u **maanddagen**kiest, wordt een agenda weergegeven waarmee u zoveel dagen kiezen als u wilt. Als u een datum kiest zoals de 31e die niet in de huidige maand plaatsvindt, wordt de planning niet uitgevoerd. Als u wilt dat het schema op de laatste dag wordt uitgevoerd, kiest u **Ja** onder **Uitvoeren op de laatste dag van de maand**. Als u **weekdagen**kiest, wordt elke optie **opnieuw** ontvangen. Kies **Eerste**, **Tweede,** **Derde**, **Vierde**of **Laatste**. Kies ten slotte een dag om op te herhalen.

       ![Maandelijks schema op de eerste, vijftiende en laatste dag van de maand](../media/schedules/monthly-first-fifteenth-last.png)

5. Klik op **Maken**als u klaar bent.

### <a name="create-a-new-schedule-with-powershell"></a>Een nieuwe planning maken met PowerShell

Gebruik de cmdlet [Nieuw-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) om schema's te maken. U geeft de begintijd voor het schema en de frequentie op die moet worden uitgevoerd. In de volgende voorbeelden ziet u hoe u veel verschillende planningsscenario's maakt.

#### <a name="create-a-one-time-schedule"></a>Een eenmalig schema maken

Met de volgende voorbeeldopdrachten wordt een eenmalig schema gemaakt.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Een terugkerend schema maken

In het volgende voorbeeld ziet u hoe u een jaar lang elke dag om 13:00 uur een terugkerend schema maakt.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Een wekelijks terugkerend schema maken

In het volgende voorbeeld ziet u hoe u een wekelijks schema maakt dat alleen op weekdagen wordt uitgevoerd.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Een wekelijks terugkerend schema voor het weekend maken

In de volgende voorbeeldopdrachten wordt uitgelegd hoe u een wekelijks schema maakt dat alleen in het weekend wordt uitgevoerd.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Een terugkerend schema maken voor de eerste, 15e en laatste dagen van de maand

In het volgende voorbeeld ziet u hoe u een terugkerend schema maakt dat wordt uitgevoerd op de 1e, 15e en laatste dag van een maand.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Een planning koppelen aan een runbook

Een runbook kan worden gekoppeld aan meerdere planningen en er kunnen meerdere runbooks aan een planning worden gekoppeld. Als een runbook parameters heeft, u er waarden voor opgeven. U moet waarden opgeven voor alle verplichte parameters en waarden opgeven voor eventuele optionele parameters. Deze waarden worden gebruikt telkens wanneer het runbook door dit schema wordt gestart. U hetzelfde runbook aan een ander schema koppelen en verschillende parameterwaarden opgeven.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Een planning koppelen aan een runbook met de Azure-portal

1. Selecteer in de Azure-portal vanuit uw automatiseringsaccount **Runbooks** onder **Procesautomatisering**.
2. Klik op de naam van het runbook dat u wilt plannen.
3. Als het runbook momenteel niet is gekoppeld aan een planning, krijgt u de optie om een nieuwe planning of koppeling naar een bestaand schema te maken.
4. Als de runbook parameters heeft, u de optie **Run-instellingen wijzigen (Standaard:Azure)** selecteren en wordt het deelvenster Parameters weergegeven. U hier parametergegevens invoeren.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Een planning koppelen aan een runbook met PowerShell

Gebruik de cmdlet [Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) om een planning te koppelen. U kunt waarden voor de parameters van het runbook opgeven met de parameter Parameters . Zie [Een runbook starten in Azure Automation](../automation-starting-a-runbook.md)voor meer informatie over het opgeven van parameterwaarden.
In het volgende voorbeeld ziet u hoe u een planning koppelt aan een runbook met behulp van een Azure Resource Manager-cmdlet met parameters.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>Runbooks plannen om vaker uit te voeren

Het meest voorkomende interval waarvoor een planning in Azure Automation kan worden geconfigureerd, is een uur. Als u wilt dat schema's vaker worden uitgevoerd dan dat, zijn er twee opties:

* Maak een [webhook](../automation-webhooks.md) voor de runbook en gebruik [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) om de webhook aan te roepen. Azure Logic Apps biedt meer fijnkorrelige granulariteit bij het definiëren van een planning.

* Maak vier schema's die allemaal binnen 15 minuten na elkaar lopen eenmaal per uur. Met dit scenario kan het runbook elke 15 minuten worden uitgevoerd met de verschillende schema's.

## <a name="disabling-a-schedule"></a>Een schema uitschakelen

Wanneer u een planning uitschakelt, wordt elk runbook dat eraan is gekoppeld, niet meer volgens dat schema uitgevoerd. U handmatig een planning uitschakelen of een vervaldatum instellen voor planningen met een frequentie wanneer u ze maakt. Zodra de vervaldatum is bereikt, is het schema uitgeschakeld.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Een planning uitschakelen vanuit de Azure-portal

1. Selecteer In uw automatiseringsaccount de optie **Planning onder** Gedeelde **resources**.
2. Klik op de naam van een schema om het detailvenster te openen.
3. **Wijzigen ingeschakeld in** **nee**.

> [!NOTE]
> Als u een planning wilt uitschakelen die in het verleden een begintijd heeft, moet u de begindatum wijzigen in een tijdstip in de toekomst voordat u deze opslaat.

### <a name="disable-a-schedule-with-powershell"></a>Een schema uitschakelen met PowerShell

Gebruik de cmdlet [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) om de eigenschappen van een bestaande planning te wijzigen. Als u de planning wilt `IsEnabled` uitschakelen, geeft u False op voor de parameter.

In het volgende voorbeeld ziet u hoe u een planning voor een runbook uitschakelt met een Azure Resource Manager-cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Een runbook starten in Azure Automation](../automation-starting-a-runbook.md)voor aan de slag met runbooks in Azure Automation.