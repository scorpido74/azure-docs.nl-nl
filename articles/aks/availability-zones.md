---
title: Beschikbaarheidszones gebruiken in Azure Kubernetes Service (AKS)
description: Meer informatie over het maken van een cluster dat knooppunten distribueert over beschikbaarheidszones in Azure Kubernetes Service (AKS)
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596807"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Een AKS-cluster (Azure Kubernetes Service) maken dat gebruikmaakt van beschikbaarheidszones

Een AKS-cluster (Azure Kubernetes Service) distribueert bronnen zoals de knooppunten en opslag over logische secties van de onderliggende Azure-compute-infrastructuur. Dit implementatiemodel zorgt ervoor dat de knooppunten worden uitgevoerd in afzonderlijke update- en foutdomeinen in één Azure-datacenter. AKS-clusters die met dit standaardgedrag zijn geïmplementeerd, bieden een hoge mate van beschikbaarheid om te beschermen tegen een hardwarefout of geplande onderhoudsgebeurtenis.

Om uw toepassingen een hoger beschikbaarheidsniveau te bieden, kunnen AKS-clusters worden verdeeld over beschikbaarheidszones. Deze zones zijn fysiek gescheiden datacenters binnen een bepaalde regio. Wanneer de clustercomponenten over meerdere zones worden verdeeld, kan uw AKS-cluster een storing in een van deze zones voorkomen. Uw applicaties en beheeractiviteiten blijven beschikbaar, zelfs als één heel datacenter een probleem heeft.

In dit artikel ziet u hoe u een AKS-cluster maakt en de knooppuntcomponenten over beschikbaarheidszones verdeelt.

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.76 of hoger installeren en configureren. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Beperkingen en beschikbaarheid van de regio

AKS-clusters kunnen momenteel worden gemaakt met behulp van beschikbaarheidszones in de volgende regio's:

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

De volgende beperkingen zijn van toepassing wanneer u een AKS-cluster maakt met beschikbaarheidszones:

* U alleen beschikbaarheidszones inschakelen wanneer het cluster wordt gemaakt.
* Instellingen voor beschikbaarheidszones kunnen niet worden bijgewerkt nadat het cluster is gemaakt. U ook een bestaand cluster van niet-beschikbaarheidszones niet bijwerken om beschikbaarheidszones te gebruiken.
* U beschikbaarheidszones voor een AKS-cluster niet uitschakelen nadat het is gemaakt.
* De geselecteerde knooppuntgrootte (VM SKU) moet beschikbaar zijn in alle beschikbaarheidszones.
* Clusters met ingeschakelde beschikbaarheidszones vereisen het gebruik van Azure Standard Load Balancers voor distributie over zones.
* U moet Kubernetes versie 1.13.5 of hoger gebruiken om Standard Load Balancers te implementeren.

AKS-clusters die beschikbaarheidszones gebruiken, moeten de Azure load *balancer-standaard* SKU gebruiken, de standaardwaarde voor het type load balancer. Dit type load balancer kan alleen worden gedefinieerd op de tijd voor het maken van clusteren. Zie [Azure load balancer standard SKU-beperkingen][standard-lb-limitations]voor meer informatie en de beperkingen van de standaardloadbalancer.

### <a name="azure-disks-limitations"></a>Azure-schijvenbeperkingen

Volumes die door Azure beheerde schijven gebruiken, zijn momenteel geen zonale bronnen. Pods die opnieuw zijn gepland in een andere zone dan hun oorspronkelijke zone, kunnen hun vorige schijf(en) niet opnieuw bevestigen. Het wordt aanbevolen om stateless workloads uit te voeren waarvoor geen permanente opslag nodig is die zonale problemen kan tegenkomen.

Als u stateful workloads moet uitvoeren, gebruikt u taints en toleranties in uw podspecs om de Kubernetes-planner te vertellen pods te maken in dezelfde zone als uw schijven. U ook netwerkopslag gebruiken, zoals Azure-bestanden die kunnen worden gekoppeld aan pods zoals deze tussen zones zijn gepland.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Overzicht van beschikbaarheidszones voor AKS-clusters

Beschikbaarheidszones is een aanbod met hoge beschikbaarheid dat uw toepassingen en gegevens beschermt tegen datacenterfouten. Zones zijn unieke fysieke locaties binnen een Azure-gebied. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Om voor tolerantie te zorgen, is er een minimum van drie afzonderlijke zones in alle ingeschakelde regio's. De fysieke scheiding tussen beschikbaarheidszones binnen een Azure-regio beschermt toepassingen en gegevens tegen storingen van het datacenter. Zoneredundante services repliceren uw toepassingen en gegevens in beschikbaarheidszones om te beschermen tegen single-points-of-failure.

Zie [Wat zijn beschikbaarheidszones in Azure voor][az-overview]meer informatie.

AKS-clusters die worden geïmplementeerd met beschikbaarheidszones kunnen knooppunten verdelen over meerdere zones binnen één regio. Een cluster in de regio *Oost-VS 2* kan bijvoorbeeld knooppunten maken in alle drie de beschikbaarheidszones in *Oost-VS 2*. Deze verdeling van AKS-clusterbronnen verbetert de beschikbaarheid van het cluster omdat ze bestand zijn tegen het uitvallen van een specifieke zone.

![AKS-knooppuntdistributie over beschikbaarheidszones](media/availability-zones/aks-availability-zones.png)

Bij een zonestoring kunnen de knooppunten handmatig opnieuw worden gebalanceerd of met behulp van de clusterautoscaler. Als één zone niet meer beschikbaar is, blijven uw toepassingen worden uitgevoerd.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Een AKS-cluster maken in beschikbaarheidszones

Wanneer u een cluster maakt met de `--zones` opdracht [az aks create,][az-aks-create] bepaalt de parameter in welke zones agentknooppunten worden geïmplementeerd. De AKS-besturingsvlakcomponenten voor uw cluster zijn ook verspreid over `--zones` zones in de hoogst beschikbare configuratie wanneer u de parameter definieert bij het maken van het cluster.

Als u geen zones definieert voor de standaardagentgroep wanneer u een AKS-cluster maakt, worden de AKS-regelvlakcomponenten voor uw cluster geen beschikbaarheidszones gebruikt. U extra knooppuntpools toevoegen met de opdracht [AZ AKS-knooppunten toevoegen][az-aks-nodepool-add] en opgeven `--zones` voor die nieuwe knooppunten, maar de onderdelen van het besturingsvlak blijven zonder beschikbaarheidszonebewustzijn. U de zonebekendheid voor een knooppuntgroep of de AKS-controlevlakonderdelen niet wijzigen zodra ze zijn geïmplementeerd.

In het volgende voorbeeld wordt een AKS-cluster met de naam *myAKSCluster* in de brongroep myResourceGroup met de naam *myResourceGroup.* Een totaal van *3* knooppunten worden gemaakt - een agent in zone *1,* een op *de 2*, en vervolgens een op *de 3*. De AKS-besturingsvlakcomponenten worden ook verdeeld over zones in de hoogst beschikbare configuratie, omdat ze zijn gedefinieerd als onderdeel van het clusterproces.

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

Het maken van het AKS-cluster duurt enkele minuten.

## <a name="verify-node-distribution-across-zones"></a>Knooppuntverdeling over zones verifiëren

Wanneer het cluster klaar is, geeft u de agentknooppunten weer in de schaalset om te zien in welke beschikbaarheidszone ze zijn geïmplementeerd.

Eerst krijgt u de AKS-clusterreferenties met behulp van de opdracht [az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Gebruik vervolgens de opdracht [kubectl describe][kubectl-describe] om de knooppunten in het cluster weer te geven. Filter op de *failure-domain.beta.kubernetes.io/zone* waarde zoals weergegeven in het volgende voorbeeld:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

In de volgende voorbeelduitvoerworden de drie knooppunten weergegeven die zijn verdeeld over de opgegeven regio- en beschikbaarheidszones, zoals *eastus2-1* voor de eerste beschikbaarheidszone en *eastus2-2* voor de tweede beschikbaarheidszone:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Als u extra knooppunten toevoegt aan een agentgroep, distribueert het Azure-platform automatisch de onderliggende VM's over de opgegeven beschikbaarheidszones.

Merk op dat in nieuwere Kubernetes-versies (1.17.0 en hoger), AKS het nieuwere label `topology.kubernetes.io/zone` gebruikt naast het afgeschafte `failure-domain.beta.kubernetes.io/zone`.

## <a name="verify-pod-distribution-across-zones"></a>Poddistributie over zones verifiëren

Zoals gedocumenteerd in [bekende labels, annotaties en taints,][kubectl-well_known_labels]gebruikt Kubernetes het `failure-domain.beta.kubernetes.io/zone` label om pods automatisch te distribueren in een replicatiecontroller of -service over de verschillende beschikbare zones. Om dit te testen, u uw cluster opschalen van 3 naar 5 knooppunten om de juiste verspreiding van de pod te controleren:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Wanneer de schaalbewerking na enkele minuten `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` is voltooid, moet de opdracht een uitvoer geven die vergelijkbaar is met dit voorbeeld:

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

Zoals u zien, hebben we nu twee extra knooppunten in zones 1 en 2. U een toepassing implementeren die bestaat uit drie replica's. We zullen NGINX gebruiken als voorbeeld:

```console
kubectl run nginx --image=nginx --replicas=3
```

Als u controleert of knooppunten waar uw pods worden uitgevoerd, ziet u dat de pods worden uitgevoerd op de pods die overeenkomen met drie verschillende beschikbaarheidszones. Bijvoorbeeld met de `kubectl describe pod | grep -e "^Name:" -e "^Node:"` opdracht zou je een uitvoer vergelijkbaar met deze te krijgen:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Zoals u zien aan de vorige uitvoer, wordt de eerste pod uitgevoerd `eastus2-1`op knooppunt 0, die zich in de beschikbaarheidszone bevindt. De tweede pod draait op knooppunt 2, `eastus2-3`die overeenkomt met , en `eastus2-2`de derde in knooppunt 4, in . Zonder extra configuratie verspreidt Kubernetes de pods correct over alle drie de beschikbaarheidszones.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe u een AKS-cluster maakt dat beschikbaarheidszones gebruikt. Zie [Best practices voor bedrijfscontinuïteit en disaster recovery in AKS voor][best-practices-bc-dr]meer overwegingen over hoog beschikbare clusters.

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
