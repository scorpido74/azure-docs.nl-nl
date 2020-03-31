---
title: Operations, events en tellers controleren voor openbare Basisbalans
titleSuffix: Azure Load Balancer
description: Meer informatie over het inschakelen van waarschuwingsgebeurtenissen en het registreren van de status van de sonde voor openbare basisbalans
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: allensu
ms.openlocfilehash: 0a21af683d9fa7849d3e96c545983c9f40a8d4c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935322"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Azure Monitor-logboeken voor openbare Basic Azure Load Balancer

>[!IMPORTANT]
>Azure Load Balancer ondersteunt twee verschillende typen: Basic en Standard. In dit artikel wordt Basic Load Balancer beschreven. Zie Overzicht van Standaard load [balancer](load-balancer-standard-overview.md) dat telemetrie via multidimensionale statistieken in Azure Monitor blootlegt voor meerinformatie over Standard Load Balancer.

U verschillende typen logboeken in Azure gebruiken om Basislastsbalansen te beheren en op te lossen. Sommige van deze logboeken zijn toegankelijk via de portal. Logboeken kunnen worden gestreamd naar een gebeurtenishub of een Log Analytics-werkruimte. Alle logboeken kunnen worden geëxtraheerd uit Azure blob-opslag en worden weergegeven in verschillende hulpprogramma's, zoals Excel en Power BI.  U meer informatie over de verschillende soorten logs uit de onderstaande lijst.

* **Activiteitslogboeken:** U [Activiteitslogboeken weergeven](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) gebruiken om acties op resources te controleren om alle activiteiten weer te geven die worden verzonden naar uw Azure-abonnement(en) en hun status. Activiteitslogboeken zijn standaard ingeschakeld en kunnen worden weergegeven in de Azure-portal.
* **Logboeken voor waarschuwingsgebeurtenissen:** U dit logboek gebruiken om waarschuwingen van de load balancer weer te geven. De status voor de load balancer wordt elke vijf minuten verzameld. Dit logboek wordt alleen geschreven als een waarschuwing voor load balancer wordt verhoogd.
* **Logboeken van de gezondheidssonde:** U dit logboek gebruiken om problemen weer te geven die zijn gedetecteerd door uw statussonde, zoals het aantal exemplaren in uw backend-pool die geen aanvragen van de load balancer ontvangen vanwege fouten in de statussonde. Dit logboek wordt geschreven wanneer er een wijziging is in de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de

> [!IMPORTANT]
> Azure Monitor-logboeken werken momenteel alleen voor openbare Basislastsaldi. Logboeken zijn alleen beschikbaar voor resources die zijn geïmplementeerd in het implementatiemodel resourcebeheer. U logboeken niet gebruiken voor resources in het klassieke implementatiemodel. Zie [Resource manager-implementatie en klassieke implementatie begrijpen](../azure-resource-manager/management/deployment-models.md)voor meer informatie over de implementatiemodellen van de implementatie.

## <a name="enable-logging"></a>Logboekregistratie inschakelen

Activiteitenlogboekregistratie is automatisch ingeschakeld voor elke Resource Manager-resource. Schakel logboekregistratie en logboekregistratie van gebeurtenissen en statussen in om te beginnen met het verzamelen van de gegevens die beschikbaar zijn via deze logboeken. Gebruik de volgende stappen om logboekregistratie in te schakelen.

Meld u aan bij [Azure Portal](https://portal.azure.com). Als u nog geen load balancer hebt, [maakt u een load balancer](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) voordat u verdergaat.

1. Klik in de portal op **Resourcegroepen**.
2. Selecteer ** \<de naam van de resourcegroep>** waar uw load balancer zich bevindt.
3. Selecteer uw load balancer.
4. Selecteer**Diagnostische instellingen voor** **controle** > selecteren .
5. Selecteer in het deelvenster **Diagnostische instellingen** onder **Diagnostische instellingen**de optie + Diagnostische **instelling toevoegen**.
6. Voer in het deelvenster **Instellingen voor diagnoseinstellingen** **mijnLBDiagnostics** in het veld **Naam** in.
7. U hebt drie opties voor de **instellingen voor diagnostische gegevens.**  U er één, twee of alle drie kiezen en elk configureren voor uw vereisten:
   * **Archiveren naar een opslagaccount**
   * **Streamen naar een gebeurtenishub**
   * **Verzenden naar Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Archiveren naar een opslagaccount
    Je hebt een opslagaccount nodig dat al is gemaakt voor dit proces.  Zie [Een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) als u een opslagaccount wilt maken

    1. Schakel het selectievakje naast **Archief in op een opslagaccount**.
    2. Selecteer **Configureren** om het deelvenster **Een opslagaccount selecteren** te openen.
    3. Selecteer het **abonnement** waarbij uw opslagaccount is gemaakt in het keuzevak.
    4. Selecteer de naam van uw opslagaccount onder **Opslagaccount** in het keuzevak.
    5. Selecteer OK.

    ### <a name="stream-to-an-event-hub"></a>Streamen naar een Event Hub
    Je hebt een gebeurtenishub nodig die al is gemaakt voor dit proces.  Zie [Quickstart: Een gebeurtenishub maken met Azure-portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) als u een gebeurtenishub wilt maken.

    1. Schakel het selectievakje naast **Streamen naar een gebeurtenishub in**
    2. Selecteer **Configureren** om het **deelvenster Gebeurtenisknooppunt selecteren** te openen.
    3. Selecteer het **abonnement** waarbij de gebeurtenishub is gemaakt in het keuzevak.
    4. **Selecteer de naamruimte van de gebeurtenishub** in het uithaalvak.
    5. Selecteer de naam van het **gebeurtenishubbeleid** in het keuzevak pull-down.
    6. Selecteer OK.

    ### <a name="send-to-log-analytics"></a>Verzenden naar Log Analytics
    U moet al een werkruimte voor logboekanalyse hebben die is gemaakt en geconfigureerd voor dit proces.  Zie Een werkruimte voor [Logboekanalyse maken in de Azure-portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) als u een werkruimte voor Logboekanalyse wilt maken

    1. Schakel het selectievakje naast **Verzenden naar logboekanalyse in.**
    2. Selecteer het **abonnement** waarin de werkruimte Log Analytics zich bevindt in het selectiekader.
    3. Selecteer de **Logboekanalysewerkruimte** in het uithaalvak.


8. Schakel onder de sectie **LOG** in het deelvenster **Instellingen voor diagnose** het selectievakje naast:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Schakel onder de sectie **METRISCHE** in het deelvenster **Instellingen voor diagnose** het selectievakje in naast:
   * **AllMetrics**

11. Controleer of alles er goed uitziet en klik boven aan het deelvenster **Diagnostische instellingen** maken op **Opslaan.**

## <a name="activity-log"></a>Activiteitenlogboek

Het activiteitenlogboek wordt standaard gegenereerd. De logboeken worden 90 dagen bewaard in de logboekenvan Azure. Lees de [activiteitslogboeken weergeven om acties op bronnenartikel te lezen.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)

## <a name="archive-to-storage-account-logs"></a>Archief naar opslagaccountlogboeken

### <a name="alert-event-log"></a>Logboek van waarschuwingsgebeurtenissen

Dit logboek wordt alleen gegenereerd als u het per load balancer hebt ingeschakeld. De gebeurtenissen worden aangemeld in de JSON-indeling en opgeslagen in het opslagaccount dat u hebt opgegeven toen u de logboekregistratie hebt ingeschakeld. Het volgende voorbeeld is van een gebeurtenis.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

De JSON-uitvoer toont de eigenschap *eventname,* waarin de reden wordt beschreven waarom de load balancer een waarschuwing heeft gemaakt. In dit geval werd de gegenereerde waarschuwing veroorzaakt door tcp-poortuitputting veroorzaakt door ip-nat-limieten (Source NAT).

### <a name="health-probe-log"></a>Logboek van de sonde van de gezondheid

Dit logboek wordt alleen gegenereerd als u het per load balancer hebt ingeschakeld, zoals hierboven beschreven. De gegevens worden opgeslagen in het opslagaccount dat u hebt opgegeven toen u de logboekregistratie hebt ingeschakeld. Er wordt een container met de naam 'insights-logs-loadbalancerprobehealthstatus' gemaakt en de volgende gegevens worden geregistreerd:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

De JSON-uitvoer toont in het veld Eigenschappen de basisinformatie voor de status van de sonde. De eigenschap *dipDownCount* toont het totale aantal exemplaren aan de back-end, die geen netwerkverkeer ontvangen vanwege mislukte sondereacties.

### <a name="view-and-analyze-the-activity-log"></a>Het activiteitenlogboek bekijken en analyseren

U gegevens over activiteitenlogboeken bekijken en analyseren met behulp van een van de volgende methoden:

* **Azure-hulpprogramma's:** Informatie ophalen uit het activiteitenlogboek via Azure PowerShell, de Azure Command Line Interface (CLI), de Azure REST API of de Azure-portal. Stapsgewijze instructies voor elke methode worden beschreven in het artikel [Controlebewerkingen met Resource Manager.](../azure-resource-manager/management/view-activity-logs.md)
* **Power BI:** Als je nog geen [Power BI-account](https:// .microsoft.com/pricing) hebt, kun je het gratis proberen. Met behulp van het [inhoudspakket Azure Audit Logs voor Power BI](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)u uw gegevens analyseren met vooraf geconfigureerde dashboards of u weergaven aanpassen aan uw vereisten.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Het logboek van de statussonde en het gebeurtenislogboek weergeven en analyseren

Maak verbinding met uw opslagaccount en haal de JSON-logboekvermeldingen op voor logboeken voor gebeurtenissen en statussondes. Zodra u de JSON-bestanden hebt gedownload, u deze converteren naar CSV en deze weergeven in Excel, Power BI of een ander hulpprogramma voor gegevensvisualisatie.

> [!TIP]
> Als u bekend bent met Visual Studio en de basisconcepten van het wijzigen van waarden voor constanten en variabelen in C#, kunt u de [logboekconversieprogramma’s](https://github.com/Azure-Samples/networking-dotnet-log-converter) gebruiken die beschikbaar zijn in GitHub.

## <a name="stream-to-an-event-hub"></a>Streamen naar een Event Hub
Wanneer diagnostische informatie wordt gestreamd naar een gebeurtenishub, kan deze worden gebruikt voor gecentraliseerde logboekanalyse in een SIEM-tool van derden met Azure Monitor-integratie. Zie [Azure-bewakingsgegevens streamen naar een gebeurtenishub voor](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration) meer informatie

## <a name="send-to-log-analytics"></a>Verzenden naar Log Analytics
Bronnen in Azure kunnen hun diagnostische gegevens rechtstreeks naar een Log Analytics-werkruimte laten sturen, waar complexe query's kunnen worden uitgevoerd tegen de informatie voor probleemoplossing en analyse.  Zie [Azure-bronlogboeken verzamelen in de werkruimte Log Analytics in Azure Monitor voor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace) meer informatie

## <a name="next-steps"></a>Volgende stappen

[Load Balancer-testen](load-balancer-custom-probe-overview.md)
