---
title: Azure Automation-taakgegevens doorsturen naar Azure Monitor-logboeken
description: In dit artikel wordt uitgelegd hoe u taakstatus- en runbook-taakstromen naar Azure Monitor-logboeken verzenden om extra inzicht en beheer te bieden.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: beb69edc57b5a13db0f6d2e5e1536804f3472aff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75421904"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Taakstatus en taakstromen doorsturen van automatisering naar Azure Monitor-logboeken

Automatisering kan de status van runbook-taken en taakstromen naar uw Log Analytics-werkruimte verzenden. Dit proces omvat geen koppeling van de werkruimte en is volledig onafhankelijk. Joblogs en taakstromen zijn zichtbaar in de Azure-portal of met PowerShell voor afzonderlijke taken en hiermee u eenvoudige onderzoeken uitvoeren. Nu u met Azure Monitor-logboeken:

* Krijg inzicht in uw automatiseringstaken.
* Activeer een e-mail of waarschuwing op basis van de status van uw runbook-taak (bijvoorbeeld mislukt of opgeschort).
* Schrijf geavanceerde query's in uw taakstromen.
* Correleer taken tussen automatiseringsaccounts.
* Visualiseer uw werkgeschiedenis in de loop van de tijd.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Voorwaarden en implementatieoverwegingen

Als u wilt beginnen met het verzenden van uw automatiseringslogboeken naar Azure Monitor-logboeken, moet u het andere doen:

* De nieuwste versie van [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Een Log Analytics-werkruimte. Zie [Aan de slag met Azure Monitor-logboeken](../log-analytics/log-analytics-get-started.md)voor meer informatie.
* De ResourceId voor uw Azure Automation-account.

Ga als verkenner op zoek naar de ResourceId voor uw Azure Automation-account:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Voer de volgende PowerShell uit om de ResourceId voor uw Log Analytics-werkruimte te vinden:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Als u meer dan één automatiseringsaccounts of werkruimten hebt in de uitvoer van de voorgaande opdrachten, zoekt u de *naam* die u moet configureren en kopiëren voor *ResourceId.*

Als u de *naam* van uw automatiseringsaccount wilt zoeken, selecteert u in de Azure-portal uw automatiseringsaccount in het beheer van het **automatiseringsaccount** blad en selecteert **u Alle instellingen**. Selecteer op de blade **Alle instellingen** onder **Accountinstellingen** de optie **Eigenschappen**.  Noteer de waarden die op de blade **Eigenschappen** worden weergegeven.<br> ![Eigenschappen van](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)automatiseringsaccount .

## <a name="set-up-integration-with-azure-monitor-logs"></a>Integratie instellen met Azure Monitor-logboeken

1. Start **Windows PowerShell** op uw computer vanaf **het** startscherm.
2. Voer de volgende PowerShell uit en `[your resource id]` bewerk de waarde voor en `[resource id of the log analytics workspace]` met de waarden uit de vorige stap.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Nadat u dit script hebt uitgevoerd, kan het een uur duren voordat u records in Azure Monitor-logboeken van nieuwe JobLogs of JobStreams ziet die worden geschreven.

Als u de logboeken wilt bekijken, voert u de volgende query uit in logboekanalyseslogboekzoekopdrachten:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Configuratie verifiëren

Controleer op het automatiseringsaccount of de diagnoses correct zijn geconfigureerd op het Automatiseringsaccount met behulp van de volgende PowerShell om te controleren of uw automatiseringsaccount logboeken verzendt naar uw log analytics-werkruimte:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Zorg er in de uitvoer voor dat:

* Onder *Logboeken*is de waarde voor *Ingeschakeld* *Waar*.
* De waarde van *WorkspaceId* is ingesteld op de ResourceId van uw Log Analytics-werkruimte.

## <a name="azure-monitor-log-records"></a>Logboekrecords voor Azure Monitor

Diagnostische gegevens van Azure Automation maken twee typen records in Azure Monitor-logboeken en worden getagd als **AzureDiagnostics**. De volgende query's gebruiken de bijgewerkte querytaal naar Azure Monitor-logboeken. Ga naar [Legacy naar het nieuwe spiekblad Azure Kusto Query Language voor](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language) informatie over veelvoorkomende query's tussen oudere querytaal en de nieuwe Azure Kusto-querytaal

### <a name="job-logs"></a>Taaklogboeken

| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |Datum en tijd van uitvoering van de runbooktaak. |
| RunbookName_s |De naam van het runbook. |
| Caller_s |Wie de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken. |
| Tenant_g | GUID die de tenant voor de beller identificeert. |
| JobId_g |De GUID die de id van de runbooktaak is. |
| ResultType |De status van de runbooktaak. Mogelijke waarden zijn:<br>- Nieuw<br>- Gemaakt<br>- Gestart<br>- Gestopt<br>- Onderbroken<br>- Mislukt<br>- Voltooid |
| Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobLogs. |
| OperationName | Hiermee wordt het type bewerking opgegeven dat in Azure wordt uitgevoerd. Voor Automatisering is de waarde Job. |
| Resource | Naam van het automatiseringsaccount |
| SourceSystem | Hoe Azure Monitor-logboeken de gegevens verzamelden. Altijd *Azure* voor Azure-diagnostiek. |
| ResultaatBeschrijving |Hiermee wordt resultaatstatus van de runbooktaak beschreven. Mogelijke waarden zijn:<br>- Taak is gestart<br>- Taak is mislukt<br>- Taak is voltooid |
| CorrelationId |De GUID die de correlatie-id van de runbooktaak is. |
| ResourceId |Hiermee geeft u de azure automation-accountbron-id van de runbook op. |
| SubscriptionId | De Azure-abonnements-id (GUID) voor het automatiseringsaccount. |
| ResourceGroup | Naam van de resourcegroep voor het account Automatisering. |
| ResourceProvider | Microsoft. Automatisering |
| ResourceType | AUTOMATISERINGSREKENINGEN |


### <a name="job-streams"></a>Taakstromen
| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |Datum en tijd van uitvoering van de runbooktaak. |
| RunbookName_s |De naam van het runbook. |
| Caller_s |Wie de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken. |
| StreamType_s |Het type taakstroom. Mogelijke waarden zijn:<br>- Voortgang<br>- Uitvoer<br>- Waarschuwing<br>- Fout<br>- Foutopsporing<br>- Uitgebreid |
| Tenant_g | GUID die de tenant voor de beller identificeert. |
| JobId_g |De GUID die de id van de runbooktaak is. |
| ResultType |De status van de runbooktaak. Mogelijke waarden zijn:<br>- In uitvoering |
| Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobStreams. |
| OperationName | Hiermee wordt het type bewerking opgegeven dat in Azure wordt uitgevoerd. Voor Automatisering is de waarde Job. |
| Resource | Naam van het automatiseringsaccount |
| SourceSystem | Hoe Azure Monitor-logboeken de gegevens verzamelden. Altijd *Azure* voor Azure-diagnostiek. |
| ResultaatBeschrijving |Bevat de uitvoerstroom van het runbook. |
| CorrelationId |De GUID die de correlatie-id van de runbooktaak is. |
| ResourceId |Hiermee geeft u de azure automation-accountbron-id van de runbook op. |
| SubscriptionId | De Azure-abonnements-id (GUID) voor het automatiseringsaccount. |
| ResourceGroup | Naam van de resourcegroep voor het account Automatisering. |
| ResourceProvider | Microsoft. Automatisering |
| ResourceType | AUTOMATISERINGSREKENINGEN |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Automatiseringslogboeken weergeven in Azure Monitor-logboeken

Nu u uw automatiseringstaaklogboeken naar Azure Monitor-logboeken bent gaan verzenden, bekijken we wat u doen met deze logboeken in Azure Monitor-logboeken.

Voer de volgende query uit om de logboeken te bekijken:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Een e-mail verzenden wanneer een runbook-taak mislukt of wordt opgeschort
Een van de beste klanten vraagt is voor de mogelijkheid om een e-mail of een tekst te sturen wanneer er iets mis gaat met een runbook baan.

Als u een waarschuwingsregel wilt maken, maakt u een logboekzoekopdracht voor de taakrecords voor runbook die de waarschuwing moeten aanroepen. Klik **op** de knop Waarschuwing om de waarschuwingsregel te maken en te configureren.

1. Klik op de pagina Overzicht van de werkruimte Log Analytics op **Logboeken weergeven**.
2. Maak een logboekzoekopdracht voor uw waarschuwing door de volgende `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` zoekopdracht in het queryveld te typen: u ook groeperen op runbookName met behulp van:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Als u logboeken instelt van meer dan één Automatiseringsaccount of -abonnement op uw werkruimte, u uw waarschuwingen groeperen op abonnements- en automatiseringsaccount. De naam van automatiseringsaccount is te vinden in het veld Resource in de zoekopdracht van JobLogs.
3. Als u het scherm **Regel maken wilt** openen, klikt u boven aan de pagina op + Nieuwe **waarschuwingsregel.** Zie [Logboekwaarschuwingen in Azure](../azure-monitor/platform/alerts-unified-log.md)voor meer informatie over de opties om de waarschuwing te configureren.

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Alle taken zoeken die zijn voltooid met fouten
Naast het waarschuwen op fouten, u vinden wanneer een runbook-taak een niet-beëindigende fout heeft. In deze gevallen produceert PowerShell een foutstroom, maar de niet-beëindigende fouten zorgen er niet voor dat uw taak wordt onderbroken of mislukt.

1. Klik in uw werkruimte Log Analytics op **Logboeken**.
2. Typ in het `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` queryveld en klik op de knop **Zoeken.**

### <a name="view-job-streams-for-a-job"></a>Jobstreams voor een taak weergeven
Wanneer u een taak debugt, u ook in de taakstromen kijken. De volgende query toont alle streams voor een enkele taak met GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Historische functiestatus weergeven
Tot slot wilt u misschien uw werkgeschiedenis in de loop van de tijd visualiseren. U deze query gebruiken om na verloop van tijd te zoeken naar de status van uw taken.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Grafiek historische functiestatus van Logboekanalyse](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Diagnostische instellingen verwijderen

Voer de volgende opdrachten uit om de diagnostische instelling uit het automatiseringsaccount te verwijderen:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Samenvatting

Door uw automatiseringstaakstatus en streamgegevens naar Azure Monitor-logboeken te verzenden, krijgt u beter inzicht in de status van uw automatiseringstaken door:
+ Het instellen van waarschuwingen om u op de hoogte te stellen wanneer er een probleem is.
+ Aangepaste weergaven en zoekopdrachten gebruiken om uw runbook-resultaten, runbook-taakstatus en andere gerelateerde belangrijke indicatoren of statistieken te visualiseren.

Azure Monitor-logboeken bieden een grotere operationele zichtbaarheid voor uw automatiseringstaken en kunnen incidenten sneller oplossen.

## <a name="next-steps"></a>Volgende stappen

* Zie [Probleemoplossing waarom Log Analytics geen gegevens meer verzamelt](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)voor hulp bij het oplossen van problemen met Logboekanalyse.
* Zie [Logboekzoekopdrachten in Azure Monitor-logboeken](../log-analytics/log-analytics-log-searches.md)voor meer informatie over het maken van verschillende zoekopdrachten en het controleren van de functielogboeken voor automatisering met Azure Monitor-logboeken.
* Zie Uitvoer van [runbooks en berichten](automation-runbook-output-and-messages.md)uitvoeren en ophalen als u wilt begrijpen hoe u uitvoer- en foutberichten uit runbooks maken en ophalen.
* Zie [Runbooktaken bijhouden](automation-runbook-execution.md) voor meer informatie over runbookuitvoering, het bewaken van runbooktaken en andere technische details.
* Zie [Azure-opslaggegevens verzamelen in azure monitor-logboekenoverzicht](../azure-monitor/platform/collect-azure-metrics-logs.md)voor meer informatie over Azure Monitor-logboeken en gegevensverzamelingsbronnen.

