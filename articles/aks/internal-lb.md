---
title: Een interne load balancer maken
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het maken en gebruiken van een interne load balancer om uw services bloot te leggen met Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 4decd66a558b031f1aaaf9c64556dae545ed05d3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668417"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Een interne load balancer gebruiken met Azure Kubernetes Service (AKS)

Als u de toegang tot uw toepassingen in Azure Kubernetes Service (AKS) wilt beperken, u een interne load balancer maken en gebruiken. Een interne load balancer maakt een Kubernetes-service alleen toegankelijk voor toepassingen die in hetzelfde virtuele netwerk worden uitgevoerd als het Kubernetes-cluster. In dit artikel ziet u hoe u een interne load balancer maakt en gebruikt met Azure Kubernetes Service (AKS).

> [!NOTE]
> Azure Load Balancer is beschikbaar in twee SKU's - *Basic* en *Standard*. Standaard wordt de Standaard SKU gebruikt wanneer u een AKS-cluster maakt.  Wanneer u een service maakt met type als LoadBalancer, krijgt u hetzelfde LB-type als wanneer u het cluster indient. Zie [SKU-vergelijking van Azure load balancer voor][azure-lb-comparison]meer informatie .

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

U hebt ook de Azure CLI-versie 2.0.59 of hoger geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

De principal van de AKS-clusterservice heeft toestemming nodig om netwerkbronnen te beheren als u een bestaand subnet of resourcegroep gebruikt. Wijs de rol *Netwerkbijdrager toe* aan uw serviceprincipal aan de gedelegeerde resources. Zie [AKS-toegang tot andere Azure-bronnen voor][aks-sp]meer informatie over machtigingen.

## <a name="create-an-internal-load-balancer"></a>Een interne load balancer maken

Als u een interne load balancer `internal-lb.yaml` wilt maken, maakt u een servicemanifest met de naam *LoadBalancer van* het servicetype en de interne annotatie *azure-load-balancer-balancer* zoals weergegeven in het volgende voorbeeld:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Implementeer de interne load balancer met behulp van de [kubectl toe te passen][kubectl-apply] en geef de naam van uw YAML manifest:

```console
kubectl apply -f internal-lb.yaml
```

Er wordt een Azure load balancer gemaakt in de knooppuntbrongroep en verbonden met hetzelfde virtuele netwerk als het AKS-cluster.

Wanneer u de servicegegevens bekijkt, wordt het IP-adres van de interne load balancer weergegeven in de kolom *EXTERN-IP.* In deze context staat *Extern* in relatie tot de externe interface van de load balancer, niet dat het een openbaar, extern IP-adres ontvangt. Het kan een minuut of twee duren * \<voordat\> * het IP-adres verandert van in behandeling naar een daadwerkelijk intern IP-adres, zoals in het volgende voorbeeld wordt weergegeven:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Een IP-adres opgeven

Als u een specifiek IP-adres met de interne load balancer wilt gebruiken, voegt u de eigenschap *loadBalancerIP* toe aan het Load balancer YAML-manifest. Het opgegeven IP-adres moet zich in hetzelfde subnet bevinden als het AKS-cluster en mag nog niet aan een resource zijn toegewezen.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Wanneer u wordt geïmplementeerd en de servicegegevens bekijkt, geeft het IP-adres in de kolom EXTERN-IP het opgegeven *IP-adres* weer:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Privénetwerken gebruiken

Wanneer u uw AKS-cluster maakt, u geavanceerde netwerkinstellingen opgeven. Met deze aanpak u het cluster implementeren in een bestaand virtueel Azure-netwerk en subnetten. Een scenario is het implementeren van uw AKS-cluster in een privénetwerk dat is aangesloten op uw on-premises omgeving en services uitvoeren die alleen intern toegankelijk zijn. Zie uw eigen virtuele netwerksubnetten configureren met [Kubenet][use-kubenet] of [Azure CNI][advanced-networking]voor meer informatie.

Er zijn geen wijzigingen in de vorige stappen nodig om een interne load balancer te implementeren in een AKS-cluster dat een privénetwerk gebruikt. De load balancer wordt gemaakt in dezelfde resourcegroep als uw AKS-cluster, maar is verbonden met uw privévirtuele netwerk en subnet, zoals in het volgende voorbeeld wordt weergegeven:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Mogelijk moet u de serviceprincipal voor uw AKS-cluster de rol *Netwerkinzender toekennen* aan de brongroep waarin uw Azure-virtuele netwerkbronnen worden geïmplementeerd. Bekijk de service principal met az `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"` [aks show,][az-aks-show]zoals . Als u een roltoewijzing wilt maken, gebruikt u de opdracht [Voor het maken van een AZ-roltoewijzing.][az-role-assignment-create]

## <a name="specify-a-different-subnet"></a>Een ander subnet opgeven

Als u een subnet voor uw load balancer wilt opgeven, voegt u de *anzuur-load-balancer-internal-subnet-annotatie* toe aan uw service. Het opgegeven subnet moet zich in hetzelfde virtuele netwerk bevinden als uw AKS-cluster. Wanneer het effect wordt geïmplementeerd, maakt het *externe IP-adres* van de load balancer deel uit van het opgegeven subnet.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>De load balancer verwijderen

Wanneer alle services die de interne load balancer gebruiken worden verwijderd, wordt ook de load balancer zelf verwijderd.

U een service ook rechtstreeks verwijderen zoals `kubectl delete service internal-app`bij elke Kubernetes-bron, zoals, die vervolgens ook de onderliggende Azure-load balancer verwijdert.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Kubernetes-services vindt u in de [documentatie van Kubernetes Services.][kubernetes-services]

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
