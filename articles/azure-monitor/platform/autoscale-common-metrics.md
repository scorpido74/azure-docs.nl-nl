---
title: Algemene statistieken automatisch schalen
description: Ontdek welke statistieken vaak worden gebruikt voor het automatisch schalen van uw Cloud Services, Virtual Machines en Web Apps.
ms.topic: conceptual
ms.date: 12/6/2016
ms.subservice: autoscale
ms.openlocfilehash: 2c335168683212337876c963a7cfdb441d0ac69a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845575"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Algemene statistieken voor automatisch schalen van Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Met Azure Monitor-automatisch schalen u het aantal lopende exemplaren omhoog of omlaag schalen op basis van telemetriegegevens (metrische gegevens). In dit document worden algemene statistieken beschreven die u mogelijk wilt gebruiken. In de Azure-portal u de statistiek kiezen van de resource waarop u wilt schalen. U echter ook een statistiek kiezen uit een andere resource om op te schalen.

Azure Monitor autoscale is alleen van toepassing op [Virtual Machine Scale Sets,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [Cloud Services](https://azure.microsoft.com/services/cloud-services/), App Service - [Web Apps](https://azure.microsoft.com/services/app-service/web/)en API [Management services](https://docs.microsoft.com/azure/api-management/api-management-key-concepts). Andere Azure-services gebruiken verschillende schalingsmethoden.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Compute metrics for Resource Manager-based VM's
Standaard zenden op Resource Manager gebaseerde virtuele machines en virtuele machineschaalsets basisstatistieken (hostniveau) uit. Wanneer u het verzamelen van diagnostische gegevens configureert voor een Azure VM en VMSS, zendt de diagnostische extensie azure bovendien ook prestatiemeteritems voor gasten-be uit (beter bekend als 'guest-OS metrics').  U gebruikt al deze statistieken in autoscale regels.

U `Get MetricDefinitions` de API/PoSH/CLI gebruiken om de statistieken weer te geven die beschikbaar zijn voor uw VMSS-bron.

Als u VM-schaalsets gebruikt en u ziet geen bepaalde metrische lijst, wordt deze waarschijnlijk *uitgeschakeld* in uw diagnostische extensie.

Als een bepaalde statistiek niet wordt bemonsterd of overgedragen op de gewenste frequentie, u de configuratie van de diagnose bijwerken.

Als een van de voorgaande gevallen waar is, controleert u [PowerShell gebruiken om Azure Diagnostics in te schakelen in een virtuele machine met Windows](../../virtual-machines/extensions/diagnostics-windows.md) over PowerShell om uw Azure VM Diagnostics-extensie te configureren en bij te werken om de statistiek in te schakelen. Dat artikel bevat ook een voorbeeld diagnostische configuratiebestand.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Host metrics for Resource Manager-based Windows and Linux VM's Host metrics for Resource Manager-based Windows and Linux VM's Host metrics for Resource Manager-based Windows and Linux VM'
De volgende statistieken op hostniveau worden standaard voor Azure VM en VMSS uitgestraald in zowel Windows- als Linux-exemplaren. Deze statistieken beschrijven uw Azure VM, maar worden verzameld vanuit de Azure VM-host in plaats van via agent die is geïnstalleerd op de gast-VM. U deze statistieken gebruiken in regels voor automatisch schalen.

- [Host metrics for Resource Manager-based Windows and Linux VM's Host metrics for Resource Manager-based Windows and Linux VM's Host metrics for Resource Manager-based Windows and Linux VM'](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [Hostmetrics voor Windows- en Linux VM-schaalsets op basis van Resource Manager](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-for-resource-manager-based-windows-vms"></a>Gaststatistieken voor Windows VM's op basis van Resource Manager
Wanneer u een VM maakt in Azure, wordt diagnostische gegevens ingeschakeld met de extensie Diagnostische gegevens. De diagnostische extensie zendt een reeks metrische gegevens uit die vanuit de VM zijn genomen. Dit betekent dat u automatisch afschalen van statistieken die niet standaard worden uitgezonden.

U een lijst met statistieken genereren met de volgende opdracht in PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

U een waarschuwing maken voor de volgende statistieken:

| Metrische naam | Eenheid |
| --- | --- |
| \Processor(_Total)\% Processor Time |Percentage |
| \Processor(_Total)\% bevoorrechte tijd |Percentage |
| \Processor(_Total)\% gebruikerstijd |Percentage |
| \Processorinformatie(_Total)\Processorfrequentie |Count |
| \Systeem\Processen |Count |
| \Proces(_Total)\Aantal thread's |Count |
| \Proces(_Total)\Aantal afhandelen |Count |
| \Memory\% Committed Bytes In Use \Memory Committed Bytes In Use \Memory Committed Bytes In Use \Memory |Percentage |
| \Memory\Available Bytes |Bytes |
| \Geheugen\Vastgelegde bytes |Bytes |
| \Memory\Commit Limit \Memory\Commit Limit \Memory\Commit Limit \Memory |Bytes |
| \Geheugen\Paginabytes bundelen |Bytes |
| \Geheugen\Niet-pagina's bundelen |Bytes |
| \PhysicalDisk(_Total)\% Schijftijd |Percentage |
| \PhysicalDisk(_Total)\% Schijfleestijd |Percentage |
| \PhysicalDisk(_Total)\% Schijfschrijftijd |Percentage |
| \PhysicalDisk(_Total)\Schijfoverdrachten per seconde |CountPerSeconde |
| \PhysicalDisk(_Total)\Schijfleest per seconde |CountPerSeconde |
| \PhysicalDisk(_Total)\Schijfschrijft per seconde |CountPerSeconde |
| \PhysicalDisk(_Total)\Schijfbytes per seconde |BytesPerSeconde |
| \PhysicalDisk(_Total)\Bytes per seconde voor schijflezen |BytesPerSeconde |
| \PhysicalDisk(_Total)\Bytes voor schijfschrijfschrijfberichten per seconde |BytesPerSeconde |
| \PhysicalDisk(_Total)\Avg. Schijfwachtrijlengte |Count |
| \PhysicalDisk(_Total)\Avg. Wachtrijlengte voor schijflezen |Count |
| \PhysicalDisk(_Total)\Avg. Schijfschrijfwachtrijlengte |Count |
| \LogicalDisk(_Total)\% Vrije ruimte |Percentage |
| \LogicalDisk(_Total)\Gratis Megabytes |Count |

### <a name="guest-os-metrics-linux-vms"></a>Guest OS metrics Linux VM's
Wanneer u een vm maakt in Azure, wordt diagnostische gegevens standaard ingeschakeld met de extensie Diagnostische gegevens.

U een lijst met statistieken genereren met de volgende opdracht in PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 U een waarschuwing maken voor de volgende statistieken:

| Metrische naam | Eenheid |
| --- | --- |
| \Geheugen\AvailableMemory \Memory\Memory \Memory\Memory\Memory\Memory |Bytes |
| \Memory\PercentAvailableMemory \Memory \Memory\Memory \Memory\Memory\Memory\ |Percentage |
| \Memory\UsedMemory \Memory\Memory \Memory\Memory\Memory\Memory |Bytes |
| \Memory\PercentUsedMemory \Memory\PercentusedMemory \Memory\ |Percentage |
| \Memory\Percentusedbycache \Memory\PercentusedbyCache \Memory\PercentusedbyCache \Memory |Percentage |
| \Memory\Pagespersec |CountPerSeconde |
| \Geheugen\PagesReadPersec |CountPerSeconde |
| \Memory\PagesWrittenPersec |CountPerSeconde |
| \Geheugen\AvailableSwap |Bytes |
| \Memory\PercentAvailableSwap \Memory\PercentAvailableSwap \Memory\PercentAvailableSwap \Memory |Percentage |
| \Memory\UsedSwap \Memory\UsedSwap \Memory\UsedSwap \Memory |Bytes |
| \Memory\PercentUsedSwap \Memory\PercentUsedSwap \Memory\PercentUsedSwap \Memory |Percentage |
| \Processor\PercentidleTime |Percentage |
| \Processor\PercentuserTime |Percentage |
| \Processor\PercentNiceTime |Percentage |
| \Processor\PercentPrivilegedTime |Percentage |
| \Processor\PercentinterruptTime |Percentage |
| \Processor\PercentdPCTime |Percentage |
| \Processor\Percentprocessortijd |Percentage |
| \Processor\PercentIOWaitTime |Percentage |
| \PhysicalDisk\BytesPerSeconde |BytesPerSeconde |
| \PhysicalDisk\ReadBytesPerSeconde |BytesPerSeconde |
| \PhysicalDisk\WriteBytesPerSeconde |BytesPerSeconde |
| \PhysicalDisk\TransfersPerSeconde |CountPerSeconde |
| \PhysicalDisk\ReadsPerSeconde |CountPerSeconde |
| \PhysicalDisk\WritesperSeconde |CountPerSeconde |
| \PhysicalDisk\AverageReadTime \PhysicalDisk\AverageReadTime \PhysicalDisk\AverageReadTime \Physical |Seconden |
| \PhysicalDisk\AverageWriteTime \PhysicalDisk\AverageWriteTime \PhysicalDisk\AverageWriteTime \Physical |Seconden |
| \PhysicalDisk\AveragetransferTime \PhysicalDisk\Averagetransfertime \PhysicalDisk\Averagetransfertime \Physical |Seconden |
| \PhysicalDisk\AverageDiskQueuelength \PhysicalDisk\AverageDiskQueuelength \PhysicalDisk\AverageDiskQueuelength \Physical |Count |
| \NetworkInterface\BytesVerzonden |Bytes |
| \NetworkInterface\BytesReceived \NetworkInterface\BytesReceived \NetworkInterface\BytesReceived \Network |Bytes |
| \NetworkInterface\Pakketten verzonden |Count |
| \NetworkInterface\PakkettenOntvangen |Count |
| \NetworkInterface\BytesTotaal |Bytes |
| \NetworkInterface\TotalRxErrors \NetworkInterface\TotalRxErrors \NetworkInterface\TotalRxErrors \Network |Count |
| \NetworkInterface\TotalTxErrors \NetworkInterface\TotalTxErrors \NetworkInterface\TotalTxErrors \Network |Count |
| \NetworkInterface\TotalCollisions \NetworkInterface\TotalCollisions \NetworkInterface\TotalCollisions \Network |Count |

## <a name="commonly-used-app-service-server-farm-metrics"></a>Veelgebruikte Server Service-statistieken (Server Farm)
U ook automatisch schalen op basis van algemene webserverstatistieken, zoals de http-wachtrijlengte. De metrische naam is **HttpQueueLength**.  In de volgende sectie worden beschikbare serverfarmstatistieken (App Service) weergegeven.

### <a name="web-apps-metrics"></a>Statistieken voor Web Apps
U een lijst met de statistieken van Web Apps genereren met de volgende opdracht in PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

U op of op schaal waarschuwen door deze statistieken.

| Metrische naam | Eenheid |
| --- | --- |
| CpuPercentage |Percentage |
| GeheugenPercentage |Percentage |
| DiskQueueLengte |Count |
| HttpQueuelength |Count |
| Ontvangen bytes |Bytes |
| BytesSent |Bytes |

## <a name="commonly-used-storage-metrics"></a>Veelgebruikte opslagstatistieken
U schalen op de lengte van de wachtrij opslag, het aantal berichten in de opslagwachtrij. De lengte van de opslagwachtrij is een speciale statistiek en de drempelwaarde is het aantal berichten per instantie. Als er bijvoorbeeld twee instanties zijn en als de drempelwaarde is ingesteld op 100, vindt schalen plaats wanneer het totale aantal berichten in de wachtrij 200 is. Dat kan 100 berichten per instantie, 120 en 80, of een andere combinatie die optelt tot 200 of meer.

Configureer deze instelling in de Azure-portal in het blad **Instellingen.** Voor VM-schaalsets u de instelling Autoscale in de sjabloon Resourcebeheer bijwerken om *metricName* te gebruiken als *ApproximateMessageCount* en de ID van de opslagwachtrij als *metricResourceUri*door te geven.

Met een Klassiek opslagaccount omvat de instelling voor automatische schaalinstelling metricTrigger bijvoorbeeld:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Voor een (niet-klassiek) opslagaccount bevat de metricTrigger:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Veelgebruikte Service Bus-statistieken
U schalen op de wachtrijlengte van servicebus, het aantal berichten in de wachtrij servicebus. De wachtrijlengte van de servicebus is een speciale statistiek en de drempelwaarde is het aantal berichten per instantie. Als er bijvoorbeeld twee instanties zijn en als de drempelwaarde is ingesteld op 100, vindt schalen plaats wanneer het totale aantal berichten in de wachtrij 200 is. Dat kan 100 berichten per instantie, 120 en 80, of een andere combinatie die optelt tot 200 of meer.

Voor VM-schaalsets u de instelling Autoscale in de sjabloon Resourcebeheer bijwerken om *metricName* te gebruiken als *ApproximateMessageCount* en de ID van de opslagwachtrij als *metricResourceUri*door te geven.

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Voor Servicebus bestaat het concept van de brongroep niet, maar Azure Resource Manager maakt een standaardbrongroep per regio. De resourcegroep bevindt zich meestal in de indeling 'StandaardserviceBus-[regio]'. Bijvoorbeeld 'Default-ServiceBus-EastUS', 'Default-ServiceBus-WestUS', 'Default-ServiceBus-AustraliaEast' etc.
>
>

