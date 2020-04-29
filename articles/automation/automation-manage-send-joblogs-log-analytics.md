---
title: Azure Automation-taakgegevens doorsturen naar Azure Monitor-logboeken
description: In dit artikel wordt beschreven hoe u taak status-en runbook-taak stromen kunt verzenden naar Azure Monitor Logboeken om meer inzicht en beheer te bieden.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: a9f4e641e60d6cf1c481c445767422e8b4df683b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457685"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Taak status en taak stromen door sturen van automatisering naar Azure Monitor logboeken

Automation kan de taak status van een runbook en taak stromen verzenden naar uw Log Analytics-werk ruimte. Voor dit proces is het koppelen van werk ruimten niet vereist en is het volledig onafhankelijk. Taak logboeken en taak stromen zijn zichtbaar in de Azure Portal, of met Power shell, voor afzonderlijke taken. Dit biedt u de mogelijkheid om eenvoudig onderzoek uit te voeren. U kunt nu met Azure Monitor-logboeken:

* Krijg inzicht in de status van uw automatiserings taken.
* Een e-mail of waarschuwing activeren op basis van de status van uw runbook-taak (bijvoorbeeld mislukt of onderbroken).
* Schrijf geavanceerde query's over uw werk stromen.
* Verdeel taken tussen Automation-accounts.
* Gebruik aangepaste weer gaven en zoek query's om uw runbook-resultaten, de status van de runbook-taak en andere gerelateerde sleutel indicatoren of metrische gegevens te visualiseren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites-and-deployment-considerations"></a>Vereisten en overwegingen voor de implementatie

Als u uw Automation-logboeken wilt gaan verzenden naar Azure Monitor-logboeken, hebt u het volgende nodig:

* De nieuwste versie van [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Een Log Analytics-werkruimte. Zie [aan de slag met Azure monitor-logboeken](../log-analytics/log-analytics-get-started.md)voor meer informatie.
* De resource-ID voor uw Azure Automation-account.

Gebruik de volgende opdracht om de resource-ID te vinden voor uw Azure Automation-account:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Voer de volgende Power shell-opdracht uit om de resource-ID voor uw Log Analytics-werk ruimte te vinden:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Als u meer dan één Automation-account of werk ruimte hebt in de uitvoer van de voor gaande opdrachten, zoekt u de naam die u nodig hebt om de waarde voor de resource-ID te configureren en te kopiëren.

1. Selecteer in de Azure Portal uw Automation-account op de Blade **Automation-account** en selecteer **alle instellingen**. 
2. Selecteer op de Blade **alle instellingen** onder **account instellingen**de optie **Eigenschappen**.  
3. In de Blade **Eigenschappen** noteert u de eigenschappen die hieronder worden weer gegeven.

    ![Eigenschappen van Automation-account](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Azure Monitor logboek records

Met Azure Automation diagnostische gegevens worden twee typen records gemaakt in Azure Monitor logboeken `AzureDiagnostics`, gelabeld als. De tabellen in de volgende secties zijn voor beelden van records die Azure Automation gegenereerd en de gegevens typen die worden weer gegeven in de zoek resultaten van het logboek.

### <a name="job-logs"></a>Taaklogboeken

| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |Datum en tijd van uitvoering van de runbooktaak. |
| RunbookName_s |De naam van het runbook. |
| Caller_s |De aanroeper die de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken. |
| Tenant_g | GUID waarmee de Tenant voor de oproepende functie wordt geïdentificeerd. |
| JobId_g |GUID waarmee de runbook-taak wordt geïdentificeerd. |
| ResultType |De status van de runbooktaak. Mogelijke waarden zijn:<br>-Nieuw<br>-Gemaakt<br>- Gestart<br>- Gestopt<br>- Onderbroken<br>- Mislukt<br>-Voltooid |
| Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobLogs. |
| OperationName | Het type bewerking dat in azure wordt uitgevoerd. Voor Automation is de waarde taak. |
| Resource | De naam van het Automation-account |
| SourceSystem | Systeem dat Azure Monitor logboeken gebruikt om de gegevens te verzamelen. De waarde is altijd Azure voor Azure Diagnostics. |
| ResultDescription |De resulterende status van de runbook-taak. Mogelijke waarden zijn:<br>- Taak is gestart<br>- Taak is mislukt<br>- Taak is voltooid |
| CorrelationId |De correlatie-GUID van de runbook-taak. |
| ResourceId |De resource-ID van het Azure Automation-account van het runbook. |
| SubscriptionId | De GUID van het Azure-abonnement voor het Automation-account. |
| ResourceGroup | De naam van de resource groep voor het Automation-account. |
| ResourceProvider | De resource provider. De waarde is micro soft. Automat. |
| ResourceType | Het resource type. De waarde is AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Taakstromen
| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |Datum en tijd van uitvoering van de runbooktaak. |
| RunbookName_s |De naam van het runbook. |
| Caller_s |De aanroeper die de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken. |
| StreamType_s |Het type taakstroom. Mogelijke waarden zijn:<br>- Voortgang<br>- Uitvoer<br>- Waarschuwing<br>- Fout<br>- Foutopsporing<br>- Uitgebreid |
| Tenant_g | GUID waarmee de Tenant voor de oproepende functie wordt geïdentificeerd. |
| JobId_g |GUID waarmee de runbook-taak wordt geïdentificeerd. |
| ResultType |De status van de runbooktaak. Mogelijke waarden zijn:<br>-Wordt uitgevoerd |
| Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobStreams. |
| OperationName | Het type bewerking dat in azure wordt uitgevoerd. Voor Automation is de waarde taak. |
| Resource | De naam van het Automation-account. |
| SourceSystem | Systeem dat Azure Monitor logboeken gebruikt om de gegevens te verzamelen. De waarde is altijd Azure voor Azure Diagnostics. |
| ResultDescription |Beschrijving die de uitvoer stroom van het runbook bevat. |
| CorrelationId |De correlatie-GUID van de runbook-taak. |
| ResourceId |De resource-ID van het Azure Automation-account van het runbook. |
| SubscriptionId | De GUID van het Azure-abonnement voor het Automation-account. |
| ResourceGroup | De naam van de resource groep voor het Automation-account. |
| ResourceProvider | De resource provider. De waarde is micro soft. Automat. |
| ResourceType | Het resource type. De waarde is AUTOMATIONACCOUNTS. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Integratie met Azure Monitor-logboeken instellen

1. Start Windows Power shell op uw computer vanuit het **Start** scherm.
2. Voer de volgende Power shell-opdrachten uit en bewerk de `[your resource ID]` waarden `[resource ID of the log analytics workspace]` voor en met de waarden uit de voor gaande sectie.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Na het uitvoeren van dit script kan het een uur duren voordat u begint met het weer geven van records in `JobLogs` Azure monitor `JobStreams` logboeken van nieuw of wordt geschreven.

Als u de logboeken wilt weer geven, voert u de volgende query uit in log Analytics zoeken in Logboeken:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Configuratie controleren

Als u wilt controleren of uw Automation-account logboeken naar uw Log Analytics-werk ruimte verzendt, controleert u of de diagnostische gegevens correct zijn geconfigureerd voor het Automation-account met behulp van de volgende Power shell-opdracht.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Controleer in de uitvoer:

* Onder `Logs`is de waarde voor `Enabled` ingesteld op ' True '.
* `WorkspaceId`is ingesteld op de `ResourceId` waarde voor uw log Analytics-werk ruimte.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Automatiserings logboeken weer geven in Azure Monitor-logboeken

Nu u bent begonnen met het verzenden van uw Automation-taak logboeken naar Azure Monitor-logboeken, ziet u wat u met deze logboeken kunt doen in Azure Monitor-Logboeken.

Voer de volgende query uit om de logboeken weer te geven:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Een e-mail verzenden wanneer een runbook-taak is mislukt of onderbroken

Een van de belangrijkste klanten vraagt om een e-mail of tekst te verzenden wanneer er iets mis gaat met een runbook-taak.

Als u een waarschuwings regel wilt maken, moet u beginnen met het maken van een zoek opdracht in het logboek voor de runbook-taak records die de waarschuwing moeten aanroepen. Klik op de knop **waarschuwing** om de waarschuwings regel te maken en te configureren.

1. Klik op de pagina overzicht van Log Analytics werk ruimte op **Logboeken weer geven**.
2. Maak een zoek opdracht in het logboek voor uw waarschuwing door de volgende zoek opdracht in het query veld in te voeren:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>U kunt ook groeperen op de naam van het runbook met behulp van:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Als u logboeken van meer dan één Automation-account of-abonnement op uw werk ruimte instelt, kunt u uw waarschuwingen groeperen op abonnement en Automation-account. De naam van het Automation-account `Resource` vindt u in het veld `JobLogs`in de zoek actie van.
3. Als u het scherm **regel maken** wilt openen, klikt u op **nieuwe waarschuwings regel** boven aan de pagina. Zie [logboek waarschuwingen in azure](../azure-monitor/platform/alerts-unified-log.md)voor meer informatie over de opties voor het configureren van de waarschuwing.

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Alle taken zoeken die zijn voltooid met fouten

Naast het melden van fouten, kunt u nagaan wanneer een runbook-taak een niet-afsluit fout heeft. In dergelijke gevallen produceert Power shell een fout stroom, maar de niet-afsluit fouten zorgen ervoor dat uw taak niet wordt onderbroken of mislukt.

1. Klik in de werk ruimte Log Analytics op **Logboeken**.
2. Typ `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`in het veld query.
3. Klik op de knop **zoeken** .

### <a name="view-job-streams-for-a-job"></a>Taak stromen voor een taak weer geven

Wanneer u fouten opspoort in een taak, wilt u mogelijk ook de taak stromen bekijken. Met de volgende query worden alle stromen weer gegeven voor één taak met GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Historische taak status weer geven

Ten slotte kunt u uw taak geschiedenis gedurende een periode visualiseren. U kunt deze query gebruiken om de status van uw taken in de loop van de tijd te zoeken.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Status diagram van Log Analytics historische taak](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Diagnostische instellingen verwijderen

Als u de diagnostische instelling van het Automation-account wilt verwijderen, voert u de volgende opdracht uit:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```
## <a name="next-steps"></a>Volgende stappen

* Zie voor hulp bij het oplossen van problemen Log Analytics [problemen oplossen waarom log Analytics niet langer gegevens verzamelt](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Zie [Zoek opdrachten in Logboeken in azure monitor logboeken](../log-analytics/log-analytics-log-searches.md)voor meer informatie over het maken van verschillende Zoek query's en het controleren van de Automation-taak logboeken met Azure monitor-Logboeken.
* Zie [Runbook-uitvoer en berichten](automation-runbook-output-and-messages.md)voor informatie over het maken en ophalen van uitvoer-en fout berichten van runbooks.
* Zie [Runbooktaken bijhouden](automation-runbook-execution.md) voor meer informatie over runbookuitvoering, het bewaken van runbooktaken en andere technische details.
* Zie [Azure Storage-gegevens verzamelen in azure monitor logs Overview](../azure-monitor/platform/collect-azure-metrics-logs.md)voor meer informatie over Azure monitor logboeken en bronnen voor gegevens verzameling.