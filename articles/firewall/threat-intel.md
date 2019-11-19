---
title: Azure Firewall op bedreigingen gebaseerd filteren
description: Filteren op basis van bedreigingsinformatie kan voor uw firewall worden ingeschakeld voor waarschuwingen over of het weigeren van verkeer van en naar bekende kwaadaardige IP-adressen en domeinen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168672"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Azure Firewall op bedreigingen gebaseerd filteren

Filteren op basis van bedreigingsinformatie kan voor uw firewall worden ingeschakeld voor waarschuwingen over of het weigeren van verkeer van en naar bekende kwaadaardige IP-adressen en domeinen. De IP-adressen en domeinen zijn afkomstig uit de feed Bedreigingsinformatie van Microsoft. [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) voorziet in micro soft Threat Intelligence en wordt gebruikt door meerdere services, waaronder Azure Security Center.

![Firewall Threat Intelligence](media/threat-intel/firewall-threat.png)

Als u op bedreigingen gebaseerd filteren hebt ingeschakeld, worden de bijbehorende regels verwerkt vóór een van de NAT-regels, netwerk regels of toepassings regels.

U kunt ervoor kiezen om alleen een waarschuwing te registreren wanneer een regel wordt geactiveerd, of u kunt de modus waarschuwing en weigeren kiezen.

Op bedreigingen gebaseerd filteren is standaard ingeschakeld in de waarschuwings modus. U kunt deze functie niet uitschakelen of de modus wijzigen totdat de portal-interface beschikbaar is in uw regio.

![Portal interface voor het filteren op basis van bedreigings informatie](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Logboeken

In het volgende logboek fragment wordt een regel geactiveerd weer gegeven:

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

- **Uitgaande tests** : waarschuwingen voor uitgaand verkeer moeten een zeldzame gebeurtenis zijn, omdat het betekent dat uw omgeving is aangetast. Er is een test-FQDN gemaakt waarmee een waarschuwing wordt geactiveerd om te voor komen dat uitgaande waarschuwingen worden uitgevoerd. Gebruik **testmaliciousdomain.eastus.cloudapp.Azure.com** voor uw uitgaande tests.

- **Inkomend testen** : u kunt verwachten dat er waarschuwingen worden weer geven voor binnenkomend verkeer als DNAT-regels zijn geconfigureerd op de firewall. Dit geldt ook als alleen specifieke bronnen zijn toegestaan in de DNAT-regel en verkeer anderszins wordt geweigerd. Azure Firewall geen waarschuwing voor alle bekende poort scanners. alleen op scanners waarvan bekend is dat ze ook schadelijke activiteiten kunnen uitvoeren.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Firewall log Analytics](log-analytics-samples.md) -voor beelden
- Meer informatie over het [implementeren en configureren van een Azure firewall](tutorial-firewall-deploy-portal.md)
- Raadpleeg het [micro soft Security Intelligence-rapport](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)