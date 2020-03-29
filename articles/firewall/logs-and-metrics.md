---
title: Overzicht van Azure Firewall-logboeken en -statistieken
description: U kunt Azure Firewall bewaken met behulp van firewall-logboeken. U kunt ook activiteitenlogboeken gebruiken om bewerkingen in Azure Firewall-resources te controleren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/22/2020
ms.author: victorh
ms.openlocfilehash: 89c6700d5df3bcef1332121c3cf7d8f720fe054c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76315028"
---
# <a name="azure-firewall-logs-and-metrics"></a>Azure Firewall-logboeken en metrische gegevens

U kunt Azure Firewall bewaken met behulp van firewall-logboeken. U kunt ook activiteitenlogboeken gebruiken om bewerkingen in Azure Firewall-resources te controleren.

Via de portal kunt u toegang verkrijgen tot sommige van deze logboeken. Logboeken kunnen worden verzonden naar [Azure Monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md), Storage en Event Hubs en kunnen worden geanalyseerd in Azure Monitor-logboeken of door verschillende hulpprogramma's zoals Excel en Power BI.

Statistieken zijn licht van gewicht en kunnen bijna realtime scenario's ondersteunen, waardoor ze handig zijn voor waarschuwingen en snelle probleemdetectie.

## <a name="diagnostic-logs"></a>Diagnostische logboeken

 De volgende diagnostische logboeken zijn beschikbaar voor Azure Firewall:

* **Logboek voor toepassingsregels**

   Het logboek van de toepassingsregel wordt alleen opgeslagen in een opslagaccount, gestreamd naar gebeurtenishubs en/of naar Azure Monitor-logboeken verzonden als u het voor elke Azure Firewall hebt ingeschakeld. Elke nieuwe verbinding die overeenkomt met een van uw geconfigureerde toepassingsregels, resulteert in een logboek voor de geaccepteerde/geweigerde verbinding. De gegevens worden geregistreerd in JSON-indeling, zoals weergegeven in het volgende voorbeeld:

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

   Het logboek van de netwerkregel wordt alleen opgeslagen in een opslagaccount, gestreamd naar gebeurtenishubs en/of naar Azure Monitor-logboeken verzonden als u het voor elke Azure Firewall hebt ingeschakeld. Elke nieuwe verbinding die overeenkomt met een van uw geconfigureerde netwerkregels, resulteert in een logboek voor de geaccepteerde/geweigerde verbinding. De gegevens worden geregistreerd in JSON-indeling, zoals weergegeven in het volgende voorbeeld:

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
* **Azure Monitor-logboeken:** Azure Monitor-logboeken worden het best gebruikt voor algemene realtime bewaking van uw toepassing of het bekijken van trends.

## <a name="activity-logs"></a>Activiteitenlogboeken

   Activiteitenlogboekitems worden standaard verzameld en kunnen in de Azure-portal worden bekeken.

   U [Azure-activiteitslogboeken](../azure-resource-manager/management/view-activity-logs.md) (voorheen bekend als operationele logboeken en controlelogboeken) gebruiken om alle bewerkingen weer te geven die zijn ingediend bij uw Azure-abonnement.

## <a name="metrics"></a>Metrische gegevens

Statistieken in Azure Monitor zijn numerieke waarden die een bepaald aspect van een systeem op een bepaald moment beschrijven. Statistieken worden elke minuut verzameld en zijn handig om te waarschuwen omdat ze vaak kunnen worden bemonsterd. Een waarschuwing kan snel worden afgevuurd met relatief eenvoudige logica.

De volgende statistieken zijn beschikbaar voor Azure Firewall:

- **Toepassingsregels raken tellen** - Het aantal keren dat een toepassingsregel is geraakt.

    Eenheid: tellen

- **Netwerkregels raken tellen** - Het aantal keren dat een netwerkregel is geraakt.

    Eenheid: tellen

- **Gegevens verwerkt** - Hoeveelheid gegevens die de firewall doorkruisen.

    Eenheid: bytes

- **Firewallstatus** - Geeft de status van de firewall aan op basis van de beschikbaarheid van de SNAT-poort.

    Eenheid: procent

   Deze statistiek heeft twee dimensies:
  - Status: Mogelijke waarden zijn *Gezond,* *Gedegradeerd,* *Ongezond*.
  - Reden: Geeft de reden voor de overeenkomstige status van de firewall aan. 

     Als SNAT-poorten > 95% worden gebruikt, worden ze als uitgeput beschouwd en is de status 50% met status=**Gedegradeerd** en reden=**SNAT-poort**. De firewall blijft het verkeer verwerken en bestaande verbindingen worden niet beïnvloed. Nieuwe verbindingen kunnen echter niet met tussenpozen tot stand worden gebracht.

     Als SNAT-poorten worden gebruikt < 95%, wordt firewall als gezond beschouwd en wordt de gezondheid weergegeven als 100%.

     Als er geen SNAT-poortenworden gerapporteerd, wordt de status weergegeven als 0%. 

- **SNAT-poortgebruik** - Het percentage SNAT-poorten dat door de firewall is gebruikt.

    Eenheid: procent

   Wanneer u meer openbare IP-adressen aan uw firewall toevoegt, zijn er meer SNAT-poorten beschikbaar, waardoor het gebruik van SNAT-poorten wordt verminderd. Bovendien, wanneer de firewall om verschillende redenen (bijvoorbeeld CPU of doorvoer) wordt geschaald, komen er ook extra SNAT-poorten beschikbaar. Dus effectief, een bepaald percentage van snat-poorten gebruik kan naar beneden gaan zonder dat u het toevoegen van openbare IP-adressen, alleen maar omdat de service uitgeschroefd. U direct het aantal openbare IP-adressen beheren dat beschikbaar is om de beschikbare poorten op uw firewall te vergroten. Maar u firewallschaling niet direct regelen. Momenteel worden SNAT-poorten alleen toegevoegd voor de eerste vijf openbare IP-adressen.   


## <a name="next-steps"></a>Volgende stappen

- Zie [Zelfstudie: Azure Firewall-logboeken controleren](tutorial-diagnostics.md)voor meer informatie over het controleren van Azure Firewall-logboeken.

- Zie [Statistieken in Azure Monitor](../azure-monitor/platform/data-platform-metrics.md)voor meer informatie over metrische gegevens in Azure Monitor.
