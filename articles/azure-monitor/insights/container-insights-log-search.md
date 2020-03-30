---
title: Logboeken opvragen vanuit Azure Monitor voor containers | Microsoft Documenten
description: Azure Monitor voor containers verzamelt metrische gegevens en logboekgegevens en in dit artikel worden de records beschreven en bevat het voorbeeldquery's.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: ff7cbff708b794847d8be69ca8f829e622d7c7ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333481"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Logboeken van Azure Monitor opvragen voor containers

Azure Monitor voor containers verzamelt prestatiestatistieken, voorraadgegevens en statusgegevens van containerhosts en -containers en stuurt deze door naar de werkruimte Log Analytics in Azure Monitor. Elke drie minuten worden gegevens verzameld. Deze gegevens zijn beschikbaar voor [query's](../../azure-monitor/log-query/log-query-overview.md) in Azure Monitor. U deze gegevens toepassen op scenario's zoals migratieplanning, capaciteitsanalyse, detectie en probleemoplossing voor prestaties op aanvraag.

## <a name="container-records"></a>Containerrecords

Voorbeelden van records die door Azure Monitor worden verzameld voor containers en de gegevenstypen die worden weergegeven in logboekzoekresultaten, worden weergegeven in de volgende tabel:

| Gegevenstype | Gegevenstype in Logboekzoeken | Velden |
| --- | --- | --- |
| Prestaties voor hosts en containers | `Perf` | Computer, objectnaam, tegennaam &#40;%Processortijd, Schijf leest MB, Disk Writes MB, Geheugengebruik MB, Netwerk ontvangen Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, Timegenerated, CounterPath, SourceSystem |
| Containervoorraad | `ContainerInventory` | Timegenerated, Computer, containernaam, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, Environmentvar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Containerlogboek | `ContainerLog` | TimeGenerated, Computer, image ID, containernaam, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Voorraad containerknooppunt | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventaris van pods in een Kubernetes-cluster | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventaris van knooppunten die deel uitmaken van een Kubernetes-cluster | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes-gebeurtenissen | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message, SourceSystem | 
| Services in het Kubernetes-cluster | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Prestatiestatistieken voor knooppunten die deel uitmaken van het Kubernetes-cluster | Perf &#124; waar ObjectName == "K8SNode" | Computer, ObjectName, CounterName &#40;cpuAllocatableBytes, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Prestatiestatistieken voor containers die deel uitmaken van het Kubernetes-cluster | Perf &#124; waar ObjectName == "K8SContainer" | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Aangepaste statistieken |`InsightsMetrics` | Computer, Naam, Naamruimte, Origin, SourceSystem, Tags<sup>1</sup>, TimeGenerated, Type, Va, _ResourceId | 

<sup>1</sup> De eigenschap *Tags* vertegenwoordigt [meerdere dimensies](../platform/data-platform-metrics.md#multi-dimensional-metrics) voor de bijbehorende statistiek. Zie [InsightsMetrics-overzicht](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)voor meer `InsightsMetrics` informatie over de statistieken die in de tabel zijn verzameld en opgeslagen en een beschrijving van de recordeigenschappen.

## <a name="search-logs-to-analyze-data"></a>Logboeken doorzoeken om gegevens te analyseren

Azure Monitor Logs kunnen u helpen bij het zoeken naar trends, het diagnosticeren van knelpunten, het voorspellen of correleren van gegevens die u kunnen helpen bepalen of de huidige clusterconfiguratie optimaal presteert. Vooraf gedefinieerde logboekzoekopdrachten worden verstrekt om onmiddellijk te beginnen met het gebruik of aan te passen om de informatie terug te sturen zoals u dat wilt.

U interactieve gegevensanalyse in de werkruimte uitvoeren door de optie **Kubernetes-gebeurtenislogboeken weergeven** of **Containerlogboeken weergeven** in het voorbeeldvenster in de vervolgkeuzelijst **Weergave in analytics** te selecteren. De pagina **Logboekzoeken** wordt rechts van de Azure-portalpagina weergegeven waarop u zich bevindt.

![Gegevens analyseren in Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

De uitvoer van containerlogboeken die naar uw werkruimte wordt doorgestuurd, zijn SOAOUT en STDERR. Omdat Azure Monitor azure-managed Kubernetes (AKS) bewaakt, wordt kube-systeem vandaag niet verzameld vanwege het grote volume van gegenereerde gegevens. 

### <a name="example-log-search-queries"></a>Voorbeeld van query's voor logboekquery's

Het is vaak handig om query's te bouwen die beginnen met een voorbeeld of twee en deze vervolgens aan te passen aan uw vereisten. Als u meer geavanceerde query's wilt maken, u experimenteren met de volgende voorbeeldquery's:

| Query’s uitvoeren | Beschrijving | 
|-------|-------------|
| ContainerVoorraad<br> &#124; project Computer, Naam, Afbeelding, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; rendertabel | Alle levenscyclusgegevens van een container weergeven| 
| KubeEvents_CL<br> &#124; waar niet(isleeg(Namespace_s))<br> &#124; sorteren op TimeGenerated desc<br> &#124; rendertabel | Kubernetes-gebeurtenissen|
| ContainerImageInventory<br> &#124; samenvatten Van Geaggregeerde Waarde = aantal() op afbeelding, imagetag, actief | Afbeeldingsinventaris | 
| **Selecteer de optie Grafiek lijnweergave:**<br> Prestaties<br> &#124; waar ObjectName == "K8SContainer" en CounterName == "cpuUsageNanoCores" &#124; AvgCPUUsageNanoCores = avg(CounterValue) per bin (TimeGenerated, 30m), InstanceName samenvatten | Container-CPU | 
| **Selecteer de optie Grafiek lijnweergave:**<br> Prestaties<br> &#124; waar ObjectName == "K8SContainer" en CounterName == "memoryRssBytes" &#124; avgusedrssmemorybytes = avg(CounterValue) per bin (TimeGenerated, 30m), InstanceName samenvatten | Containergeheugen |
| InsightsMetrics<br> &#124; waar Naam == "requests_count"<br> &#124; Val=any(Val) samenvatten op TimeGenerated=bin (TimeGenerated, 1m)<br> &#124; sorteren op TimeGenerated asc<br> &#124; project RequestsPerMinute = Val - prev(Val), TimeGenerated <br> &#124; renderdiagram  | Aanvragen per minuut met aangepaste statistieken |

## <a name="query-prometheus-metrics-data"></a>Gegevens over prometheus-statistieken van Query

Het volgende voorbeeld is een Prometheus-metrische query met schijfreads per seconde per schijf per knooppunt.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Als u Prometheus-statistieken wilt weergeven die zijn geschraapt door Azure Monitor gefilterd op Naamruimte, geeft u 'prometheus' op. Hier is een voorbeeldquery om Prometheus-statistieken te bekijken vanuit de `default` kubernetes-naamruimte.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Prometheus-gegevens kunnen ook direct op naam worden opgevraagd.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Fouten in queryconfig of scraping

Als u configuratie- of schrapfouten wilt onderzoeken, retourneert de volgende voorbeeldquery informatieve gebeurtenissen uit de `KubeMonAgentEvents` tabel.

```
KubeMonAgentEvents | where Level != "Info" 
```

De uitvoer toont resultaten die vergelijkbaar zijn met de volgende resultaten:

![Queryresultaten van informatieve gebeurtenissen van agent registreren](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Volgende stappen

Azure Monitor voor containers bevat geen vooraf gedefinieerde set waarschuwingen. Bekijk de [prestatiewaarschuwingen maken met Azure Monitor voor containers](container-insights-alerts.md) voor meer informatie over het maken van aanbevolen waarschuwingen voor een hoog CPU- en geheugengebruik ter ondersteuning van uw DevOps of operationele processen en procedures. 
