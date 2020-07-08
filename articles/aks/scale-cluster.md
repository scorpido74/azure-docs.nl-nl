---
title: Een AKS-cluster (Azure Kubernetes Service) schalen
description: Meer informatie over het schalen van het aantal knoop punten in een Azure Kubernetes service-cluster (AKS).
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 55d7a00a0a8c0b655f06810f8bcea7126bb9167f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79368414"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Aantal knooppunten in een AKS-cluster (Azure Kubernetes Service) schalen

Als de resource behoeften van uw toepassingen veranderen, kunt u een AKS-cluster hand matig schalen om een ander aantal knoop punten uit te voeren. Wanneer u omlaag schaalt, worden knoop punten zorgvuldig [afgebakend en][kubernetes-drain] vertraagd om onderbreking van het uitvoeren van toepassingen tot een minimum te beperken. Wanneer u omhoog schaalt, wacht AKS totdat er knoop punten zijn gemarkeerd `Ready` door het Kubernetes-cluster voordat de peulen worden gepland.

## <a name="scale-the-cluster-nodes"></a>De clusterknooppunten schalen

Haal eerst de *naam* van de groep van het knoop punt op met behulp van de opdracht [AZ AKS show][az-aks-show] . In het volgende voor beeld wordt de naam van de knooppunt groep opgehaald voor het cluster met de naam *myAKSCluster* in de resource groep *myResourceGroup* :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

In de volgende voorbeeld uitvoer ziet u dat de *naam* *nodepool1*is:

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Gebruik de opdracht [AZ AKS Scale][az-aks-scale] om de cluster knooppunten te schalen. In het volgende voor beeld wordt een cluster met de naam *myAKSCluster* naar één knoop punt geschaald. Geef uw eigen *-nodepool-naam* op uit de vorige opdracht, zoals *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

In de volgende voorbeeld uitvoer ziet u dat het cluster met één knoop punt is geschaald, zoals wordt weer gegeven in de sectie *agentPoolProfiles* :

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u hand matig een AKS-cluster geschaald om het aantal knoop punten te verg Roten of verkleinen. U kunt ook de cluster-automatische [schaal][cluster-autoscaler] functie gebruiken om uw cluster automatisch te schalen.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
