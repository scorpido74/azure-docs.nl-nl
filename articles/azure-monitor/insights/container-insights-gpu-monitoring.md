---
title: GPU-bewaking configureren met Azure Monitor voor containers | Microsoft Docs
description: In dit artikel wordt beschreven hoe u bewakings Kubernetes-clusters kunt configureren met NVIDIA-en AMD GPU-knoop punten met Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80373308"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>GPU-bewaking configureren met Azure Monitor voor containers

Met ingang van agent versie *ciprod03022019*biedt Azure monitor voor containers Integrated agent nu ondersteuning voor het gebruik van GPU (grafische verwerkings eenheden) op GPU-bewuste Kubernetes-cluster knooppunten en bewaakt u voor het bewaken van en het bevragen en gebruiken van de GPU-resources.

## <a name="supported-gpu-vendors"></a>Ondersteunde GPU-leveranciers

Azure Monitor voor containers ondersteunt bewaking van GPU-clusters van de volgende GPU-leveranciers:

- [GRAFISCHE](https://developer.nvidia.com/kubernetes-gpu)

- [POWERNOW](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Azure Monitor voor containers worden het GPU-gebruik automatisch op knoop punten gestart en GPU die een Peul en werk belasting aanvraagt door de volgende metrische gegevens op 60sec intervallen te verzamelen en op te slaan in de tabel **InsightMetrics** :

|Naam van metrische gegevens |Metrische dimensie (Tags) |Beschrijving |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Het percentage tijd in de afgelopen sample periode (60 seconden) gedurende welke GPU bezig was/actief werd verwerkt voor een container. Taak cyclus is een getal tussen 1 en 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Elke container kan limieten als een of meer Gpu's opgeven. Het is niet mogelijk een fractie van een GPU aan te vragen of te beperken. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Elke container kan een of meer Gpu's aanvragen. Het is niet mogelijk een fractie van een GPU aan te vragen of te beperken.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Hoeveelheid GPU-geheugen in beschik bare bytes voor een specifieke container. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Hoeveelheid GPU-geheugen in bytes die door een bepaalde container worden gebruikt. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Aantal Gpu's in een knoop punt dat kan worden gebruikt door Kubernetes. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Het totale aantal Gpu's in een knoop punt. |

## <a name="gpu-performance-charts"></a>Grafieken GPU-prestaties 

Azure Monitor voor containers bevat vooraf geconfigureerde grafieken voor de metrische gegevens die eerder in de tabel zijn opgenomen als een GPU-werkmap voor elk cluster. U kunt de GPU-werkmap **knooppunt GPU** rechtstreeks vanuit een AKS-cluster vinden door **werkmappen** te selecteren in het linkerdeel venster en in de vervolg keuzelijst **werkmappen weer geven** in het inzicht.

## <a name="next-steps"></a>Volgende stappen

- Zie [Gpu's gebruiken voor computerintensieve werk belastingen op Azure Kubernetes service](../../aks/gpu-cluster.md) (AKS) voor meer informatie over het implementeren van een AKS-cluster met GPU-ingeschakelde knoop punten.

- Meer informatie over door [GPU geoptimaliseerde VM-sku's in Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Bekijk [GPU-ondersteuning in Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) voor meer informatie over de Kubernetes-experimentele ondersteuning voor het beheren van gpu's over een of meer knoop punten in een cluster.