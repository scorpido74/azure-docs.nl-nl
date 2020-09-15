---
title: Configuratie van Azure Firewall Threat Intelligence
description: Meer informatie over het configureren van op Threat Intelligence gebaseerde filtering voor uw Azure Firewall-beleid om verkeer van en naar bekende schadelijke IP-adressen en domeinen te Signa leren en weigeren.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: a663c5f3bcf3492c4a9bc74fe93c6ed6a86137ee
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530638"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Configuratie van Azure Firewall Threat Intelligence

Op bedreigingen gebaseerd filteren kan worden geconfigureerd voor uw Azure Firewall-beleid om verkeer van en naar bekende schadelijke IP-adressen en domeinen te Signa lering en te weigeren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) voorziet in micro soft Threat Intelligence en wordt gebruikt door meerdere services, waaronder Azure Security Center.<br>

Als u op bedreigingen gebaseerd filteren hebt geconfigureerd, worden de bijbehorende regels verwerkt vóór een van de NAT-regels, netwerk regels of toepassings regels.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Beleid voor bedreigings informatie":::

## <a name="threat-intelligence-mode"></a>Threat Intelligence-modus

U kunt ervoor kiezen om alleen een waarschuwing te registreren wanneer een regel wordt geactiveerd, of u kunt de modus waarschuwing en weigeren kiezen.

Op bedreigingen gebaseerd filteren is standaard ingeschakeld in de waarschuwings modus.

## <a name="allowed-list-addresses"></a>Lijst met toegestane adressen

U kunt een lijst met toegestane IP-adressen configureren zodat bedreigings informatie geen van de adressen, bereiken of subnetten die u opgeeft, filtert.



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

- Raadpleeg het [micro soft Security Intelligence-rapport](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)