---
title: Een AKS-cluster (Azure Kubernetes Service) upgraden
description: Meer informatie over het upgraden van een AKS-cluster (Azure Kubernetes Service) om de nieuwste functies en beveiligingsupdates te ontvangen.
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 183e0a85f65d24dc7133307391931bea754a456d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632612"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Een AKS-cluster (Azure Kubernetes Service) upgraden

Als onderdeel van de levenscyclus van een AKS-cluster moet u vaak upgraden naar de nieuwste Kubernetes-versie. Het is belangrijk dat u de nieuwste Kubernetes-beveiligingsreleases toepast of een upgrade uitvoert om de nieuwste functies te krijgen. In dit artikel ziet u hoe u de hoofdcomponenten of een enkele standaardknooppuntgroep in een AKS-cluster upgraden.

Zie [Een knooppuntgroep in AKS][nodepool-upgrade]upgraden voor AKS-clusters die meerdere knooppuntgroepen of Windows Server-knooppunten gebruiken (momenteel in preview in AKS.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel moet u de Azure CLI-versie 2.0.65 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

> [!WARNING]
> Een AKS-clusterupgrade activeert een cordon en drain van uw knooppunten. Als er een laag rekenquotum beschikbaar is, kan de upgrade mislukken. Zie [verhoging van de quota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) voor meer informatie.
> Als u uw eigen cluster autoscaler implementatie uit te schakelen (u schalen naar nul replica's) tijdens de upgrade als er een kans is dat het zal interfereren met het upgradeproces. Managed autoscaler verwerkt dit automatisch. 

## <a name="check-for-available-aks-cluster-upgrades"></a>Controleren op beschikbare AKS-clusterupgrades

Om te controleren welke Kubernetes releases beschikbaar zijn voor uw cluster, gebruik maken van de [az aks get-upgrades][az-aks-get-upgrades] commando. In het volgende voorbeeld wordt gecontroleerd op beschikbare upgrades van het cluster met de naam *myAKSCluster* in de brongroep *myResourceGroup:*

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Wanneer u een AKS-cluster upgradet, kunnen kleine versies van Kubernetes niet worden overgeslagen. Upgrades tussen *1.12.x* -> *1.13.x* of *1.13.x* -> *1.14.x* zijn bijvoorbeeld toegestaan, maar *1.12.x* -> *1.14.x* niet.
>
> Upgraden, van *1.12.x* -> *1.14.x*, eerste upgrade van *1.12.x* -> *1.13.x*, en vervolgens upgraden van *1.13.x* -> *1.14.x*.

Uit de volgende voorbeelduitvoer blijkt dat het cluster kan worden geüpgraded naar versies *1.13.9* en *1.13.10:*

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Als er geen upgrade beschikbaar is, krijgt u het:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>Een AKS-cluster upgraden

Gebruik met een lijst met beschikbare versies voor uw AKS-cluster de [upgradeopdracht az aks][az-aks-upgrade] om te upgraden. Tijdens het upgradeproces voegt AKS een nieuw knooppunt toe aan het cluster dat de opgegeven Kubernetes-versie uitvoert, en vervolgens zorgvuldig een van de oude knooppunten [te cordonen en leeg][kubernetes-drain] te voeren om onderbreking van het uitvoeren van toepassingen te minimaliseren. Wanneer het nieuwe knooppunt wordt bevestigd als het uitvoeren van toepassingspods, wordt het oude knooppunt verwijderd. Dit proces wordt herhaald totdat alle knooppunten in het cluster zijn bijgewerkt.

In het volgende voorbeeld wordt een cluster geopwaardeerd naar versie *1.13.10:*

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

Het duurt een paar minuten om het cluster te upgraden, afhankelijk van het aantal knooppunten dat u hebt. 

> [!NOTE]
> Er is een totale toegestane tijd voor een clusterupgrade om te voltooien. Deze tijd wordt berekend door `10 minutes * total number of nodes in the cluster`het product van . In een cluster met 20 node moeten upgradebewerkingen bijvoorbeeld binnen 200 minuten slagen of wordt de bewerking mislukt om een niet-herstelbare clusterstatus te voorkomen. Als u wilt herstellen bij een upgradefout, probeert u de upgradebewerking opnieuw nadat de time-out is getroffen.

Gebruik de opdracht [az aks show][az-aks-show] om te bevestigen dat de upgrade succesvol is geweest:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Uit de volgende voorbeelduitvoer blijkt dat het cluster nu *1.13.10*draait:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel ziet u hoe u een bestaand AKS-cluster upgraden. Zie de set zelfstudies voor meer informatie over het implementeren en beheren van AKS-clusters.

> [!div class="nextstepaction"]
> [AKS-zelfstudies][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
