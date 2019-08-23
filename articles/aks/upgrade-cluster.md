---
title: Een AKS-cluster (Azure Kubernetes service) upgraden
description: Meer informatie over het upgraden van een Azure Kubernetes service-cluster (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 50264b1364f40ff5e68ae4a93783d62837c167b3
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898817"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Een AKS-cluster (Azure Kubernetes service) upgraden

Als onderdeel van de levens cyclus van een AKS-cluster moet u vaak een upgrade uitvoeren naar de nieuwste versie van Kubernetes. Het is belang rijk dat u de meest recente Kubernetes-beveiligings releases toepast of een upgrade uitvoert om de nieuwste functies op te halen. In dit artikel wordt beschreven hoe u de hoofd onderdelen of een enkele standaard knooppunt groep in een AKS-cluster bijwerkt.

Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor AKS-clusters die gebruikmaken van meerdere knooppunt groepen of Windows Server-knoop punten (momenteel in de preview-versie van AKS).

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel moet u de Azure CLI-versie 2.0.65 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!WARNING]
> Een AKS-cluster upgrade activeert een cordon en afvoer van uw knoop punten. Als er een beperkt reken quotum beschikbaar is, kan de upgrade mislukken.  Zie [quota verg Roten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request?branch=pr-en-us-83289) voor meer informatie.

## <a name="check-for-available-aks-cluster-upgrades"></a>Controleren op beschik bare AKS-cluster upgrades

Als u wilt controleren welke Kubernetes-releases beschikbaar zijn voor uw cluster, gebruikt u de opdracht [AZ AKS Get-upgrades][az-aks-get-upgrades] . In het volgende voor beeld wordt gecontroleerd op beschik bare upgrades voor het cluster met de naam *myAKSCluster* in de resource groep met de naam *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Wanneer u een upgrade uitvoert voor een AKS-cluster, kunnen secundaire versies van Kubernetes niet worden overgeslagen. Bijvoorbeeld: upgrades tussen *1.12. x* -> *1.13. x* of *1.13. x* -> *1.14. x* zijn toegestaan, maar *1.12. x* -> *1.14. x* is niet.
>
> Als u een upgrade wilt uitvoeren, van *1.12. x* -> *1.14. x*, moet u eerst een upgrade uitvoeren van *1.12. x* -> *1.13. x*en vervolgens een upgrade uitvoeren van *1.13. x* -> *1.14. x*.

In de volgende voorbeeld uitvoer ziet u dat het cluster kan worden bijgewerkt naar versies *1.13.9* en *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```

## <a name="upgrade-an-aks-cluster"></a>Een AKS-cluster upgraden

Met een lijst met beschik bare versies voor uw AKS-cluster gebruikt u de opdracht [AZ AKS upgrade][az-aks-upgrade] . Tijdens het upgrade proces voegt AKS een nieuw knoop punt toe aan het cluster waarop de opgegeven Kubernetes-versie wordt uitgevoerd. vervolgens wordt een van de oude knoop punten door [Cordon en][kubernetes-drain] vertraagd, zodat de onderbreking van de toepassingen tot een minimum wordt beperkt. Wanneer het nieuwe knoop punt is bevestigd als het uitvoeren van de toepassing Peul, wordt het oude knoop punt verwijderd. Dit proces wordt herhaald totdat alle knoop punten in het cluster zijn bijgewerkt.

In het volgende voor beeld wordt een cluster bijgewerkt naar versie *1.13.10*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

Het duurt enkele minuten om het cluster bij te werken, afhankelijk van het aantal knoop punten dat u hebt.

Als u wilt controleren of de upgrade is voltooid, gebruikt u de opdracht [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

In de volgende voorbeeld uitvoer ziet u dat het cluster nu *1.13.10*uitvoert:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u een bestaand AKS-cluster bijwerkt. Zie de set zelf studies voor meer informatie over het implementeren en beheren van AKS-clusters.

> [!div class="nextstepaction"]
> [Zelf studies voor AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
