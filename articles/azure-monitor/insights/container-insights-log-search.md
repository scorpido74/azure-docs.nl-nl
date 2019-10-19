---
title: Logboeken van Azure Monitor voor containers opvragen | Microsoft Docs
description: Azure Monitor voor containers worden metrische gegevens en logboek registraties verzameld, en in dit artikel worden de records beschreven en worden voorbeeld query's opgenomen.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/12/2019
ms.openlocfilehash: c3a034776b32db57f70ddee960c1cd5fc96b170b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555406"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Logboeken van Azure Monitor voor containers opvragen

Azure Monitor voor containers worden metrische gegevens over prestaties, inventaris gegevens en informatie over de status van container hosts en containers verzameld en doorgestuurd naar de Log Analytics-werk ruimte in Azure Monitor. De gegevens worden elke drie minuten verzameld. Deze gegevens zijn beschikbaar voor [query's](../../azure-monitor/log-query/log-query-overview.md) in azure monitor. U kunt deze gegevens Toep assen op scenario's met inbegrip van migratie planning, capaciteits analyse, detectie en prestatie problemen oplossen op aanvraag.

## <a name="container-records"></a>Container records

Voor beelden van records die worden verzameld door Azure Monitor voor containers en de gegevens typen die worden weer gegeven in de zoek resultaten voor logboeken worden weer gegeven in de volgende tabel:

| Gegevenstype | Gegevens type in zoeken in Logboeken | Velden |
| --- | --- | --- |
| Prestaties voor hosts en containers | `Perf` | Computer, ObjectName, CounterName &#40;% processor tijd, schijf lezen MB, schijf schrijf bewerkingen, geheugen gebruik MB, ontvangen bytes van het netwerk, verzonden bytes van de CPU, processor gebruik SEC&#41;, netwerk, CounterValue, TimeGenerated, CounterPath, hebben |
| Container voorraad | `ContainerInventory` | TimeGenerated, computer, container naam, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, hebben, ContainerID, ImageID |
| Container logboek | `ContainerLog` | TimeGenerated, computer, afbeeldings-ID, container naam, LogEntrySource, LogEntry, hebben, ContainerID |
| Container-knooppunt inventaris | `ContainerNodeInventory`| TimeGenerated, computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, hebben|
| Inventaris van Peul in een Kubernetes-cluster | `KubePodInventory` | TimeGenerated, computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, controller naam, container status,  ContainerStatusReason, ContainerID, containerName, name, PodLabel, namespace, PodStatus, clustername, PodIp, hebben |
| Inventaris van knoop punten die deel uitmaken van een Kubernetes-cluster | `KubeNodeInventory` | TimeGenerated, computer, clustername, ClusterId, LastTransitionTimeReady, labels, status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, hebben | 
| Kubernetes-gebeurtenissen | `KubeEvents` | TimeGenerated, computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message, hebben | 
| Services in het Kubernetes-cluster | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, hebben | 
| Prestatie gegevens voor knoop punten die deel uitmaken van het Kubernetes-cluster | Perf &#124; waarbij ObjectName = = "K8SNode" | Computer, ObjectName, CounterName &#40;CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, hebben | 
| Prestatie gegevens voor containers onderdeel van het Kubernetes-cluster | Perf &#124; waarbij ObjectName = = "K8SContainer" | Counternaam &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, hebben | 
| Aangepaste metrische gegevens |`InsightsMetrics` | Computer, naam, naam ruimte, oorsprong, hebben, tags<sup>1</sup>, TimeGenerated, type, VA, _ResourceId | 

<sup>1</sup> de eigenschap *Tags* vertegenwoordigt [meerdere dimensies](../platform/data-platform-metrics.md#multi-dimensional-metrics) voor de bijbehorende metriek. Zie [overzicht van InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)voor meer informatie over de metrische gegevens die worden verzameld en opgeslagen in de tabel `InsightsMetrics` en een beschrijving van de record eigenschappen.

>[!NOTE]
>De ondersteuning voor Prometheus is op dit moment een functie in open bare preview.
>

## <a name="search-logs-to-analyze-data"></a>Zoek Logboeken om gegevens te analyseren

Met Azure Monitor Logboeken kunt u zoeken naar trends, knel punten diagnosticeren, prognoses maken of correleren van gegevens waarmee u kunt bepalen of de huidige cluster configuratie optimaal presteert. Vooraf gedefinieerde zoek opdrachten in Logboeken worden weer gegeven zodat u direct aan de slag kunt gaan met of kunt aanpassen om de informatie op de gewenste manier te retour neren.

U kunt een interactieve analyse van gegevens in de werk ruimte uitvoeren door de **gebeurtenis logboeken Kubernetes weer geven** of **container logboeken weer geven** te selecteren in het voorbeeld venster. De pagina **Zoeken in Logboeken** wordt weer gegeven aan de rechter kant van de Azure portal pagina waarop u zich bevond.

![Gegevens analyseren in Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

De container logboeken uitvoer die wordt doorgestuurd naar uw werk ruimte zijn STDOUT en STDERR. Omdat Azure Monitor bewaakt Azure-Managed Kubernetes (AKS), wordt uitvoeren-System vandaag niet verzameld vanwege het grote volume van de gegenereerde gegevens. 

### <a name="example-log-search-queries"></a>Voor beeld van zoek opdrachten in Logboeken

Het is vaak handig om query's te bouwen die beginnen met een voor beeld of twee en deze vervolgens te wijzigen zodat ze aan uw vereisten voldoen. Om geavanceerdere query's te kunnen bouwen, kunt u experimenteren met de volgende voorbeeld query's:

| Query | Beschrijving | 
|-------|-------------|
| ContainerInventory<br> &#124;project computer, naam, afbeelding, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;tabel weer geven | Alle levenscyclus gegevens van een container weer geven| 
| KubeEvents_CL<br> &#124;where not (IsEmpty (Namespace_s))<br> &#124;sorteren op TimeGenerated desc<br> &#124;tabel weer geven | Kubernetes-gebeurtenissen|
| ContainerImageInventory<br> &#124;samenvatten AggregatedValue = Count () per afbeelding, ImageTag, uitgevoerd | Inventaris van installatie kopieën | 
| **Selecteer de weergave optie lijn diagram**:<br> Prestatie<br> &#124;waarbij ObjectName = = "K8SContainer" en CounterName = = "cpuUsageNanoCores" &#124; samen vatting AvgCPUUsageNanoCores = AVG (CounterValue) by bin (TimeGenerated, 30m), INSTANCENAME | Container-CPU | 
| **Selecteer de weergave optie lijn diagram**:<br> Prestatie<br> &#124;waarbij ObjectName = = "K8SContainer" en CounterName = = "memoryRssBytes" &#124; samen vatting AvgUsedRssMemoryBytes = AVG (CounterValue) by bin (TimeGenerated, 30m), INSTANCENAME | Containergeheugen |
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
