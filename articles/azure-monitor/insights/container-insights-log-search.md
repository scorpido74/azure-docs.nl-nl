---
title: Logboeken van Azure Monitor voor containers opvragen | Microsoft Docs
description: Azure Monitor voor containers worden metrische gegevens en logboek registraties verzameld, en in dit artikel worden de records beschreven en worden voorbeeld query's opgenomen.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: ae8dd4cccb6795faa02e6705404644f6ccc24864
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948048"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Logboeken van Azure Monitor voor containers opvragen

Azure Monitor voor containers worden metrische gegevens over prestaties, inventaris gegevens en informatie over de status van container hosts en containers verzameld en doorgestuurd naar de Log Analytics-werk ruimte in Azure Monitor. Gegevens worden verzameld om de drie minuten. Deze gegevens zijn beschikbaar voor [query's](../../azure-monitor/log-query/log-query-overview.md) in azure monitor. U kunt deze gegevens Toep assen op scenario's met inbegrip van migratie planning, capaciteits analyse, detectie en prestatie problemen oplossen op aanvraag.

## <a name="container-records"></a>Container-records

Voorbeelden van records die door Azure Monitor worden verzameld voor containers en de gegevenstypen die worden weergegeven in de resultaten van de logboekzoekopdracht worden weergegeven in de volgende tabel:

| Gegevenstype | Het gegevenstype in zoeken in Logboeken | Velden |
| --- | --- | --- |
| Prestaties voor hosts en -containers | `Perf` | Computer, ObjectName, CounterName &#40;% processortijd, schijf leest MB, schijf schrijft MB, gebruik van geheugen (MB), netwerk ontvangen Bytes, netwerk verzenden Bytes, Processor gebruik seconde, netwerk&#41;, CounterValue, TimeGenerated, itempad, SourceSystem |
| Container-inventaris | `ContainerInventory` | TimeGenerated, de Computer, de containernaam, ContainerHostname, afbeelding, ImageTag, ContainerState, ExitCode, EnvironmentVar, opdracht, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Container-logboek | `ContainerLog` | TimeGenerated, Computer, afbeeldings-ID, containernaam van de, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventaris van containerknooppunten | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventaris van schillen in een Kubernetes-cluster | `KubePodInventory` | TimeGenerated, computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, controller naam, container status,  ContainerStatusReason, ContainerID, containerName, name, PodLabel, namespace, PodStatus, clustername, PodIp, hebben |
| Inventarisatie van een deel van de knooppunten van een Kubernetes-cluster | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes-gebeurtenissen | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, bericht, SourceSystem | 
| Services in het Kubernetes-cluster | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metrische gegevens voor prestaties voor een deel van de knooppunten van het Kubernetes-cluster | Perf &#124; waarbij ObjectName == "K8SNode" | Computer, ObjectName, CounterName &#40;CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, hebben | 
| Metrische gegevens voor prestaties voor containers die deel uitmaken van het Kubernetes-cluster | Perf &#124; waarbij ObjectName == "K8SContainer" | Counternaam &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, hebben | 
| Aangepaste metrische gegevens |`InsightsMetrics` | Computer, naam, naam ruimte, oorsprong, hebben, tags<sup>1</sup>, TimeGenerated, type, VA, _ResourceId | 

<sup>1</sup> de eigenschap *Tags* vertegenwoordigt [meerdere dimensies](../platform/data-platform-metrics.md#multi-dimensional-metrics) voor de bijbehorende metriek. Zie [overzicht van InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)voor meer informatie over de metrische gegevens die worden verzameld en opgeslagen in de tabel `InsightsMetrics` en een beschrijving van de record eigenschappen.

>[!NOTE]
>De ondersteuning voor Prometheus is op dit moment een functie in open bare preview.
>

## <a name="search-logs-to-analyze-data"></a>Zoeken in Logboeken om gegevens te analyseren

Met Azure Monitor Logboeken kunt u zoeken naar trends, knel punten diagnosticeren, prognoses maken of correleren van gegevens waarmee u kunt bepalen of de huidige cluster configuratie optimaal presteert. Vooraf gedefinieerde zoekopdrachten in Logboeken vindt u onmiddellijk kunt beginnen met of om aan te passen als u wilt de gegevens van de manier waarop die u wilt retourneren.

U kunt uitvoeren interactieve analyses van gegevens in de werkruimte selecteren de **weergave Kubernetes gebeurtenislogboeken** of **containerlogboeken bekijken** optie in het voorbeeldvenster. De **zoeken in logboeken** pagina wordt weergegeven aan de rechterkant van de Azure portal-pagina die u al had geopend.

![Gegevens analyseren in Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

De container logboeken uitvoer die wordt doorgestuurd naar uw werk ruimte zijn STDOUT en STDERR. Omdat Azure Monitor wordt bewaakt door Azure beheerde Kubernetes (AKS), Kube-systeem niet vandaag verzameld vanwege het grote aantal gegenereerde gegevens. 

### <a name="example-log-search-queries"></a>Voorbeeld van de logboekbestanden zoekquery 's

Vaak is het handig om te maken van query's die beginnen met een voorbeeld of twee en vervolgens kan aanpassen aan uw behoeften. Om te helpen meer geavanceerde query's, kunt u experimenteren met de volgende voorbeeldquery's:

| Query’s uitvoeren | Description | 
|-------|-------------|
| ContainerInventory<br> &#124;Computer, de naam, afbeelding, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime project<br> &#124;tabel weergeven | Lijst met alle van de container levenscyclus van gegevens| 
| KubeEvents_CL<br> &#124;waar not(isempty(Namespace_s))<br> &#124;sorteren op TimeGenerated desc<br> &#124;tabel weergeven | Kubernetes-gebeurtenissen|
| ContainerImageInventory<br> &#124;summarize AggregatedValue = count() by afbeelding, ImageTag, actief | Voorraad | 
| **Selecteer de optie lijndiagram weergeven**:<br> Prestaties<br> &#124;waarbij ObjectName == "K8SContainer" en CounterName == "cpuUsageNanoCores" &#124; AvgCPUUsageNanoCores samenvatten avg(CounterValue) door bin (TimeGenerated, 30 min.), InstanceName = | Container CPU | 
| **Selecteer de optie lijndiagram weergeven**:<br> Prestaties<br> &#124;waarbij ObjectName == "K8SContainer" en CounterName == "memoryRssBytes" &#124; AvgUsedRssMemoryBytes samenvatten avg(CounterValue) door bin (TimeGenerated, 30 min.), InstanceName = | Container-geheugen |
| InsightsMetrics<br> &#124;WHERE name = = "requests_count"<br> &#124;noeme val = any (val) by TimeGenerated = bin (TimeGenerated, 1M)<br> &#124;sorteren op TimeGenerated ASC<br> &#124;project RequestsPerMinute = val-vorig (val), TimeGenerated <br> &#124;Barchart weer geven  | Aanvragen per minuut met aangepaste metrische gegevens |

Het volgende voor beeld is een query voor Prometheus-metrische gegevens. De verzamelde metrische gegevens zijn aantallen en om te bepalen hoeveel fouten binnen een bepaalde periode zijn opgetreden, moeten we van de telling aftrekken. De gegevensset wordt gepartitioneerd door *partitionKey*, wat betekent dat voor elke unieke set met de *naam*, de *hostnaam*en de *OperationType*een subquery wordt uitgevoerd op die set die de logboeken bestelt op *TimeGenerated*, een proces dat het mogelijk maakt om Zoek het vorige *TimeGenerated* en het aantal dat voor die tijd is vastgelegd om een bepaald aantal te bepalen.

```
let data = InsightsMetrics 
| where Namespace contains 'prometheus' 
| where Name == 'kubelet_docker_operations' or Name == 'kubelet_docker_operations_errors'    
| extend Tags = todynamic(Tags) 
| extend OperationType = tostring(Tags['operation_type']), HostName = tostring(Tags.hostName) 
| extend partitionKey = strcat(HostName, '/' , Name, '/', OperationType) 
| partition by partitionKey ( 
    order by TimeGenerated asc 
    | extend PrevVal = prev(Val, 1), PrevTimeGenerated = prev(TimeGenerated, 1) 
    | extend Rate = iif(TimeGenerated == PrevTimeGenerated, 0.0, Val - PrevVal) 
    | where isnull(Rate) == false 
) 
| project TimeGenerated, Name, HostName, OperationType, Rate; 
let operationData = data 
| where Name == 'kubelet_docker_operations' 
| project-rename OperationCount = Rate; 
let errorData = data 
| where Name == 'kubelet_docker_operations_errors' 
| project-rename ErrorCount = Rate; 
operationData 
| join kind = inner ( errorData ) on TimeGenerated, HostName, OperationType 
| project-away TimeGenerated1, Name1, HostName1, OperationType1 
| extend SuccessPercentage = iif(OperationCount == 0, 1.0, 1 - (ErrorCount / OperationCount))
```

De uitvoer ziet er ongeveer als volgt uit:

![Query resultaten van het volume gegevens opname vastleggen](./media/container-insights-log-search/log-query-example-prometheus-metrics.png)

## <a name="next-steps"></a>Volgende stappen

Azure Monitor voor containers bevat geen vooraf gedefinieerde set met waarschuwingen. Raadpleeg de procedures [voor het maken van prestatie waarschuwingen met Azure monitor voor containers](container-insights-alerts.md) voor meer informatie over het maken van aanbevolen waarschuwingen voor hoog CPU-en geheugen gebruik ter ondersteuning van uw DevOps-of operationele processen en procedures. 
