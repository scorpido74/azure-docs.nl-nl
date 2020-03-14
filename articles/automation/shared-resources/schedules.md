---
title: Schema's in Azure Automation
description: Automation-schema's worden gebruikt voor het plannen van runbooks in Azure Automation om automatisch te starten. Hierin wordt beschreven hoe u een planning maakt en beheert in, zodat u automatisch een runbook kunt starten op een bepaald tijdstip of in een terugkerend schema.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4898ba62abdc42d95b77b9a77387bfe71fb4771
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252660"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Een runbook in Azure Automation plannen

Als u een runbook in Azure Automation wilt plannen om op een opgegeven tijdstip te starten, koppelt u dit aan een of meer planningen. Een planning kan worden geconfigureerd om één keer te worden uitgevoerd of op een terugkerende of dagelijkse planning voor runbooks in de Azure Portal. U kunt ze ook plannen voor wekelijkse, maandelijkse, specifieke dagen van de week of dagen van de maand of een bepaalde dag van de maand. Een runbook kan worden gekoppeld aan meerdere planningen en er kunnen meerdere runbooks aan een planning worden gekoppeld.

> [!NOTE]
> Schema's ondersteunen momenteel geen Azure Automation DSC-configuraties.

## <a name="powershell-cmdlets"></a>PowerShell-cmdlets

De cmdlets in de volgende tabel worden gebruikt voor het maken en beheren van planningen met Power shell in Azure Automation. Ze worden geleverd als onderdeel van de [module Azure PowerShell](/powershell/azure/overview).

| Cmdlets | Beschrijving |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Haalt een schema op. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Hiermee maakt u een nieuw schema. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Hiermee verwijdert u een schema. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Hiermee stelt u de eigenschappen voor een bestaande planning. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Hiermee worden geplande runbooks opgehaald. |
| [REGI ster-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Hiermee wordt een runbook gekoppeld aan een schema. |
| [Registratie ongedaan maken-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Hiermee wordt een runbook ontkoppeld van een schema. |

## <a name="creating-a-schedule"></a>Het maken van een planning

U kunt een nieuw schema maken voor runbooks in de Azure Portal of met Power shell.

> [!NOTE]
> Azure Automation gebruikt de nieuwste modules in uw Automation-account wanneer een nieuwe geplande taak wordt uitgevoerd.  Om te voor komen dat uw runbooks en de processen worden geautomatiseerd, moet u eerst alle runbooks met gekoppelde schema's testen met een Automation-account dat is toegewezen voor testen.  Hiermee wordt gecontroleerd of uw geplande runbooks goed werken en als dat niet het geval is, kunt u verdere problemen oplossen en alle vereiste wijzigingen Toep assen voordat u de bijgewerkte runbook-versie naar productie migreert.
> Uw Automation-account haalt niet automatisch nieuwe versies van modules op, tenzij u deze hand matig hebt bijgewerkt door de optie [Azure-modules bijwerken](../automation-update-azure-modules.md) in de **modules**te selecteren.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Een nieuw schema maken in de Azure Portal

1. Selecteer in de Azure Portal, vanuit uw Automation-account, de optie **schema's** onder de sectie **gedeelde resources** aan de linkerkant.
2. Klik boven aan de pagina op **een planning toevoegen** .
3. Typ in het deel venster **Nieuw schema** een **naam** en optioneel een **Beschrijving** voor het nieuwe schema.
4. Selecteer of het schema één keer wordt uitgevoerd of dat er een terugkerende planning plaatsvindt door **eenmalig** of **terugkerend**te selecteren. Als u **eenmaal** selecteert, geeft u een **begin tijd**op en klikt u vervolgens op **maken**. Als u **terugkerend**selecteert, een **begin tijd** en voor **elke**herhaling opgeeft, selecteert u de frequentie waarmee u het runbook wilt herhalen per **uur**, **dag**, **week**of **maand**.
    1. Als u **week**selecteert, ontvangt u een lijst van de dagen van de week waaruit u kunt kiezen. Selecteer zoveel dagen als u wilt. De eerste uitvoering van uw planning gebeurt op de eerste dag die na de begin tijd is geselecteerd. Als u bijvoorbeeld een weekend planning wilt kiezen, kiest u **zaterdag** en **zondag**.

       ![Terugkerende weekend planning instellen](../media/schedules/week-end-weekly-recurrence.png)

    2. Als u **maand**selecteert, krijgt u verschillende opties. Voor de optie voor **maandelijkse exemplaren** selecteert u **maand dagen** of **week dagen**. Als u **maand dagen**kiest, wordt er een kalender weer gegeven waarin u zoveel dagen kunt kiezen als u wilt. Als u een datum kiest zoals de 31e die niet voor komt in de huidige maand, wordt het schema niet uitgevoerd. Als u het schema op de laatste dag wilt uitvoeren, kiest u **Ja** onder **uitvoeren op de laatste dag van de maand**. Als u **week dagen**kiest, wordt **elke optie herhalen** weer gegeven. Kies **eerste**, **tweede**, **derde**, **vierde**of **laatste**. Kies ten slotte een dag om te herhalen.

       ![Maandelijks plannen op de eerste, vijftiende en laatste dag van de maand](../media/schedules/monthly-first-fifteenth-last.png)

5. Wanneer u klaar bent, klikt u op **maken**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Een nieuw schema maken met Power shell

U gebruikt de cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) om planningen te maken. U geeft de begin tijd voor de planning op en de frequentie die moet worden uitgevoerd. In de volgende voor beelden ziet u hoe u veel verschillende plannings scenario's maakt.

#### <a name="create-a-one-time-schedule"></a>Een eenmalige planning maken

Met de volgende voorbeeld opdrachten wordt één tijd schema gemaakt.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Een terugkerend schema maken

De volgende voorbeeld opdrachten laten zien hoe u een terugkerend schema maakt dat elke dag wordt uitgevoerd om 1:13:00 voor een jaar.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Een wekelijks terugkerende planning maken

De volgende voorbeeld opdrachten laten zien hoe u een wekelijks schema maakt dat alleen op week dagen wordt uitgevoerd.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Een wekelijks terugkerende planning voor weekends maken

De volgende voorbeeld opdrachten laten zien hoe u een wekelijks schema maakt dat alleen in weekends wordt uitgevoerd.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Een terugkerend schema maken voor de eerste, 15e en de laatste dag van de maand

De volgende voorbeeld opdrachten laten zien hoe u een terugkerend schema maakt dat wordt uitgevoerd op de 1e, 15e en laatste dag van een maand.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Een planning aan een runbook koppelen

Een runbook kan worden gekoppeld aan meerdere planningen en er kunnen meerdere runbooks aan een planning worden gekoppeld. Als een runbook para meters heeft, kunt u waarden voor deze opgeven. U moet waarden opgeven voor de verplichte para meters en kan waarden opgeven voor optionele para meters. Deze waarden worden gebruikt wanneer het runbook wordt gestart door dit schema. U kunt hetzelfde runbook aan een ander schema koppelen en verschillende parameter waarden opgeven.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Een planning aan een runbook koppelen met de Azure Portal

1. Selecteer in het Azure Portal van uw Automation-account **Runbooks** onder de sectie **proces automatisering** aan de linkerkant.
2. Klik op de naam van het runbook dat u wilt plannen.
3. Als het runbook momenteel niet is gekoppeld aan een schema, krijgt u de optie om een nieuw schema te maken of een koppeling naar een bestaand schema te koppelen.
4. Als het runbook para meters heeft, kunt u de optie **instellingen voor uitvoeren wijzigen (standaard: Azure)** selecteren en het deel venster **para meters** wordt weer gegeven waarin u de gegevens kunt invoeren.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Een planning aan een runbook koppelen met Power shell

U kunt de cmdlet [REGI ster-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) gebruiken om een schema te koppelen. U kunt waarden voor de parameters van het runbook opgeven met de parameter Parameters. Zie [starten van een Runbook in azure Automation](../automation-starting-a-runbook.md)voor meer informatie over het opgeven van parameter waarden.
De volgende voorbeeld opdrachten laten zien hoe u een schema aan een runbook koppelt met behulp van een Azure Resource Manager-cmdlet met para meters.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Vaker runbooks plannen

Het meest frequente interval voor een planning in Azure Automation kan één uur worden geconfigureerd. Als u planningen nodig hebt om vaker te worden uitgevoerd, zijn er twee opties:

* Maak een [webhook](../automation-webhooks.md) voor het runbook en gebruik [Azure Logic apps](../../logic-apps/logic-apps-overview.md) om de webhook aan te roepen. Azure Logic Apps biedt meer verfijnde granulatie bij het definiëren van een schema.

* Maak één keer per uur vier minuten schema's, beginnend bij elke andere. Met dit scenario kan het runbook om de 15 minuten worden uitgevoerd met de verschillende schema's.

## <a name="disabling-a-schedule"></a>Een planning uitschakelen

Wanneer u een planning uitschakelt, wordt elk runbook dat is gekoppeld aan het schema niet meer op dat schema uitgevoerd. U kunt een planning hand matig uitschakelen of een verloop tijd instellen voor schema's met een frequentie wanneer u deze maakt. Zodra de verloop tijd is bereikt, wordt het schema uitgeschakeld.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Een planning uit de Azure Portal uitschakelen

1. Selecteer in de Azure Portal, vanuit uw Automation-account, de optie **schema's** onder de sectie **gedeelde resources** aan de linkerkant.
2. Klik op de naam van een planning om het deel venster Details te openen.
3. Wijzig **ingeschakeld** in **Nee**.

> [!NOTE]
> Als u een planning wilt uitschakelen met een begin tijd in het verleden, moet u de begin datum wijzigen in een tijd in de toekomst voordat u deze opslaat.

### <a name="to-disable-a-schedule-with-powershell"></a>Een planning uitschakelen met Power shell

U kunt de cmdlet [set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) gebruiken om de eigenschappen van een bestaande planning te wijzigen. Als u het schema wilt uitschakelen, geeft u **False** op voor de para meter **IsEnabled** .

De volgende voorbeeld opdrachten laten zien hoe u een planning voor een runbook kunt uitschakelen met behulp van een Azure Resource Manager-cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Volgende stappen

* Zie [starten van een Runbook in azure Automation](../automation-starting-a-runbook.md) om aan de slag te gaan met runbooks in azure Automation

