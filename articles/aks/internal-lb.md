---
title: Een interne load balancer maken
titleSuffix: Azure Kubernetes Service
description: Leer hoe u een interne load balancer maakt en gebruikt om uw services beschikbaar te maken met Azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 0789a866ebda270f3e5e8b150e072c7aedea7f04
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790606"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Een interne load balancer gebruiken met Azure Kubernetes service (AKS)

Als u de toegang tot uw toepassingen in azure Kubernetes service (AKS) wilt beperken, kunt u een interne load balancer maken en gebruiken. Een interne load balancer maakt een Kubernetes-service alleen toegankelijk voor toepassingen die worden uitgevoerd in hetzelfde virtuele netwerk als het Kubernetes-cluster. In dit artikel wordt beschreven hoe u een intern load balancer maakt en gebruikt met Azure Kubernetes service (AKS).

> [!NOTE]
> Azure Load Balancer is beschikbaar in twee Sku's: *Basic* en *Standard*. Standaard wordt de standaard-SKU gebruikt wanneer u een AKS-cluster maakt.  Bij het maken van een service met het type Load Balancer krijgt u hetzelfde LB-type als bij het inrichten van het cluster. Zie [vergelijking van Azure load BALANCER sku's][azure-lb-comparison]voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

Ook moet de Azure CLI-versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

De AKS-Cluster service-principal moet machtigingen hebben om netwerk bronnen te beheren als u een bestaand subnet of een bestaande resource groep gebruikt. In het algemeen wijst u de rol *netwerk bijdrage* toe aan uw service-principal op de gedelegeerde resources. In plaats van een Service-Principal kunt u de door het systeem toegewezen beheerde identiteit voor machtigingen gebruiken. Zie [beheerde identiteiten gebruiken](use-managed-identity.md)voor meer informatie. Zie [AKS toegang tot andere Azure-resources delegeren][aks-sp]voor meer informatie over machtigingen.

## <a name="create-an-internal-load-balancer"></a>Een interne load balancer maken

Als u een interne load balancer wilt maken, maakt u een `internal-lb.yaml` service manifest met de naam met de *LoadBalancer* van het Service type en de *Azure-Load Balancer-interne* aantekening, zoals wordt weer gegeven in het volgende voor beeld:

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

Implementeer de interne load balancer met behulp van de [kubectl-toepassing][kubectl-apply] en geef de naam van uw yaml-manifest op:

```console
kubectl apply -f internal-lb.yaml
```

Er wordt een Azure-load balancer gemaakt in de knooppunt resource groep en verbonden met hetzelfde virtuele netwerk als het AKS-cluster.

Wanneer u de service details bekijkt, wordt het IP-adres van de interne load balancer weer gegeven in de kolom *extern-IP* . In deze context bevindt *extern* zich ten opzichte van de externe interface van de Load Balancer, niet dat het een openbaar, extern IP-adres ontvangt. Het kan een paar minuten duren voordat het IP-adres is gewijzigd van * \<in\> behandeling* naar een effectief intern IP-adres, zoals wordt weer gegeven in het volgende voor beeld:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Geef een IP-adres op

Als u een specifiek IP-adres met de interne load balancer wilt gebruiken, voegt u de eigenschap *loadBalancerIP* toe aan het yaml-manifest van de Load Balancer. Het opgegeven IP-adres moet zich in hetzelfde subnet bevinden als het AKS-cluster en mag niet al zijn toegewezen aan een resource.

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

Wanneer u de service Details implementeert en bekijkt, wordt het IP-adres in de kolom *extern-IP* weer gegeven voor het opgegeven IP-adres:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Particuliere netwerken gebruiken

Wanneer u uw AKS-cluster maakt, kunt u geavanceerde netwerk instellingen opgeven. Met deze aanpak kunt u het cluster implementeren in een bestaand virtueel Azure-netwerk en-subnetten. Eén scenario is het implementeren van uw AKS-cluster in een particulier netwerk dat is verbonden met uw on-premises omgeving en voor het uitvoeren van services die alleen intern toegankelijk zijn. Zie uw eigen virtuele netwerk subnetten configureren met [Kubenet][use-kubenet] of [Azure cni][advanced-networking]voor meer informatie.

Er zijn geen wijzigingen in de vorige stappen nodig om een interne load balancer te implementeren in een AKS-cluster dat gebruikmaakt van een particulier netwerk. De load balancer wordt gemaakt in dezelfde resource groep als uw AKS-cluster, maar is verbonden met uw particuliere virtuele netwerk en subnet, zoals wordt weer gegeven in het volgende voor beeld:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Mogelijk moet u de service-principal voor uw AKS-cluster de rol *Network contributor* verlenen aan de resource groep waar uw virtuele Azure-netwerk resources zijn geïmplementeerd. Bekijk de service-principal met [AZ AKS show][az-aks-show], zoals `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Als u een roltoewijzing wilt maken, gebruikt u de opdracht [AZ Role Assignment Create][az-role-assignment-create] .

## <a name="specify-a-different-subnet"></a>Geef een ander subnet op

Als u een subnet voor uw load balancer wilt opgeven, voegt u de *Azure-Load-Balancer-interne-subnet* -aantekening toe aan uw service. Het opgegeven subnet moet zich in hetzelfde virtuele netwerk bevinden als uw AKS-cluster. Bij implementatie is het load balancer *externe-IP-* adres onderdeel van het opgegeven subnet.

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

Wanneer alle services die gebruikmaken van de interne load balancer worden verwijderd, wordt de load balancer zelf ook verwijderd.

U kunt een service ook rechtstreeks verwijderen, net als bij elke Kubernetes-resource `kubectl delete service internal-app`, zoals, waardoor de onderliggende Azure-Load Balancer worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Kubernetes Services vindt u in de [documentatie van Kubernetes Services][kubernetes-services].

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/skus.md
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
