---
title: Een standaard SKU-load balancer gebruiken in azure Kubernetes service (AKS)
description: Meer informatie over het gebruik van een load balancer met een standaard-SKU om uw services beschikbaar te maken met Azure Kubernetes service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 8ebd91f8f02ad7eacd8440b34a31b78f5cac5741
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472617"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Een standaard SKU-load balancer gebruiken in azure Kubernetes service (AKS)

Om toegang te bieden tot uw toepassingen in azure Kubernetes service (AKS), kunt u een Azure Load Balancer maken en gebruiken. Een load balancer die wordt uitgevoerd op AKS kan worden gebruikt als een intern of een extern load balancer. Een interne load balancer maakt een Kubernetes-service alleen toegankelijk voor toepassingen die worden uitgevoerd in hetzelfde virtuele netwerk als het AKS-cluster. Een externe load balancer ontvangt een of meer open bare Ip's voor binnenkomend verkeer en maakt een Kubernetes-service extern toegankelijk met behulp van de open bare Ip's.

Azure Load Balancer is beschikbaar in twee Sku's: *Basic* en *Standard*. Standaard wordt de *standaard* -SKU gebruikt wanneer u een AKS-cluster maakt. Het gebruik van een *standaard* -SKU Load Balancer biedt extra functies en functionaliteit, zoals een grotere back-end-pool grootte en Beschikbaarheidszones. Het is belang rijk dat u de verschillen tussen de load balancers *Standard* en *Basic* begrijpt voordat u kiest voor gebruik. Wanneer u een AKS-cluster hebt gemaakt, kunt u de load balancer SKU voor dat cluster niet wijzigen. Zie voor meer informatie over de *Basic* -en *Standard* -sku's [Azure Load Balancer SKU-vergelijking][azure-lb-comparison].

In dit artikel wordt ervan uitgegaan dat u basis informatie krijgt over Kubernetes-en Azure Load Balancer concepten. Zie [Kubernetes core-concepten voor Azure Kubernetes service (AKS)][kubernetes-concepts] en [wat Azure Load Balancer?][azure-lb]voor meer informatie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel de Azure CLI-versie 2.0.74 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="before-you-begin"></a>Voordat u begint
In dit artikel wordt ervan uitgegaan dat u een AKS-cluster hebt met de *standaard* SKU Azure Load Balancer. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

De AKS-Cluster service-principal moet ook machtigingen hebben om netwerk bronnen te beheren als u een bestaand subnet of een bestaande resource groep gebruikt. In het algemeen wijst u de rol *netwerk bijdrage* toe aan uw service-principal op de gedelegeerde resources. Zie [AKS toegang tot andere Azure-resources delegeren][aks-sp]voor meer informatie over machtigingen.

### <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die ondersteuning bieden voor een load balancer met de *standaard* -SKU:

* Er is ten minste één openbaar IP-of IP-voor voegsel vereist voor het toestaan van uitgaand verkeer van het AKS-cluster. Het open bare IP-of IP-voor voegsel is ook vereist voor het onderhouden van de connectiviteit tussen het besturings vlak en de agent knooppunten, en voor het behouden van de compatibiliteit met eerdere versies van AKS. U hebt de volgende opties voor het opgeven van open bare Ip's of IP-voor voegsels met een *standaard* -SKU Load Balancer:
    * Geef uw eigen open bare Ip's op.
    * Geef uw eigen open bare IP-voor voegsels op.
    * Geef een waarde op van Maxi maal 100 zodat het AKS-cluster ervoor kan zorgen dat veel *standaard* -SKU open bare ip's in dezelfde resource groep zijn gemaakt als het AKS-cluster, die meestal met *MC_* aan het begin wordt genoemd. AKS wijst het open bare IP-adres toe aan de *standaard* -SKU Load Balancer. Standaard wordt één openbaar IP-adres automatisch gemaakt in dezelfde resource groep als het AKS-cluster als er geen openbaar IP-adres, openbaar IP-voor voegsel of aantal Ip's is opgegeven. U moet ook open bare adressen toestaan en voor komen dat u een Azure Policy maakt waardoor het IP-adres niet kan worden gemaakt.
* Wanneer u de *standaard* -SKU voor een Load Balancer gebruikt, moet u Kubernetes versie 1,13 of hoger gebruiken.
* Het definiëren van de load balancer SKU kan alleen worden uitgevoerd wanneer u een AKS-cluster maakt. U kunt de load balancer SKU niet wijzigen nadat er een AKS-cluster is gemaakt.
* U kunt slechts één load balancer SKU in één cluster gebruiken.

## <a name="configure-the-load-balancer-to-be-internal"></a>Configureer de load balancer intern

U kunt ook configureren dat de load balancer intern is en geen openbaar IP-adres weergeeft. Als u de load balancer als intern wilt configureren, voegt u `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` als aantekening toe aan de *Load Balancer* -service. [Hier][internal-lb-yaml]ziet u een voor beeld van een yaml-manifest, evenals meer informatie over een intern Load Balancer.

## <a name="scale-the-number-of-managed-public-ips"></a>Het aantal beheerde open bare Ip's schalen

Wanneer u een *standaard* -SKU Load Balancer met beheerde uitgaande open bare ip's, die standaard worden gemaakt, kunt u het aantal beheerde uitgaande open bare ip's schalen met behulp van de para meter *Load-Balancer-managed-IP-Count* .

Voer de volgende opdracht uit om een bestaand cluster bij te werken. Deze para meter kan ook op een cluster worden ingesteld voor het maken van meerdere beheerde open bare IP-adressen.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

In het bovenstaande voor beeld wordt het aantal beheerde uitgaande open bare Ip's ingesteld op *2* voor het *myAKSCluster* -cluster in *myResourceGroup*. 

U kunt ook de para meter voor door het *netwerk met taak verdeling beheerde IP-aantal* gebruiken om het eerste aantal beheerde uitgaande open bare ip's in te stellen bij het maken van het cluster door de para meter `--load-balancer-managed-outbound-ip-count` toe te voegen en deze op de gewenste waarde in te stellen. Het standaard aantal beheerde uitgaande open bare Ip's is 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Geef uw eigen open bare Ip's of voor voegsels voor uitgang op

Wanneer u een *standaard* -SKU Load Balancer gebruikt, wordt in het AKS-cluster automatisch een openbaar IP-adres gemaakt in dezelfde resource groep die is gemaakt voor het AKS-cluster en wordt het open bare IP-adres toegewezen aan de *standaard* -SKU Load Balancer. U kunt ook uw eigen open bare IP toewijzen tijdens het maken van het cluster of u kunt de load balancer eigenschappen van een bestaand cluster bijwerken.

Door meerdere IP-adressen of voor voegsels te maken, kunt u meerdere services voor het aanbrengen van een back-up definiëren wanneer u het IP-adres achter een enkel load balancer-object definieert. Het uitgaand eind punt van specifieke knoop punten is afhankelijk van de service waaraan ze zijn gekoppeld.

> [!IMPORTANT]
> U moet de *standaard* -SKU open bare ip's gebruiken om uw Load Balancer te doen met uw *standaard* SKU. U kunt de SKU van uw open bare Ip's controleren met behulp van de opdracht [AZ Network public-ip show][az-network-public-ip-show] :
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Gebruik de opdracht [AZ Network public-ip show][az-network-public-ip-show] om de id's van uw open bare ip's weer te geven.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

De bovenstaande opdracht toont de ID voor het open bare IP-adres *myPublicIP* in de resource groep *myResourceGroup* .

Gebruik de opdracht *AZ AKS update* met de para meter *Load-Balancer-outbound-ip's* om uw cluster bij te werken met uw open bare ip's.

In het volgende voor beeld wordt de para meter *Load-Balancer-outbound-ip's* gebruikt met de id's van de vorige opdracht.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

U kunt ook open bare IP-voor voegsels gebruiken om uit te voeren met uw *standaard* SKU-Load Balancer. In het volgende voor beeld wordt de opdracht [AZ Network Public-IP prefix show][az-network-public-ip-prefix-show] gebruikt om de id's van uw open bare IP-voor voegsels weer te geven:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

De bovenstaande opdracht toont de ID voor het open bare IP-voor voegsel *myPublicIPPrefix* in de resource groep *myResourceGroup* .

In het volgende voor beeld wordt de para meter *Load Balancer-uitgaand-IP-voor voegsels* gebruikt met de id's van de vorige opdracht.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> De open bare Ip's en IP-voor voegsels moeten zich in dezelfde regio bevinden en deel uitmaken van hetzelfde abonnement als uw AKS-cluster. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Uw eigen open bare IP of voor voegsels definiëren op het moment dat het cluster wordt gemaakt

U kunt uw eigen IP-adressen of IP-voor voegsels maken voor uitgaand verkeer tijdens het maken van het cluster ter ondersteuning van scenario's zoals white list-uitgangs eindpunten. Voeg de hierboven weer gegeven para meters toe aan de stap voor het maken van het cluster om uw eigen open bare Ip's en IP-voor voegsels te definiëren aan het begin van de levens cyclus van een cluster.

Gebruik de opdracht *AZ AKS Create* met de para meter *Load-Balancer-outbound-ip's* om aan het begin een nieuw cluster met uw open bare ip's te maken.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Gebruik de opdracht *AZ AKS Create* met de para meter *Load-Balancer-uitgaand-IP-voor voegsels* om een nieuw cluster te maken met uw open bare IP-voor voegsels aan het begin.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Kubernetes Services vindt u in de [documentatie van Kubernetes Services][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
