---
title: Uitsluitingslijsten voor firewalls in Azure Front Door - Azure-portal
description: In dit artikel vindt u informatie over de configuratie van uitsluitingslijsten in Azure Front met de Azure-portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925928"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Web Application Firewall (WAF) met uitsluitingslijsten voor front door-service 

Soms blokkeert Web Application Firewall (WAF) een verzoek dat u wilt toestaan voor uw toepassing. In Active Directory worden bijvoorbeeld tokens ingevoegd die worden gebruikt voor verificatie. Deze tokens kunnen speciale tekens bevatten die een vals-positief uit de WAF-regels kunnen activeren. Met waf-uitsluitingslijsten u bepaalde aanvraagkenmerken weglaten uit een WAF-evaluatie.  Een uitsluitingslijst kan worden geconfigureerd met [PowserShell,](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0) [Azure CLI,](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add) [Rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)of de Azure-portal. In het volgende voorbeeld wordt de azure-portalconfiguratie weergegeven. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Uitsluitingslijsten configureren met de Azure-portal
**Uitsluitingen beheren** is toegankelijk vanaf waf-portal onder **Beheerde regels**

![Uitsluiting](../media/waf-front-door-exclusion/exclusion1.png)
![beheren Beheer exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 Een voorbeelduitsluitingslijst: ![exclusion_define beheren](../media/waf-front-door-exclusion/exclusion3.png)

In dit voorbeeld wordt de waarde in het veld *gebruikerskoptekst* uitgesloten. Een geldig verzoek kan het *gebruikersveld* bevatten dat een tekenreeks bevat die een SQL-injectieregel activeert. U de *gebruikersparameter* in dit geval uitsluiten, zodat de WAF-regel niets in het veld evalueert.

De volgende kenmerken kunnen op naam aan uitsluitingslijsten worden toegevoegd. De waarden van de velden die u gebruikt, worden niet beoordeeld aan de hand van de WAF-regels, maar de namen ervan worden geëvalueerd. De uitsluitingslijsten verwijderen de inspectie van de waarde van het veld.

* Naam van koptekst aanvragen
* Cookienaam aanvragen
* Naam van querytekenreeksargs
* Hoofdpost-naam aanvragen

U een exacte aanvraagkop-, hoofdtekst-, cookie- of querytekenreekskenmerkovereenkomst opgeven.  U ook optioneel gedeeltelijke overeenkomsten opgeven. De volgende operatoren zijn de ondersteunde wedstrijdcriteria:

- **Gelijk aan**: Deze operator wordt gebruikt voor een exacte overeenkomst. Als u bijvoorbeeld een koptekst met de naam **bearerToken**wilt selecteren, gebruikt u de operator gelijk en de selectorset als **bearerToken**.
- **Begin met**: Deze operator komt overeen met alle velden die beginnen met de opgegeven selectorwaarde.
- **Eindigt met**: Deze operator komt overeen met alle aanvraagvelden die eindigen met de opgegeven selectorwaarde.
- **Bevat:** deze operator komt overeen met alle aanvraagvelden die de opgegeven selectorwaarde bevatten.
- **Is gelijk aan elk**: Deze operator komt overeen met alle aanvraagvelden. * is de selectorwaarde.

Kop- en cookienamen zijn geval ongevoelig.

U uitsluitingslijst toepassen op alle regels binnen de beheerde regelset, op regels voor een specifieke regelgroep of op één regel zoals in het vorige voorbeeld wordt weergegeven. 

## <a name="next-steps"></a>Volgende stappen

Nadat u uw WAF-instellingen hebt geconfigureerd, leest u hoe u uw WAF-logboeken bekijken. Zie Voor meer informatie [voor de diagnose van de voordeur.](../afds/waf-front-door-monitor.md)
