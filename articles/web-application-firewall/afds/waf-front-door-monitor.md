---
title: Bewaking en logboek registratie van de firewall van Azure Web Application
description: WAF (Web Application firewall) leren met-ingang controle en logboek registratie
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 0c705139c082f13f40362e598f0fda9ba0a128a5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512481"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Bewaking en logboek registratie van de firewall van Azure Web Application 

Azure Web Application firewall (WAF) bewaking en logboek registratie worden verschaft via logboek registratie en integratie met Azure Monitor-en Azure Monitor-Logboeken.

## <a name="azure-monitor"></a>Azure Monitor

WAF met-ingang-logboek is geïntegreerd met [Azure monitor](../../azure-monitor/overview.md). Met Azure Monitor kunt u Diagnostische gegevens bijhouden, waaronder WAF-waarschuwingen en-Logboeken. U kunt WAF-bewaking configureren binnen de voor deur van de portal op het tabblad **Diagnostische gegevens** of via de Azure Monitor-service rechtstreeks.

Ga vanuit Azure Portal naar het resource type voor deur. Op het tabblad **bewaking**/**metrische gegevens** aan de linkerkant kunt u **WebApplicationFirewallRequestCount** toevoegen voor het bijhouden van het aantal aanvragen dat overeenkomt met WAF-regels. Aangepaste filters kunnen worden gemaakt op basis van actie typen en regel namen.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Logboeken en diagnostische gegevens

WAF met de voor deur biedt gedetailleerde rapportage over elke bedreiging die wordt gedetecteerd. Logboekregistratie is geïntegreerd met de logboeken van Azure Diagnostics en waarschuwingen worden vastgelegd in de JSON-indeling. Deze logboeken kunnen worden geïntegreerd met [Azure monitor-logboeken](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog registreert alle aanvragen die worden doorgestuurd naar de back-ends van de klant. FrontdoorWebApplicationFirewallLog registreert alle aanvragen die overeenkomen met een WAF-regel.

Met de volgende voorbeeld query worden WAF-logboeken op geblokkeerde aanvragen opgehaald:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Met de volgende voorbeeld query worden AccessLogs-vermeldingen opgehaald:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [voor deur](../../frontdoor/front-door-overview.md).

