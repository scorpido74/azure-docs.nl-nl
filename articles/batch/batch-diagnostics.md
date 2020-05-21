---
title: Metrische gegevens, waarschuwingen en Diagnostische logboeken
description: Registreer en analyseer logboek gebeurtenissen van het diagnostische gegevens voor Azure Batch account resources, zoals Pools en taken.
ms.topic: how-to
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: 0a33f71cd185a327bfe6852b9acd7d7317b94c2c
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726737"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metrische batches, waarschuwingen en logboeken voor diagnostische evaluatie en bewaking

 
In dit artikel wordt uitgelegd hoe u een batch-account kunt bewaken met behulp van functies van [Azure monitor](../azure-monitor/overview.md). Azure Monitor verzamelt [metrische gegevens](../azure-monitor/platform/data-platform-metrics.md) en [Diagnostische logboeken](../azure-monitor/platform/platform-logs-overview.md) voor resources in uw batch-account. Verzamel en gebruik deze gegevens op verschillende manieren om uw batch-account te controleren en problemen op te sporen. U kunt ook [metrische waarschuwingen](../azure-monitor/platform/alerts-overview.md) configureren zodat u meldingen ontvangt wanneer een metriek een opgegeven waarde bereikt. 

## <a name="batch-metrics"></a>Metrische batches

Metrische gegevens zijn Azure-telemetriegegevens (ook wel prestatie meter items genoemd) die worden verzonden door uw Azure-resources die worden gebruikt door de Azure Monitor service. Voor beelden van metrische gegevens in een batch-account zijn: groep maken gebeurtenissen, aantal knoop punten met een lage prioriteit en gebeurtenissen volt ooien. 

Bekijk de [lijst met ondersteunde metrische gegevens voor batches](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Metrische gegevens zijn:

* Standaard ingeschakeld in elk batch-account zonder aanvullende configuratie
* Elke 1 minuut gegenereerd
* Wordt niet automatisch gehandhaafd, maar heeft een rolling geschiedenis van 30 dagen. U kunt metrische gegevens van activiteiten persistent maken als onderdeel van de diagnostische logboek registratie.

### <a name="view-metrics"></a>Metrische gegevens bekijken

Metrische gegevens weer geven voor uw batch-account in de Azure Portal. De **overzichts** pagina voor het account toont standaard de metrische gegevens van het sleutel knooppunt, kernen en taak. 

Alle metrische gegevens van een batch-account weer geven: 

1. Klik in de portal op **alle services**  >  **batch-accounts**en klik vervolgens op de naam van uw batch-account.
2. Klik onder **bewaking**op **metrische gegevens**.
3. Selecteer een of meer metrische gegevens. Als u wilt, selecteert u extra resource metrieken met behulp van de vervolg keuzelijsten **abonnementen**, **resource groep**, **resource type**en **resource** .
    * Voor metrische gegevens op basis van een teller (zoals ' toegewezen aantal kernen ' of ' aantal knoop punten met lage prioriteit '), gebruikt u de aggregatie ' gemiddeld '. Gebruik de aggregatie ' count ' voor metrische gegevens op basis van gebeurtenissen (zoals ' pool formaat wijzigen voltooid ').

> [!WARNING]
> Gebruik niet de aggregatie Sum, waarmee de waarden van alle gegevens punten worden toegevoegd die gedurende de periode van de grafiek worden ontvangen
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Als u metrische gegevens wilt ophalen via een programma, gebruikt u de Azure Monitor-Api's. Zie bijvoorbeeld [Azure monitor metrische gegevens ophalen met .net](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Betrouw baarheid batch metriek

Metrieken zijn bedoeld om te worden gebruikt voor trending en gegevens analyse. Metrische levering is niet gegarandeerd en is onderhevig aan levering, gegevens verlies en/of duplicatie van de bestelling. Het gebruik van afzonderlijke gebeurtenissen voor waarschuwing of activerings functies wordt niet aanbevolen. Zie de sectie [batch metrische waarschuwingen](#batch-metric-alerts) voor meer informatie over het instellen van drempel waarden voor waarschuwingen.

De metrische gegevens die in de afgelopen drie minuten zijn verzonden, kunnen nog steeds worden samengevoegd. Tijdens deze periode kunnen de metrische waarden worden onderkeerd.

## <a name="batch-metric-alerts"></a>Waarschuwingen voor batch metingen

Optioneel kunt u bijna realtime *waarschuwingen* configureren die activeren wanneer de waarde van een opgegeven metriek een drempel overschrijdt die u toewijst. De waarschuwing genereert een [melding](../monitoring-and-diagnostics/insights-alerts-portal.md) die u kiest wanneer de waarschuwing ' geactiveerd ' is (wanneer de drempel wordt overschreden en er wordt voldaan aan de voor waarde van de waarschuwing), en wanneer deze is opgelost (wanneer de drempel waarde opnieuw wordt overschreden en de voor waarde niet meer wordt bereikt). Waarschuwingen op basis van één gegevens punt worden niet aanbevolen, omdat metrische waarden zijn onderworpen aan een buiten-of-bestel levering, gegevens verlies en/of dubbels. Voor waarschuwingen moet het gebruik van drempel waarden worden gebruikt om deze inconsistenties op te nemen.

U kunt bijvoorbeeld een waarschuwing voor metrische gegevens configureren wanneer het aantal kernen op basis van de prioriteit een bepaald niveau heeft, zodat u de samen stelling van uw groeps aanpassen. Het is raadzaam om een periode van 10 of meer minuten in te stellen, waarbij waarschuwingen worden geactiveerd als het aantal kernen met een lage prioriteit lager is dan de drempel waarde voor de hele periode. Het wordt niet aangeraden om een melding te maken over een periode van 1-5 minuten, aangezien metrische gegevens mogelijk nog steeds worden geaggregeerd.

Een waarschuwing voor metrische gegevens configureren in de portal:

1. Klik op **alle services**  >  **batch-accounts**en klik vervolgens op de naam van uw batch-account.
2. Klik onder **bewaking**op **waarschuwings regels**waarschuwing voor  >  **metrische gegevens toevoegen**.
3. Selecteer een metriek, een waarschuwings voorwaarde (bijvoorbeeld wanneer een metriek een bepaalde waarde overschrijdt tijdens een periode) en een of meer meldingen.

U kunt ook een nabije realtime-waarschuwing configureren met behulp van de [rest API](https://docs.microsoft.com/rest/api/monitor/). Zie [overzicht van waarschuwingen](../azure-monitor/platform/alerts-overview.md)voor meer informatie. Zie de informatie over zoek query's in [reageren op gebeurtenissen met Azure monitor-waarschuwingen](../azure-monitor/learn/tutorial-response.md) om taak-, taak-of pool-specifieke informatie in uw waarschuwingen op te vragen.

## <a name="batch-diagnostics"></a>Batch-diagnose

Diagnostische logboeken bevatten informatie die wordt verzonden door Azure-resources die de werking van elke resource beschrijven. Voor batch kunt u de volgende logboeken verzamelen:

* **Service logboeken** gebeurtenissen die worden verzonden door de Azure batch-service tijdens de levens duur van een afzonderlijke batch resource, zoals een groep of taak. 

* **Metrische gegevens** worden geregistreerd op account niveau. 

De instellingen voor het inschakelen van het verzamelen van Diagnostische logboeken zijn standaard niet ingeschakeld. Schakel de diagnostische instellingen expliciet in voor elk batch-account dat u wilt bewaken.

### <a name="log-destinations"></a>Logboek bestemmingen

Een veelvoorkomend scenario is het selecteren van een Azure Storage-account als de logboek bestemming. Als u Logboeken in Azure Storage wilt opslaan, moet u het account maken voordat u het verzamelen van logboeken inschakelt. Als u een opslag account aan uw batch-account hebt gekoppeld, kunt u dat account kiezen als de logboek bestemming. 

Andere optionele bestemmingen voor Diagnostische logboeken:

* Batch diagnostische logboek gebeurtenissen streamen naar een [Azure Event hub](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs kan miljoenen gebeurtenissen per seconde opnemen, die u vervolgens kunt transformeren en opslaan met behulp van een real-time analyse provider. 

* Diagnostische logboeken verzenden naar [Azure monitor-logboeken](../log-analytics/log-analytics-overview.md), waar u ze kunt analyseren of exporteren voor analyse in Power bi of Excel.

> [!NOTE]
> Mogelijk worden er extra kosten in rekening gebracht om diagnostische logboek gegevens met Azure-Services op te slaan of te verwerken. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Verzamelen van batch-diagnose logboeken inschakelen

1. Klik in de portal op **alle services**  >  **batch-accounts**en klik vervolgens op de naam van uw batch-account.
2. Klik onder **bewaking**op **Diagnostische logboeken**  >  **Diagnostische gegevens inschakelen**.
3. Voer in **Diagnostische instellingen**een naam in voor de instelling en kies een logboek bestemming (bestaand opslag account, Event Hub of Azure monitor-Logboeken). Selecteer ofwel **ServiceLog** als **AllMetrics**.

    Wanneer u een opslag account selecteert, kunt u desgewenst een Bewaar beleid instellen. Als u niet een aantal dagen opgeeft voor retentie, worden gegevens bewaard tijdens de levens duur van het opslag account.

4. Klik op **Opslaan**.

    ![Batch-diagnose](media/batch-diagnostics/diagnostics-portal.png)

Andere opties voor het inschakelen van de logboek verzameling zijn onder andere: gebruik Azure Monitor in de portal om Diagnostische instellingen te configureren, een [Resource Manager-sjabloon](../azure-monitor/platform/diagnostic-settings-template.md)te gebruiken of Azure PowerShell of de Azure CLI te gebruiken. Zie [logboek gegevens verzamelen en gebruiken van uw Azure-resources](../azure-monitor/platform/platform-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Diagnostische logboeken openen in Storage

Als u batch-diagnose logboeken archiveert in een opslag account, wordt er een opslag container gemaakt in het opslag account zodra er een gerelateerde gebeurtenis plaatsvindt. Blobs worden gemaakt aan de hand van het volgende naamgevings patroon:

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
Elk `PT1H.json` blob-bestand bevat gebeurtenissen in JSON-indeling die zijn opgetreden binnen het uur dat is opgegeven in de BLOB-URL (bijvoorbeeld `h=12` ). Tijdens het huidige uur worden gebeurtenissen aan het `PT1H.json` bestand toegevoegd wanneer deze zich voordoen. De minuut waarde ( `m=00` ) is altijd `00` , omdat de diagnostische logboek gebeurtenissen per uur worden opgesplitst in afzonderlijke blobs. (Alle tijden zijn in UTC.)

Hieronder ziet u een voor beeld van een `PoolResizeCompleteEvent` vermelding in een `PT1H.json` logboek bestand. Het bevat informatie over het huidige en het doel aantal toegewezen knoop punten met een lage prioriteit, evenals de begin-en eind tijd van de bewerking:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Zie voor meer informatie over het schema van Diagnostische logboeken in het opslag account [Azure Diagnostic logs archiveren](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). Als u via een programma toegang wilt krijgen tot de logboeken in uw opslag account, gebruikt u de opslag-Api's. 

### <a name="service-log-events"></a>Service logboek gebeurtenissen
Azure Batch service logboeken, indien verzameld, bevatten gebeurtenissen die worden gegenereerd door de Azure Batch-service tijdens de levens duur van een afzonderlijke batch resource, zoals een groep of taak. Elke gebeurtenis die wordt verzonden door batch, wordt geregistreerd in JSON-indeling. Dit is bijvoorbeeld de hoofd tekst van een voor beeld van een **groep maken gebeurtenis**:

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

De batch-service verzendt momenteel de volgende service logboek gebeurtenissen. Deze lijst is mogelijk niet volledig, omdat er mogelijk aanvullende gebeurtenissen zijn toegevoegd sinds dit artikel voor het laatst is bijgewerkt.

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
