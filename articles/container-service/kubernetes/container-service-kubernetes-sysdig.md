---
title: KEUR Azure Kubernetes-cluster bewaken-Sysdig
description: Kubernetes-cluster bewaken in Azure Container Service met behulp van Sysdig
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 3cb9c628993201553b8da1d1bd37b4705e0f23dc
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271639"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>KEUR Een Azure Container Service Kubernetes-cluster bewaken met behulp van Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Vereisten
In dit scenario wordt ervan uitgegaan dat u [een Kubernetes-cluster hebt gemaakt met behulp van Azure container service](container-service-kubernetes-walkthrough.md).

Er wordt ook van uitgegaan dat u de Azure CLI-en kubectl-hulpprogram ma's hebt geïnstalleerd.

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

## <a name="sysdig"></a>Sysdig
Sysdig is een externe bewaking als een service bedrijf dat containers kan bewaken in uw Kubernetes-cluster dat wordt uitgevoerd in Azure. Voor het gebruik van Sysdig is een actief Sysdig-account vereist.
U kunt zich aanmelden voor een account op hun [site](https://app.sysdigcloud.com).

Nadat u bent aangemeld bij de Sysdig-cloudwebsite, klikt u op uw gebruikersnaam. Op de pagina ziet u uw ‘toegangssleutel’. 

![Sysdig API-sleutel](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>De Sysdig-agents worden geïnstalleerd in Kubernetes
Voor het bewaken van uw containers voert Sysdig een proces uit op elke machine met behulp van een Kubernetes-`DaemonSet`.
DaemonSets zijn Kubernetes API-objecten die één exemplaar van een container per computer uitvoeren.
Ze zijn perfect voor het installeren van hulpprogram ma's zoals de bewakings agent van Sysdig.

Als u de Sysdig-daemonset wilt installeren, moet u [de sjabloon](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) eerst downloaden van Sysdig. Sla het bestand op als `sysdig-daemonset.yaml`.

In Linux en OS X kunt u het volgende uitvoeren:

```console
$ curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

In PowerShell:

```console
$ Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Bewerk vervolgens dat bestand om uw toegangs sleutel in te voegen, die u hebt verkregen via uw Sysdig-account.

Maak ten slotte de Daemonset:

```console
$ kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Uw bewaking weer geven
Zodra de agents zijn geïnstalleerd en worden uitgevoerd, moeten ze gegevens weer naar Sysdig.  Ga terug naar het [sysdig-dash board](https://app.sysdigcloud.com) en Bekijk de informatie over uw containers.

U kunt ook Kubernetes-specifieke Dash boards installeren via de [wizard Nieuw dash board](https://app.sysdigcloud.com/#/dashboards/new).
