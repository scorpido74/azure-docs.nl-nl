---
title: Door API server geautoriseerde IP-bereiken in azure Kubernetes service (AKS)
description: Meer informatie over het beveiligen van uw cluster met behulp van een IP-adres bereik voor toegang tot de API-server in azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8418499cc3e094162ac7483aaa6c71e74db95ae1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472955"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Veilige toegang tot de API-server met behulp van geautoriseerde IP-adresbereiken in azure Kubernetes service (AKS)

In Kubernetes ontvangt de API-server aanvragen voor het uitvoeren van acties in het cluster, zoals het maken van resources of het schalen van het aantal knoop punten. De API-server is de centrale manier om met een cluster te communiceren en te beheren. Als u de beveiliging van het cluster wilt verbeteren en aanvallen wilt minimaliseren, moet de API-server alleen toegankelijk zijn vanuit een beperkt aantal IP-adresbereiken.

Dit artikel laat u zien hoe u met de API-server geautoriseerde IP-adresbereiken kunt beperken welke IP-adressen en CIDRs het beheer vlak kunnen hebben.

> [!IMPORTANT]
> Voor nieuwe clusters worden geautoriseerde IP-adresbereiken van de API-server alleen ondersteund op de *standaard* -SKU Load Balancer. Bestaande clusters met de *basis* -SKU Load Balancer en de geconfigureerde IP-adresbereiken van de API-server blijven werken zoals u dat wilt. Deze bestaande clusers kunnen ook worden bijgewerkt en blijven werken.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u werkt met clusters die gebruikmaken van [kubenet][kubenet].  Met [Azure container Networking interface (cni)][cni-networking] gebaseerde clusters hebt u niet de vereiste route tabel nodig om de toegang te beveiligen.  U moet de route tabel hand matig maken.  Zie [route tabellen beheren](https://docs.microsoft.com/azure/virtual-network/manage-route-table) voor meer informatie.

Door de API-server geautoriseerde IP-adresbereiken werken alleen voor nieuwe AKS-clusters die u maakt. In dit artikel wordt beschreven hoe u een AKS-cluster maakt met behulp van de Azure CLI.

U moet de Azure CLI-versie 2.0.76 of hoger hebben geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u wilt installeren of upgraden, raadpleegt u [Azure cli installeren][install-azure-cli].

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u de API-server geautoriseerde IP-bereiken configureert:

* U kunt momenteel geen Azure dev-ruimten gebruiken omdat de communicatie met de API-server ook wordt geblokkeerd.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Overzicht van door de API server geautoriseerde IP-adresbereiken

De Kubernetes API-server is hoe de onderliggende Kubernetes-Api's worden weer gegeven. Dit onderdeel biedt de interactie voor beheer hulpprogramma's, zoals `kubectl` of het Kubernetes-dash board. AKS biedt een cluster Master met één Tenant, met een speciale API-server. De API-server krijgt standaard een openbaar IP-adres en u moet de toegang beheren met behulp van op rollen gebaseerd toegangs beheer (RBAC).

Als u de toegang tot de anderszins toegankelijke AKS Control vlak/API-server wilt beveiligen, kunt u geautoriseerde IP-bereiken inschakelen en gebruiken. Met deze geautoriseerde IP-bereiken zijn alleen gedefinieerde IP-adresbereiken toegestaan om te communiceren met de API-server. Een aanvraag naar de API-server vanaf een IP-adres dat geen deel uitmaakt van deze toegestane IP-bereiken, wordt geblokkeerd. U moet RBAC blijven gebruiken om gebruikers en de acties die ze aanvragen te autoriseren.

Zie [Kubernetes core-concepten voor AKS][concepts-clusters-workloads]voor meer informatie over de API-server en andere cluster onderdelen.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Door API server geautoriseerde IP-adresbereiken werken alleen voor nieuwe AKS-clusters. Het is niet mogelijk om gemachtigde IP-bereiken in te scha kelen als onderdeel van de bewerking cluster create. Als u probeert toegestane IP-bereiken in te scha kelen als onderdeel van het cluster proces maken, hebben de cluster knooppunten tijdens de implementatie geen toegang tot de API-server omdat het uitgaande IP-adres op dat moment niet is gedefinieerd.

Maak eerst een cluster met behulp van de opdracht [AZ AKS Create][az-aks-create] . In het volgende voor beeld wordt een cluster met één knoop punt met de naam *myAKSCluster* gemaakt in de resource groep met de naam *myResourceGroup*.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-cluster-with-authorized-ip-ranges"></a>Cluster bijwerken met geautoriseerde IP-bereiken

Uw cluster maakt standaard gebruik van de [standaard-SKU Load Balancer][standard-sku-lb], die u kunt gebruiken om de uitgaande gateway te configureren. Gebruik [AZ Network Public-IP List][az-network-public-ip-list] en geef de resource groep van uw AKS-cluster op, dat meestal begint met *MC_* . Hiermee wordt het open bare IP-adres voor uw cluster weer gegeven, dat u kunt toestaan. Gebruik de opdracht [AZ AKS update][az-aks-update] en geef de para meter *--API-server-Authorized-IP-Ranges* op om het IP-adres van het cluster toe te staan. Bijvoorbeeld:

```azurecli-interactive
RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSCluster
```

Als u geautoriseerde IP-adresbereiken voor de API-server wilt inschakelen, gebruikt u [AZ AKS update][az-aks-update] Command en geeft u de para meter *--API-server-Authorized-IP-Ranges* op om een lijst met geautoriseerde IP-adresbereiken op te geven. Deze IP-adresbereiken zijn meestal adresbereiken die worden gebruikt door uw on-premises netwerken of open bare Ip's. Wanneer u een CIDR-bereik opgeeft, begint u met het eerste IP-adres in het bereik. *137.117.106.90/29* is bijvoorbeeld een geldig bereik, maar zorg ervoor dat u het eerste IP-adres in het bereik opgeeft, zoals *137.117.106.88/29*.

In het volgende voor beeld worden geautoriseerde IP-adresbereiken van de API-server ingeschakeld in het cluster met de naam *myAKSCluster* in de resource groep genaamd *myResourceGroup*. De IP-adresbereiken die u wilt autoriseren, zijn *172.0.0.0/16* (adres bereik pod/nodes) en *168.10.0.0/18* (ServiceCidr):

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 172.0.0.0/16,168.10.0.0/18
```

> [!NOTE]
> Voeg deze bereiken toe aan een acceptatie lijst:
> - Het open bare IP-adres van de firewall
> - De service-CIDR
> - Het adres bereik voor de subnetten, met de knoop punten en het Peul
> - Elk bereik dat netwerken vertegenwoordigt waarvan u het cluster beheert

## <a name="disable-authorized-ip-ranges"></a>Toegestane IP-bereiken uitschakelen

Als u gemachtigde IP-bereiken wilt uitschakelen, gebruikt u [AZ AKS update][az-aks-update] en geeft u een leeg bereik op om IP-adresbereiken met de API-server uit te scha kelen. Bijvoorbeeld:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de API-server geautoriseerde IP-adresbereiken ingeschakeld. Deze benadering is een onderdeel van hoe u een veilig AKS-cluster kunt uitvoeren.

Zie voor meer informatie [beveiligings concepten voor toepassingen en clusters in AKS][concepts-security] en [Aanbevolen procedures voor het beveiligen van het cluster en upgrades in AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[standard-sku-lb]: load-balancer-standard.md
