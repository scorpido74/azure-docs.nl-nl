---
title: Start a runbook in Azure Automation (Een runbook starten in Azure Automation)
description: Vat de verschillende methoden samen die kunnen worden gebruikt om een runbook in Azure Automation te starten en geeft details over het gebruik van zowel de Azure-portal als Windows PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 7f2c0dda952959db3bffba6016f48b986016c19e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679455"
---
# <a name="start-a-runbook-in-azure-automation"></a>Start a runbook in Azure Automation (Een runbook starten in Azure Automation)

Met de volgende tabel u bepalen welke methode u wilt starten in Azure Automation die het meest geschikt is voor uw specifieke scenario. Dit artikel bevat details over het starten van een runbook met de Azure-portal en met Windows PowerShell. Details over de andere methoden zijn opgenomen in andere documentatie die u openen via de onderstaande links.

| **Methode** | **Kenmerken** |
| --- | --- |
| [Azure Portal](#start-a-runbook-with-the-azure-portal) |<li>Eenvoudigste methode met interactieve gebruikersinterface.<br> <li>Formulier om eenvoudige parameterwaarden te bieden.<br> <li>Volg eenvoudig de status van de taak.<br> <li>Toegang geverifieerd met Azure-aanmelding. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Oproep vanaf de opdrachtregel met Windows PowerShell-cmdlets.<br> <li>Kan worden opgenomen in geautomatiseerde oplossing met meerdere stappen.<br> <li>Aanvraag is geverifieerd met certificaat of OAuth user principal / service principal.<br> <li>Geef eenvoudige en complexe parameterwaarden.<br> <li>Spoor de taakstatus bij.<br> <li>Client vereist om PowerShell-cmdlets te ondersteunen. |
| [Azure Automation API](/rest/api/automation/) |<li>Meest flexibele methode, maar ook het meest complex.<br> <li>Bel vanuit elke aangepaste code die HTTP-verzoeken kan indienen.<br> <li>Aanvraag geverifieerd met certificaat, of Oauth user principal / service principal.<br> <li>Geef eenvoudige en complexe parameterwaarden. *Als u een Python-runbook aanroept met behulp van de API, moet de JSON-payload worden geserialiseerd.*<br> <li>Spoor de taakstatus bij. |
| [Webhooks](automation-webhooks.md) |<li>Start runbook vanuit één HTTP-aanvraag.<br> <li>Geverifieerd met beveiligingstoken in URL.<br> <li>Client kan parameterwaarden die zijn opgegeven bij het maken van webhook niet overschrijven. Runbook kan één parameter definiëren die wordt gevuld met de HTTP-aanvraagdetails.<br> <li>Geen mogelijkheid om de status van de taak te volgen via de URL van webhook. |
| [Reageren op Azure Alert](../log-analytics/log-analytics-alerts.md) |<li>Start een runbook als reactie op Azure-waarschuwing.<br> <li>Configureer webhook voor runbook en link naar alert.<br> <li>Geverifieerd met beveiligingstoken in URL. |
| [Planning](automation-schedules.md) |<li>Start automatisch runbook op uur-, dag-, week-of maandschema.<br> <li>Plan manipuleren via Azure-portal, PowerShell-cmdlets of Azure API.<br> <li>Geef parameterwaarden op die met planning moeten worden gebruikt. |
| [Uit een ander runbook](automation-child-runbooks.md) |<li>Gebruik een runbook als activiteit in een ander runbook.<br> <li>Handig voor functionaliteit die wordt gebruikt door meerdere runbooks.<br> <li>Geef parameterwaarden op aan onderliggend runbook en gebruik uitvoer in bovenliggende runbook. |

De volgende afbeelding illustreert een gedetailleerd stapsgewijs proces in de levenscyclus van een runbook. Het bevat verschillende manieren waarop een runbook wordt gestart in Azure Automation, welke onderdelen nodig zijn voor Hybrid Runbook Worker om Azure Automation runbooks en interacties tussen verschillende componenten uit te voeren. Voor meer informatie over het uitvoeren van automatiseringsrunboeken in uw datacenter verwijzen we je door naar [hybride runbook-werknemers](automation-hybrid-runbook-worker.md)

![Runbook-architectuur](media/automation-starting-runbook/runbooks-architecture.png)

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="runbook-parameters"></a>Runbook-parameters

Wanneer u een runbook start vanuit de Azure-portal of Windows PowerShell, wordt de instructie verzonden via de webservice Azure Automation. Deze service ondersteunt geen parameters met complexe gegevenstypen. Als u een waarde moet opgeven voor een complexe parameter, moet u deze inline noemen vanuit een andere runbook zoals beschreven in [onderliggende runbooks in Azure Automation.](automation-child-runbooks.md)

De webservice Azure Automation biedt speciale functionaliteit voor parameters die bepaalde gegevenstypen gebruiken, zoals beschreven in de volgende secties:

### <a name="named-values"></a>Benoemde waarden

Als de parameter gegevenstype [object] is, u de volgende JSON-notatie gebruiken om deze een lijst met benoemde waarden te sturen: *{Name1:'Value1', Name2:'Value2', Name3:'Value3'}*. Deze waarden moeten eenvoudige typen zijn. De runbook ontvangt de parameter als een [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) met eigenschappen die overeenkomen met elke benoemde waarde.

Bekijk het volgende testrunbook dat de parameter useraccepteert.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

De volgende tekst kan worden gebruikt voor de parameter user.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Dit resulteert in de volgende uitvoer:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Matrixen

Als de parameter een array is zoals [array] of [string[]], u de volgende JSON-notatie gebruiken om deze een lijst met waarden te sturen: *[Waarde1, Waarde2, Waarde3]*. Deze waarden moeten eenvoudige typen zijn.

Bekijk het volgende testrunbook dat de parameter *user*accepteert.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

De volgende tekst kan worden gebruikt voor de parameter user.

```input
["Joe","Smith",2,true]
```

Dit resulteert in de volgende uitvoer:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Referenties

Als de parameter `PSCredential`gegevenstype is, u de naam opgeven van een [azure automation-referentieactief](automation-credentials.md). Het runbook haalt de referentie op met de naam die u opgeeft. In het volgende testrunbook `credential`wordt een parameter geaccepteerd die wordt genoemd .

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

De volgende tekst kan worden gebruikt voor de parameter `My Credential`gebruiker ervan uitgaande dat er een referentie-asset genaamd .

```input
My Credential
```

Ervan uitgaande dat de gebruikersnaam `jsmith`in de referentie is, wordt de volgende uitvoer weergegeven.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Een runbook starten met de Azure-portal

1. Selecteer in de Azure-portal **Automatisering** en klik vervolgens op de naam van een automatiseringsaccount.
2. Selecteer **Runbooks**in het menu Hub .
3. Selecteer op de pagina Runbooks een runbook en klik op **Start**.
4. Als de runbook parameters heeft, wordt u gevraagd waarden te voorzien van een tekstvak voor elke parameter. Zie [Runbook Parameters voor](#runbook-parameters)meer informatie over parameters.
5. In het deelvenster Taak u de status van de runbook-taak weergeven.

## <a name="start-a-runbook-with-powershell"></a>Een runbook starten met PowerShell

U de [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) gebruiken om een runbook te starten met Windows PowerShell. Met de volgende voorbeeldcode wordt een runbook gestart met de naam **Test-Runbook**.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook`retourneert een taakobject dat u gebruiken om de status bij te houden zodra het runbook is gestart. U dit taakobject vervolgens gebruiken met [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) om de status van de taak te bepalen en [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjoboutput?view=azps-3.7.0) om de uitvoer op te halen. In het volgende voorbeeld wordt een runbook gestart met de naam **Test-Runbook,** wordt gewacht tot het is voltooid en wordt de uitvoer weergegeven.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Als de runbook parameters vereist, moet u ze opgeven als een [hashtable.](https://technet.microsoft.com/library/hh847780.aspx) De sleutel van de hashtabel moet overeenkomen met de parameternaam en de waarde is de parameterwaarde. Het volgende voorbeeld laat zien hoe u een runbook met twee reeksparameters met de naam FirstName en LastName, een geheel getal met de naam RepeatCount met de naam en een Boole-parameter met de naam Show start. Zie [Runbook Parameters voor](#runbook-parameters)meer informatie over parameters.

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg [Hybride runbook Workers](automation-hybrid-runbook-worker.md)voor meer informatie over het uitvoeren van runbooks voor automatisering in uw datacenter.
* Zie [Onderliggende runbooks](automation-child-runbooks.md)voor meer informatie over het maken van modulaire runbooks die door andere runbooks voor specifieke of algemene functies moeten worden gebruikt.
* Zie voor meer informatie over PowerShell, inclusief taalverwijzingen en leermodules, de [PowerShell-documenten](https://docs.microsoft.com/powershell/scripting/overview).
