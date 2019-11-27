---
title: Container instanties bewaken
description: Het gebruik van reken resources zoals CPU en geheugen door uw containers in Azure Container Instances bewaken.
ms.topic: article
ms.date: 04/24/2019
ms.openlocfilehash: bd86161bc7840be599eb5ee9a20f6dbf143f5f22
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533651"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Containerresources in Azure Container Instances bewaken

[Azure monitor][azure-monitoring] biedt inzicht in de reken resources die worden gebruikt door de instanties van uw containers. Deze gegevens over resource gebruik helpen u bij het bepalen van de beste bron instellingen voor uw container groepen. Azure Monitor biedt ook metrische gegevens waarmee netwerk activiteiten in uw container instanties worden bijgehouden.

Dit document bevat informatie over het verzamelen van Azure Monitor metrische gegevens voor container instanties die gebruikmaken van zowel de Azure Portal als de Azure CLI.

> [!IMPORTANT]
> Azure Monitor metrische gegevens in Azure Container Instances zijn momenteel beschikbaar als preview-versie en er zijn enkele [beperkingen van toepassing](#preview-limitations). Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functie worden mogelijk nog gewijzigd voordat de functie algemeen beschikbaar wordt.

## <a name="preview-limitations"></a>Preview-beperkingen

Op dit moment zijn Azure Monitor metrische gegevens alleen beschikbaar voor Linux-containers.

## <a name="available-metrics"></a>Beschikbare metrische gegevens

Azure Monitor biedt de volgende [metrische gegevens voor Azure container instances][supported-metrics]. Deze metrische gegevens zijn beschikbaar voor een container groep en afzonderlijke containers.

* **CPU-gebruik** : gemeten in **millicores**. Een millicore is 1/1000th van een CPU-kern, dus 500 millicores (of 500 m) staat voor 50% van het gebruik van een CPU-kern. Geaggregeerd als **gemiddeld gebruik** over alle kernen.

* **Geheugen gebruik** : samengevoegd als **gemiddeld aantal bytes**.

* **Ontvangen netwerk bytes per seconde** en **verzonden netwerk bytes per seconde** , samengevoegd als **gemiddeld aantal bytes per seconde**. 

## <a name="get-metrics---azure-portal"></a>Metrische gegevens ophalen - Azure Portal

Wanneer een containergroep wordt gemaakt, zijn Azure Monitor-gegevens beschikbaar op de Azure Portal. Als u de metrische gegevens voor een container groep wilt weer geven, gaat u naar de pagina **overzicht** voor de container groep. Hier kunt u vooraf gemaakte grafieken zien voor elk van de beschik bare metrische gegevens.

![dubbele grafiek][dual-chart]

Gebruik in een container groep die meerdere containers bevat een [dimensie][monitor-dimension] om metrische gegevens per container weer te geven. Als u een grafiek wilt maken met metrische gegevens voor elke afzonderlijke container, voert u de volgende stappen uit:

1. Selecteer op de pagina **overzicht** een van de metrische grafieken, zoals **CPU**. 
1. Selecteer de knop **splitsing Toep assen** en selecteer **container naam**.

![dimensie][dimension]

## <a name="get-metrics---azure-cli"></a>Metrische gegevens ophalen - Azure CLI

Metrische gegevens voor container instanties kunnen ook worden verzameld met behulp van de Azure CLI. Haal eerst de id van de containergroep op met de volgende opdracht. Vervang `<resource-group>` door de naam van uw resourcegroep en `<container-group>` door de naam van uw containergroep.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Gebruik de volgende opdracht om metrische gegevens over **CPU-gebruik** op te halen.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

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

Wijzig de waarde van de para meter `--metric` in de opdracht om andere [ondersteunde metrische gegevens][supported-metrics]op te halen. Gebruik bijvoorbeeld de volgende opdracht om metrische gegevens over het **geheugen** gebruik op te halen. 

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

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

Voor een groep met meerdere containers kan de dimensie `containerName` worden toegevoegd om metrische gegevens per container te retour neren.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table

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

Meer informatie over Azure-bewaking vindt u in het [overzicht van Azure-bewaking][azure-monitoring].

Meer informatie over het maken van [metrische waarschuwingen][metric-alert] om een melding te ontvangen wanneer een metriek voor Azure container instances een drempel waarde overschrijdt.

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
