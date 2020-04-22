---
title: (AFGESCHAFT) Een Azure Kubernetes-cluster bewaken met CoScale
description: Een Kubernetes-cluster in Azure Container Service bewaken met CoScale
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f195a5c05c6c95dac898b2d471747952a3446d52
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681721"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>(AFGESCHAFT) Een Kubernetes-cluster van Azure Container Service bewaken met CoScale

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

In dit artikel laten we u zien hoe u de [CoScale-agent](https://web.archive.org/web/20180317071550/https://www.coscale.com/) implementeert om alle knooppunten en containers in uw Kubernetes-cluster in Azure Container Service te controleren. Voor deze configuratie hebt u een account bij CoScale nodig. 


## <a name="about-coscale"></a>Over CoScale 

CoScale is een monitoringplatform dat statistieken en gebeurtenissen verzamelt van alle containers in verschillende orchestration platforms. CoScale biedt full-stack monitoring voor Kubernetes-omgevingen. Het biedt visualisaties en analyses voor alle lagen in de stack: het besturingssysteem, Kubernetes, Docker en toepassingen die in uw containers worden uitgevoerd. CoScale biedt verschillende ingebouwde monitoringdashboards en heeft ingebouwde anomaliedetectie waarmee operators en ontwikkelaars snel infrastructuur- en toepassingsproblemen kunnen vinden.

![Gebruikersinterface voor coscale](./media/container-service-kubernetes-coscale/coscale.png)

Zoals in dit artikel wordt weergegeven, u agents op een Kubernetes-cluster installeren om CoScale als Een SaaS-oplossing uit te voeren. Als u uw gegevens on-site wilt houden, is CoScale ook beschikbaar voor on-premises installatie.


## <a name="prerequisites"></a>Vereisten

U moet eerst [een CoScale-account aanmaken.](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial)

Deze walkthrough gaat ervan uit dat u [een Kubernetes-cluster](container-service-kubernetes-walkthrough.md)hebt gemaakt met Azure Container Service.

Er wordt ook van `az` uitgegaan dat `kubectl` u de Azure CLI en hulpprogramma's hebt geïnstalleerd.

U testen of `az` u het gereedschap hebt geïnstalleerd door het uitvoeren van:

```azurecli
az --version
```

Als u het `az` gereedschap niet hebt geïnstalleerd, zijn er [hier](/cli/azure/install-azure-cli)instructies.

U testen of `kubectl` u het gereedschap hebt geïnstalleerd door het uitvoeren van:

```bash
kubectl version
```

Als u niet `kubectl` hebt geïnstalleerd, u het:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>De CoScale-agent installeren met een DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) worden door Kubernetes gebruikt om één exemplaar van een container op elke host in het cluster uit te voeren.
Ze zijn perfect voor het uitvoeren van bewakingsagenten, zoals de CoScale agent.

Nadat u zich hebt aangemeld bij CoScale, gaat u naar de [agentpagina](https://developer.newrelic.com/) om CoScale-agents op uw cluster te installeren met behulp van een DaemonSet. De CoScale UI biedt begeleide configuratiestappen om een agent te maken en uw volledige Kubernetes-cluster te controleren.

![CoScale-agentconfiguratie](./media/container-service-kubernetes-coscale/installation.png)

Voer de meegeleverde opdracht uit om de agent in het cluster te starten:

![De coscale-agent starten](./media/container-service-kubernetes-coscale/agent_script.png)

Dat is alles. Zodra de agents operationeel zijn, ziet u binnen enkele minuten gegevens in de console. Ga naar de [agentpagina](https://developer.newrelic.com/) om een overzicht van uw cluster te bekijken, extra configuratiestappen uit te voeren en dashboards zoals het **Kubernetes-clusteroverzicht**te bekijken.

![Overzicht van Kubernetes-cluster](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

De CoScale-agent wordt automatisch geïmplementeerd op nieuwe machines in het cluster. De agent wordt automatisch bijgewerkt wanneer een nieuwe versie wordt uitgebracht.


## <a name="next-steps"></a>Volgende stappen

Zie de [CoScale documentatie](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) en [blog](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) voor meer informatie over CoScale monitoring oplossingen. 

