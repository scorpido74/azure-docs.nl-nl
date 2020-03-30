---
title: Containerexemplaren controleren
description: Het verbruik van rekenresources zoals CPU en geheugen door uw containers in Azure Container Instances controleren.
ms.topic: article
ms.date: 04/24/2019
ms.openlocfilehash: b4a66254c18d7e01b6d56e64e6b62721b620d499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250037"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Containerresources in Azure Container Instances bewaken

[Azure Monitor][azure-monitoring] biedt inzicht in de rekenbronnen die worden gebruikt door uw containers-exemplaren. Met deze gegevens over het gebruik van resources u de beste resourceinstellingen voor uw containergroepen bepalen. Azure Monitor biedt ook statistieken die netwerkactiviteit in uw containerexemplaren bijhouden.

In dit document worden Azure Monitor-statistieken voor containerexemplaren verzameld met behulp van zowel de Azure-portal als Azure CLI.

> [!IMPORTANT]
> Azure Monitor-statistieken in Azure Container Instances zijn momenteel in preview en er gelden enkele [beperkingen](#preview-limitations). Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="preview-limitations"></a>Preview-beperkingen

Op dit moment zijn Azure Monitor-statistieken alleen beschikbaar voor Linux-containers.

## <a name="available-metrics"></a>Beschikbare metrische gegevens

Azure Monitor biedt de volgende [statistieken voor Azure Container Instances][supported-metrics]. Deze statistieken zijn beschikbaar voor een containergroep en afzonderlijke containers.

* **CPU-gebruik** - gemeten in **millicores**. Een millicore is 1/1000e van een CPU-kern, dus 500 millicores (of 500 m) vertegenwoordigt 50% gebruik van een CPU-kern. Geaggregeerd als **gemiddeld gebruik** in alle kernen.

* **Geheugengebruik** - geaggregeerd als **gemiddelde bytes**.

* **Netwerkbytes per seconde ontvangen** en **netwerkbytes per seconde** - geaggregeerd als gemiddelde bytes per **seconde**. 

## <a name="get-metrics---azure-portal"></a>Metrische gegevens ophalen - Azure Portal

Wanneer een containergroep wordt gemaakt, zijn Azure Monitor-gegevens beschikbaar op de Azure Portal. Als u statistieken voor een containergroep wilt weergeven, gaat u naar de pagina **Overzicht** voor de containergroep. Hier u vooraf gemaakte grafieken voor elk van de beschikbare statistieken bekijken.

![dubbele grafiek][dual-chart]

Gebruik in een containergroep die meerdere containers bevat een [dimensie][monitor-dimension] om statistieken per container weer te geven. Als u een grafiek wilt maken met metrische gegevens voor elke afzonderlijke container, voert u de volgende stappen uit:

1. Selecteer **op** de pagina Overzicht een van de metrische grafieken, zoals **CPU.** 
1. Selecteer de knop **Splitsen toepassen** en selecteer **Containernaam**.

![dimensie][dimension]

## <a name="get-metrics---azure-cli"></a>Metrische gegevens ophalen - Azure CLI

Statistieken voor containerexemplaren kunnen ook worden verzameld met behulp van de Azure CLI. Haal eerst de id van de containergroep op met de volgende opdracht. Vervang `<resource-group>` door de naam van uw resourcegroep en `<container-group>` door de naam van uw containergroep.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Gebruik de volgende opdracht om metrische gegevens over **CPU-gebruik** op te halen.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table
```

```output
Timestamp            Name       Average
-------------------  ---------  ---------
2019-04-23 22:59:00  CPU Usage
2019-04-23 23:00:00  CPU Usage
2019-04-23 23:01:00  CPU Usage  0.0
2019-04-23 23:02:00  CPU Usage  0.0
2019-04-23 23:03:00  CPU Usage  0.5
2019-04-23 23:04:00  CPU Usage  0.5
2019-04-23 23:05:00  CPU Usage  0.5
2019-04-23 23:06:00  CPU Usage  1.0
2019-04-23 23:07:00  CPU Usage  0.5
2019-04-23 23:08:00  CPU Usage  0.5
2019-04-23 23:09:00  CPU Usage  1.0
2019-04-23 23:10:00  CPU Usage  0.5
```

Wijzig de waarde `--metric` van de parameter in de opdracht om andere [ondersteunde statistieken][supported-metrics]te krijgen. Gebruik bijvoorbeeld de volgende opdracht om statistieken **voor geheugengebruik** op te halen. 

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table
```

```output
Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

Voor een groep met `containerName` meerdere containers kan de dimensie worden toegevoegd aan retourstatistieken per container.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table
```

```output
Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Monitoring is te vinden in het [Azure Monitoring-overzicht][azure-monitoring].

Meer informatie over het maken [van metrische waarschuwingen][metric-alert] om een melding te ontvangen wanneer een statistiek voor Azure Container Instances een drempelwaarde overschrijdt.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/platform/alerts-metric.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups
