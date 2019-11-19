---
title: Overzicht van Azure Firewall logboeken en metrische gegevens
description: U kunt Azure Firewall bewaken met behulp van firewall-logboeken. U kunt ook activiteitenlogboeken gebruiken om bewerkingen in Azure Firewall-resources te controleren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: f233b1a60202b440abf34edd1c56eebaecba18e2
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167000"
---
# <a name="azure-firewall-logs-and-metrics"></a>Azure Firewall-logboeken en metrische gegevens

U kunt Azure Firewall bewaken met behulp van firewall-logboeken. U kunt ook activiteitenlogboeken gebruiken om bewerkingen in Azure Firewall-resources te controleren.

Via de portal kunt u toegang verkrijgen tot sommige van deze logboeken. Logboeken kunnen worden verzonden naar [Azure Monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md), Storage en Event Hubs en kunnen worden geanalyseerd in Azure Monitor-logboeken of door verschillende hulpprogramma's zoals Excel en Power BI.

Metrische gegevens zijn licht gewicht en kunnen bijna realtime-scenario's ondersteunen, waardoor ze nuttig zijn voor waarschuwingen en snelle detectie van problemen. 

## <a name="diagnostic-logs"></a>Diagnostische logboeken

 De volgende diagnostische logboeken zijn beschikbaar voor Azure Firewall:

* **Logboek voor toepassingsregels**

   Het toepassings regel logboek wordt opgeslagen in een opslag account, gestreamd naar Event hubs en/of verzonden naar Azure Monitor Logboeken alleen als u het hebt ingeschakeld voor elke Azure Firewall. Elke nieuwe verbinding die overeenkomt met een van uw geconfigureerde toepassingsregels, resulteert in een logboek voor de geaccepteerde/geweigerde verbinding. De gegevens worden geregistreerd in JSON-indeling, zoals weergegeven in het volgende voorbeeld:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Logboek voor netwerkregels**

   Het netwerk regel logboek wordt opgeslagen in een opslag account, gestreamd naar Event hubs en/of verzonden naar Azure Monitor Logboeken alleen als u het hebt ingeschakeld voor elke Azure Firewall. Elke nieuwe verbinding die overeenkomt met een van uw geconfigureerde netwerkregels, resulteert in een logboek voor de geaccepteerde/geweigerde verbinding. De gegevens worden geregistreerd in JSON-indeling, zoals weergegeven in het volgende voorbeeld:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

U hebt drie opties voor het opslaan van uw logboeken:

* **Opslagaccount**: opslagaccounts kunnen het best worden gebruikt wanneer logboeken voor een langere periode worden opgeslagen en moeten kunnen worden bekeken wanneer dat nodig is.
* **Event Hubs**: Event Hubs zijn een geweldige optie voor integratie met andere SIEM-hulpprogramma’s (Security Information and Event Management) om waarschuwingen over uw resources te krijgen.
* **Azure monitor-logboeken**: Azure monitor-logboeken worden het beste gebruikt voor algemene realtime-bewaking van uw toepassing of voor het bekijken van trends.

## <a name="activity-logs"></a>Activiteitenlogboeken

   Activiteitenlogboekitems worden standaard verzameld en kunnen in de Azure-portal worden bekeken.

   U kunt [Azure-activiteiten logboeken](../azure-resource-manager/resource-group-audit.md) (voorheen bekend als operationele logboeken en audit Logboeken) gebruiken om alle bewerkingen weer te geven die zijn verzonden naar uw Azure-abonnement.

## <a name="metrics"></a>Metrische gegevens

Metrische gegevens in Azure Monitor zijn numerieke waarden die een aspect van een systeem op een bepaald moment beschrijven. Metrische gegevens worden elke minuut verzameld en zijn nuttig voor waarschuwingen omdat ze regel matig kunnen worden gesampled. Een waarschuwing kan snel worden geactiveerd met relatief eenvoudige logica.

De volgende metrische gegevens zijn beschikbaar voor Azure Firewall:

- **Aantal op toepassings regels gebaseerde treffers** -het aantal keren dat een toepassings regel is bereikt.

    Eenheid: aantal

- **Aantal netwerk regels treffers** -het aantal keren dat een netwerk regel is bereikt.

    Eenheid: aantal

- **Verwerkte gegevens** -hoeveelheid gegevens waarmee de firewall wordt gepasseerd.

    Eenheid: bytes

- Status van **firewall** : geeft de status van de firewall aan.

    Eenheid: percentage

   Deze metriek heeft twee dimensies:
  - **Status**: mogelijke waarden zijn *in orde*, *gedegradeerd*, *beschadigd.*
  - **Reden**: geeft de reden aan van de bijbehorende status van de firewall. Het kan bijvoorbeeld duiden op *SNAT-poorten* als de firewall status wordt gedegradeerd of beschadigd.





- **SNAT-poort gebruik** : het percentage SNAT-poorten dat door de firewall is gebruikt.

    Eenheid: percentage

   Wanneer u meer open bare IP-adressen aan uw firewall toevoegt, zijn er meer SNAT-poorten beschikbaar, waardoor het gebruik van de SNAT-poorten wordt verminderd. Daarnaast worden er ook extra SNAT-poorten beschikbaar wanneer de firewall wordt geschaald om verschillende redenen (bijvoorbeeld CPU of door Voer). Een bepaald percentage van het gebruik van de SNAT-poorten kan dan ook worden vervolgd zonder dat u open bare IP-adressen toevoegt, alleen omdat de service is uitgeschaald. U kunt het aantal open bare IP-adressen dat beschikbaar is voor het verg Roten van de beschik bare poorten op uw firewall, direct beheren. Maar u kunt het schalen van de firewall niet rechtstreeks beheren. Op dit moment worden er alleen SNAT-poorten toegevoegd voor de eerste vijf open bare IP-adressen.   


## <a name="next-steps"></a>Volgende stappen

- Zie [zelf studie: Azure firewall logboeken controleren](tutorial-diagnostics.md)voor meer informatie over het bewaken van Azure firewall logboeken en metrische gegevens.

- Zie [metrische gegevens in azure monitor](../azure-monitor/platform/data-platform-metrics.md)voor meer informatie over metrische gegevens in azure monitor.