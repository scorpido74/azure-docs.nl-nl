---
title: Lijst met uitsluitingen voor Web Application firewall in azure front deur-Azure Portal
description: Dit artikel bevat informatie over de configuratie van een uitsluitings lijst in azure front met de Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77925928"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Web Application firewall (WAF) met uitsluitings lijsten voor de front-deur service 

Soms kan Web Application firewall (WAF) een aanvraag blok keren die u wilt toestaan voor uw toepassing. Active Directory voegt bijvoorbeeld tokens in die worden gebruikt voor verificatie. Deze tokens kunnen speciale tekens bevatten die een onjuiste positieve waarde van de WAF-regels kunnen activeren. Met WAF-uitsluitings lijsten kunt u bepaalde kenmerken van aanvragen van een WAF-evaluatie weglaten.  Een uitsluitings lijst kan worden geconfigureerd met behulp van [PowserShell](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0), [Azure cli](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), [rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)of de Azure Portal. In het volgende voor beeld ziet u de Azure Portal configuratie. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Uitsluitings lijsten configureren met behulp van de Azure Portal
**Uitsluitingen beheren** is toegankelijk vanuit de WAF-portal onder **beheerde regels**

![Uitsluitings](../media/waf-front-door-exclusion/exclusion1.png)
![exclusion_add beheren](../media/waf-front-door-exclusion/exclusion2.png)

 Een voor beeld van een ![uitsluitings lijst: exclusion_define beheren](../media/waf-front-door-exclusion/exclusion3.png)

In dit voor beeld wordt de waarde in het veld voor de *gebruikers* header uitgesloten. Een geldige aanvraag kan het *gebruikers* veld bevatten dat een teken reeks bevat die een SQL-injectie regel activeert. U kunt de para meter *gebruiker* in dit geval uitsluiten, zodat de regel WAF niets in het veld evalueert.

De volgende kenmerken kunnen worden toegevoegd aan de uitsluitings lijsten op naam. De waarden van de velden die u gebruikt, worden niet geëvalueerd op basis van WAF-regels, maar hun namen worden geëvalueerd. De uitsluitings lijsten verwijderen de inspectie van de veld waarde.

* Naam van aanvraag header
* Cookie naam van aanvraag
* Naam van de query teken reeks argumenten
* Bericht argument naam van aanvraag Body

U kunt een exacte overeenkomst voor de aanvraag header, hoofd tekst, cookie of query teken reeks kenmerken opgeven.  Of u kunt eventueel gedeeltelijke overeenkomsten opgeven. De volgende Opera tors worden ondersteund:

- **Is gelijk aan**: deze operator wordt gebruikt voor een exacte overeenkomst. Als u bijvoorbeeld een header met de naam **bearerToken**wilt selecteren, gebruikt u de operator equals met de selector ingesteld als **bearerToken**.
- **Begint met**: deze operator komt overeen met alle velden die beginnen met de opgegeven selector-waarde.
- **Eindigt op**: deze operator komt overeen met alle aanvraag velden die eindigen op de opgegeven selector-waarde.
- **Contains**: deze operator komt overeen met alle aanvraag velden die de opgegeven selector-waarde bevatten.
- **Is gelijk aan**: deze operator komt overeen met alle aanvraag velden. * is de waarde selector.

Namen van kopteksten en cookies zijn niet hoofdletter gevoelig.

U kunt de uitsluitings lijst Toep assen op alle regels in de set met beheerde regels, op regels voor een specifieke regel groep of op een enkele regel zoals weer gegeven in het vorige voor beeld. 

## <a name="next-steps"></a>Volgende stappen

Nadat u uw WAF-instellingen hebt geconfigureerd, leert u hoe u uw WAF-Logboeken kunt weer geven. Zie voor meer informatie [Diagnostische gegevens over de voor deur](../afds/waf-front-door-monitor.md).
