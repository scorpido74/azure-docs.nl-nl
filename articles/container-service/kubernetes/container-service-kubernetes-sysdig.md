---
title: (AFGESCHAFT) Azure Kubernetes-cluster bewaken - Sysdig
description: Kubernetes-cluster in Azure Container Service bewaken met Sysdig
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 68136d5b9ec16c822cb62e4fee85b8ace9b1899a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371096"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>(AFGESCHAFT) Een Kubernetes-cluster van Azure Container Service bewaken met Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Vereisten
Deze walkthrough gaat ervan uit dat u [een Kubernetes-cluster](container-service-kubernetes-walkthrough.md)hebt gemaakt met Azure Container Service.

Het gaat er ook van uit dat u de azure cli en kubectl tools geïnstalleerd.

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

## <a name="sysdig"></a>Sysdig
Sysdig is een externe monitoring als servicebedrijf dat containers in uw Kubernetes-cluster die in Azure wordt uitgevoerd, kan controleren. Voor het gebruik van Sysdig is een actief Sysdig-account vereist.
U zich aanmelden voor een account op hun [site.](https://app.sysdigcloud.com)

Nadat u bent aangemeld bij de Sysdig-cloudwebsite, klikt u op uw gebruikersnaam. Op de pagina ziet u uw ‘toegangssleutel’. 

![Sysdig API-sleutel](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Het installeren van de Sysdig-agents naar Kubernetes
Om uw containers te controleren, voert Sysdig `DaemonSet`een proces uit op elke machine met behulp van een Kubernetes.
DaemonSets zijn Kubernetes API-objecten die één exemplaar van een container per machine uitvoeren.
Ze zijn perfect voor het installeren van tools zoals de Sysdig's monitoring agent.

Als u de Sysdig daemonset wilt installeren, moet u [eerst de sjabloon](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) van sysdig downloaden. Sla dat `sysdig-daemonset.yaml`bestand op als .

Op Linux en OS X kun je draaien:

```console
curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

In PowerShell:

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Bewerk vervolgens dat bestand om uw Toegangssleutel in te voegen, die u hebt verkregen van uw Sysdig-account.

Maak ten slotte de DaemonSet:

```console
kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Bekijk uw controle
Eenmaal geïnstalleerd en uitgevoerd, moeten de agenten de gegevens terugpompen naar Sysdig.  Ga terug naar het [sysdig dashboard](https://app.sysdigcloud.com) en je moet informatie over je containers zien.

U ook Kubernetes-specifieke dashboards installeren via de [nieuwe dashboardwizard.](https://app.sysdigcloud.com/#/dashboards/new)
