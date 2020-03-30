---
title: (AFGESCHAFT) Azure Kubernetes-cluster bewaken met Datadog
description: Kubernetes-cluster in Azure Container Service bewaken met Datadog
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 1f3f70c30ab397bd549a2f3305a738274ee4f64f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371168"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>(AFGESCHAFT) Een Azure Container Service-cluster bewaken met DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Vereisten
Deze walkthrough gaat ervan uit dat u [een Kubernetes-cluster](container-service-kubernetes-walkthrough.md)hebt gemaakt met Azure Container Service.

Het gaat er ook `az` van uit `kubectl` dat u de Azure cli en tools hebt geïnstalleerd.

U testen of `az` u het gereedschap hebt geïnstalleerd door het uitvoeren van:

```azurecli
az --version
```

Als u het `az` gereedschap niet hebt geïnstalleerd, zijn er [hier](https://github.com/azure/azure-cli#installation)instructies.

U testen of `kubectl` u het gereedschap hebt geïnstalleerd door het uitvoeren van:

```console
kubectl version
```

Als u niet `kubectl` hebt geïnstalleerd, u het:

```azurecli
az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog (DataDog)
Datadog is een bewakingsservice die bewakingsgegevens uit uw containers verzamelt binnen uw Azure Container Service-cluster. Datadog heeft een Docker Integration Dashboard waar u specifieke statistieken in uw containers zien. Statistieken verzameld uit uw containers worden georganiseerd door CPU, Geheugen, Netwerk en I/O. Datadog splitst statistieken op in containers en afbeeldingen.

Je moet eerst [een account aanmaken](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>De Datadog Agent installeren met een DaemonSet
DaemonSets worden door Kubernetes gebruikt om één exemplaar van een container op elke host in het cluster uit te voeren.
Ze zijn perfect voor het runnen van bewakingsagenten.

Zodra u zich hebt aangemeld bij Datadog, u de [Datadog-instructies](https://app.datadoghq.com/account/settings#agent/kubernetes) volgen om Datadog-agents op uw cluster te installeren met behulp van een DaemonSet.

## <a name="conclusion"></a>Conclusie
Dat is alles. Zodra de agents operationeel zijn, ziet u binnen enkele minuten gegevens in de console. U het geïntegreerde [kubernetes-dashboard](https://app.datadoghq.com/screen/integration/kubernetes) bezoeken om een samenvatting van uw cluster te bekijken.
