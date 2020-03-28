---
title: 'Zelfstudie voor Kubernetes op Azure: Een cluster bijwerken'
description: In deze zelfstudie Azure Kubernetes Service (AKS) leert u hoe u een bestaand AKS-cluster kunt bijwerken naar de nieuwste Kubernetes-versie.
services: container-service
ms.topic: tutorial
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 4d9ef061904fb1a0fff25506eedb82158971bed5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77622033"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Zelfstudie: Kubernetes bijwerken in AKS (Azure Kubernetes Service)

Gedurende de levenscyclus van de toepassing en het cluster, kunt u desgewenst Kubernetes bijwerken naar de nieuwste versie, en nieuwe functies gebruiken. Een AKS-cluster (Azure Kubernetes Service) kan worden bijgewerkt met de Azure CLI.

In deze zelfstudie, deel zeven, wordt een upgrade uitgevoerd van een Kubernetes-cluster. Procedures voor:

> [!div class="checklist"]
> * Huidige en beschikbare Kubernetes-versies identificeren
> * De Kubernetes-knooppunten upgraden
> * Een geslaagde upgrade valideren

## <a name="before-you-begin"></a>Voordat u begint

In eerdere zelfstudies is een toepassing verpakt in een containerinstallatiekopie. Deze installatiekopie is geüpload naar Azure Container Registry en u hebt een AKS-cluster gemaakt. De toepassing is vervolgens geïmplementeerd in het AKS-cluster. Als u deze stappen niet hebt uitgevoerd en u deze zelfstudie toch wilt volgen, begint u met [Tutorial 1 – Create container images][aks-tutorial-prepare-app] (Zelfstudie 1: containerinstallatiekopieën maken).

Voor deze zelfstudie moet u Azure CLI versie 2.0.53 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Beschikbare clusterversies verkrijgen

Voordat u een cluster bijwerkt, gebruikt u de opdracht [az aks get-upgrades][] om te controleren welke Kubernetes-releases beschikbaar zijn voor de upgrade:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

In het volgende voorbeeld is de huidige versie *1.14.8*en worden de beschikbare versies weergegeven onder de kolom *Upgrades.*

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.14.8           1.14.8             1.15.5, 1.15.7
```

## <a name="upgrade-a-cluster"></a>Een cluster upgraden

Om onderbreking van actieve toepassingen te minimaliseren, worden AKS-knooppunten zorgvuldig afgebakend en geleegd. In dit proces worden de volgende stappen uitgevoerd:

1. Kubernetes Scheduler voorkomt dat er extra pods worden gepland op een knooppunt dat moet worden bijgewerkt.
1. Actieve pods op het knooppunt worden gepland voor uitvoering op andere knooppunten in het cluster.
1. Er wordt een knooppunt gemaakt waarop de meest recente Kubernetes-onderdelen worden uitgevoerd.
1. Als het nieuwe knooppunt klaar en is toegevoegd aan het cluster, worden er pods gepland op het knooppunt.
1. Het oude knooppunt wordt verwijderd en het proces van afbakenen en legen begint op het volgende knooppunt in het cluster.

Gebruik de opdracht [az aks upgrade][] als u het AKS-cluster wilt bijwerken. In het volgende voorbeeld wordt het cluster ge-overweg naar Kubernetes-versie *1.14.6*.

> [!NOTE]
> U kunt slechts één secundaire versie per keer bijwerken. U bijvoorbeeld upgraden van *1.14.x* naar *1.15.x,* maar u niet rechtstreeks upgraden van *1.14.x* naar *1.16.x.* Om te upgraden van *1.14.x* naar *1.16.x*, eerst upgraden van *1.14.x* naar *1.15.x*, voer dan een andere upgrade uit van *1.15.x* naar *1.16.x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.15.5
```

De volgende verkorte voorbeelduitvoer toont de *kubernetesVersion* rapporteert nu *1.15.5:*

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.15.5",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Een upgrade valideren

Controleer als volgt of de upgrade is geslaagd met de opdracht [az aks show][]:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

In de volgende voorbeelduitvoer ziet u dat het AKS-cluster *KubernetesVersion 1.15.5*uitvoert:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.15.5               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Het cluster verwijderen

Omdat deze zelfstudie de laatste is van de reeks zelfstudies, kunt u het AKS-cluster verwijderen. Aangezien de Kubernetes-knooppunten worden uitgevoerd op virtuele Azure machines (VM's), worden er nog steeds kosten in rekening gebracht, zelfs als u het cluster niet gebruikt. Gebruik de opdracht [az group delete][az-group-delete] om de resourcegroep, de containerservice en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een upgrade van Kubernetes in een AKS-cluster uitgevoerd. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Huidige en beschikbare Kubernetes-versies identificeren
> * De Kubernetes-knooppunten upgraden
> * Een geslaagde upgrade valideren

Volg deze koppeling voor meer informatie over AKS.

> [!div class="nextstepaction"]
> [Overzicht van AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
