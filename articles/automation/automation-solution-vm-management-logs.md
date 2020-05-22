---
title: Query's uitvoeren op Logboeken van Azure Automation VM's buiten bedrijfsuren starten/stoppen
description: In dit artikel leest u hoe u Azure Monitor kunt gebruiken om logboek gegevens te zoeken die zijn gegenereerd door VM's buiten bedrijfsuren starten/stoppen.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 49b573c5cd0125ba378f01db05b925f8e5eef3eb
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743729"
---
# <a name="query-logs-from-startstop-vms-during-off-hours"></a>Query's uitvoeren op Logboeken van VM's buiten bedrijfsuren starten/stoppen

Azure Automation twee typen records worden doorgestuurd naar de gekoppelde Log Analytics werk ruimte: taak logboeken en taak stromen. In dit artikel worden de gegevens weer gegeven die beschikbaar zijn voor [query's](../azure-monitor/log-query/log-query-overview.md) in azure monitor.

## <a name="job-logs"></a>Taaklogboeken

|Eigenschap | Beschrijving|
|----------|----------|
|Caller |  Wie de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken.|
|Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobLogs.|
|CorrelationId | De GUID die de correlatie-ID van de runbook-taak is.|
|JobId | De GUID die de ID van de runbook-taak is.|
|operationName | Hiermee wordt het type bewerking opgegeven dat in Azure wordt uitgevoerd. Voor Automation is de waarde taak.|
|resourceId | Hiermee wordt het resourcetype in Azure opgegeven. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|ResourceGroup | Hiermee wordt de resourcegroepnaam van de runbooktaak opgegeven.|
|ResourceProvider | Hiermee wordt de Azure-service opgegeven waarmee de resources worden geleverd die u kunt implementeren en beheren. Voor Automation is de waarde Azure Automation.|
|ResourceType | Hiermee wordt het resourcetype in Azure opgegeven. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|resultType | De status van de runbooktaak. Mogelijke waarden zijn:<br>- Gestart<br>- Gestopt<br>- Onderbroken<br>- Mislukt<br>- Geslaagd|
|resultDescription | Hiermee wordt resultaatstatus van de runbooktaak beschreven. Mogelijke waarden zijn:<br>- Taak is gestart<br>- Taak is mislukt<br>- Taak is voltooid|
|RunbookName | Hiermee wordt de naam van het runbook opgegeven.|
|SourceSystem | Hiermee wordt het bronsysteem voor de verzonden gegevens opgegeven. Voor Automation is de waarde OpsManager|
|StreamType | Hiermee wordt het type gebeurtenis opgegeven. Mogelijke waarden zijn:<br>- Uitgebreid<br>- Uitvoer<br>- Fout<br>- Waarschuwing|
|SubscriptionId | Hiermee wordt de abonnements-id van de taak opgegeven.
|Time | Datum en tijd van uitvoering van de runbooktaak.|

## <a name="job-streams"></a>Taakstromen

|Eigenschap | Beschrijving|
|----------|----------|
|Caller |  Wie de bewerking heeft gestart. Mogelijke waarden zijn een e-mailadres of het systeem voor geplande taken.|
|Categorie | Classificatie van het type gegevens. Voor Automation is de waarde JobStreams.|
|JobId | De GUID die de ID van de runbook-taak is.|
|operationName | Hiermee wordt het type bewerking opgegeven dat in Azure wordt uitgevoerd. Voor Automation is de waarde taak.|
|ResourceGroup | Hiermee wordt de resourcegroepnaam van de runbooktaak opgegeven.|
|resourceId | Hiermee geeft u de resource-ID in azure op. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|ResourceProvider | Hiermee wordt de Azure-service opgegeven waarmee de resources worden geleverd die u kunt implementeren en beheren. Voor Automation is de waarde Azure Automation.|
|ResourceType | Hiermee wordt het resourcetype in Azure opgegeven. Voor Automation is de waarde het Automation-account dat is gekoppeld aan het runbook.|
|resultType | Het resultaat van de runbooktaak op het moment dat de gebeurtenis werd gegenereerd. Een mogelijke waarde is:<br>- Wordt uitgevoerd|
|resultDescription | Bevat de uitvoerstroom van het runbook.|
|RunbookName | De naam van het runbook.|
|SourceSystem | Hiermee wordt het bronsysteem voor de verzonden gegevens opgegeven. Voor Automation is de waarde OpsManager.|
|StreamType | Het type taakstroom. Mogelijke waarden zijn:<br>-Voortgang<br>- Uitvoer<br>- Waarschuwing<br>- Fout<br>- Foutopsporing<br>- Uitgebreid|
|Time | Datum en tijd van uitvoering van de runbooktaak.|

Wanneer u een zoek opdracht in het logboek uitvoert waarmee categorie records van **JobLogs** of **JobStreams**worden geretourneerd, kunt u de weer gave **JobLogs** of **JobStreams** selecteren, waarin een reeks tegels wordt weer gegeven met een overzicht van de updates die worden geretourneerd door de zoek opdracht.

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken

De volgende tabel bevat voor beelden van zoek opdrachten in Logboeken voor taak records die worden verzameld door VM's buiten bedrijfsuren starten/stoppen.

|Query’s uitvoeren | Beschrijving|
|----------|----------|
|Taken zoeken voor runbook-ScheduledStartStop_Parent die zijn voltooid | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Taken zoeken voor runbook-ScheduledStartStop_Parent die niet zijn voltooid | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Taken zoeken voor runbook-SequencedStartStop_Parent die zijn voltooid | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Taken zoeken voor runbook-SequencedStartStop_Parent die niet zijn voltooid | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Volgende stappen

* [Logboek waarschuwingen met Azure Monitor maken](../azure-monitor/platform/alerts-log.md)
* [Vm's voor stoppen/starten configureren tijdens buiten kantoor uren](automation-solution-vm-management-config.md)
* [Problemen met VM's buiten bedrijfsuren starten/stoppen oplossen](troubleshoot/start-stop-vm.md)