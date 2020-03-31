---
title: GPU-bewaking configureren met Azure Monitor voor containers | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Kubernetes-clusters beheren met NVIDIA- en AMD GPU-enabled nodes met Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373308"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>GPU-bewaking configureren met Azure Monitor voor containers

Vanaf de agentversie *ciprod03022019*ondersteunt Azure-monitor voor geïntegreerde containeragent nu het monitoren van GPU-gebruik (grafische verwerkingseenheden) op GPU-bewuste Kubernetes-clusterknooppunten en monitorpods/containers die GPU-bronnen aanvragen en gebruiken.

## <a name="supported-gpu-vendors"></a>Ondersteunde GPU-leveranciers

Azure Monitor for Containers ondersteunt monitoring GPU-clusters van volgende GPU-leveranciers:

- [Nvidia](https://developer.nvidia.com/kubernetes-gpu)

- [Amd](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Azure Monitor voor containers begint automatisch het GPU-gebruik op knooppunten te controleren en GPU-pods en workloads op te vragen door de volgende statistieken met intervallen van 60 sec te verzamelen en op te slaan in de tabel **InsightMetrics:**

|Naam van meetwaarde |Metrische dimensie (tags) |Beschrijving |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Percentage tijd in de afgelopen voorbeeldperiode (60 seconden) waarin GPU bezig/actief werd verwerkt/actief voor een container werd verwerkt. Duty cycle is een getal tussen 1 en 100. |
|containerGpuLimieten |container.azm.ms/clusterId, container.azm.ms/clusterName, containerNaam |Elke container kan limieten opgeven als een of meer GPU's. Het is niet mogelijk om een fractie van een GPU aan te vragen of te beperken. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerNaam |Elke container kan een of meer GPU's aanvragen. Het is niet mogelijk om een fractie van een GPU aan te vragen of te beperken.|
|containerGpu-geheugenTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Hoeveelheid GPU-geheugen in bytes die beschikbaar zijn voor een specifieke container. |
|containerGpu-geheugenUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Hoeveelheid GPU-geheugen in bytes die door een specifieke container worden gebruikt. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Aantal GPU's in een knooppunt dat door Kubernetes kan worden gebruikt. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Totaal aantal GPU's in een knooppunt. |

## <a name="gpu-performance-charts"></a>GPU-prestatiegrafieken 

Azure Monitor voor containers bevat vooraf geconfigureerde grafieken voor de statistieken die eerder in de tabel worden vermeld als GPU-werkmap voor elk cluster. U de **GPU-werkmap-gpu voor knooppunt GPU** rechtstreeks vanuit een AKS-cluster vinden door **Werkmappen** te selecteren in het linkerdeelvenster en in de vervolgkeuzelijst **Werkmappen weergeven** in het Insight.

## <a name="next-steps"></a>Volgende stappen

- Zie [GPU's gebruiken voor computerintensieve workloads op Azure Kubernetes Service](../../aks/gpu-cluster.md) (AKS) voor meer informatie over het implementeren van een AKS-cluster met GPU-knooppunten.

- Meer informatie over [GPU-geoptimaliseerde VM SKU's in Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Bekijk [GPU-ondersteuning in Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) voor meer informatie over kubernetes experimentele ondersteuning voor het beheren van GPU's op een of meer knooppunten in een cluster.