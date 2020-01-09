---
title: Door API server geautoriseerde IP-bereiken in azure Kubernetes service (AKS)
description: Meer informatie over het beveiligen van uw cluster met behulp van een IP-adres bereik voor toegang tot de API-server in azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/05/2019
ms.author: mlearned
ms.openlocfilehash: 5f3e6cf8c5de8d5f3de17ad0b5d4bb4c004c06df
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442981"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Veilige toegang tot de API-server met behulp van geautoriseerde IP-adresbereiken in azure Kubernetes service (AKS)

In Kubernetes ontvangt de API-server aanvragen voor het uitvoeren van acties in het cluster, zoals het maken van resources of het schalen van het aantal knoop punten. De API-server is de centrale manier om met een cluster te communiceren en te beheren. Als u de beveiliging van het cluster wilt verbeteren en aanvallen wilt minimaliseren, moet de API-server alleen toegankelijk zijn vanuit een beperkt aantal IP-adresbereiken.

Dit artikel laat u zien hoe u met de API-server geautoriseerde IP-adresbereiken kunt beperken welke IP-adressen en CIDRs het beheer vlak kunnen hebben.

> [!IMPORTANT]
> Voor nieuwe clusters worden geautoriseerde IP-adresbereiken van de API-server alleen ondersteund op de *standaard* -SKU Load Balancer. Bestaande clusters met de *basis* -SKU Load Balancer en de door de API server geautoriseerde IP-adresbereiken blijven werken, maar kunnen niet worden gemigreerd naar een *standaard* -SKU Load Balancer. Deze bestaande clusters blijven werken als hun Kubernetes-versie of besturings vlak wordt geüpgraded.

## <a name="before-you-begin"></a>Voordat u begint

Door de API-server geautoriseerde IP-adresbereiken werken alleen voor nieuwe AKS-clusters die u maakt. In dit artikel wordt beschreven hoe u een AKS-cluster maakt met behulp van de Azure CLI.

U moet de Azure CLI-versie 2.0.76 of hoger hebben geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u wilt installeren of upgraden, raadpleegt u [Azure cli installeren][install-azure-cli].

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Overzicht van door de API server geautoriseerde IP-adresbereiken

De Kubernetes API-server is hoe de onderliggende Kubernetes-Api's worden weer gegeven. Dit onderdeel biedt de interactie voor beheer hulpprogramma's, zoals `kubectl` of het Kubernetes-dash board. AKS biedt een cluster Master met één Tenant, met een speciale API-server. De API-server krijgt standaard een openbaar IP-adres en u moet de toegang beheren met behulp van op rollen gebaseerd toegangs beheer (RBAC).

Als u de toegang tot de anderszins toegankelijke AKS Control vlak/API-server wilt beveiligen, kunt u geautoriseerde IP-bereiken inschakelen en gebruiken. Met deze geautoriseerde IP-bereiken zijn alleen gedefinieerde IP-adresbereiken toegestaan om te communiceren met de API-server. Een aanvraag naar de API-server vanaf een IP-adres dat geen deel uitmaakt van deze toegestane IP-bereiken, wordt geblokkeerd. Blijf RBAC gebruiken om gebruikers en de acties die ze aanvragen te autoriseren.

Zie [Kubernetes core-concepten voor AKS][concepts-clusters-workloads]voor meer informatie over de API-server en andere cluster onderdelen.

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Een AKS-cluster maken waarvoor een API-server geautoriseerde IP-adresbereiken is ingeschakeld

Door API server geautoriseerde IP-adresbereiken werken alleen voor nieuwe AKS-clusters. Maak een cluster met de para meter [AZ AKS Create][az-aks-create] en geef de *--API-server-Authorized-IP-Ranges* op om een lijst met geautoriseerde IP-adresbereiken op te geven. Deze IP-adresbereiken zijn meestal adresbereiken die worden gebruikt door uw on-premises netwerken of open bare Ip's. Wanneer u een CIDR-bereik opgeeft, begint u met het eerste IP-adres in het bereik. *137.117.106.90/29* is bijvoorbeeld een geldig bereik, maar zorg ervoor dat u het eerste IP-adres in het bereik opgeeft, zoals *137.117.106.88/29*.

> [!IMPORTANT]
> Uw cluster maakt standaard gebruik van de [standaard-SKU Load Balancer][standard-sku-lb] die u kunt gebruiken om de uitgaande gateway te configureren. Wanneer u toegestane IP-bereiken van de API-server inschakelt tijdens het maken van het cluster, is het open bare IP-adres voor uw cluster standaard ook toegestaan naast de bereiken die u opgeeft. Als u *' '* of geen waarde opgeeft voor *--API-server-geautoriseerde IP-bereiken*, wordt de API-server geautoriseerde IP-adresbereiken uitgeschakeld.

In het volgende voor beeld wordt een cluster met één knoop punt met de naam *myAKSCluster* gemaakt in de resource groep met de naam *myResourceGroup* waarvoor geautoriseerde IP-bereiken voor de API-server zijn ingeschakeld. De toegestane IP-adresbereiken zijn *73.140.245.0/24*:

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
> Voeg deze bereiken toe aan een acceptatie lijst:
> - Het open bare IP-adres van de firewall
> - Elk bereik dat netwerken vertegenwoordigt waarvan u het cluster beheert
> - Als u Azure dev Spaces op uw AKS-cluster gebruikt, moet u [extra bereiken op basis van uw regio][dev-spaces-ranges]toestaan.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Geef de uitgaande Ip's op voor de standaard-SKU load balancer

Als u een AKS-cluster maakt en u de uitgaande IP-adressen of voor voegsels voor het cluster opgeeft, worden deze adressen of voor voegsels ook toegestaan. Bijvoorbeeld:

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

In het bovenstaande voor beeld zijn alle IP-adressen die zijn opgenomen in de para meter *--Load-Balancer-uitgaand-IP-voor voegsels* , toegestaan in combi natie met de IP-adressen in de para meter *--API-server-Authorized-IPSec-Ranges* .

U kunt ook de para meter *--Load-Balancer-uitgaand-IP-voor voegsels* opgeven om uitgaande Load Balancer IP-voor voegsels toe te staan.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Alleen het uitgaande open bare IP-adres van de standaard-SKU toestaan load balancer

Wanneer u toegestane IP-bereiken van de API-server inschakelt tijdens het maken van het cluster, is het uitgaande open bare IP-adres voor de standaard SKU-load balancer voor uw cluster ook standaard toegestaan naast de bereiken die u opgeeft. Als u alleen het uitgaande open bare IP-adres van de standaard-SKU load balancer wilt toestaan, gebruikt u *0.0.0.0/32* bij het opgeven van de para meter *--API-server-Authorized-IP-bereiken* .

In het volgende voor beeld is alleen het uitgaande open bare IP-adres van de standaard-SKU-load balancer toegestaan en u hebt alleen toegang tot de API-server vanuit de knoop punten in het cluster.

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

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>De door de API-server geautoriseerde IP-adresbereiken van een cluster bijwerken

Als u de door de API-server geautoriseerde IP-adresbereiken op een bestaand cluster wilt bijwerken, gebruikt u [AZ AKS update][az-aks-update] Command en gebruikt u de para meter *--API-server-Authorized-IP-Ranges*,-- *Load-Balancer-uitgaand-IP-voor voegsels*, *--Load-* Balancer-outbound-IP- *Preloads* .

In het volgende voor beeld worden de geautoriseerde IP-adresbereiken van de API-server bijgewerkt in het cluster met de naam *myAKSCluster* in de resource groep genaamd *myResourceGroup*. Het IP-adres bereik dat moet worden geautoriseerd, is *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

U kunt ook *0.0.0.0/32* gebruiken bij het opgeven van de para meter *--API-server-Authorized-IP-Ranges* om alleen het open bare IP-adres van de standaard-SKU Load Balancer toe te staan.

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
