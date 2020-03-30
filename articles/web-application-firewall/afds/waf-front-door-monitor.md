---
title: Azure Web Application Firewall-controle en -logboekregistratie
description: Lees Web Application Firewall (WAF) met FrontDoor-monitoring en logboekregistratie
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 4488fadf5db3b32049b5dce4bbee1fa76c320e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284140"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure Web Application Firewall-controle en -logboekregistratie 

De bewaking en logboekregistratie van Azure Web Application Firewall (WAF) wordt geleverd door middel van logboekregistratie en integratie met Azure Monitor- en Azure Monitor-logboeken.

## <a name="azure-monitor"></a>Azure Monitor

WAF met FrontDoor-logboek is geïntegreerd met [Azure Monitor.](../../azure-monitor/overview.md) Met Azure Monitor u diagnostische gegevens bijhouden, waaronder WAF-waarschuwingen en -logboeken. U WAF-bewaking configureren binnen de Front Door-bron in de portal onder het tabblad **Diagnostische gegevens** of via de Azure Monitor-service.

Ga vanuit Azure-portal naar Het brontype Voorde door. Op het tabblad/**Controlestatistieken** aan de linkerkant u **WebApplicationFirewallRequestCount** toevoegen om het aantal aanvragen bij te houden dat overeenkomt met de WAF-regels. **Monitoring** Aangepaste filters kunnen worden gemaakt op basis van actietypen en regelnamen.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Logboeken en diagnostiek

WAF met Voordeur biedt gedetailleerde rapportage over elke bedreiging die het detecteert. Logboekregistratie is geïntegreerd met de logboeken van Azure Diagnostics en waarschuwingen worden vastgelegd in de JSON-indeling. Deze logboeken kunnen worden geïntegreerd met [Azure Monitor-logboeken.](../../azure-monitor/insights/azure-networking-analytics.md)

![WAFDiag WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog registreert alle aanvragen die worden doorgestuurd naar back-ends van klanten. FrontdoorWebApplicationFirewallLog registreert elk verzoek dat overeenkomt met een WAF-regel.

In de volgende voorbeeldquery worden WAF-logboeken op geblokkeerde aanvragen verkregen:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_name_s == "Block"

```

Hier is een voorbeeld van een aangemeld verzoek in WAF log:

``` WAFlogQuerySample
{
    "PreciseTimeStamp": "2020-01-25T00:11:19.3866091Z",
    "time": "2020-01-25T00:11:19.3866091Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "xx.xx.xxx.xxx",
        "socketIP": "xx.xx.xxx.xxx",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/?q=../../x",
        "ruleName": "Microsoft_DefaultRuleSet-1.1-LFI-930100",
        "policy": "WafDemoCustomPolicy",
        "action": "Block",
        "host": "wafdemofrontdoorwebapp.azurefd.net",
        "refString": "0p4crXgAAAABgMq5aIpu0T6AUfCYOroltV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode": "prevention"
    }
}

``` 

In de volgende voorbeeldquery worden AccessLogs-vermeldingen verkregen:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Hier is een voorbeeld van een aangemeld verzoek in Access log:

``` AccessLogSample
{
    "PreciseTimeStamp": "2020-01-25T00:11:12.0160150Z",
    "time": "2020-01-25T00:11:12.0160150Z",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0n4crXgAAAACnRKbdALbyToAqNfSHssDvV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "httpMethod": "GET",
        "httpVersion": "2.0",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/",
        "requestBytes": "710",
        "responseBytes": "3116",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4017.0 Safari/537.36 Edg/81.0.389.2",
        "clientIp": "xx.xx.xxx.xxx",
        "timeTaken": "0.598",
        "securityProtocol": "TLS 1.2",
        "routingRuleName": "WAFdemoWebAppRouting",
        "backendHostname": "wafdemouksouth.azurewebsites.net:443",
        "sentToOriginShield": false,
        "httpStatusCode": "200",
        "httpStatusDetails": "200"
    }
}

```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Voordeur.](../../frontdoor/front-door-overview.md)
