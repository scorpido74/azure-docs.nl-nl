---
title: WAF-logboeken onderzoeken met Azure Log Analytics
titleSuffix: Azure Application Gateway
description: In dit artikel ziet u hoe u Azure Log Analytics gebruiken om firewalllogboeken van Application Gateway Web Application Firewall te onderzoeken
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 9fe4462a71852e5f66268f798f6f0418f2dd39c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048103"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Logboekanalyse gebruiken om Web Application Firewall-logboeken van Application Gateway te onderzoeken

Zodra uw Application Gateway WAF operationeel is, u logboeken inschakelen om te controleren wat er met elke aanvraag gebeurt. Firewalllogboeken geven inzicht in wat de WAF evalueert, matcht en blokkeert. Met Log Analytics u de gegevens in de firewalllogboeken onderzoeken om nog meer inzichten te geven. Zie [Een werkruimte logboekanalyse maken in de Azure-portal](../azure-monitor/learn/quick-create-workspace.md)voor meer informatie over het maken van een werkruimte voor Logboekanalyse. Zie [Overzicht van logboekquery's in Azure Monitor](../azure-monitor/log-query/log-query-overview.md)voor meer informatie over logboekquery's.

## <a name="import-waf-logs"></a>WAF-logboeken importeren

Zie [Back-endstatus, diagnostische logboeken en statistieken voor Application Gateway als](application-gateway-diagnostics.md#diagnostic-logging)u uw firewalllogboeken wilt importeren in Log Analytics. Wanneer u de firewalllogboeken in uw Log Analytics-werkruimte hebt, u gegevens bekijken, query's schrijven, visualisaties maken en toevoegen aan uw portaldashboard.

## <a name="explore-data-with-examples"></a>Gegevens verkennen met voorbeelden

Als u de ruwe gegevens in het firewalllogboek wilt weergeven, u de volgende query uitvoeren:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Dit lijkt op de volgende query:

![Query Logboekanalyse](media/log-analytics/log-query.png)

U inzoomen op de gegevens en grafieken inkaart of vanaf hier visualisaties maken. Zie de volgende query's als uitgangspunt:

### <a name="matchedblocked-requests-by-ip"></a>Gematchte/geblokkeerde aanvragen per IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Gematchte/geblokkeerde aanvragen door URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Hoogste overeenkomende regels

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Top vijf overeenkomende regelgroepen

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Toevoegen aan uw dashboard

Zodra u een query hebt gemaakt, u deze toevoegen aan uw dashboard.  Selecteer het **dashboard Vastmaken aan** het dashboard rechtsboven in de werkruimte logboekanalyse. Bij de vorige vier query's die zijn vastgemaakt aan een voorbeelddashboard, zijn dit de gegevens die u in één oogopslag zien:

![Dashboard](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Volgende stappen

[Back-endstatus, diagnostische logboeken en statistieken voor Application Gateway](application-gateway-diagnostics.md)
