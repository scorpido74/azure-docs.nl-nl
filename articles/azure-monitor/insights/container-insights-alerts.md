---
title: Prestatiewaarschuwingen maken voor Azure Monitor voor containers | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u aangepaste waarschuwingen maakt op basis van logboekquery's voor geheugen- en CPU-gebruik van Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 5d73f4399d10683597fb2a2e8a3a2ab4ba0d1165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75730922"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Waarschuwingen instellen voor prestatieproblemen in Azure Monitor voor containers

Azure Monitor voor containers bewaakt de prestaties van containerworkloads die zijn geïmplementeerd in Azure Container Instances of naar beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes Service (AKS).

In dit artikel wordt beschreven hoe u waarschuwingen inschakelt voor de volgende situaties:

- Wanneer CPU- of geheugengebruik op clusterknooppunten een drempelwaarde overschrijdt
- Wanneer cpu- of geheugengebruik op een container binnen een controller een drempelwaarde overschrijdt in vergelijking met een limiet die is ingesteld op de desbetreffende resource
- *Statusknooppunt NotReady* telt
- *Aantal* *tellingen* in *behandeling,* *onbekend,* *lopend*of geslaagd
- Wanneer vrije schijfruimte op clusterknooppunten een drempelwaarde overschrijdt 

Als u wilt waarschuwen voor een hoog CPU- of geheugengebruik of een lage vrije schijfruimte op clusterknooppunten, gebruikt u de query's die worden geleverd om een metrische waarschuwing of een metrische metingswaarschuwing te maken. Metrische waarschuwingen hebben een lagere latentie dan logboekwaarschuwingen. Maar logboekwaarschuwingen bieden geavanceerde query's en meer verfijning. Query's voor logboekwaarschuwingen vergelijken een datumtijd met het heden door de *operator nu* te gebruiken en een uur terug te gaan. (Azure Monitor voor containers slaat alle datums op in de UTC-indeling (Coordinated Universal Time).)

Zie Overzicht van waarschuwingen in Microsoft Azure voordat u begint een [overzicht van waarschuwingen in Microsoft Azure.](../platform/alerts-overview.md) Zie [Logboekwaarschuwingen in Azure Monitor](../platform/alerts-unified-log.md)voor meer informatie over waarschuwingen die logboekquery's gebruiken. Zie Metrische waarschuwingen in Azure Monitor voor meer informatie over metrische [waarschuwingen.](../platform/alerts-metric-overview.md)

## <a name="resource-utilization-log-search-queries"></a>Query's voor resourcegebruiklogboekquery's

De query's in deze sectie ondersteunen elk waarschuwingsscenario. Ze worden gebruikt in stap 7 van het gedeelte [Waarschuwing maken](#create-an-alert-rule) van dit artikel.

De volgende query berekent het gemiddelde CPU-gebruik als een gemiddelde van het CPU-gebruik van ledenknooppunten elke minuut.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

De volgende query berekent het gemiddelde geheugengebruik als een gemiddelde van het geheugengebruik van ledenknooppunten elke minuut.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>De volgende query's gebruiken \<de tijdelijke aanduidingwaarden \<uw clusternaam> en de naam van uw controller> om uw cluster en controller weer te geven. Vervang ze door waarden die specifiek zijn voor uw omgeving wanneer u waarschuwingen instelt.

De volgende query berekent het gemiddelde CPU-gebruik van alle containers in een controller als een gemiddelde van het CPU-gebruik van elke containerinstantie in een controller elke minuut. De meting is een percentage van de limiet die is ingesteld voor een container.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

De volgende query berekent het gemiddelde geheugengebruik van alle containers in een controller als een gemiddelde van het geheugengebruik van elke containerinstantie in een controller elke minuut. De meting is een percentage van de limiet die is ingesteld voor een container.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Met de volgende query worden alle knooppunten en tellingen geretourneerd die de status *Ready* en *NotReady*hebben.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
De volgende query retourneert podfase telt op basis van alle fasen: *Mislukt*, *In behandeling*, *Onbekend*, *Lopend*of *Geslaagd*.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Als u wilt waarschuwen voor bepaalde podfasen, zoals *In behandeling,* *Mislukt*of *Onbekend,* wijzigt u de laatste regel van de query. Bijvoorbeeld om te waarschuwen bij het gebruik *van FailedCount:* <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

Met de volgende query worden clusterknooppuntenschijven geretourneerd die meer dan 90% vrije ruimte hebben. Als u de cluster-id wilt ophalen, voert `ClusterId` u eerst de volgende query uit en kopieert u de waarde van de eigenschap:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Een waarschuwingsregel maken

Volg deze stappen om een logboekwaarschuwing in Azure Monitor te maken met behulp van een van de eerder opgegeven logboekzoekregels. Zie [Voorbeeldlogboekwaarschuwing maken met Azure Resource Template](../platform/alerts-log.md#sample-log-alert-creation-using-azure-resource-template)als u wilt maken met behulp van een ARM-sjabloon.

>[!NOTE]
>De volgende procedure voor het maken van een waarschuwingsregel voor het gebruik van containerresources vereist dat u overschakelt naar een nieuwe API voor logboekwaarschuwingen zoals beschreven in [de Switch API-voorkeur voor logboekwaarschuwingen.](../platform/alerts-log-api-switch.md)
>

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zoek en selecteer in de Azure-portal **log Analytics-werkruimten**en selecteer deze.
3. Selecteer in uw lijst met Log Analytics-werkruimten de werkruimte die Azure Monitor voor containers ondersteunt. 
4. Selecteer **Logboeken** om de pagina Azure Monitor-logboeken te openen in het deelvenster aan de linkerkant. U gebruikt deze pagina om Azure Log Analytics-query's te schrijven en uit te voeren.
5. Plak op de pagina **Logboeken** een van de eerder verstrekte [query's](#resource-utilization-log-search-queries) in het **veld Zoeken en** selecteer **Uitvoeren** om de resultaten te valideren. Als u deze stap niet uitvoert, is de optie **+Nieuw-waarschuwing** niet beschikbaar om te selecteren.
6. Selecteer **+Nieuwe waarschuwing** om een logboekwaarschuwing te maken.
7. Selecteer **in** de sectie Voorwaarde de **optie \<Wanneer de aangepaste logboekzoekopdracht logica is, niet gedefinieerd>** vooraf gedefinieerde aangepaste logboekvoorwaarde. Het **aangepaste zoeksignaaltype voor logboeken** wordt automatisch geselecteerd omdat we rechtstreeks vanaf de pagina Azure Monitor-logboeken een waarschuwingsregel maken.  
8. Plak een van de eerder opgegeven [query's](#resource-utilization-log-search-queries) in het **veld Zoeken.**
9. Configureer de waarschuwing als volgt:

    1. Selecteer **metrische meting**in de vervolgkeuzelijst **Op basis van** . Met een metrische meting wordt een waarschuwing gemaakt voor elk object in de query met een waarde boven onze opgegeven drempelwaarde.
    1. Selecteer **Bij Voorwaarde**Groter **dan**, en voer **75** in als een eerste **basislijndrempel** voor de waarschuwingen voor het cpu- en geheugengebruik. Voer **90**in voor de waarschuwing voor weinig schijfruimte . Of voer een andere waarde in die aan uw criteria voldoet.
    1. Selecteer **opeenvolgende inbreuken**in de sectie **Trigger Alert Based On** . Selecteer **groter dan**in de vervolgkeuzelijst en voer **2**in .
    1. Als u een waarschuwing voor container-CPU of geheugengebruik wilt configureren, selecteert u **ContainerName**onder **Samenvoegen op**, Als u wilt configureren voor waarschuwing voor een lage schijf van een clusterknooppunt , selecteert u **Clusterid**.
    1. Stel **in de sectie Geëvalueerd op basis van** sectie de **periodewaarde** in op **60 minuten**. De regel wordt elke 5 minuten uitgevoerd en retourrecords die in het laatste uur vanaf de huidige tijd zijn gemaakt. Het instellen van de periode op een breed venster zorgt voor mogelijke gegevenslatentie. Het zorgt er ook voor dat de query gegevens retourneert om een vals negatief te voorkomen waarbij de waarschuwing nooit wordt geactiveerd.

10. Selecteer **Gereed** om de waarschuwingsregel te voltooien.
11. Voer een naam in het veld Naam van de **regel waarschuwen** in. Geef een **beschrijving** op met details over de waarschuwing. En selecteer een geschikt ernstniveau in de opgegeven opties.
12. Als u de waarschuwingsregel onmiddellijk wilt activeren, accepteert u de standaardwaarde voor **Inschakelen-regel bij het maken**.
13. Selecteer een bestaande **actiegroep** of maak een nieuwe groep. Deze stap zorgt ervoor dat dezelfde acties worden uitgevoerd elke keer dat een waarschuwing wordt geactiveerd. Configureer op basis van hoe uw IT- of DevOps-operationele team incidenten beheert.
14. Selecteer **Waarschuwingsregel maken** om de waarschuwingsregel te voltooien. Deze wordt onmiddellijk uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

- Voorbeelden [van logboekquery's](container-insights-log-search.md#search-logs-to-analyze-data) weergeven om vooraf gedefinieerde query's en voorbeelden te bekijken die u wilt evalueren of aanpassen voor het waarschuwen, visualiseren of analyseren van uw clusters.
- Zie [Kubernetes-clusterprestaties bekijken](container-insights-analyze.md) en [Kubernetes-clusterstatus weergeven](container-insights-health.md)voor meer informatie over Azure Monitor en hoe u andere aspecten van uw Kubernetes-cluster controleren.
