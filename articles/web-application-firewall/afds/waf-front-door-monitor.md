---
title: Bewaking en logboek registratie van de firewall van Azure Web Application
description: WAF (Web Application firewall) leren met-ingang controle en logboek registratie
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: b4f666415a96307b89022c6caf6af90581f294f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115360"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Bewaking en logboek registratie van de firewall van Azure Web Application 

Azure Web Application firewall (WAF) bewaking en logboek registratie worden verschaft via logboek registratie en integratie met Azure Monitor-en Azure Monitor-Logboeken.

## <a name="azure-monitor"></a>Azure Monitor

WAF met-ingang-logboek is geïntegreerd met [Azure monitor](../../azure-monitor/overview.md). Met Azure Monitor kunt u Diagnostische gegevens bijhouden, waaronder WAF-waarschuwingen en-Logboeken. U kunt WAF-bewaking configureren binnen de voor deur van de portal op het tabblad **Diagnostische gegevens** of via de Azure Monitor-service rechtstreeks.

Ga vanuit Azure Portal naar het resource type voor deur. Op het tabblad **bewakings**/**gegevens** aan de linkerkant kunt u **WebApplicationFirewallRequestCount** toevoegen om het aantal aanvragen bij te houden dat overeenkomt met WAF-regels. Aangepaste filters kunnen worden gemaakt op basis van actie typen en regel namen.

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

Hier volgt een voor beeld van een geregistreerde aanvraag in WAF-logboek:

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

Met de volgende voorbeeld query worden AccessLogs-vermeldingen opgehaald:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Hier volgt een voor beeld van een vastgelegde aanvraag in het Access-logboek:

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

- Meer informatie over de [voor deur](../../frontdoor/front-door-overview.md).
