---
title: Metrische gegevens, waarschuwingen en Diagnostische logboeken-Azure Batch | Microsoft Docs
description: Registreren en analyseren van diagnostische gebeurtenissen voor Azure Batch-accountresources zoals pools en taken.
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
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254863"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch metrische gegevens, waarschuwingen en logboeken voor diagnostische evaluatie en bewaking

 
In dit artikel wordt uitgelegd hoe u een batch-account kunt bewaken met behulp van functies van [Azure monitor](../azure-monitor/overview.md). Azure Monitor verzamelt [metrische gegevens](../azure-monitor/platform/data-platform-metrics.md) en [Diagnostische logboeken](../azure-monitor/platform/platform-logs-overview.md) voor resources in uw batch-account. Verzamelen en gebruiken deze gegevens op verschillende manieren voor het bewaken van uw Batch-account en problemen diagnosticeren. U kunt ook [metrische waarschuwingen](../azure-monitor/platform/alerts-overview.md) configureren zodat u meldingen ontvangt wanneer een metriek een opgegeven waarde bereikt. 

## <a name="batch-metrics"></a>Metrische gegevens voor batch

Metrische gegevens zijn Azure telemetriegegevens (ook wel prestatiemeteritems) die door uw Azure-resources die worden verbruikt door de service Azure Monitor. Voorbeeld van de metrische gegevens in een Batch-account zijn: Pool maken gebeurtenissen, aantal van de knooppunten met lage prioriteit en taak voltooid. 

Bekijk de [lijst met ondersteunde metrische gegevens voor batches](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Metrische gegevens zijn:

* Standaard ingeschakeld in elke Batch-account zonder aanvullende configuratie
* Elke minuut gegenereerd
* Niet permanent automatisch, maar beschikken over een 30-daagse rolling overzicht. U kunt metrische gegevens van activiteiten persistent maken als onderdeel van de diagnostische logboek registratie.

### <a name="view-metrics"></a>Metrische gegevens bekijken

Bekijk metrische gegevens voor uw Batch-account in Azure portal. De **overzichts** pagina voor het account toont standaard de metrische gegevens van het sleutel knooppunt, kernen en taak. 

Alle Batch-account metrische gegevens weergeven: 

1. Klik in de portal op **alle services** > **batch-accounts**en klik vervolgens op de naam van uw batch-account.
2. Klik onder **bewaking**op **metrische gegevens**.
3. Selecteer een of meer van de metrische gegevens. Als u wilt, selecteert u extra resource metrieken met behulp van de vervolg keuzelijsten **abonnementen**, **resource groep**, **resource type**en **resource** .
    * Voor metrische gegevens op basis van een teller (zoals ' toegewezen aantal kernen ' of ' aantal knoop punten met lage prioriteit '), gebruikt u de aggregatie ' gemiddeld '. Gebruik de aggregatie ' count ' voor metrische gegevens op basis van gebeurtenissen (zoals ' pool formaat wijzigen voltooid ').

> [!WARNING]
> Gebruik niet de aggregatie Sum, waarmee de waarden van alle gegevens punten worden toegevoegd die gedurende de periode van de grafiek worden ontvangen
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Om op te halen metrische gegevens via een programma, gebruikt u de Azure Monitor-API's. Zie bijvoorbeeld [Azure monitor metrische gegevens ophalen met .net](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Metrische betrouwbaarheid van batch

Metrische gegevens zijn bedoeld om te worden gebruikt voor trending en -gegevensanalyse. Levering van metrische gegevens kan niet worden gegarandeerd en is onderhevig aan de out-van-volgorde, verlies van gegevens en/of dupliceren. Met behulp van één gebeurtenissen in waarschuwing of trigger functies wordt niet aanbevolen. Zie de sectie [batch metrische waarschuwingen](#batch-metric-alerts) voor meer informatie over het instellen van drempel waarden voor waarschuwingen.

Metrische gegevens worden weergegeven in de afgelopen 3 minuten kunnen nog steeds worden geaggregeerd. Gedurende deze periode, kunnen de metrische waarden worden ondergerapporteerd.

## <a name="batch-metric-alerts"></a>Batch metrische waarschuwingen

Optioneel kunt u bijna realtime *waarschuwingen* configureren die activeren wanneer de waarde van een opgegeven metriek een drempel overschrijdt die u toewijst. De waarschuwing genereert een [melding](../monitoring-and-diagnostics/insights-alerts-portal.md) die u kiest wanneer de waarschuwing ' geactiveerd ' is (wanneer de drempel wordt overschreden en er wordt voldaan aan de voor waarde van de waarschuwing), en wanneer deze is opgelost (wanneer de drempel waarde opnieuw wordt overschreden en de voor waarde niet meer wordt bereikt). Waarschuwingen op basis van één gegevenspunten wordt niet aanbevolen omdat metrische gegevens onderworpen aan de out-van-volgorde, verlies van gegevens en/of dupliceren zijn. Waarschuwingen moet het gebruik van de drempelwaarden voor deze inconsistenties.

Bijvoorbeeld, als u wilt een waarschuwing voor metrische gegevens configureren wanneer uw kerngeheugens met lage prioriteit op een bepaald niveau, valt zodat u de samenstelling van uw toepassingen kunt aanpassen. Het verdient aanbeveling om in te stellen van een periode van meer dan 10 minuten waar waarschuwingen activeren als de gemiddelde met lage prioriteit core-telling onder de drempelwaarde voor de hele periode valt. Het wordt niet aanbevolen om te waarschuwen over een periode van 1-5 minuten, zoals metrische gegevens kunnen nog steeds aggregeren.

Het configureren van een waarschuwing voor metrische gegevens in de portal:

1. Klik op **Alle services** > **Batch-accounts** en klik vervolgens op de naam van uw Batch-account.
2. Klik onder **bewaking**op **waarschuwings regels** > **waarschuwing voor metrische gegevens toevoegen**.
3. Selecteer een metrische waarde, de voorwaarde voor een waarschuwing (zoals wanneer een metrische waarde een bepaalde waarde gedurende een periode overschrijdt) en een of meer meldingen.

U kunt ook een nabije realtime-waarschuwing configureren met behulp van de [rest API](https://docs.microsoft.com/rest/api/monitor/). Zie [overzicht van waarschuwingen](../azure-monitor/platform/alerts-overview.md)voor meer informatie. Zie de informatie over zoek query's in [reageren op gebeurtenissen met Azure monitor-waarschuwingen](../azure-monitor/learn/tutorial-response.md) om taak-, taak-of pool-specifieke informatie in uw waarschuwingen op te vragen.

## <a name="batch-diagnostics"></a>Diagnostische gegevens van batch

Diagnostische logboeken bevatten gegevens die zijn gegenereerd door Azure-resources die de werking van elke resource beschrijven. Voor Batch, kunt u de volgende logboeken te verzamelen:

* **Service logboeken** gebeurtenissen die worden verzonden door de Azure batch-service tijdens de levens duur van een afzonderlijke batch resource, zoals een groep of taak. 

* **Metrische gegevens** worden geregistreerd op account niveau. 

Instellingen om te verzamelen van logboeken met diagnostische gegevens worden niet standaard ingeschakeld. Diagnostische instellingen voor elke Batch-account dat u wilt bewaken expliciet inschakelen.

### <a name="log-destinations"></a>Logboek bestemmingen

Een veelvoorkomend scenario is een Azure Storage-account selecteren als het doel van het logboek. Voor het opslaan van Logboeken in Azure Storage, moet u het account maken voordat u het verzamelen van logboeken inschakelt. Als u een storage-account gekoppeld aan uw Batch-account, kunt u dat account als de logboekbestemming. 

Andere optionele bestemmingen voor diagnostische logboeken:

* Batch diagnostische logboek gebeurtenissen streamen naar een [Azure Event hub](../event-hubs/event-hubs-what-is-event-hubs.md). Eventhubs kunnen miljoenen gebeurtenissen per seconde, die u kunt vervolgens transformeren en opslaan met elke gewenste realtime analyseprovider opnemen. 

* Diagnostische logboeken verzenden naar [Azure monitor-logboeken](../log-analytics/log-analytics-overview.md), waar u ze kunt analyseren of exporteren voor analyse in Power bi of Excel.

> [!NOTE]
> Er worden mogelijk extra kosten als u wilt opslaan of verwerken van diagnostische gegevens van een met Azure-services. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Verzamelen van diagnostische logboeken voor Batch

1. Klik in de portal op **alle services** > **batch-accounts**en klik vervolgens op de naam van uw batch-account.
2. Klik onder **bewaking**op **diagnostische logboeken** > **Diagnostische gegevens inschakelen**.
3. Voer in **Diagnostische instellingen**een naam in voor de instelling en kies een logboek bestemming (bestaand opslag account, Event Hub of Azure monitor-Logboeken). Selecteer ofwel **ServiceLog** als **AllMetrics**.

    Wanneer u een storage-account selecteert, moet u eventueel een bewaarbeleid instellen. Als u een aantal dagen voor bewaarperiode niet opgeeft, worden gegevens worden bewaard gedurende de levensduur van het storage-account.

4. Klik op **Opslaan**.

    ![Diagnostische gegevens van batch](media/batch-diagnostics/diagnostics-portal.png)

Andere opties voor het inschakelen van de logboek verzameling zijn onder andere: gebruik Azure Monitor in de portal om Diagnostische instellingen te configureren, een [Resource Manager-sjabloon](../azure-monitor/platform/diagnostic-settings-template.md)te gebruiken of Azure PowerShell of de Azure CLI te gebruiken. Zie [logboek gegevens verzamelen en gebruiken van uw Azure-resources](../azure-monitor/platform/platform-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Toegang tot diagnostische logboeken in de opslag

Als u diagnostische logboeken voor Batch in een opslagaccount archiveren, wordt een opslagcontainer wordt gemaakt in de storage-account als een gerelateerde gebeurtenis zich voordoet. BLOBs zijn gemaakt op basis van een naamgevingspatroon uit het volgende:

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
Elk `PT1H.json` blob-bestand bevat gebeurtenissen in JSON-indeling die zijn opgetreden binnen het uur dat is opgegeven in de BLOB-URL (bijvoorbeeld `h=12`). Tijdens het huidige uur worden gebeurtenissen aan het `PT1H.json`-bestand toegevoegd wanneer deze zich voordoen. De minuut waarde (`m=00`) is altijd `00`, omdat de diagnostische logboek gebeurtenissen worden onderverdeeld in afzonderlijke blobs per uur. (Alle tijden zijn in UTC).

Hieronder ziet u een voor beeld van een `PoolResizeCompleteEvent` vermelding in een `PT1H.json`-logboek bestand. Het bevat informatie over het huidige en het doel aantal toegewezen knoop punten met een lage prioriteit, evenals de begin-en eind tijd van de bewerking:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Zie voor meer informatie over het schema van Diagnostische logboeken in het opslag account [Azure Diagnostic logs archiveren](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). Als u wilt toegang krijgen tot de logboeken in uw storage-account via een programma, de opslag-API's te gebruiken. 

### <a name="service-log-events"></a>Gebeurtenissen van de service-logboek
Azure Batch-Service zich aanmeldt, als die worden verzameld, bevatten gebeurtenissen die zijn gegenereerd door de Azure Batch-service tijdens de levensduur van een afzonderlijke Batch-bron, zoals een groep of -taak. Elke gebeurtenis verzonden door de Batch wordt geregistreerd in JSON-indeling. Dit is bijvoorbeeld de hoofd tekst van een voor beeld van een **groep maken gebeurtenis**:

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

De Batch-service verzendt momenteel de volgende Service-logboek-gebeurtenissen. Deze lijst zijn mogelijk niet volledig, omdat er aanvullende gebeurtenissen zijn toegevoegd sinds dit artikel voor het laatst is bijgewerkt.

| **Service logboek gebeurtenissen** |
| --- |
| [Groep maken](batch-pool-create-event.md) |
| [Groep verwijderen starten](batch-pool-delete-start-event.md) |
| [Groep verwijderen is voltooid](batch-pool-delete-complete-event.md) |
| [Begin grootte van groep wijzigen](batch-pool-resize-start-event.md) |
| [Grootte van groep volt ooien voltooid](batch-pool-resize-complete-event.md) |
| [Taak starten](batch-task-start-event.md) |
| [Taak voltooid](batch-task-complete-event.md) |
| [Taak mislukt](batch-task-fail-event.md) |



## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Batch-API's en -hulpprogramma's](batch-apis-tools.md) die beschikbaar zijn voor het bouwen van Batch-oplossingen.
* Meer informatie over het [bewaken van batch-oplossingen](monitoring-overview.md).
