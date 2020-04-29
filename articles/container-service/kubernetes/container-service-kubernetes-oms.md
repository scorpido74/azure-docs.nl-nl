---
title: KEUR Azure Kubernetes-cluster bewaken-Operations Management
description: Kubernetes-cluster bewaken in Azure Container Service met behulp van Log Analytics
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 02d04076ccc41d243a493838667f5e8cc6bfa5ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371151"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>KEUR Een Azure Container Service cluster met Log Analytics bewaken

> [!TIP]
> Zie [Azure monitor voor containers](../../azure-monitor/insights/container-insights-overview.md)voor de bijgewerkte versie van dit artikel waarin de Azure Kubernetes-service wordt gebruikt.

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Vereisten
In dit scenario wordt ervan uitgegaan dat u [een Kubernetes-cluster hebt gemaakt met behulp van Azure container service](container-service-kubernetes-walkthrough.md).

Er wordt ook van uitgegaan dat u `az` de Azure cli `kubectl` en hulpprogram ma's hebt geïnstalleerd.

U kunt testen of u het hulp `az` programma hebt geïnstalleerd door het volgende uit te voeren:

```azurecli
az --version
```

Als u het `az` hulp programma niet hebt geïnstalleerd, vindt u [hier](https://github.com/azure/azure-cli#installation)instructies.
U kunt ook [Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview)gebruiken. Dit is de Azure cli `az` en `kubectl` de hulpprogram ma's die al voor u zijn geïnstalleerd.

U kunt testen of u het hulp `kubectl` programma hebt geïnstalleerd door het volgende uit te voeren:

```console
kubectl version
```

Als u niet hebt `kubectl` geïnstalleerd, kunt u het volgende uitvoeren:

```azurecli
az acs kubernetes install-cli
```

Als u wilt testen of er kubernetes-sleutels zijn geïnstalleerd in uw kubectl-hulp programma, kunt u het volgende uitvoeren:

```console
kubectl get nodes
```

Als de bovenstaande opdracht fouten opleveren, moet u kubernetes-cluster sleutels installeren in uw kubectl-hulp programma. U kunt dit doen met de volgende opdracht:

```azurecli
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Containers controleren met Log Analytics

Log Analytics is de Cloud oplossing van micro soft die u helpt bij het beheren en beveiligen van uw on-premises en Cloud infrastructuur.Container oplossing is een oplossing in Log Analytics, waarmee u de container inventaris, de prestaties en logboeken op één locatie kunt bekijken. U kunt controleren, problemen met containers oplossen door de logboeken op gecentraliseerde locatie te bekijken en te zoeken naar een overmatige container op een host.

![](media/container-service-monitoring-oms/image1.png)

Raadpleeg de [container solution log Analytics](../../azure-monitor/insights/containers.md)voor meer informatie over container oplossingen.

## <a name="installing-log-analytics-on-kubernetes"></a>Log Analytics op Kubernetes installeren

### <a name="obtain-your-workspace-id-and-key"></a>Uw werk ruimte-ID en-sleutel ophalen
De Log Analytics-agent om te communiceren met de service die moet worden geconfigureerd met een werk ruimte-ID en een werkruimte sleutel. Als u de werk ruimte-ID en-sleutel wilt ophalen, moet <https://mms.microsoft.com>u een account maken op.
Volg de stappen voor het maken van een account. Zodra u klaar bent met het maken van het account, kunt u uw ID en sleutel verkrijgen door te klikken op de Blade **log Analytics** en vervolgens op de naam van uw werk ruimte. Onder **Geavanceerde instellingen**, **verbonden bronnen**en vervolgens **Linux-servers**vindt u de informatie die u nodig hebt, zoals hieronder wordt weer gegeven.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>De Log Analytics agent installeren met behulp van een Daemonset
DaemonSets worden door Kubernetes gebruikt om één exemplaar van een container op elke host in het cluster uit te voeren.
Ze zijn perfect voor het uitvoeren van bewakings agenten.

Dit is het [yaml-bestand van de daemonset](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Sla het op in een bestand `oms-daemonset.yaml` met de naam en vervang de plaatsings `WSID` instellingen `KEY` voor en door de werk ruimte-id en-sleutel in het bestand.

Nadat u uw werk ruimte-ID en-sleutel aan de configuratie van de Daemonset hebt toegevoegd, kunt u de Log Analytics agent op `kubectl` uw cluster installeren met het opdracht regel programma:

```console
kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>De Log Analytics-agent installeren met behulp van een Kubernetes-geheim
Als u uw Log Analytics werk ruimte-ID en-sleutel wilt beveiligen, kunt u Kubernetes Secret gebruiken als onderdeel van het bestand Daemonset YAML.

- Kopieer het script, het geheime sjabloon bestand en het YAML-bestand van de Daemonset (uit de [opslag plaats](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) en zorg ervoor dat deze zich in dezelfde map bevinden.
  - script voor het genereren van geheimen-secret-gen.sh
  - geheime sjabloon-geheim: sjabloon. yaml
    - Daemonset YAML-bestand-omsagent-DS-geheimen. yaml
- Voer het script uit. Het script vraagt om de Log Analytics werk ruimte-ID en primaire sleutel. Voeg dit toe en het script maakt een geheim yaml-bestand zodat u het kunt uitvoeren.

  ```console
  sudo bash ./secret-gen.sh
  ```

  - Maak de geheimen pod door het volgende uit te voeren:

     ```console
     kubectl create -f omsagentsecret.yaml
     ```

  - Als u wilt controleren, voert u de volgende handelingen uit:

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

  - Maak uw omsagent-daemon-set door de volgende handelingen uit te voeren:
  
  ```console
  kubectl create -f omsagent-ds-secrets.yaml
  ```

### <a name="conclusion"></a>Conclusie
Dat is alles. Na enkele minuten moet u de gegevens stroom naar uw Log Analytics dash board kunnen zien.
