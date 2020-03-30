---
title: Regels aanpassen met CLI - Azure Web Application Firewall
description: In dit artikel vindt u informatie over het aanpassen van firewallregels voor webtoepassingen in Application Gateway met de Azure CLI.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048532"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Firewallregels voor webtoepassingen aanpassen met de Azure CLI

De Azure Application Gateway Web Application Firewall (WAF) biedt bescherming voor webtoepassingen. Deze beveiligingen worden geboden door de Open Web Application Security Project (OWASP) Core Rule Set (CRS). Sommige regels kunnen valse positieven veroorzaken en echt verkeer blokkeren. Daarom biedt Application Gateway de mogelijkheid om regelgroepen en regels aan te passen. Zie [Lijst met CRS-regelgroepen en -regels voor webtoepassingsfirewall.](application-gateway-crs-rulegroups-rules.md)

## <a name="view-rule-groups-and-rules"></a>Regelgroepen en regels weergeven

In de volgende codevoorbeelden ziet u hoe u regels en regelgroepen weergeven die configureerbaar zijn.

### <a name="view-rule-groups"></a>Regelgroepen weergeven

In het volgende voorbeeld ziet u hoe u de regelgroepen weergeven:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

De volgende uitvoer is een afgekapt antwoord uit het voorgaande voorbeeld:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Regels in een regelgroep weergeven

In het volgende voorbeeld ziet u hoe u regels in een bepaalde regelgroep weergeeft:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

De volgende uitvoer is een afgekapt antwoord uit het voorgaande voorbeeld:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Regels uitschakelen

In het volgende `910018` voorbeeld `910017` worden regels en op een toepassingsgateway uitgeschakeld:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
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

Nadat u uw uitgeschakelde regels hebt geconfigureerd, u leren hoe u uw WAF-logboeken bekijken. Zie [Application Gateway diagnostics](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)voor meer informatie.

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
