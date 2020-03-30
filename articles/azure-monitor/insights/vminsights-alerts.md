---
title: Waarschuwingen van Azure Monitor voor VM's
description: Beschrijft hoe u waarschuwingsregels maakt op basis van prestatiegegevens die zijn verzameld door Azure Monitor voor VM's.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: 987537d8497b3d8f2728941334d8328320ec6997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289600"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>Waarschuwingen maken vanuit Azure Monitor voor VM's
[Waarschuwingen in Azure Monitor](../platform/alerts-overview.md) stellen u proactief op de hoogte van interessante gegevens en patronen in uw bewakingsgegevens. Azure Monitor voor VM's bevat geen vooraf geconfigureerde waarschuwingsregels, maar u uw eigen regels maken op basis van gegevens die worden gegenereerd. In dit artikel vindt u richtlijnen voor het maken van waarschuwingsregels, waaronder een reeks voorbeeldquery's.


## <a name="alert-rule-types"></a>Waarschuwingsregeltypen
Azure Monitor heeft [verschillende typen waarschuwingsregels](../platform/alerts-overview.md#what-you-can-alert-on) op basis van de gegevens die worden gebruikt om de waarschuwing te maken. Alle gegevens die door Azure Monitor voor VM's worden verzameld, worden opgeslagen in Azure Monitor-logboeken die [logboekwaarschuwingen](../platform/alerts-log.md)ondersteunen. U momenteel geen [metrische waarschuwingen](../platform/alerts-log.md) gebruiken met prestatiegegevens die zijn verzameld uit Azure Monitor voor VM's, omdat de gegevens niet worden verzameld in Azure Monitor Metrics. Als u gegevens wilt verzamelen voor metrische waarschuwingen, installeert u de [diagnostische extensie](../platform/diagnostics-extension-overview.md) voor Windows VM's of de [Telegraf-agent](../platform/collect-custom-metrics-linux-telegraf.md) voor Linux-VM's om prestatiegegevens te verzamelen in Metrische gegevens.

Er zijn twee typen logboekwaarschuwingen in Azure Monitor:

- [Het aantal resultatenwaarschuwingen](../platform/alerts-unified-log.md#number-of-results-alert-rules) maakt één waarschuwing wanneer een query ten minste een opgegeven aantal records retourneert. Deze zijn ideaal voor niet-numerieke gegevens zoals Windows- en Syslog-gebeurtenissen die zijn verzameld door de [log-analyseagent](../platform/log-analytics-agent.md) of voor het analyseren van prestatietrends op meerdere computers.
- [Metrische metingswaarschuwingen](../platform/alerts-unified-log.md#metric-measurement-alert-rules) maken een afzonderlijke waarschuwing voor elke record in een query met een waarde die een drempelwaarde overschrijdt die is gedefinieerd in de waarschuwingsregel. Deze waarschuwingsregels zijn ideaal voor prestatiegegevens die door Azure Monitor voor VM's worden verzameld, omdat ze voor elke computer afzonderlijke waarschuwingen kunnen maken.


## <a name="alert-rule-walkthrough"></a>Waarschuwingsregel-walkthrough
In deze sectie wordt een metrische meetwaarschuwingsregel gemaakt met prestatiegegevens van Azure Monitor voor VM's. U dit basisproces gebruiken met verschillende logboekquery's om te waarschuwen op verschillende prestatiemeteritems.

Begin met het maken van een nieuwe waarschuwingsregel volgens de procedure in [Logboekwaarschuwingen maken, weergeven en beheren met Azure Monitor](../platform/alerts-log.md). Selecteer voor de **bron**de werkruimte Log Analytics die Azure Monitor VM's in uw abonnement gebruikt. Aangezien de doelbron voor logboekwaarschuwingsregels altijd een Log Analytics-werkruimte is, moet de logboekquery elk filter bevatten voor bepaalde virtuele machines of virtuele machineschaalsets. 

Gebruik **voor** de voorwaarde van de waarschuwingsregel een van de query's in de [sectie hieronder](#sample-alert-queries) als **de zoekopdracht .** De query moet een numerieke eigenschap met de naam *AggregatedValue*retourneren. Het moet een samenvatting van de gegevens per computer, zodat u een aparte waarschuwing voor elke virtuele machine die de drempel overschrijdt maken.

Selecteer in de **logica Waarschuwing** **metrische meting** en geef vervolgens een **drempelwaarde**op . Geef **in Trigger Alert Based On**op hoe vaak de drempelwaarde moet worden overschreden voordat er een waarschuwing wordt gemaakt. Het kan u bijvoorbeeld waarschijnlijk niet schelen of de processor een keer een drempel overschrijdt en vervolgens weer normaal wordt, maar het kan u wel schelen of deze de drempel over meerdere opeenvolgende metingen blijft overschrijden.

In **de sectie Geëvalueerd** wordt gedefinieerd hoe vaak de query wordt uitgevoerd en hoe vaak de query wordt uitgevoerd en hoe lang het venster voor de query is. In het onderstaande voorbeeld wordt de query elke 15 minuten uitgevoerd en worden prestatiewaarden geëvalueerd die in de afgelopen 15 minuten zijn verzameld.


![Waarschuwingsregel met metrische meting](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Waarschuwingsquery's van een voorbeeld
De volgende query's kunnen worden gebruikt met een metrische meetwaarschuwingsregel met prestatiegegevens die zijn verzameld door Azure Monitor voor VM's. Elk vat gegevens per computer samen, zodat er voor elke computer een waarschuwing wordt gemaakt met een waarde die de drempelwaarde overschrijdt.

### <a name="cpu-utilization"></a>CPU-gebruik

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Beschikbaar geheugen in MB

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Beschikbaar geheugen in percentage

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Logische schijf gebruikt - alle schijven op elke computer

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Gebruikte logische schijf - afzonderlijke schijven

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>Logische schijf IOPS

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Logische schijfgegevenssnelheid

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Ontvangen bytes voor netwerkinterfaces - alle interfaces

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Ontvangen bytes netwerkinterfaces - afzonderlijke interfaces

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Netwerkinterfaces bytes verzonden - alle interfaces

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Verzonden bytes voor netwerkinterfaces - afzonderlijke interfaces

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Schaalset voor virtuele machines
Wijzigen met de naam van uw abonnements-id, resourcegroep en de naam van de virtuele machineschaalset.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Specifieke virtuele machine
Wijzigen met uw abonnements-ID, resourcegroep en VM-naam.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>CPU-gebruik voor alle rekenbronnen in een abonnement
Wijzigen met uw abonnements-ID.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>CPU-gebruik voor alle rekenbronnen in een resourcegroep
Wijzigen met uw abonnements-ID en resourcegroep.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [waarschuwingen in Azure Monitor](../platform/alerts-overview.md).
- Meer informatie over [logboekquery's met gegevens uit Azure Monitor voor VM's](vminsights-log-search.md).
