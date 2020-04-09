---
title: Statische IP gebruiken met load balancer
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het maken en gebruiken van een statisch IP-adres met de Azure Kubernetes Service (AKS) load balancer.
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 5051232f29ad51d9fee893a4a660fc81f6e60d77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886735"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Een statisch openbaar IP-adres en DNS-label gebruiken met de Laadbalans (Azure Kubernetes Service)

Het openbare IP-adres dat is toegewezen aan een load balancer-bron die is gemaakt door een AKS-cluster, is standaard alleen geldig voor de levensduur van die bron. Als u de Kubernetes-service verwijdert, worden ook de bijbehorende load balancer en IP-adres verwijderd. Als u een specifiek IP-adres wilt toewijzen of een IP-adres wilt behouden voor opnieuw geïmplementeerde Kubernetes-services, u een statisch openbaar IP-adres maken en gebruiken.

In dit artikel ziet u hoe u een statisch openbaar IP-adres maakt en het aan uw Kubernetes-service toewijst.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

U hebt ook de Azure CLI-versie 2.0.59 of hoger geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

Dit artikel gaat over het gebruik van een *Standaard* SKU IP met een *Standaard* SKU load balancer. Zie [IP-adrestypen en toewijzingsmethoden in Azure][ip-sku]voor meer informatie.

## <a name="create-a-static-ip-address"></a>Een statisch IP-adres maken

Maak een statisch openbaar IP-adres met de openbare [ip-opdracht voor het az-netwerk.][az-network-public-ip-create] Als volgt wordt een statische IP-bron met de naam *myAKSPublicIP* gemaakt in de *brongroep myResourceGroup:*

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Als u een *Basic* SKU-loadbalancer gebruikt in uw AKS-cluster, gebruikt u *Basis* voor de *parameter sku* bij het definiëren van een openbaar IP-adres. Alleen *Basic* SKU IP's werken met de *Basic* SKU load balancer en alleen *Standard* SKU IP's werken met *standaard* SKU load balancers. 

Het IP-adres wordt weergegeven, zoals weergegeven in de volgende verkorte voorbeelduitvoer:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

U later het openbare IP-adres krijgen met behulp van de opdracht [openbare-ip-lijst van het AZ-netwerk.][az-network-public-ip-list] Geef de naam op van de knooppuntbrongroep en het openbare IP-adres dat u hebt gemaakt en query voor het *ipAdres* zoals weergegeven in het volgende voorbeeld:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Een service maken met het statische IP-adres

Voordat u een service maakt, moet u ervoor zorgen dat de serviceprincipal die wordt gebruikt door het AKS-cluster machtigingen heeft gedelegeerd aan de andere resourcegroep. Bijvoorbeeld:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

U ook de beheerde identiteit met het systeem gebruiken voor machtigingen in plaats van de serviceprincipal. Zie [Beheerde identiteiten gebruiken voor](use-managed-identity.md)meer informatie .

Als u een *LoadBalancer-service* wilt maken `loadBalancerIP` met het statische openbare IP-adres, voegt u de eigenschap en de waarde van het statische openbare IP-adres toe aan het YAML-manifest. Maak een `load-balancer-service.yaml` bestand met de naam en kopie in de volgende YAML. Geef uw eigen openbare IP-adres op dat in de vorige stap is gemaakt. In het volgende voorbeeld wordt ook de annotatie ingesteld op de resourcegroep met de naam *myResourceGroup*. Geef uw eigen naam van de resourcegroep op.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Maak de service en `kubectl apply` implementatie met de opdracht.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Een DNS-label toepassen op de service

Als uw service een dynamisch of statisch openbaar IP-adres gebruikt, u de service-annotatie `service.beta.kubernetes.io/azure-dns-label-name` gebruiken om een openbaar DNS-label in te stellen. Hiermee wordt een volledig gekwalificeerde domeinnaam voor uw service gepubliceerd met behulp van de openbare DNS-servers en het domein op het hoogste niveau van Azure. De annotatiewaarde moet uniek zijn binnen de Azure-locatie, dus het wordt aanbevolen om een voldoende gekwalificeerd label te gebruiken.   

Azure voegt dan automatisch een standaardsubnet `<location>.cloudapp.azure.com` toe, zoals (waar locatie het geselecteerde gebied is), aan de door u opteerde naam om de volledig gekwalificeerde DNS-naam te maken. Bijvoorbeeld:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> Zie [Azure DNS][azure-dns-zone] en het [externe dns-project][external-dns] om de service op uw eigen domein te publiceren.

## <a name="troubleshoot"></a>Problemen oplossen

Als het statische IP-adres dat is gedefinieerd in de eigenschap *loadBalancerIP* van het Kubernetes-servicemanifest niet bestaat of niet is gemaakt in de knooppuntbrongroep en er geen extra delegaties is geconfigureerd, mislukt de taakverzamelingsservicecreatie. Als u problemen wilt oplossen, controleert u de gebeurtenissen voor het maken van services met de opdracht [kubectl-beschrijving.][kubectl-describe] Geef de naam van de service op zoals gespecificeerd in het YAML-manifest, zoals in het volgende voorbeeld wordt weergegeven:

```console
kubectl describe service azure-load-balancer
```

Er wordt informatie weergegeven over de Kubernetes-servicebron. De *gebeurtenissen* aan het einde van de volgende voorbeelduitvoer geven aan dat de *door de gebruiker opgegeven IP-adres niet is gevonden*. Controleer in deze scenario's of u het statische openbare IP-adres in de knooppuntbrongroep hebt gemaakt en of het IP-adres dat is opgegeven in het Kubernetes-servicemanifest juist is.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Volgende stappen

Voor extra controle over het netwerkverkeer naar uw toepassingen u in plaats daarvan [een invallende controller maken.][aks-ingress-basic] U ook [een invallende controller maken met een statisch openbaar IP-adres.][aks-static-ingress]

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
