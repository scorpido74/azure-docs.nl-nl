---
title: Waarschuwing over problemen in Azure Cloud Services met behulp van de Azure Diagnostics-integratie met Azure Application Insights | Microsoft Documenten
description: Controleren op problemen zoals opstartfouten, crashes en rolrecyclinglussen in Azure Cloud Services met Azure Application Insights
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: 997c5e063c4181a597520e60e2a7669401b9677d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669740"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Waarschuwing over problemen in Azure Cloud Services met behulp van de Azure-diagnostische integratie met Azure Application Insights

In dit artikel beschrijven we hoe u waarschuwingsregels instelt die controleren op problemen zoals opstartfouten, crashes en rolrecyclinglussen in Azure Cloud Services (web- en werknemersrollen).

De methode die in dit artikel wordt beschreven, is gebaseerd op de [Azure Diagnostics-integratie met Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)en de onlangs uitgebrachte mogelijkheid [Logboekwaarschuwingen voor Application Insights.](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/)

## <a name="define-a-base-query"></a>Een basisquery definiëren

Om aan de slag te gaan, definiëren we een basisquery waarmee de gebeurtenissen in Windows Event Log worden opgehaald uit het Windows Azure-kanaal, die worden vastgelegd in Application Insights als traceerrecords.
Deze records kunnen worden gebruikt voor het detecteren van verschillende problemen in Azure Cloud Services, zoals opstartfouten, runtime-fouten en recyclelussen.

> [!NOTE]
> De onderstaande basisquery controleert op problemen in een tijdvenster van 30 minuten en gaat uit van een latentie van 10 minuten bij het innemen van de telemetrierecords. Deze standaardinstellingen kunnen naar eigen inzicht worden geconfigureerd.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>Controleren op specifieke gebeurtenis--geïdentificeerde kenmerken

Na het ophalen van de gebeurtenissen in het Windows-gebeurtenislogboek kunnen specifieke problemen worden gedetecteerd door te controleren op hun respectievelijke gebeurtenis-id en berichteigenschappen (zie voorbeelden hieronder).
Combineer de bovenstaande basisquery met een van de onderstaande query's en gebruik die gecombineerde query bij het definiëren van de regel voor logboekwaarschuwingen.

> [!NOTE]
> In de onderstaande voorbeelden wordt een probleem gedetecteerd als er meer dan drie gebeurtenissen worden gevonden tijdens het geanalyseerde tijdvenster. Deze standaardinstelling kan worden geconfigureerd om de gevoeligheid van de waarschuwingsregel te wijzigen.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>Een waarschuwing maken

Ga in het navigatiemenu in de bron Application Insights naar **Waarschuwingen**en selecteer **Vervolgens Nieuwe waarschuwingsregel**.

![Schermafbeelding van Regel maken](./media/proactive-cloud-services/001.png)

Klik in het venster **Regel maken** onder de sectie **Waarschuwingsvoorwaarde definiëren** op Criteria **toevoegen**en selecteer Vervolgens **Aangepaste logboekzoekopdrachten**.

![Schermafbeelding van de voorwaardencriteria voor waarschuwing definiëren](./media/proactive-cloud-services/002.png)

Plak in het queryvak **Zoeken** de gecombineerde query die u in de vorige stap hebt voorbereid.

Ga vervolgens verder naar het vak **Drempelwaarde** en stel de waarde ervan in op 0. U optioneel de **velden** **Periode en** Frequentie aanpassen.
Klik op **Gereed**.

![Schermafbeelding van de query voor signaallogica configureren](./media/proactive-cloud-services/003.png)

Geef onder de sectie **Waarschuwingsdetails definiëren** een **naam** en **beschrijving** op aan de waarschuwingsregel en stel de **ernst**in.
Zorg er ook voor dat de **knop Inschakelen bij het maken** is ingesteld op **Ja**.

![Details van schermafbeeldingen](./media/proactive-cloud-services/004.png)

Onder de sectie **Actiegroep definiëren** u een bestaande **actiegroep** selecteren of een nieuwe groep maken.
U ervoor kiezen om de actiegroep meerdere acties van verschillende typen te laten bevatten.

![Schermafbeelding van de actiegroep](./media/proactive-cloud-services/005.png)

Zodra u de actiegroep hebt gedefinieerd, bevestigt u de wijzigingen en klikt u op **Waarschuwingsregel maken**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over automatisch detecteren:

[Storing anomalieën](../../azure-monitor/app/proactive-failure-diagnostics.md)
[Memory Leaks](../../azure-monitor/app/proactive-potential-memory-leak.md)
[Prestatieafwijkingen](../../azure-monitor/app/proactive-performance-diagnostics.md)

