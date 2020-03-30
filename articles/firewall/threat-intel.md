---
title: Azure Firewall threat intelligence based filtering Azure Firewall threat intelligence
description: Filtering op basis van bedreigingsinformatie kan worden ingeschakeld voor uw firewall om verkeer van/naar bekende kwaadaardige IP-adressen en domeinen te waarschuwen en te weigeren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168672"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Azure Firewall threat intelligence-gebaseerde filtering

Filtering op basis van bedreigingsinformatie kan worden ingeschakeld voor uw firewall om verkeer van/naar bekende kwaadaardige IP-adressen en domeinen te waarschuwen en te weigeren. De IP-adressen en domeinen zijn afkomstig uit de Microsoft Threat Intelligence-feed. [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) is de drijvende kracht achter Microsoft-bedreigingsinformatie en wordt gebruikt door meerdere services, waaronder Azure Security Center.

![Firewall threat intelligence](media/threat-intel/firewall-threat.png)

Als u filtering op basis van bedreigingsinformatie hebt ingeschakeld, worden de bijbehorende regels verwerkt vóór een van de NAT-regels, netwerkregels of toepassingsregels.

U ervoor kiezen om gewoon een waarschuwing in te loggen wanneer een regel wordt geactiveerd, of u de waarschuwings- en weigeringsmodus kiezen.

Standaard is filtering op basis van bedreigingsinformatie ingeschakeld in de waarschuwingsmodus. U deze functie niet uitschakelen of de modus wijzigen totdat de portalinterface beschikbaar is in uw regio.

![Op bedreigingsinformatie gebaseerde filterportalinterface](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Logboeken

In het volgende logboekfragment ziet u een geactiveerde regel:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testen

- **Uitgaande tests** - Uitgaande verkeerswaarschuwingen moeten een zeldzame gebeurtenis zijn, omdat dit betekent dat uw omgeving is aangetast. Om te helpen testen uitgaande waarschuwingen werken, is een test FQDN gemaakt die een waarschuwing activeert. Gebruik **testmaliciousdomain.eastus.cloudapp.azure.com** voor uw uitgaande tests.

- **Binnenkomende tests** - U waarschuwingen verwachten voor binnenkomend verkeer als DNAT-regels zijn geconfigureerd op de firewall. Dit geldt zelfs als alleen specifieke bronnen zijn toegestaan op de DNAT-regel en het verkeer anders wordt geweigerd. Azure Firewall waarschuwt niet voor alle bekende poortscanners; alleen op scanners waarvan bekend is dat ze ook kwaadaardige activiteiten uitvoeren.

## <a name="next-steps"></a>Volgende stappen

- Zie [Voorbeelden van Azure Firewall Log Analytics](log-analytics-samples.md)
- Meer informatie over het [implementeren en configureren van een Azure Firewall](tutorial-firewall-deploy-portal.md)
- Het [rapport microsoft security intelligence bekijken](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)