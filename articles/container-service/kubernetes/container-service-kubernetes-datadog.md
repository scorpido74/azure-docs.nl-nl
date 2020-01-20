---
title: KEUR Azure Kubernetes-cluster bewaken met Datadog
description: Kubernetes-cluster bewaken in Azure Container Service met behulp van Datadog
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: c8ed146a224ec4225a7a0e85c76227fb1dc71b0b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271055"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>KEUR Een Azure Container Service cluster bewaken met DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Vereisten
In dit scenario wordt ervan uitgegaan dat u [een Kubernetes-cluster hebt gemaakt met behulp van Azure container service](container-service-kubernetes-walkthrough.md).

Er wordt ook van uitgegaan dat u de `az` Azure CLI en `kubectl`-hulpprogram ma's hebt geïnstalleerd.

U kunt testen of u het `az`-hulp programma hebt geïnstalleerd door uit te voeren:

```console
$ az --version
```

Als u het hulp programma `az` niet hebt geïnstalleerd, zijn er [hier](https://github.com/azure/azure-cli#installation)instructies.

U kunt testen of u het `kubectl`-hulp programma hebt geïnstalleerd door uit te voeren:

```console
$ kubectl version
```

Als `kubectl` niet is geïnstalleerd, kunt u het volgende uitvoeren:

```console
$ az acs kubernetes install-cli
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
