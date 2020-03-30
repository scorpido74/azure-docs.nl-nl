---
title: API-server geautoriseerde IP-bereiken in Azure Kubernetes Service (AKS)
description: Meer informatie over het beveiligen van uw cluster met behulp van een IP-adresbereik voor toegang tot de API-server in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 593f9e0b335e6f4d62c76ce92f833ff4e9143372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126615"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Beveiligde toegang tot de API-server met geautoriseerde IP-adresbereiken in Azure Kubernetes Service (AKS)

In Kubernetes ontvangt de API-server aanvragen om acties in het cluster uit te voeren, bijvoorbeeld om resources te maken of het aantal knooppunten te schalen. De API-server is de centrale manier om met een cluster te communiceren en te beheren. Om de clusterbeveiliging te verbeteren en aanvallen te minimaliseren, moet de API-server alleen toegankelijk zijn vanuit een beperkt aantal IP-adresbereiken.

In dit artikel ziet u hoe u door API-server geautoriseerde IP-adresbereiken gebruiken om te beperken welke IP-adressen en CIDRs toegang hebben tot het controlevlak.

> [!IMPORTANT]
> Op nieuwe clusters worden door API-server geautoriseerde IP-adresbereiken alleen ondersteund op de *standaard* SKU-loadbalancer. Bestaande clusters met de basissKU-load balancer en API-server geconfigureerde IP-adresbereiken blijven werken zoals het is, maar kunnen niet worden gemigreerd naar een *standaard* SKU-loadbalancer. *Basic* Deze bestaande clusters blijven ook werken als hun Kubernetes-versie of -controlevlak wordt bijgewerkt.

## <a name="before-you-begin"></a>Voordat u begint

Door API-servers geautoriseerde IP-bereiken werken alleen voor nieuwe AKS-clusters die u maakt. In dit artikel ziet u hoe u een AKS-cluster maakt met de Azure CLI.

U moet de Azure CLI-versie 2.0.76 of hoger installeren en configureren. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Overzicht van door API-server geautoriseerde IP-bereiken

De Kubernetes API-server is hoe de onderliggende Kubernetes API's worden blootgesteld. Deze component biedt de interactie voor `kubectl` beheertools, zoals of het Kubernetes-dashboard. AKS biedt een clustermaster met één tenant, met een speciale API-server. Standaard krijgt de API-server een openbaar IP-adres toegewezen en moet u de toegang beheren met behulp van RBAC (Role-based access controls).

Om de toegang tot de anders openbaar toegankelijke AKS-besturingsvlak / API-server te beveiligen, u geautoriseerde IP-bereiken inschakelen en gebruiken. Deze geautoriseerde IP-bereiken staan alleen gedefinieerde IP-adresbereiken toe om met de API-server te communiceren. Een verzoek aan de API-server vanaf een IP-adres dat geen deel uitmaakt van deze geautoriseerde IP-bereiken, wordt geblokkeerd. Blijf RBAC gebruiken om gebruikers en de acties die ze aanvragen te autoriseren.

Zie [Kubernetes-kernconcepten voor AKS voor][concepts-clusters-workloads]meer informatie over de API-server en andere clustercomponenten.

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Een AKS-cluster maken met API-server geautoriseerde IP-bereiken ingeschakeld

Api-servergeautoriseerde IP-bereiken werken alleen voor nieuwe AKS-clusters. Maak een cluster met behulp van de [az aks maken][az-aks-create] en geef de *--api-server-geautoriseerde-ip-ranges* parameter om een lijst van geautoriseerde IP-adres bereiken. Deze IP-adresbereiken zijn meestal adresbereiken die worden gebruikt door uw on-premises netwerken of openbare IP-ip-ip-ip-netwerken. Wanneer u een CIDR-bereik opgeeft, begint u met het eerste IP-adres in het bereik. *137.117.106.90/29* is bijvoorbeeld een geldig bereik, maar zorg ervoor dat u het eerste IP-adres in het bereik opgeeft, zoals *137.117.106.88/29*.

> [!IMPORTANT]
> Standaard gebruikt uw cluster de [Standaard SKU-loadbalancer][standard-sku-lb] waarmee u de uitgaande gateway configureren. Wanneer u API-servergeautoriseerde IP-bereiken inschakelt tijdens het maken van het cluster, is het openbare IP voor uw cluster standaard ook standaard toegestaan naast de bereiken die u opgeeft. Als u *''* of geen waarde opgeeft voor *--api-server-authorized-ip-ranges,* worden api-servergeautoriseerde IP-bereiken uitgeschakeld.

In het volgende voorbeeld wordt een cluster met één knooppunt met de naam *myAKSCluster* gemaakt in de brongroep *myResourceGroup* met API-server geautoriseerde IP-bereiken ingeschakeld. De toegestane IP-adresbereiken zijn *73.140.245.0/24:*

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> U moet deze bereiken toevoegen aan een lijst met toegestane bepalingen:
> - Het openbare IP-adres van de firewall
> - Elk bereik dat netwerken vertegenwoordigt waarvan u het cluster beheert
> - Als u Azure Dev Spaces gebruikt op uw AKS-cluster, moet u extra bereiken toestaan [op basis van uw regio.][dev-spaces-ranges]

> De bovengrens voor het aantal IP-bereiken dat u opgeven is 3500. 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>De uitgaande IP's opgeven voor de standaard SKU-loadbalancer

Bij het maken van een AKS-cluster, als u de uitgaande IP-adressen of voorvoegsels voor het cluster opgeeft, zijn deze adressen of voorvoegsels ook toegestaan. Bijvoorbeeld:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

In het bovenstaande voorbeeld zijn alle IP's in de parameter *--load-balancer-outbound-ip-prefixes* toegestaan, samen met de IP's in de parameter *--api-server-authorized-ip-ranges.*

U ook de parameter *--load-balancer-outbound-ip-prefixes* opgeven om ip-voorvoegsels voor uitgaande load balancer toe te staan.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Alleen het uitgaande openbare IP van de Standaard SKU-loadbalancer toestaan

Wanneer u API-servergeautoriseerde IP-bereiken inschakelt tijdens het maken van het cluster, is het uitgaande openbare IP-adres voor de standaard SKU-loadbalancer voor uw cluster standaard ook standaard toegestaan, naast de bereiken die u opgeeft. Als u alleen het uitgaande openbare IP van de Standaard SKU-load balancer wilt toestaan, gebruikt u *0.0.0.0/32* bij het opgeven van de parameter *--api-server-authorized-ip-ranges.*

In het volgende voorbeeld is alleen het uitgaande openbare IP van de Standaard SKU-loadbalancer toegestaan en hebt u alleen toegang tot de API-server vanaf de knooppunten binnen het cluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>De door een cluster geautoriseerde IP-bereiken van een cluster bijwerken

Als u de door api-server geautoriseerde IP-bereiken op een bestaand cluster wilt bijwerken, [gebruikt][az-aks-update] u de opdracht *--api-server-authorized-ip-ranges*, *--load-balancer-outbound-ip-prefixes*, *--load-balancer-outbound-ips*, of *--load-balancer-outbound-ip-prefixes.*

In het volgende voorbeeld worden API-servergeautoriseerde IP-bereiken bijgewerkt op het cluster met de naam *myAKSCluster* in de brongroep *myResourceGroup*. Het toe te staan IP-adresbereik is *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

U ook *0.0.0.0/32* gebruiken bij het opgeven van de parameter *--api-server-authorized-ip-ranges* om alleen het openbare IP-adres van de standaard SKU-load balancer toe te staan.

## <a name="disable-authorized-ip-ranges"></a>Geautoriseerde IP-bereiken uitschakelen

Als u geautoriseerde IP-bereiken wilt uitschakelen, gebruikt u [de AZ Aks-update][az-aks-update] en geeft u een leeg bereik op om de door API-server geautoriseerde IP-bereiken uit te schakelen. Bijvoorbeeld:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u api-servergeautoriseerde IP-bereiken ingeschakeld. Deze aanpak is een onderdeel van hoe u een beveiligd AKS-cluster uitvoeren.

Zie [Beveiligingsconcepten voor toepassingen en clusters in AKS en][concepts-security] [Best practices voor clusterbeveiliging en upgrades in AKS voor][operator-best-practices-cluster-security]meer informatie.

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
