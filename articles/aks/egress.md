---
title: Statisch IP gebruiken voor uitgangsverkeer
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het maken en gebruiken van een statisch openbaar IP-adres voor het uitreizigers verkeer in een AKS-cluster (Azure Kubernetes Service)
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 08a9682434605fffde73c835e7a9e9d6971d7ff0
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803379"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Een statisch openbaar IP-adres gebruiken voor uitgaande verkeer in Azure Kubernetes Service (AKS)

Standaard wordt het IP-adres van een Azure Kubernetes Service (AKS) willekeurig toegewezen. Deze configuratie is niet ideaal wanneer u een IP-adres moet identificeren voor bijvoorbeeld toegang tot externe services. In plaats daarvan moet u mogelijk een statisch IP-adres toewijzen dat op de witte lijst kan worden weergegeven voor servicetoegang.

In dit artikel ziet u hoe u een statisch openbaar IP-adres maakt en gebruikt voor gebruik met uitgangsverkeer in een AKS-cluster.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

U hebt ook de Azure CLI-versie 2.0.59 of hoger geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

## <a name="egress-traffic-overview"></a>Overzicht van het verkeer van Uitgang

Uitgaand verkeer van een AKS-cluster volgt [Azure Load Balancer-conventies.][outbound-connections] Voordat de eerste Kubernetes-service van het type `LoadBalancer` wordt gemaakt, maken de agentknooppunten in een AKS-cluster geen deel uit van een Azure Load Balancer-groep. In deze configuratie hebben de knooppunten geen openbaar IP-adres op instantieniveau. Azure vertaalt de uitgaande stroom naar een IP-adres van een openbare bron dat niet configureerbaar of deterministisch is.

Zodra een Kubernetes-service van het type `LoadBalancer` is gemaakt, worden agentknooppunten toegevoegd aan een Azure Load Balancer-groep. Voor uitgaande stroom vertaalt Azure dit naar het eerste openbare IP-adres dat is geconfigureerd op de load balancer. Dit openbare IP-adres is alleen geldig voor de levensduur van die bron. Als u de Kubernetes LoadBalancer-service verwijdert, worden ook de bijbehorende load balancer en IP-adres verwijderd. Als u een specifiek IP-adres wilt toewijzen of een IP-adres wilt behouden voor opnieuw geïmplementeerde Kubernetes-services, u een statisch openbaar IP-adres maken en gebruiken.

## <a name="create-a-static-public-ip"></a>Een statisch openbaar IP-adres maken

Download de naam van de resourcegroep met `--query nodeResourceGroup` de opdracht [az aks show][az-aks-show] en voeg de queryparameter toe. In het volgende voorbeeld wordt de knooppuntbrongroep voor de AKS-clusternaam *myAKSCluster* in de naam *myResourceGroup*van de resourcegroep:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Maak nu een statisch openbaar IP-adres met de [openbare ip-opdracht voor az-netwerk.][az-network-public-ip-create] Geef de naam van de knooppuntbrongroep op die in de vorige opdracht is verkregen en vervolgens een naam voor de IP-adresbron, zoals *myAKSPublicIP:*

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Het IP-adres wordt weergegeven, zoals wordt weergegeven in de volgende verkorte voorbeelduitvoer:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

U later het openbare IP-adres krijgen met behulp van de opdracht [openbare-ip-lijst van het AZ-netwerk.][az-network-public-ip-list] Geef de naam van de knooppuntbrongroep op en query voor het *ipAdres* zoals weergegeven in het volgende voorbeeld:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Een service maken met het statische IP-adres

Als u een service wilt maken met `loadBalancerIP` het statische openbare IP-adres, voegt u de eigenschap en de waarde van het statische openbare IP-adres toe aan het YAML-manifest. Maak een `egress-service.yaml` bestand met de naam en kopie in de volgende YAML. Geef uw eigen openbare IP-adres op dat in de vorige stap is gemaakt.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Maak de service en `kubectl apply` implementatie met de opdracht.

```console
kubectl apply -f egress-service.yaml
```

Met deze service configureert u een nieuw frontend-IP op de Azure Load Balancer. Als u geen andere IP's hebt geconfigureerd, moet **al** het uitgaande verkeer dit adres nu gebruiken. Wanneer meerdere adressen zijn geconfigureerd op de Azure Load Balancer, gebruikt uitgang het eerste IP op die load balancer.

## <a name="verify-egress-address"></a>Het uitgangsadres verifiëren

Als u wilt controleren of het statische openbare IP-adres wordt `checkip.dyndns.org`gebruikt, u dns-opzoekservice gebruiken, zoals.

Start en bevestig aan een *basisPod van Debian:*

```console
kubectl run -it --rm aks-ip --image=debian --generator=run-pod/v1
```

Als u een website wilt openen `apt-get` vanuit `curl` de container, gebruikt u deze om in de container te installeren.

```console
apt-get update && apt-get install curl -y
```

Gebruik nu krul om toegang te krijgen tot de *checkip.dyndns.org* site. Het IP-adres van uitgang wordt weergegeven, zoals weergegeven in de volgende voorbeelduitvoer. Dit IP-adres komt overeen met het statische openbare IP-adres dat is gemaakt en gedefinieerd voor de loadBalancer-service:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Volgende stappen

Om te voorkomen dat meerdere openbare IP-adressen op de Azure Load Balancer worden onderhouden, u in plaats daarvan een invallende controller gebruiken. Ingress-controllers bieden extra voordelen, zoals SSL/TLS-beëindiging, ondersteuning voor uri-herschrijft en upstream SSL/TLS-versleuteling. Zie [Een basiscontroller voor binnendringen maken in AKS][ingress-aks-cluster]voor meer informatie.

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
