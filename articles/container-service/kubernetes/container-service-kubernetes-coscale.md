---
title: KEUR Een Azure Kubernetes-cluster bewaken met coscale
description: Een Kubernetes-cluster in Azure Container Service bewaken met de functie voor automatisch schalen
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f1d0ca1ffc2e7a3d645ac5acbaafdf45f85550be
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271095"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>KEUR Een Azure Container Service Kubernetes-cluster met coscale bewaken

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

In dit artikel laten we zien hoe u de [coscale](https://web.archive.org/web/20180317071550/https://www.coscale.com/) -agent implementeert voor het bewaken van alle knoop punten en containers in uw Kubernetes-cluster in azure container service. U hebt een account met de functie voor het aanpassen van deze configuratie nodig. 


## <a name="about-coscale"></a>Over coscale 

Coscale is een bewakings platform dat metrische gegevens en gebeurtenissen verzamelt van alle containers in verschillende Orchestration-platforms. Coscale biedt volledige stack bewaking voor Kubernetes-omgevingen. Het biedt visualisaties en analyses voor alle lagen in de stack: het besturings systeem, Kubernetes, docker en toepassingen die in uw containers worden uitgevoerd. Coscale biedt verschillende ingebouwde controle dashboards en heeft ingebouwde afwijkings detectie zodat Opera tors en ontwikkel aars snel infra structuur-en toepassings problemen kunnen vinden.

![Gebruikers interface aanpassen](./media/container-service-kubernetes-coscale/coscale.png)

Zoals in dit artikel wordt weer gegeven, kunt u agents installeren op een Kubernetes-cluster om de coscale als een SaaS-oplossing uit te voeren. Als u uw gegevens on-site wilt blijven, is de functie voor on-premises installatie ook beschikbaar.


## <a name="prerequisites"></a>Vereisten

U moet eerst [een Coscale-account maken](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial).

In dit scenario wordt ervan uitgegaan dat u [een Kubernetes-cluster hebt gemaakt met behulp van Azure container service](container-service-kubernetes-walkthrough.md).

Er wordt ook van uitgegaan dat u de `az` Azure CLI en `kubectl`-hulpprogram ma's hebt geïnstalleerd.

U kunt testen of u het `az`-hulp programma hebt geïnstalleerd door uit te voeren:

```azurecli
az --version
```

Als u het hulp programma `az` niet hebt geïnstalleerd, zijn er [hier](/cli/azure/install-azure-cli)instructies.

U kunt testen of u het `kubectl`-hulp programma hebt geïnstalleerd door uit te voeren:

```bash
kubectl version
```

Als `kubectl` niet is geïnstalleerd, kunt u het volgende uitvoeren:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>De coscale-agent installeren met een Daemonset
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) worden door Kubernetes gebruikt om één exemplaar van een container op elke host in het cluster uit te voeren.
Ze zijn perfect voor het uitvoeren van bewakings agenten zoals de coscale-agent.

Nadat u zich hebt aangemeld bij coscale, gaat u naar de [agent pagina](https://app.coscale.com/) om coscale-agents te installeren op uw cluster met behulp van een daemonset. De gebruikers interface voor automatisch schalen biedt begeleide configuratie stappen voor het maken van een agent en het bewaken van uw volledige Kubernetes-cluster.

![Configuratie van de agent voor coscale](./media/container-service-kubernetes-coscale/installation.png)

Als u de agent op het cluster wilt starten, voert u de opgegeven opdracht uit:

![De coscale-agent starten](./media/container-service-kubernetes-coscale/agent_script.png)

Dat is alles. Zodra de agents actief zijn, ziet u in een paar minuten de gegevens in de-console. Ga naar de [agent pagina](https://app.coscale.com/) om een samen vatting van uw cluster te bekijken, aanvullende configuratie stappen uit te voeren en dash boards te bekijken, zoals het **Kubernetes-cluster overzicht**.

![Overzicht van Kubernetes-cluster](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

De coscale-agent wordt automatisch geïmplementeerd op nieuwe computers in het cluster. De agent wordt automatisch bijgewerkt wanneer er een nieuwe versie wordt uitgebracht.


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de documentatie en het [blog](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) voor [coscale](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) voor meer informatie over de oplossing voor het controleren van de coscale. 

