---
title: Een AKS-cluster (Azure Kubernetes Service) schalen
description: Meer informatie over het schalen van het aantal knooppunten in een AKS-cluster (Azure Kubernetes Service).
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 55d7a00a0a8c0b655f06810f8bcea7126bb9167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368414"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Aantal knooppunten in een AKS-cluster (Azure Kubernetes Service) schalen

Als de resourcebehoeften van uw toepassingen veranderen, u een AKS-cluster handmatig schalen om een ander aantal knooppunten uit te voeren. Wanneer u afschaalt, worden knooppunten zorgvuldig [gecordoneerd en afgevoerd][kubernetes-drain] om onderbreking van lopende toepassingen te minimaliseren. Wanneer u opschaalt, wacht AKS `Ready` totdat knooppunten worden gemarkeerd door het Kubernetes-cluster voordat er pods op worden gepland.

## <a name="scale-the-cluster-nodes"></a>De clusterknooppunten schalen

Eerst de *naam* van uw knooppuntzwembad met behulp van de [az aks show][az-aks-show] commando. In het volgende voorbeeld wordt de naam van de knooppuntgroep voor het cluster met de naam *myAKSCluster* in de brongroep *myResourceGroup:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Uit de volgende voorbeelduitvoer blijkt dat de *naam* *nodepool1*is:

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

Gebruik de opdracht [az aks-schaal][az-aks-scale] om de clusterknooppunten te schalen. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluster* geschaald naar één knooppunt. Geef uw eigen *--nodepool-naam* op van de vorige opdracht, zoals *nodepool1:*

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

In de volgende voorbeelduitvoerwordt aangegeven dat het cluster is geschaald naar één knooppunt, zoals wordt weergegeven in de sectie *agentPoolProfiles:*

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

In dit artikel hebt u handmatig een AKS-cluster geschaald om het aantal knooppunten te verhogen of te verminderen. U de [clusterautoscaler][cluster-autoscaler] ook gebruiken om uw cluster automatisch te schalen.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
