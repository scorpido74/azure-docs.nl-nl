---
title: (AFGESCHAFT) Azure Kubernetes-cluster bewaken - Operations Management
description: Kubernetes-cluster in Azure Container Service bewaken met Behulp van Log Analytics
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 02d04076ccc41d243a493838667f5e8cc6bfa5ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371151"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(AFGESCHAFT) Een Azure Container Service-cluster bewaken met Log Analytics

> [!TIP]
> Zie [Azure Monitor voor containers voor](../../azure-monitor/insights/container-insights-overview.md)de bijgewerkte versie van dit artikel die Azure Kubernetes Service gebruikt.

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Vereisten
Deze walkthrough gaat ervan uit dat u [een Kubernetes-cluster](container-service-kubernetes-walkthrough.md)hebt gemaakt met Azure Container Service.

Het gaat er ook `az` van uit `kubectl` dat u de Azure cli en tools hebt geïnstalleerd.

U testen of `az` u het gereedschap hebt geïnstalleerd door het uitvoeren van:

```azurecli
az --version
```

Als u het `az` gereedschap niet hebt geïnstalleerd, zijn er [hier](https://github.com/azure/azure-cli#installation)instructies.
U ook [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)gebruiken, waar de `az` Azure cli en `kubectl` tools al voor u zijn geïnstalleerd.

U testen of `kubectl` u het gereedschap hebt geïnstalleerd door het uitvoeren van:

```console
kubectl version
```

Als u niet `kubectl` hebt geïnstalleerd, u het:

```azurecli
az acs kubernetes install-cli
```

Ga als of u kubernetes-toetsen in uw kubectl-tool hebt geïnstalleerd:

```console
kubectl get nodes
```

Als de bovenstaande opdracht fouten uit, moet u kubernetes cluster sleutels te installeren in uw kubectl tool. U dat doen met de volgende opdracht:

```azurecli
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Containers controleren met Log Analytics

Log Analytics is de cloudgebaseerde IT-beheeroplossing van Microsoft die u helpt bij het beheren en beschermen van uw on-premises en cloudinfrastructuur.Container Solution is een oplossing in Log Analytics, waarmee u de containervoorraad, prestaties en logboeken op één locatie bekijken. U controleren, containers oplossen door de logboeken op een centrale locatie te bekijken en luidruchtige overtollige container op een host vinden.

![](media/container-service-monitoring-oms/image1.png)

Voor meer informatie over Container Solution verwijzen wij u naar de [Container Solution Log Analytics](../../azure-monitor/insights/containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Log Analytics installeren op Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Uw werkplek-id en -sleutel verkrijgen
Als de loganalytics-agent met de service wilt praten, moet deze worden geconfigureerd met een werkruimte-id en een werkruimtesleutel. Als u de werkruimte-id en -sleutel <https://mms.microsoft.com>wilt krijgen, moet u een account aanmaken bij .
Volg de stappen om een account aan te maken. Zodra u klaar bent met het maken van het account, u uw id en sleutel verkrijgen door te klikken op het **blad Log Analytics,** vervolgens de naam van uw werkruimte. Dan, onder **Geavanceerde instellingen**, **Verbonden Bronnen**, en vervolgens Linux **Servers**, vindt u de informatie die u nodig hebt, zoals hieronder weergegeven.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>De log-analyse-agent installeren met een DaemonSet
DaemonSets worden door Kubernetes gebruikt om één exemplaar van een container op elke host in het cluster uit te voeren.
Ze zijn perfect voor het runnen van bewakingsagenten.

Hier is het [DaemonSet YAML-bestand.](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) Sla het op `oms-daemonset.yaml` in een bestand met `WSID` de `KEY` naam en vervang de plaatsaanduidingswaarden voor en door uw werkruimte-id en sleutel in het bestand.

Zodra u uw werkruimte-id en sleutel hebt toegevoegd aan de Configuratie DaemonSet, `kubectl` u de agent Log Analytics op uw cluster installeren met het opdrachtregelgereedschap:

```console
kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>De Log Analytics-agent installeren met een Kubernetes Secret
Om uw Log Analytics workspace ID en sleutel te beschermen, u Kubernetes Secret gebruiken als onderdeel van DaemonSet YAML-bestand.

- Kopieer het script, het geheime sjabloonbestand en het DaemonSet YAML-bestand (uit [de opslagplaats)](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)en zorg ervoor dat ze zich in dezelfde map bevinden.
  - geheim genereren script - secret-gen.sh
  - geheime sjabloon - secret-template.yaml
    - DaemonSet YAML bestand - omsagent-ds-secrets.yaml
- Voer het script uit. Het script vraagt om de Log Analytics Workspace ID en Primary Key. Voeg dat in en het script maakt een geheim yaml-bestand zodat je het uitvoeren.

  ```console
  sudo bash ./secret-gen.sh
  ```

  - Maak de pod Geheimen door het volgende uit te voeren:

     ```console
     kubectl create -f omsagentsecret.yaml
     ```

  - Voer het volgende uit om het volgende te controleren:

  ```console
  kubectl get secrets
  ```

  ```output
  NAME                  TYPE                                  DATA      AGE
  default-token-gvl91   kubernetes.io/service-account-token   3         50d
  omsagent-secret       Opaque                                2         1d
  root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
  Name:           omsagent-secret
  Namespace:      default
  Labels:         <none>
  Annotations:    <none>

  Type:   Opaque

  Data
  ====
  WSID:   36 bytes
  KEY:    88 bytes
  ```

  - Maak je omsagent daemon-set door het volgende uit te voeren:
  
  ```console
  kubectl create -f omsagent-ds-secrets.yaml
  ```

### <a name="conclusion"></a>Conclusie
Dat is alles. Na een paar minuten u gegevens zien die naar uw Log Analytics-dashboard stromen.
