---
title: Azure Automation-taakgegevens doorsturen naar Azure Monitor-logboeken
description: In dit artikel wordt uitgelegd hoe u taakstatus- en runbook-taakstromen naar Azure Monitor-logboeken verzenden om extra inzicht en beheer te bieden.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: a9f4e641e60d6cf1c481c445767422e8b4df683b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457685"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Taakstatus en taakstromen doorsturen van automatisering naar Azure Monitor-logboeken

Automatisering kan de status van runbook-taken en taakstromen naar uw Log Analytics-werkruimte verzenden. Dit proces omvat geen koppeling van de werkruimte en is volledig onafhankelijk. Joblogs en taakstromen zijn zichtbaar in de Azure-portal of met PowerShell voor afzonderlijke taken en hiermee u eenvoudige onderzoeken uitvoeren. Nu u met Azure Monitor-logboeken:

* Krijg inzicht in de status van uw automatiseringstaken.
* Activeer een e-mail of waarschuwing op basis van de status van uw runbook-taak (bijvoorbeeld mislukt of opgeschort).
* Schrijf geavanceerde query's in uw taakstromen.
* Correleer taken tussen automatiseringsaccounts.
* Gebruik aangepaste weergaven en zoekopdrachten om uw runbook-resultaten, runbook-taakstatus en andere gerelateerde belangrijke indicatoren of statistieken te visualiseren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites-and-deployment-considerations"></a>Voorwaarden en implementatieoverwegingen

Als u wilt beginnen met het verzenden van uw automatiseringslogboeken naar Azure Monitor-logboeken, moet u het andere doen:

* De nieuwste versie van [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Een Log Analytics-werkruimte. Zie [Aan de slag met Azure Monitor-logboeken](../log-analytics/log-analytics-get-started.md)voor meer informatie.
* De resource-id voor uw Azure Automation-account.

Gebruik de volgende opdracht om de bron-id voor uw Azure Automation-account te vinden:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Voer de volgende PowerShell-opdracht uit om de resource-id voor uw Log Analytics-werkruimte te vinden:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Als u meer dan één automatiseringsaccount of werkruimte hebt in de uitvoer van de voorgaande opdrachten, zoekt u de naam die u moet configureren en kopieert u de waarde voor de resource-id.

1. Selecteer in de Azure-portal uw Automatiseringsaccount in het beheer van het **automatiseringsaccount** en selecteer **Alle instellingen**. 
2. Selecteer **Eigenschappen**in het blad **Alle instellingen** onder **Accountinstellingen**.  
3. Noteer in het blad **Eigenschappen** de eigenschappen die hieronder worden weergegeven.

    ![Eigenschappen van automatiseringsaccount](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Logboekrecords voor Azure Monitor

Azure Automation-diagnostiek maakt twee typen records in `AzureDiagnostics`Azure Monitor-logboeken, gelabeld als . De tabellen in de volgende secties zijn voorbeelden van records die Azure Automation genereert en de gegevenstypen die worden weergegeven in logboekzoekresultaten.

### <a name="job-logs"></a>Taaklogboeken

| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |Datum en tijd van uitvoering van de runbooktaak. |
| RunbookName_s |De naam van het runbook. |
| Caller_s |De beller die de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken. |
| Tenant_g | GUID die de tenant voor de beller identificeert. |
| JobId_g |GUID die de runbook-taak identificeert. |
| ResultType |De status van de runbooktaak. Mogelijke waarden zijn:<br>- Nieuw<br>- Gemaakt<br>- Gestart<br>- Gestopt<br>- Onderbroken<br>- Mislukt<br>- Voltooid |
| Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobLogs. |
| OperationName | Het type bewerking dat in Azure wordt uitgevoerd. Voor Automatisering is de waarde Job. |
| Resource | De naam van het automatiseringsaccount |
| SourceSystem | Systeem dat Azure Monitor-logboeken gebruiken om de gegevens te verzamelen. De waarde is altijd Azure voor Azure-diagnostiek. |
| ResultaatBeschrijving |De status van het runbook-taakresultaat. Mogelijke waarden zijn:<br>- Taak is gestart<br>- Taak is mislukt<br>- Taak is voltooid |
| CorrelationId |De correlatie-GUID van de runbook-taak. |
| ResourceId |De Azure Automation-accountbron-id van het runbook. |
| SubscriptionId | De GUID van het Azure-abonnement voor het automatiseringsaccount. |
| ResourceGroup | De naam van de resourcegroep voor het account Automatisering. |
| ResourceProvider | De resourceprovider. De waarde is MICROSOFT. Automatisering. |
| ResourceType | Het resourcetype. De waarde is AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Taakstromen
| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |Datum en tijd van uitvoering van de runbooktaak. |
| RunbookName_s |De naam van het runbook. |
| Caller_s |De beller die de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken. |
| StreamType_s |Het type taakstroom. Mogelijke waarden zijn:<br>- Voortgang<br>- Uitvoer<br>- Waarschuwing<br>- Fout<br>- Foutopsporing<br>- Uitgebreid |
| Tenant_g | GUID die de tenant voor de beller identificeert. |
| JobId_g |GUID die de runbook-taak identificeert. |
| ResultType |De status van de runbooktaak. Mogelijke waarden zijn:<br>- In uitvoering |
| Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobStreams. |
| OperationName | Type bewerking uitgevoerd in Azure. Voor Automatisering is de waarde Job. |
| Resource | De naam van het automatiseringsaccount. |
| SourceSystem | Systeem dat Azure Monitor-logboeken gebruiken om de gegevens te verzamelen. De waarde is altijd Azure voor Azure-diagnostiek. |
| ResultaatBeschrijving |Beschrijving die de uitvoerstroom uit het runbook bevat. |
| CorrelationId |De correlatie-GUID van de runbook-taak. |
| ResourceId |De Azure Automation-accountbron-id van het runbook. |
| SubscriptionId | De GUID van het Azure-abonnement voor het automatiseringsaccount. |
| ResourceGroup | De naam van de resourcegroep voor het account Automatisering. |
| ResourceProvider | De resourceprovider. De waarde is MICROSOFT. Automatisering. |
| ResourceType | Het resourcetype. De waarde is AUTOMATIONACCOUNTS. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Integratie instellen met Azure Monitor-logboeken

1. Start Windows PowerShell op uw computer vanaf **het** startscherm.
2. Voer de volgende PowerShell-opdrachten uit en `[your resource ID]` `[resource ID of the log analytics workspace]` bewerk de waarden voor en met de waarden uit de vorige sectie.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Nadat u dit script hebt uitgevoerd, kan het een uur duren `JobLogs` `JobStreams` voordat u records in Azure Monitor-logboeken van nieuwe of geschreven logboeken ziet.

Als u de logboeken wilt bekijken, voert u de volgende query uit in logboekanalyseslogboekzoekopdrachten:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Configuratie verifiëren

Als u wilt controleren of uw Automatiseringsaccount logboeken verzendt naar uw Log Analytics-werkruimte, controleert u of de diagnose correct is geconfigureerd op het automatiseringsaccount met behulp van de volgende PowerShell-opdracht.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Zorg er in de uitvoer voor dat:

* Onder `Logs`, de `Enabled` waarde voor is Waar.
* `WorkspaceId`is ingesteld `ResourceId` op de waarde voor uw Log Analytics-werkruimte.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Automatiseringslogboeken weergeven in Azure Monitor-logboeken

Nu u uw automatiseringstaaklogboeken naar Azure Monitor-logboeken bent gaan verzenden, bekijken we wat u doen met deze logboeken in Azure Monitor-logboeken.

Voer de volgende query uit om de logboeken te bekijken:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Een e-mail verzenden wanneer een runbook-taak mislukt of wordt opgeschort

Een van de beste klanten vraagt is voor de mogelijkheid om een e-mail of een tekst te sturen wanneer er iets mis gaat met een runbook baan.

Als u een waarschuwingsregel wilt maken, moet u eerst een logboekzoekopdracht maken voor de taakrecords voor runbook die de waarschuwing moeten aanroepen. Klik **op** de knop Waarschuwing om de waarschuwingsregel te maken en te configureren.

1. Klik op de pagina Overzicht van de werkruimte Log Analytics op **Logboeken weergeven**.
2. Maak een logboekzoekopdracht voor uw waarschuwing door de volgende zoekopdracht in het queryveld te typen:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>U ook groeperen op de naam van het runbook met behulp van:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Als u logboeken instelt van meer dan één Automatiseringsaccount of -abonnement op uw werkruimte, u uw waarschuwingen groeperen op abonnements- en automatiseringsaccount. De naam van `Resource` `JobLogs`het automatiseringsaccount is te vinden in het veld bij het zoeken naar .
3. Als u het scherm **Regel maken wilt** openen, klikt u boven aan de pagina op Nieuwe **waarschuwingsregel.** Zie [Logboekwaarschuwingen in Azure](../azure-monitor/platform/alerts-unified-log.md)voor meer informatie over de opties om de waarschuwing te configureren.

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Alle taken zoeken die zijn voltooid met fouten

Naast het waarschuwen op fouten, u vinden wanneer een runbook-taak een niet-beëindigende fout heeft. In deze gevallen produceert PowerShell een foutstroom, maar de niet-beëindigende fouten zorgen er niet voor dat uw taak wordt onderbroken of mislukt.

1. Klik in uw werkruimte Log Analytics op **Logboeken**.
2. Typ in het `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`queryveld .
3. Klik op de knop **Zoeken.**

### <a name="view-job-streams-for-a-job"></a>Jobstreams voor een taak weergeven

Wanneer u een taak debugt, u ook in de taakstromen kijken. De volgende query toont alle streams voor een enkele taak met GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Historische functiestatus weergeven

Tot slot wilt u misschien uw werkgeschiedenis in de loop van de tijd visualiseren. U deze query gebruiken om na verloop van tijd te zoeken naar de status van uw taken.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Grafiek historische functiestatus van Logboekanalyse](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Diagnostische instellingen verwijderen

Voer de volgende opdracht uit om de diagnostische instelling uit het automatiseringsaccount te verwijderen:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```
## <a name="next-steps"></a>Volgende stappen

* Zie [Probleemoplossing waarom Log Analytics geen gegevens meer verzamelt](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)voor hulp bij het oplossen van problemen met Logboekanalyse.
* Zie [Logboekzoekopdrachten in Azure Monitor-logboeken](../log-analytics/log-analytics-log-searches.md)voor meer informatie over het maken van verschillende zoekopdrachten en het controleren van de functielogboeken voor automatisering met Azure Monitor-logboeken.
* Zie Uitvoer van [runbooks en berichten](automation-runbook-output-and-messages.md)uitvoeren en ophalen als u wilt begrijpen hoe u uitvoer- en foutberichten uit runbooks maken en ophalen.
* Zie [Runbooktaken bijhouden](automation-runbook-execution.md) voor meer informatie over runbookuitvoering, het bewaken van runbooktaken en andere technische details.
* Zie [Azure-opslaggegevens verzamelen in azure monitor-logboekenoverzicht](../azure-monitor/platform/collect-azure-metrics-logs.md)voor meer informatie over Azure Monitor-logboeken en gegevensverzamelingsbronnen.