---
title: Beschikbaarheids zones gebruiken in azure Kubernetes service (AKS)
description: Meer informatie over het maken van een cluster dat knoop punten distribueert over beschikbaarheids zones in azure Kubernetes service (AKS)
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596807"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Een AKS-cluster (Azure Kubernetes service) maken dat gebruikmaakt van beschikbaarheids zones

Een Azure Kubernetes service (AKS)-cluster distribueert bronnen zoals de knoop punten en opslag ruimte over logische secties van de onderliggende Azure Compute-infra structuur. Dit implementatie model zorgt ervoor dat de knoop punten worden uitgevoerd op afzonderlijke updates en fout domeinen in één Azure-Data Center. AKS-clusters die zijn geïmplementeerd met dit standaard gedrag bieden een hoog niveau van Beschik baarheid om te beschermen tegen een hardwarestoring of gepland onderhouds gebeurtenis.

Als u uw toepassingen een hoger niveau van Beschik baarheid wilt bieden, kunnen AKS-clusters worden gedistribueerd in verschillende beschikbaarheids zones. Deze zones zijn fysiek gescheiden data centers binnen een bepaalde regio. Wanneer de cluster onderdelen over meerdere zones worden verdeeld, kan uw AKS-cluster een fout in een van deze zones verdragen. Uw toepassingen en beheer bewerkingen blijven beschikbaar, zelfs als er een probleem is met één heel Data Center.

In dit artikel wordt beschreven hoe u een AKS-cluster maakt en hoe u de knooppunt onderdelen over beschikbaarheids zones distribueert.

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.76 of hoger hebben geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u wilt installeren of upgraden, raadpleegt u [Azure cli installeren][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Beperkingen en beschik baarheid van regio's

AKS-clusters kunnen momenteel worden gemaakt met beschikbaarheids zones in de volgende regio's:

* VS - centraal
* VS - oost 2
* VS - oost
* Frankrijk - centraal
* Japan - oost
* Europa - noord
* Azië - zuidoost
* Verenigd Koninkrijk Zuid
* Europa -west
* VS - west 2

De volgende beperkingen zijn van toepassing wanneer u een AKS-cluster maakt met beschikbaarheids zones:

* U kunt beschikbaarheids zones alleen inschakelen wanneer het cluster is gemaakt.
* De instellingen van de beschikbaarheids zone kunnen niet worden bijgewerkt nadat het cluster is gemaakt. U kunt ook een bestaand niet-beschikbaarheids zone cluster bijwerken om beschikbaarheids zones te gebruiken.
* U kunt geen beschikbaarheids zones uitschakelen voor een AKS-cluster nadat het is gemaakt.
* De geselecteerde knooppunt grootte (VM-SKU) moet beschikbaar zijn in alle beschikbaarheids zones.
* Voor clusters waarvoor beschikbaarheids zones zijn ingeschakeld, moet Azure Standard load balancers worden gebruikt voor distributie in meerdere zones.
* U moet Kubernetes-versie 1.13.5 of hoger gebruiken om standaard load balancers te kunnen implementeren.

AKS-clusters die gebruikmaken van beschikbaarheids zones, moeten de Azure load balancer *Standard* -SKU gebruiken. Dit is de standaard waarde voor het type Load Balancer. Dit type load balancer kan alleen worden gedefinieerd tijdens het maken van het cluster. Zie [Azure Load Balancer Standard SKU-beperkingen][standard-lb-limitations]voor meer informatie en de beperkingen van de standaard Load Balancer.

### <a name="azure-disks-limitations"></a>Beperkingen voor Azure-schijven

Volumes die gebruikmaken van Azure Managed disks zijn momenteel geen zonegebonden bronnen. Een opnieuw gepland in een andere zone dan de oorspronkelijke zone kan de vorige schijven niet opnieuw koppelen. Het wordt aanbevolen om staatloze werk belastingen uit te voeren waarvoor geen permanente opslag nodig is die zich in zonegebonden-problemen kunnen voordoen.

Als u stateful werk belastingen moet uitvoeren, gebruikt u taints en verdragen in uw Pod-spec om te laten zien dat de Kubernetes scheduler in staat is om in dezelfde zone als uw schijven een Peul te maken. U kunt ook op het netwerk gebaseerde opslag gebruiken, zoals Azure Files die kan worden gekoppeld aan een van de verschillende zones.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Overzicht van beschikbaarheids zones voor AKS-clusters

Beschikbaarheids zones is een aanbieding met hoge Beschik baarheid die uw toepassingen en gegevens beveiligt tegen Data Center-fouten. Zones zijn unieke fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Om voor tolerantie te zorgen, is er een minimum van drie afzonderlijke zones in alle ingeschakelde regio's. De fysieke scheiding tussen beschikbaarheidszones binnen een Azure-regio beschermt toepassingen en gegevens tegen storingen van het datacenter. Zone-redundante Services repliceren uw toepassingen en gegevens in beschikbaarheids zones om te beschermen tegen enkele punten van een storing.

Zie [Wat zijn beschikbaarheids zones in azure?][az-overview]voor meer informatie.

AKS-clusters die zijn geïmplementeerd met beschikbaarheids zones, kunnen knoop punten distribueren over meerdere zones binnen één regio. Een cluster in de regio *VS Oost 2* kan bijvoorbeeld knoop punten maken in alle drie beschikbaarheids zones in *VS-Oost 2*. Deze distributie van AKS-cluster resources verbetert de beschik baarheid van het cluster, omdat ze robuust zijn in het mislukken van een specifieke zone.

![Distributie van AKS-knoop punten in beschikbaarheids zones](media/availability-zones/aks-availability-zones.png)

In een zone storing kunnen de knoop punten hand matig worden gebalanceerd of gebruikmaken van de cluster-automatische schaal functie. Als een enkele zone niet meer beschikbaar is, blijven uw toepassingen actief.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Een AKS-cluster maken in verschillende beschikbaarheids zones

Wanneer u een cluster maakt met behulp van de opdracht [AZ AKS Create][az-aks-create] , wordt in de para meter `--zones` gedefinieerd welke zone-agent knooppunten worden geïmplementeerd. De AKS-besturings vlak onderdelen voor uw cluster worden ook verspreid over zones in de hoogste beschik bare configuratie wanneer u de para meter `--zones` definieert tijdens het maken van het cluster.

Als u geen zones voor de standaard agent groep definieert wanneer u een AKS-cluster maakt, zullen de onderdelen van de AKS-besturings elementen voor uw cluster geen beschikbaarheids zones gebruiken. U kunt extra knooppunt Pools toevoegen met behulp van de opdracht [AZ AKS nodepool add][az-aks-nodepool-add] en `--zones` op te geven voor die nieuwe knoop punten. de onderdelen van het besturings element blijven echter behouden zonder de beschik baarheid van de zone. U kunt de zone bewustzijn voor een knooppunt groep of de onderdelen van het AKS-besturings element niet wijzigen zodra deze zijn geïmplementeerd.

In het volgende voor beeld wordt een AKS-cluster gemaakt met de naam *myAKSCluster* in de resource groep met de naam *myResourceGroup*. Er zijn in totaal *drie* knoop punten gemaakt: één agent in zone *1*, één in *2*, en vervolgens een in *3*. De onderdelen van het AKS-besturings element worden ook gedistribueerd over zones in de hoogste beschik bare configuratie, omdat deze zijn gedefinieerd als onderdeel van het proces voor het maken van het cluster.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Het duurt enkele minuten om het AKS-cluster te maken.

## <a name="verify-node-distribution-across-zones"></a>Knooppunt distributie in zones verifiëren

Wanneer het cluster gereed is, vermeldt u de agent knooppunten in de schaalset om te zien in welke beschikbaarheids zone ze zijn geïmplementeerd.

Haal eerst de AKS-cluster referenties op met de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Gebruik vervolgens de opdracht [kubectl beschrijven][kubectl-describe] om de knoop punten in het cluster weer te geven. Filter op de waarde *failure-Domain.beta.kubernetes.io/zone* zoals weer gegeven in het volgende voor beeld:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

In de volgende voorbeeld uitvoer ziet u de drie knoop punten verdeeld over de opgegeven regio en beschikbaarheids zones, zoals *eastus2-1* voor de eerste beschikbaarheids zone en *eastus2-2* voor de tweede beschikbaarheids zone:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Wanneer u extra knoop punten aan een agent groep toevoegt, distribueert het Azure-platform automatisch de onderliggende virtuele machines over de opgegeven beschikbaarheids zones.

Houd er rekening mee dat in nieuwere Kubernetes-versies (1.17.0 en hoger) AKS naast de afgeschafte `failure-domain.beta.kubernetes.io/zone`de nieuwere label `topology.kubernetes.io/zone` gebruikt.

## <a name="verify-pod-distribution-across-zones"></a>Pod-distributie in zones controleren

Zoals beschreven in [bekende labels, aantekeningen en taints][kubectl-well_known_labels], gebruikt Kubernetes het label `failure-domain.beta.kubernetes.io/zone` om automatisch een Peul te distribueren in een replicatie controller of service in de verschillende zones die beschikbaar zijn. Als u dit wilt testen, kunt u het cluster van 3 tot 5 knoop punten omhoog schalen om de juiste pod-sprei ding te controleren:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Wanneer de schaal bewerking na een paar minuten is voltooid, moet de opdracht `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` een uitvoer hebben die vergelijkbaar is met dit voor beeld:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Zoals u ziet, hebben we nu twee extra knoop punten in zones 1 en 2. U kunt een toepassing implementeren die bestaat uit drie replica's. We gebruiken NGINX als voor beeld:

```console
kubectl run nginx --image=nginx --replicas=3
```

Als u controleert of knoop punten met uw peul worden uitgevoerd, ziet u dat de peulen worden uitgevoerd op het Peul dat overeenkomt met drie verschillende beschikbaarheids zones. Als u bijvoorbeeld de opdracht `kubectl describe pod | grep -e "^Name:" -e "^Node:"` ziet u een uitvoer die er ongeveer als volgt uitziet:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Zoals u kunt zien in de vorige uitvoer, wordt de eerste pod uitgevoerd op het knoop punt 0, dat zich bevindt in de beschikbaarheids zone `eastus2-1`. De tweede pod wordt uitgevoerd op knoop punt 2, dat overeenkomt met `eastus2-3`en de derde in knoop punt 4, in `eastus2-2`. Zonder enige aanvullende configuratie Kubernetes de gehele wereld op de juiste wijze over in alle drie de beschikbaarheids zones.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe u een AKS-cluster maakt dat gebruikmaakt van beschikbaarheids zones. Zie [Aanbevolen procedures voor bedrijfs continuïteit en herstel na nood gevallen in AKS][best-practices-bc-dr]voor meer informatie over Maxi maal beschik bare clusters.

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
