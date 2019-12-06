---
title: Azure Automation-taakgegevens doorsturen naar Azure Monitor-logboeken
description: In dit artikel wordt beschreven hoe u taak status-en runbook-taak stromen kunt verzenden naar Azure Monitor Logboeken om meer inzicht en beheer te bieden.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2433e8193026b8aaa3cbf29eb1411c7449a4953
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849731"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Taak status en taak stromen door sturen van automatisering naar Azure Monitor logboeken

Automation kan de taak status van een runbook en taak stromen verzenden naar uw Log Analytics-werk ruimte. Voor dit proces is het koppelen van werk ruimten niet vereist en is het volledig onafhankelijk. Taak logboeken en taak stromen zijn zichtbaar in de Azure Portal, of met Power shell, voor afzonderlijke taken. Dit biedt u de mogelijkheid om eenvoudig onderzoek uit te voeren. U kunt nu met Azure Monitor-logboeken:

* Inzicht verwerven in uw Automation-taken.
* Een e-mail of waarschuwing activeren op basis van de status van uw runbook-taak (bijvoorbeeld mislukt of onderbroken).
* Geavanceerde query's voor verschillende taakstromen schrijven.
* Taken voor verschillende Automation-accounts aan elkaar te relateren.
* Uw taakgeschiedenis in de loop van de tijd visualiseren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Vereisten en overwegingen voor de implementatie

Als u uw Automation-logboeken wilt gaan verzenden naar Azure Monitor-logboeken, hebt u het volgende nodig:

* De nieuwste versie van [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Een Log Analytics-werkruimte. Zie [aan de slag met Azure monitor-logboeken](../log-analytics/log-analytics-get-started.md)voor meer informatie.
* De ResourceId voor uw Azure Automation-account.

De ResourceId voor uw Azure Automation-account zoeken:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Als u de ResourceId voor uw Log Analytics-werk ruimte wilt zoeken, voert u de volgende Power shell uit:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Als u meer dan één Automation-accounts of werk ruimten hebt, zoekt u in de uitvoer van de voor gaande opdrachten de *naam* die u moet configureren en kopieert u de waarde voor *ResourceID*.

Als u de *naam* van uw Automation-account moet vinden, klikt u in het Azure Portal Selecteer uw Automation-account op de Blade **Automation-account** en selecteert u **alle instellingen**. Selecteer op de blade **Alle instellingen** onder **Accountinstellingen** de optie **Eigenschappen**.  Noteer de waarden die op de blade **Eigenschappen** worden weergegeven.<br> ![de eigenschappen van het Automation-account](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Integratie met Azure Monitor-logboeken instellen

1. Start **Windows Power shell** op uw computer vanuit het **Start** scherm.
2. Voer de volgende Power shell uit en bewerk de waarde voor de `[your resource id]` en `[resource id of the log analytics workspace]` met de waarden uit de vorige stap.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Na het uitvoeren van dit script kan het een uur duren voordat u begint met het weer geven van records in Azure Monitor logboeken van nieuwe JobLogs of JobStreams dat wordt geschreven.

Als u de logboeken wilt weer geven, voert u de volgende query uit in Logboeken in log Analytics zoeken: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Configuratie controleren

Als u wilt controleren of uw Automation-account logboeken naar uw Log Analytics-werk ruimte verzendt, controleert u of de diagnostische gegevens correct zijn geconfigureerd voor het Automation-account met behulp van de volgende Power shell:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Zorg er in de uitvoer voor dat:

* Onder *Logboeken*is de waarde voor *ingeschakeld ingesteld* op *True*.
* De waarde van *WorkspaceId* wordt ingesteld op de ResourceID van uw log Analytics-werk ruimte.

## <a name="azure-monitor-log-records"></a>Azure Monitor logboek records

Diagnostische gegevens van Azure Automation twee typen records in Azure Monitor logboeken maken en als **AzureDiagnostics**worden gelabeld. De volgende query's gebruiken de bijgewerkte query taal om logboeken te Azure Monitor. Voor informatie over algemene query's tussen de verouderde query taal en de nieuwe Azure Kusto-query taal gaat u naar het [nieuwe Azure Kusto query language Cheat-blad](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language) .

### <a name="job-logs"></a>Taak logboeken

| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |Datum en tijd van uitvoering van de runbooktaak. |
| RunbookName_s |De naam van het runbook. |
| Caller_s |Wie de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken. |
| Tenant_g | GUID waarmee de Tenant voor de oproepende functie wordt geïdentificeerd. |
| JobId_g |De GUID die de id van de runbooktaak is. |
| ResultType |De status van de runbooktaak. Mogelijke waarden zijn:<br>-Nieuw<br>-Gemaakt<br>- Gestart<br>- Gestopt<br>- Onderbroken<br>- Mislukt<br>-Voltooid |
| Category | Classificatie van het type gegevens. Voor Automation is de waarde JobLogs. |
| OperationName | Hiermee wordt het type bewerking opgegeven dat in Azure wordt uitgevoerd. Voor Automation is de waarde taak. |
| Bron | De naam van het Automation-account |
| SourceSystem | Hoe Azure Monitor Logboeken de gegevens verzamelen. Altijd *Azure* voor Azure Diagnostics. |
| ResultDescription |Hiermee wordt resultaatstatus van de runbooktaak beschreven. Mogelijke waarden zijn:<br>- Taak is gestart<br>- Taak is mislukt<br>- Taak is voltooid |
| CorrelationId |De GUID die de correlatie-id van de runbooktaak is. |
| ResourceId |Hiermee geeft u de Azure Automation account resource-id van het runbook. |
| SubscriptionId | De id (GUID) van het Azure-abonnement voor het Automation-account. |
| ResourceGroup | De naam van de resource groep voor het Automation-account. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Taak stromen
| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |Datum en tijd van uitvoering van de runbooktaak. |
| RunbookName_s |De naam van het runbook. |
| Caller_s |Wie de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken. |
| StreamType_s |Het type taakstroom. Mogelijke waarden zijn:<br>\- Voortgang<br>- Uitvoer<br>- Waarschuwing<br>- Fout<br>- Foutopsporing<br>- Uitgebreid |
| Tenant_g | GUID waarmee de Tenant voor de oproepende functie wordt geïdentificeerd. |
| JobId_g |De GUID die de id van de runbooktaak is. |
| ResultType |De status van de runbooktaak. Mogelijke waarden zijn:<br>-Wordt uitgevoerd |
| Category | Classificatie van het type gegevens. Voor Automation is de waarde JobStreams. |
| OperationName | Hiermee wordt het type bewerking opgegeven dat in Azure wordt uitgevoerd. Voor Automation is de waarde taak. |
| Bron | De naam van het Automation-account |
| SourceSystem | Hoe Azure Monitor Logboeken de gegevens verzamelen. Altijd *Azure* voor Azure Diagnostics. |
| ResultDescription |Bevat de uitvoerstroom van het runbook. |
| CorrelationId |De GUID die de correlatie-id van de runbooktaak is. |
| ResourceId |Hiermee geeft u de Azure Automation account resource-id van het runbook. |
| SubscriptionId | De id (GUID) van het Azure-abonnement voor het Automation-account. |
| ResourceGroup | De naam van de resource groep voor het Automation-account. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Automatiserings logboeken weer geven in Azure Monitor-logboeken

Nu u bent begonnen met het verzenden van uw Automation-taak logboeken naar Azure Monitor-logboeken, ziet u wat u met deze logboeken kunt doen in Azure Monitor-Logboeken.

Voer de volgende query uit om de logboeken weer te geven: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Een e-mail verzenden wanneer een runbook-taak is mislukt of onderbroken
Een van de belangrijkste klanten vraagt om een e-mail of tekst te verzenden wanneer er iets mis gaat met een runbook-taak.

Als u een waarschuwings regel wilt maken, maakt u eerst een zoek opdracht in het logboek voor de runbook-taak records die de waarschuwing moeten aanroepen. Klik op de knop **waarschuwing** om de waarschuwings regel te maken en te configureren.

1. Klik op de pagina overzicht van Log Analytics werk ruimte op **Logboeken weer geven**.
2. Maak een zoek opdracht in het logboek voor uw waarschuwing door de volgende zoek opdracht in te voeren in het query veld: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` u kunt ook groeperen op RunbookName met behulp van: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Als u logboeken van meer dan één Automation-account of-abonnement op uw werk ruimte instelt, kunt u uw waarschuwingen groeperen op abonnement en Automation-account. De naam van het Automation-account vindt u in het veld Resource in de zoek opdracht van JobLogs.
3. Als u het scherm **regel maken** wilt openen, klikt u boven aan de pagina op **nieuwe waarschuwings regel** . Zie [logboek waarschuwingen in azure](../azure-monitor/platform/alerts-unified-log.md)voor meer informatie over de opties voor het configureren van de waarschuwing.

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Alle taken zoeken die zijn voltooid met fouten
Naast het melden van fouten, kunt u nagaan wanneer een runbook-taak een niet-afsluit fout heeft. In deze gevallen produceert Power shell een fout stroom, maar de niet-afsluit fouten zorgen ervoor dat uw taak niet wordt onderbroken of mislukt.

1. Klik in de werk ruimte Log Analytics op **Logboeken**.
2. Typ `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` in het veld query en klik vervolgens op de knop **zoeken** .

### <a name="view-job-streams-for-a-job"></a>Taak stromen voor een taak weer geven
Wanneer u fouten opspoort in een taak, wilt u mogelijk ook de taak stromen bekijken. Met de volgende query worden alle stromen weer gegeven voor één taak met GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Historische taak status weer geven
Ten slotte kunt u de geschiedenis van uw taken gedurende een periode visualiseren. U kunt deze query gebruiken om de status van uw taken in de loop van de tijd te zoeken.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics historische taak status diagram](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Diagnostische instellingen verwijderen

Als u de diagnostische instelling van het Automation-account wilt verwijderen, voert u de volgende opdrachten uit:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Samenvatting

Door de status van uw Automation-taak te verzenden en gegevens naar Azure Monitor logboeken te streamen, kunt u beter inzicht krijgen in de status van uw automatiserings taken:
+ Stel waarschuwingen in om u te waarschuwen wanneer er een probleem is.
+ Met aangepaste weer gaven en zoek query's kunt u de runbook-resultaten, de status van de runbook-taak en andere gerelateerde sleutel indicatoren of metrische gegevens visualiseren.

Azure Monitor-Logboeken biedt meer operationele zicht baarheid van uw automatiserings taken en kunnen incidenten sneller aanpakken.

## <a name="next-steps"></a>Volgende stappen

* Zie voor hulp bij het oplossen van problemen Log Analytics [problemen oplossen waarom log Analytics niet langer gegevens verzamelt](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Zie [Zoek opdrachten in Logboeken in azure monitor logboeken](../log-analytics/log-analytics-log-searches.md)voor meer informatie over het maken van verschillende Zoek query's en het controleren van de Automation-taak logboeken met Azure monitor-Logboeken.
* Zie [Runbook-uitvoer en berichten](automation-runbook-output-and-messages.md)voor informatie over het maken en ophalen van uitvoer-en fout berichten van runbooks.
* Zie [Runbooktaken bijhouden](automation-runbook-execution.md) voor meer informatie over runbookuitvoering, het bewaken van runbooktaken en andere technische details.
* Zie [Azure Storage-gegevens verzamelen in azure monitor logs Overview](../azure-monitor/platform/collect-azure-metrics-logs.md)voor meer informatie over Azure monitor logboeken en bronnen voor gegevens verzameling.

