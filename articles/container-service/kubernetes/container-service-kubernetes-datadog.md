---
title: KEUR Azure Kubernetes-cluster bewaken met Datadog
description: Kubernetes-cluster bewaken in Azure Container Service met behulp van Datadog
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 1f3f70c30ab397bd549a2f3305a738274ee4f64f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371168"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>KEUR Een Azure Container Service cluster bewaken met DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Vereisten
In dit scenario wordt ervan uitgegaan dat u [een Kubernetes-cluster hebt gemaakt met behulp van Azure container service](container-service-kubernetes-walkthrough.md).

Er wordt ook van uitgegaan dat u `az` de Azure cli `kubectl` en hulpprogram ma's hebt geïnstalleerd.

U kunt testen of u het hulp `az` programma hebt geïnstalleerd door het volgende uit te voeren:

```azurecli
az --version
```

Als u het `az` hulp programma niet hebt geïnstalleerd, vindt u [hier](https://github.com/azure/azure-cli#installation)instructies.

U kunt testen of u het hulp `kubectl` programma hebt geïnstalleerd door het volgende uit te voeren:

```console
kubectl version
```

Als u niet hebt `kubectl` geïnstalleerd, kunt u het volgende uitvoeren:

```azurecli
az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Datadog is een bewakings service waarmee bewakings gegevens worden verzameld van uw containers in uw Azure Container Service cluster. Datadog heeft een docker-integratie dashboard waarin u specifieke metrische gegevens in uw containers kunt bekijken. De metrische gegevens die uit uw containers zijn verzameld, zijn ingedeeld op basis van CPU, geheugen, netwerk en I/O. Datadog splitst metrische gegevens in containers en afbeeldingen.

U moet eerst [een account maken](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>De Datadog-agent installeren met een Daemonset
DaemonSets worden door Kubernetes gebruikt om één exemplaar van een container op elke host in het cluster uit te voeren.
Ze zijn perfect voor het uitvoeren van bewakings agenten.

Zodra u bent aangemeld bij Datadog, kunt u de [Datadog-instructies](https://app.datadoghq.com/account/settings#agent/kubernetes) volgen om Datadog-agents op uw cluster te installeren met behulp van een daemonset.

## <a name="conclusion"></a>Conclusie
Dat is alles. Zodra de agents actief zijn, moet u binnen enkele minuten gegevens in de-console zien. U kunt het [dash board Integrated kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) bezoeken om een samen vatting van uw cluster weer te geven.
