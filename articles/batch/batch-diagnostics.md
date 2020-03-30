---
title: Metrische gegevens, waarschuwingen en diagnostische logboeken - Azure Batch | Microsoft Documenten
description: Diagnostische logboekgebeurtenissen voor Azure Batch-accountbronnen zoals pools en taken registreren en analyseren.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 68d5976a5a79dbde88b7f80b02b39793ffc86de9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254863"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batchstatistieken, waarschuwingen en logboeken voor diagnostische evaluatie en monitoring

 
In dit artikel wordt uitgelegd hoe u een Batch-account controleren met behulp van functies van [Azure Monitor.](../azure-monitor/overview.md) Azure Monitor verzamelt [statistieken](../azure-monitor/platform/data-platform-metrics.md) en [diagnostische logboeken](../azure-monitor/platform/platform-logs-overview.md) voor resources in uw Batch-account. Verzamel en gebruik deze gegevens op verschillende manieren om uw Batch-account te controleren en problemen te diagnosticeren. U ook [metrische waarschuwingen](../azure-monitor/platform/alerts-overview.md) configureren, zodat u meldingen ontvangt wanneer een statistiek een opgegeven waarde bereikt. 

## <a name="batch-metrics"></a>Batchstatistieken

Metrische gegevens zijn Azure-telemetriegegevens (ook wel prestatiemeteritems genoemd) die worden uitgestoten door uw Azure-resources die worden verbruikt door de Azure Monitor-service. Voorbeeldstatistieken in een Batch-account zijn: Gebeurtenissen maken maken, knooppuntaantal met lage prioriteit en gebeurtenissen taakvoltooien. 

Zie de [lijst met ondersteunde batchstatistieken](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Statistieken zijn:

* Standaard ingeschakeld in elk Batch-account zonder extra configuratie
* Elke 1 minuut gegenereerd
* Niet automatisch blijven bestaan, maar hebben een 30-daagse rollende geschiedenis. U activiteitsstatistieken blijven uitvoeren als onderdeel van diagnostische logboekregistratie.

### <a name="view-metrics"></a>Metrische gegevens bekijken

Bekijk statistieken voor uw Batch-account in de Azure-portal. Op de pagina **Overzicht** voor het account worden standaard sleutelknooppunt-, kern- en taakstatistieken weergegeven. 

Ga als volgende over de volgende statistieken van batchaccount: 

1. Klik in de portal op**Batch-accounts**voor **alle services** > en klik vervolgens op de naam van uw Batch-account.
2. Klik **onder Controleren**op **Statistieken**.
3. Selecteer een of meer van de statistieken. Selecteer desgewenst aanvullende resourcestatistieken met de **vervolgkeuzelijst Abonnementen**, **Resourcegroep** **, Resourcetype**en **Resource.**
    * Voor count-based metrics (zoals 'Dedicated Core Count' of 'Low-Priority Node Count'), gebruik je de aggregatie 'Gemiddeld'. Voor statistieken op basis van gebeurtenissen (zoals 'Complete gebeurtenissen aanvullen aanvullen verkleinen' gebruikt u de aggregatie 'Aantal'.

> [!WARNING]
> Gebruik de aggregatie "Som" niet, die de waarden optelt van alle gegevenspunten die tijdens de periode van de grafiek zijn ontvangen
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Als u statistieken programmatisch wilt ophalen, gebruikt u de AZURE Monitor API's. Zie Bijvoorbeeld [Azure Monitor-statistieken ophalen met .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Batchmetrische betrouwbaarheid

Statistieken zijn bedoeld om te worden gebruikt voor trending en data-analyse. Metrische levering is niet gegarandeerd en is onderhevig aan levering buiten de bestelling, verlies van gegevens en/of duplicatie. Het wordt afgeraden om afzonderlijke gebeurtenissen te waarschuwen of te activeren. Zie de sectie [Batch metrische waarschuwingen](#batch-metric-alerts) voor meer informatie over het instellen van drempelwaarden voor waarschuwingen.

Statistieken die in de laatste 3 minuten zijn uitgestoten, kunnen nog steeds worden verzameld. Gedurende dit tijdsbestek kunnen de metrische waarden ondergerapporteerd worden.

## <a name="batch-metric-alerts"></a>Waarschuwingen voor batchmetrische gegevens

Configureer desgevraagd bijna realtime *metrische waarschuwingen* die worden geactiveerd wanneer de waarde van een opgegeven statistiek een drempelwaarde overschrijdt die u toewijst. De waarschuwing genereert een [melding die](../monitoring-and-diagnostics/insights-alerts-portal.md) u kiest wanneer de waarschuwing wordt geactiveerd (wanneer de drempel wordt overschreden en de waarschuwingsvoorwaarde is voldaan) en wanneer deze is opgelost (wanneer de drempel opnieuw wordt overschreden en de voorwaarde niet meer wordt voldaan). Waarschuwingen op basis van afzonderlijke gegevenspunten wordt niet aanbevolen omdat statistieken onderhevig zijn aan levering buiten de bestelling, gegevensverlies en/of duplicatie. Alarmering moet gebruik maken van drempels om rekening te houden met deze inconsistenties.

U bijvoorbeeld een metrische waarschuwing configureren wanneer het aantal kernen met lage prioriteit tot een bepaald niveau daalt, zodat u de samenstelling van uw pools aanpassen. Het wordt aanbevolen om een periode van 10 of meer minuten in te stellen wanneer waarschuwingen worden geactiveerd als het gemiddelde kernaantal met lage prioriteit onder de drempelwaarde voor de gehele periode valt. Het wordt afgeraden om te waarschuwen op een periode van 1-5 minuten als metrische gegevens kunnen nog steeds aggregeren.

Ga als lid van het werk om een metrische waarschuwing in de portal te configureren:

1. Klik op**Batchaccounts** **voor alle services** > en klik vervolgens op de naam van uw Batch-account.
2. Klik **onder Controle**op **Waarschuwingsregels** > **Metmetrische waarschuwing toevoegen**.
3. Selecteer een statistiek, een waarschuwingsvoorwaarde (zoals wanneer een statistiek een bepaalde waarde gedurende een bepaalde periode overschrijdt) en een of meer meldingen.

U ook een bijna realtime waarschuwing configureren met behulp van de [REST API.](https://docs.microsoft.com/rest/api/monitor/) Zie [Overzicht van waarschuwingen voor](../azure-monitor/platform/alerts-overview.md)meer informatie. Zie de informatie over zoekopdrachten in Reageren op gebeurtenissen met Azure [Monitor Alerts als](../azure-monitor/learn/tutorial-response.md) u taak-, taak- of poolspecifieke informatie wilt opnemen in uw waarschuwingen.

## <a name="batch-diagnostics"></a>Batch-diagnose

Diagnostische logboeken bevatten informatie die wordt uitgestoten door Azure-bronnen die de werking van elke resource beschrijven. Voor Batch u de volgende logboeken verzamelen:

* **Service logboeken** gebeurtenissen uitgezonden door de Azure Batch service tijdens de levensduur van een afzonderlijke Batch bron zoals een pool of taak. 

* **Statistieken** logboeken op accountniveau. 

Instellingen voor het inschakelen van het verzamelen van diagnostische logboeken zijn niet standaard ingeschakeld. Schakel expliciet diagnostische instellingen in voor elk Batch-account dat u wilt controleren.

### <a name="log-destinations"></a>Logbestemmingen

Een veelvoorkomend scenario is het selecteren van een Azure Storage-account als de logboekbestemming. Als u logboeken wilt opslaan in Azure Storage, maakt u het account voordat u logboeken inschakelt. Als u een opslagaccount aan uw Batch-account hebt gekoppeld, u dat account als logboekbestemming kiezen. 

Andere optionele bestemmingen voor diagnostische logboeken:

* Diagnostische logboekgebeurtenissen voor batch's streamen naar een [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs kunnen miljoenen gebeurtenissen per seconde opnemen, die u vervolgens transformeren en opslaan met elke realtime analyseprovider. 

* Stuur diagnostische logboeken naar [Azure Monitor-logboeken,](../log-analytics/log-analytics-overview.md)waar u ze analyseren of exporteren voor analyse in Power BI of Excel.

> [!NOTE]
> U extra kosten maken voor het opslaan of verwerken van diagnostische logboekgegevens met Azure-services. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Verzameling van diagnostische logboeken van batch inschakelen

1. Klik in de portal op**Batch-accounts**voor **alle services** > en klik vervolgens op de naam van uw Batch-account.
2. Klik **onder Controle**op Diagnostische logboeken Diagnostische **logboeken** > **Inschakelen .**
3. Voer **in diagnostische instellingen**een naam voor de instelling in en kies een logboekbestemming (bestaand opslagaccount, gebeurtenishub of Azure Monitor-logboeken). Selecteer een van beide of beide **ServiceLog** en **AllMetrics**.

    Wanneer u een opslagaccount selecteert, stelt u optioneel een bewaarbeleid in. Als u geen aantal dagen voor bewaring opgeeft, worden gegevens bewaard tijdens de levensduur van het opslagaccount.

4. Klik op **Opslaan**.

    ![Batch-diagnose](media/batch-diagnostics/diagnostics-portal.png)

Andere opties om logboekverzameling in te schakelen zijn: gebruik Azure Monitor in de portal om diagnostische instellingen te configureren, een [Resource Manager-sjabloon](../azure-monitor/platform/diagnostic-settings-template.md)te gebruiken of Azure PowerShell of Azure CLI te gebruiken. zie [Logboekgegevens verzamelen en gebruiken uit uw Azure-bronnen](../azure-monitor/platform/platform-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Toegang tot diagnostische logboeken in opslag

Als u batchdiagnostische logboeken archiveert in een opslagaccount, wordt er een opslagcontainer gemaakt in het opslagaccount zodra een gerelateerde gebeurtenis optreedt. Blobs worden gemaakt volgens het volgende naamgevingspatroon:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Voorbeeld:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Elk `PT1H.json` blobbestand bevat JSON-opgemaakte gebeurtenissen die zijn opgetreden binnen `h=12`het uur dat is opgegeven in de blob-URL (bijvoorbeeld ). Tijdens het huidige uur worden gebeurtenissen `PT1H.json` toegevoegd aan het bestand wanneer ze zich voordoen. De minieme waarde (`m=00`) is altijd `00`, omdat diagnostische log gebeurtenissen zijn opgesplitst in individuele blobs per uur. (Alle tijden zijn in UTC.)

Hieronder vindt u `PoolResizeCompleteEvent` een voorbeeld `PT1H.json` van een vermelding in een logbestand. Het bevat informatie over het huidige en doelaantal toegewezen en lage prioriteitknooppunten, evenals de begin- en eindtijd van de bewerking:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Zie [ArchiveAzure Diagnostic Logs](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account)voor meer informatie over het schema van diagnostische logboeken in het opslagaccount. Als u de logboeken in uw opslagaccount programmatisch wilt openen, gebruikt u de OPSLAG-API's. 

### <a name="service-log-events"></a>Serviceloggebeurtenissen
Azure Batch Service Logs bevatten, indien verzameld, gebeurtenissen die worden uitgestraald door de Azure Batch-service tijdens de levensduur van een afzonderlijke Batch-bron, zoals een groep of taak. Elke gebeurtenis die door Batch wordt uitgezonden, wordt aangemeld in de JSON-indeling. Dit is bijvoorbeeld de hoofdtekst van een **voorbeeldgroep maken gebeurtenis:**

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

De batchservice zendt momenteel de volgende servicelogboekgebeurtenissen uit. Deze lijst is mogelijk niet volledig, omdat er mogelijk aanvullende gebeurtenissen zijn toegevoegd sinds dit artikel voor het laatst is bijgewerkt.

| **Serviceloggebeurtenissen** |
| --- |
| [Poolen maken](batch-pool-create-event.md) |
| [Begin van het verwijderen van de groep](batch-pool-delete-start-event.md) |
| [Groep verwijderen voltooid](batch-pool-delete-complete-event.md) |
| [Beginvan het formaat van de groep](batch-pool-resize-start-event.md) |
| [Het formaat van de groep wijzigen voltooid](batch-pool-resize-complete-event.md) |
| [Taakstart](batch-task-start-event.md) |
| [Taak voltooid](batch-task-complete-event.md) |
| [Taak mislukt](batch-task-fail-event.md) |



## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Batch-API's en -hulpprogramma's](batch-apis-tools.md) die beschikbaar zijn voor het bouwen van Batch-oplossingen.
* Meer informatie over [het bewaken van batchoplossingen](monitoring-overview.md).
