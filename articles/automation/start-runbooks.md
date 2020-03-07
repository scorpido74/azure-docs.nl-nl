---
title: Een runbook starten in Azure Automation
description: Hierin wordt een overzicht gegeven van de verschillende methoden die kunnen worden gebruikt om een runbook te starten in Azure Automation en vindt u informatie over het gebruik van zowel de Azure Portal als Windows Power shell.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 71dd83db02537ed12dc2e711127e32d90603af6f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373124"
---
# <a name="start-a-runbook-in-azure-automation"></a>Een runbook starten in Azure Automation

De volgende tabel helpt u bij het bepalen van de methode voor het starten van een runbook in Azure Automation dat het meest geschikt is voor uw specifieke scenario. Dit artikel bevat informatie over het starten van een runbook met de Azure Portal en met Windows Power shell. Meer informatie over de andere methoden vindt u in andere documentatie die u kunt openen via de onderstaande koppelingen.

| **Methode** | **Ervan** |
| --- | --- |
| [Azure-portal](#start-a-runbook-with-the-azure-portal) |<li>Eenvoudigste methode met interactieve gebruikers interface.<br> <li>Formulier om eenvoudige parameter waarden op te geven.<br> <li>De taak status eenvoudig bijhouden.<br> <li>Toegang is geverifieerd met Azure-aanmelding. |
| [Windows Power shell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Aanroepen vanaf de opdracht regel met Windows Power shell-cmdlets.<br> <li>Kan worden opgenomen in een geautomatiseerde oplossing met meerdere stappen.<br> <li>De aanvraag is geverifieerd met het certificaat of de OAuth User Principal/Service-Principal.<br> <li>Eenvoudige en complexe parameter waarden opgeven.<br> <li>De taak status bijhouden.<br> <li>De client is vereist voor de ondersteuning van Power shell-cmdlets. |
| [Azure Automation-API](/rest/api/automation/) |<li>De meest flexibele methode, maar ook het meest complexe.<br> <li>Roep een aangepaste code aan die HTTP-aanvragen kan maken.<br> <li>Aanvraag is geverifieerd met certificaat, of OAuth User Principal/Service Principal.<br> <li>Eenvoudige en complexe parameter waarden opgeven. *Als u een python-runbook aanroept met behulp van de API, moet de JSON-nettolading worden geserialiseerd.*<br> <li>De taak status bijhouden. |
| [Webhooks](automation-webhooks.md) |<li>Het runbook starten vanuit een enkele HTTP-aanvraag.<br> <li>Is geverifieerd met een beveiligings token in een URL.<br> <li>De client kan de parameter waarden die zijn opgegeven tijdens het maken van de webhook niet overschrijven. Met Runbook kan één para meter worden gedefinieerd die wordt gevuld met de details van de HTTP-aanvraag.<br> <li>Het is niet mogelijk om de taak status via de webhook-URL bij te houden. |
| [Reageren op de Azure-waarschuwing](../log-analytics/log-analytics-alerts.md) |<li>Een runbook starten in reactie op de Azure-waarschuwing.<br> <li>Configureer webhook voor runbook en koppel deze aan een waarschuwing.<br> <li>Is geverifieerd met een beveiligings token in een URL. |
| [Planning](automation-schedules.md) |<li>Runbook automatisch starten op elk uur, dagelijks, wekelijks of maandelijks schema.<br> <li>Bewerk het schema via Azure Portal, Power shell-cmdlets of de Azure-API.<br> <li>Geef parameter waarden op die met schema moeten worden gebruikt. |
| [Vanuit een ander Runbook](automation-child-runbooks.md) |<li>Gebruik een runbook als een activiteit in een ander runbook.<br> <li>Handig voor functionaliteit die wordt gebruikt door meerdere runbooks.<br> <li>Geef parameter waarden op als onderliggend runbook en gebruik uitvoer in het bovenliggende runbook. |

In de volgende afbeelding ziet u een gedetailleerd stapsgewijs proces in de levens cyclus van een runbook. Het bevat verschillende manieren waarop een runbook wordt gestart in Azure Automation, welke onderdelen vereist zijn voor het uitvoeren Hybrid Runbook Worker van Azure Automation runbooks en interacties tussen verschillende onderdelen. Voor meer informatie over het uitvoeren van Automation-runbooks in uw Data Center raadpleegt u [Hybrid runbook Workers](automation-hybrid-runbook-worker.md)

![Runbook-architectuur](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="start-a-runbook-with-the-azure-portal"></a>Een runbook starten met de Azure Portal

1. Selecteer in de Azure Portal **Automation** en klik vervolgens op de naam van een Automation-account.
2. Selecteer **Runbooks**in het menu hub.
3. Selecteer op de pagina **Runbooks** een runbook en klik vervolgens op **starten**.
4. Als het runbook para meters heeft, wordt u gevraagd waarden op te geven voor elke para meter in een tekstvak. Zie [Runbook para meters](#runbook-parameters)voor meer informatie over para meters.
5. Op de pagina **taak** kunt u de status van de runbook-taak bekijken.

## <a name="start-a-runbook-with-powershell"></a>Een runbook starten met Power shell

U kunt de [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) gebruiken om een runbook te starten met Windows Power shell. Met de volgende voorbeeld code wordt een runbook met de naam test-Runbook gestart.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook retourneert een taak object dat u kunt gebruiken om de status bij te houden zodra het runbook is gestart. U kunt dit taak object vervolgens gebruiken met [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) om de status van de taak te bepalen en [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) om de uitvoer te verkrijgen. Met de volgende voorbeeld code wordt een runbook met de naam test-Runbook gestart, wordt gewacht tot het is voltooid en wordt vervolgens de uitvoer weer gegeven.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Als het runbook para meters vereist, moet u deze opgeven als [hashtabel](https://technet.microsoft.com/library/hh847780.aspx). De sleutel van de hashtabel moet overeenkomen met de naam van de para meter en de waarde is de parameter waarde. In het volgende voor beeld ziet u hoe u een runbook start met twee teken reeks parameters met de naam FirstName en LastName, een geheel getal met de naam RepeatCount en een Booleaanse para meter met de naam show. Zie [Runbook para meters](#runbook-parameters) hieronder voor meer informatie over para meters.

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Runbook-parameters

Wanneer u een runbook start vanuit de Azure Portal of Windows Power shell, wordt de instructie verzonden via de Azure Automation-webservice. Deze service biedt geen ondersteuning voor para meters met complexe gegevens typen. Als u een waarde moet opgeven voor een complexe para meter, moet u deze inline vanuit een ander runbook aanroepen, zoals wordt beschreven in [onderliggende runbooks in azure Automation](automation-child-runbooks.md).

De Azure Automation-webservice biedt speciale functionaliteit voor para meters die bepaalde gegevens typen gebruiken, zoals beschreven in de volgende secties:

### <a name="named-values"></a>Benoemde waarden

Als de para meter van het gegevens type [object] is, kunt u de volgende JSON-indeling gebruiken om een lijst met benoemde waarden te verzenden: *{Name1: ' waarde1 ', naam2: ' Value2 ', Name3: ' Value3 '}* . Deze waarden moeten eenvoudige typen zijn. Het runbook ontvangt de para meter als een [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) met eigenschappen die overeenkomen met elke benoemde waarde.

Bekijk het volgende testrunbook dat de parameter user accepteert.

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

De volgende tekst kan worden gebruikt voor de para meter User.

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

Als de para meter een matrix is, zoals [array] of [string []], kunt u de volgende JSON-indeling gebruiken om een lijst met waarden te verzenden: *[waarde1, Value2, Value3]* . Deze waarden moeten eenvoudige typen zijn.

Houd rekening met het volgende test runbook dat een para meter met de naam *User*accepteert.

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

De volgende tekst kan worden gebruikt voor de para meter User.

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

Als de para meter van het gegevens type **PSCredential**is, kunt u de naam van een Azure Automation [referentie-element](automation-credentials.md)opgeven. Het runbook haalt de referentie op met de naam die u opgeeft.

Bekijk het volgende testrunbook dat de parameter credential accepteert.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

De volgende tekst kan worden gebruikt voor de para meter User, ervan uitgaande dat er een referentie-element met de naam *mijn referentie*is.

```input
My Credential
```

Ervan uitgaande dat de gebruikers naam in de referentie *jsmith*is, resulteert dit in de volgende uitvoer:

```output
jsmith
```

## <a name="next-steps"></a>Volgende stappen

* De runbook-architectuur in het huidige artikel biedt een overzicht op hoog niveau van runbooks voor het beheren van resources in Azure en on-premises met de Hybrid Runbook Worker. Raadpleeg [Hybrid Runbook Workers](automation-hybrid-runbook-worker.md)voor meer informatie over het uitvoeren van Automation-runbooks in uw Data Center.
* Raadpleeg voor meer informatie over het maken van modulaire runbooks die door andere runbooks voor specifieke of algemene functies moeten worden gebruikt, de [onderliggende runbooks](automation-child-runbooks.md).
* Raadpleeg de [Power shell-documenten](https://docs.microsoft.com/powershell/scripting/overview)voor meer informatie over Power shell, inclusief taal referentie-en leer modules.
