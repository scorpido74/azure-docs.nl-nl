---
title: Logboeken opvragen vanaf de VM's-oplossing voor begin/stop
description: In dit artikel wordt beschreven hoe u logboekgegevens opvraagt die zijn gegenereerd door de VM's start/stop-oplossing van Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 00f6a5f88a66f0a19943ff3a2c722ae1a9938e9e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550394"
---
# <a name="how-to-query-logs-from-startstop-vms-solution"></a>Logboeken opvragen vanaf de VM's-oplossing voor begin/stop

Azure Automation stuurt twee typen records door naar de gekoppelde Log Analytics-werkruimte: taaklogboeken en taakstromen. Deze gegevens zijn beschikbaar voor [query's](../azure-monitor/log-query/log-query-overview.md) in Azure Monitor.

## <a name="job-logs"></a>Taaklogboeken

|Eigenschap | Beschrijving|
|----------|----------|
|Caller |  Wie de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken.|
|Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobLogs.|
|CorrelationId | GUID is de correlatie-id van de runbook-taak.|
|JobId | GUID dat is de ID van de runbook taak.|
|operationName | Hiermee wordt het type bewerking opgegeven dat in Azure wordt uitgevoerd. Voor Automatisering is de waarde Job.|
|resourceId | Hiermee wordt het resourcetype in Azure opgegeven. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|ResourceGroup | Hiermee wordt de resourcegroepnaam van de runbooktaak opgegeven.|
|ResourceProvider | Hiermee wordt de Azure-service opgegeven waarmee de resources worden geleverd die u kunt implementeren en beheren. Voor Automation is de waarde Azure Automation.|
|ResourceType | Hiermee wordt het resourcetype in Azure opgegeven. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|resultType | De status van de runbooktaak. Mogelijke waarden zijn:<br>- Gestart<br>- Gestopt<br>- Onderbroken<br>- Mislukt<br>- Geslaagd|
|resultDescription | Hiermee wordt resultaatstatus van de runbooktaak beschreven. Mogelijke waarden zijn:<br>- Taak is gestart<br>- Taak is mislukt<br>- Taak is voltooid|
|RunbookName | Hiermee wordt de naam van het runbook opgegeven.|
|SourceSystem | Hiermee wordt het bronsysteem voor de verzonden gegevens opgegeven. Voor Automatisering is de waarde OpsManager|
|StreamType | Hiermee wordt het type gebeurtenis opgegeven. Mogelijke waarden zijn:<br>- Uitgebreid<br>- Uitvoer<br>- Fout<br>- Waarschuwing|
|SubscriptionId | Hiermee wordt de abonnements-id van de taak opgegeven.
|Time | Datum en tijd van uitvoering van de runbooktaak.|

## <a name="job-streams"></a>Taakstromen

|Eigenschap | Beschrijving|
|----------|----------|
|Caller |  Wie de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken.|
|Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobStreams.|
|JobId | GUID dat is de ID van de runbook taak.|
|operationName | Hiermee wordt het type bewerking opgegeven dat in Azure wordt uitgevoerd. Voor Automatisering is de waarde Job.|
|ResourceGroup | Hiermee wordt de resourcegroepnaam van de runbooktaak opgegeven.|
|resourceId | Hiermee geeft u de bron-id in Azure op. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|ResourceProvider | Hiermee wordt de Azure-service opgegeven waarmee de resources worden geleverd die u kunt implementeren en beheren. Voor Automation is de waarde Azure Automation.|
|ResourceType | Hiermee wordt het resourcetype in Azure opgegeven. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|resultType | Het resultaat van de runbooktaak op het moment dat de gebeurtenis werd gegenereerd. Een mogelijke waarde is:<br>- Wordt uitgevoerd|
|resultDescription | Bevat de uitvoerstroom van het runbook.|
|RunbookName | De naam van het runbook.|
|SourceSystem | Hiermee wordt het bronsysteem voor de verzonden gegevens opgegeven. Voor Automatisering is de waarde OpsManager.|
|StreamType | Het type taakstroom. Mogelijke waarden zijn:<br>- Vooruitgang<br>- Uitvoer<br>- Waarschuwing<br>- Fout<br>- Foutopsporing<br>- Uitgebreid|
|Time | Datum en tijd van uitvoering van de runbooktaak.|

Wanneer u een logboekzoekopdracht uitvoert die categorierecords van **JobLogs** of **JobStreams retourneert,** u de weergave **JobLogs** of **JobStreams** selecteren, waarin een set tegels wordt weergegeven waarin de updates worden samengevat die door de zoekopdracht worden geretourneerd.

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken

De volgende tabel bevat voorbeeldzoekopdrachten in logboeken voor taakrecords die worden verzameld met deze oplossing.

|Query’s uitvoeren | Beschrijving|
|----------|----------|
|Vacatures zoeken voor ScheduledStartStop_Parent die zijn voltooid | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Vacatures zoeken voor runbook-ScheduledStartStop_Parent die niet zijn voltooid | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Vacatures zoeken voor runbook-SequencedStartStop_Parent die zijn voltooid | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Vacatures zoeken voor runbook-SequencedStartStop_Parent die niet zijn voltooid | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Volgende stappen

De vm's voor starten/stoppen tijdens de oplossing voor buitenkantooruren bevat geen vooraf gedefinieerde set waarschuwingen. Bekijk de [logboekwaarschuwingen maken](../azure-monitor/platform/alerts-log.md) met Azure Monitor voor meer informatie over het maken van waarschuwingen die zijn mislukt om uw DevOps of operationele processen en procedures te ondersteunen.