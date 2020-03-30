---
title: Regels aanpassen met PowerShell
titleSuffix: Azure Web Application Firewall
description: In dit artikel vindt u informatie over het aanpassen van firewallregels voor webtoepassingen in Application Gateway met PowerShell.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048510"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>Firewallregels voor webtoepassingen aanpassen met PowerShell

De Azure Application Gateway Web Application Firewall (WAF) biedt bescherming voor webtoepassingen. Deze beveiligingen worden geboden door de Open Web Application Security Project (OWASP) Core Rule Set (CRS). Sommige regels kunnen valse positieven veroorzaken en echt verkeer blokkeren. Daarom biedt Application Gateway de mogelijkheid om regelgroepen en regels aan te passen. Zie [Lijst met CRS-regels voor webtoepassingen](application-gateway-crs-rulegroups-rules.md)firewall crs voor meer informatie over de specifieke regelgroepen en regels .

## <a name="view-rule-groups-and-rules"></a>Regelgroepen en regels weergeven

In de volgende codevoorbeelden ziet u hoe u regels en regelgroepen weergeven die kunnen worden geconfigureerd op een toepassinggateway met WAF.The following code examples show how to view rules and rule groups that are configurable on a WAF-enabled application gateway.

### <a name="view-rule-groups"></a>Regelgroepen weergeven

In het volgende voorbeeld ziet u hoe u regelgroepen weergeven:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

De volgende uitvoer is een afgekapt antwoord uit het voorgaande voorbeeld:

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Regels uitschakelen

In het volgende `911011` voorbeeld `911012` worden regels en op een toepassingsgateway uitgeschakeld:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Verplichte regels

De volgende lijst bevat voorwaarden die ervoor zorgen dat de WAF het verzoek blokkeert in de preventiemodus (in de detectiemodus worden ze geregistreerd als uitzonderingen). Deze kunnen niet worden geconfigureerd of uitgeschakeld:

* Als u de aanvraaginstantie niet ontleedt, wordt de aanvraag geblokkeerd, tenzij de lichaamsinspectie is uitgeschakeld (XML, JSON, formuliergegevens)
* De gegevenslengte van de aanvraagtekst (zonder bestanden) is groter dan de geconfigureerde limiet
* Aanvraagbody (inclusief bestanden) is groter dan de limiet
* Er is een interne fout opgetreden in de WAF-engine

CRS 3.x specifiek:

* Inkomende anomaliescore overschreden drempelwaarde

## <a name="next-steps"></a>Volgende stappen

Nadat u uw uitgeschakelde regels hebt geconfigureerd, u leren hoe u uw WAF-logboeken bekijken. Zie [Application Gateway Diagnostics](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)voor meer informatie.

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
