---
title: Proximity-plaatsings groepen gebruiken om de latentie voor Azure Kubernetes service (AKS)-clusters te verminderen
description: Meer informatie over het gebruik van proximity placement groups om de latentie van uw AKS-cluster workloads te verminderen.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 06/22/2020
ms.openlocfilehash: f5b87d048127bf856a5a683397540769a7040fd6
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323439"
---
# <a name="reduce-latency-with-proximity-placement-groups-preview"></a>Minder latentie met proximity-plaatsings groepen (preview-versie)

> [!Note]
> Wanneer u proximity placement groups gebruikt met AKS, is de co-locatie alleen van toepassing op de agent knooppunten. Knoop punt naar knoop punt en de bijbehorende gehoste Pod naar pod latentie is verbeterd. De co-locatie heeft geen invloed op de plaatsing van het besturings vlak van een cluster.

Wanneer u uw toepassing in azure implementeert, wordt er netwerk latentie gemaakt door het verspreiden van virtuele machine-exemplaren (VM) in regio's of beschikbaarheids zones. Dit kan van invloed zijn op de algehele prestaties van uw toepassing. Een proximity-plaatsings groep is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure Compute-resources zich fysiek dicht bij elkaar bevinden. Sommige toepassingen, zoals gaming, technische simulaties en hoge frequentie handel (HFT) vereisen een lage latentie en taken die snel kunnen worden voltooid. Voor scenario's met High Performance Computing (HPC) zoals deze kunt u overwegen om [proximity-plaatsings groepen](https://docs.microsoft.com/azure/virtual-machines/linux/co-location#proximity-placement-groups) te gebruiken voor de knooppunt groepen van uw cluster.

## <a name="limitations"></a>Beperkingen

* De Proximity-plaatsings groep omvat één beschikbaarheids zone.
* Er is geen huidige ondersteuning voor AKS-clusters die gebruikmaken van beschikbaarheids sets voor virtuele machines.
* U kunt bestaande knooppunt groepen niet wijzigen om een proximity-plaatsings groep te gebruiken.

> [!IMPORTANT]
> AKS preview-functies zijn beschikbaar op self-service. Previews worden ' as-is ' en ' as available ' gegeven en zijn uitgesloten van de service level agreements en beperkte garantie. AKS-previews worden gedeeltelijk gedekt door de klant ondersteuning. Daarom zijn deze functies niet bedoeld voor productie gebruik. Zie de volgende ondersteunings artikelen voor meer informatie:
>
> - [AKS-ondersteunings beleid](support-policies.md)
> - [Veelgestelde vragen over ondersteuning voor Azure](faq.md)

## <a name="before-you-begin"></a>Voordat u begint

U moet de volgende resources hebben geïnstalleerd:

- De 0.4.53-uitbrei ding AKS-preview

### <a name="set-up-the-preview-feature-for-proximity-placement-groups"></a>De preview-functie voor proximity-plaatsings groepen instellen

> [!IMPORTANT]
> Wanneer u proximity placement groups gebruikt met AKS, is de co-locatie alleen van toepassing op de agent knooppunten. Knoop punt naar knoop punt en de bijbehorende gehoste Pod naar pod latentie is verbeterd. De co-locatie heeft geen invloed op de plaatsing van het besturings vlak van een cluster.

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "ProximityPlacementGroupPreview"
```

Het kan enkele minuten duren voordat de registratie is uitgevoerd. Gebruik de onderstaande opdracht om te controleren of de functie is geregistreerd:

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/ProximityPlacementGroupPreview')].{Name:name,State:properties.state}"
```

Tijdens de preview-versie hebt u de *AKS-preview cli-* extensie nodig om proximity-plaatsings groepen te gebruiken. Gebruik de opdracht [AZ extension add][az-extension-add] en controleer vervolgens of er beschik bare updates zijn met behulp van de opdracht [AZ extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
## <a name="node-pools-and-proximity-placement-groups"></a>Knooppunt groepen en proximity-plaatsings groepen

De eerste resource die u implementeert met een proximity-plaatsings groep, wordt gekoppeld aan een specifiek Data Center. Aanvullende resources die zijn geïmplementeerd met dezelfde proximity-plaatsings groep, worden in hetzelfde Data Center geplaatst. Wanneer alle resources die gebruikmaken van de Proximity-plaatsings groep zijn gestopt (toewijzing opgeheven) of verwijderd, is deze niet meer gekoppeld.

* Veel knooppunt groepen kunnen worden gekoppeld aan één proximity-plaatsings groep.
* Een knooppunt groep mag alleen worden gekoppeld aan één proximity-plaatsings groep.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Een nieuw AKS-cluster maken met een proximity-plaatsings groep

In het volgende voor beeld wordt de opdracht [AZ Group Create][az-group-create] gebruikt voor het maken van een resource groep met de naam *myResourceGroup* in de regio *middenus* . Er wordt een AKS-cluster met de naam *myAKSCluster* gemaakt met behulp van de opdracht [AZ AKS Create][az-aks-create] . 

Versneld netwerken verbetert de netwerk prestaties van virtuele machines aanzienlijk. In het ideale geval moet u proximity-plaatsings groepen gebruiken in combi natie met versneld netwerken. AKS maakt standaard gebruik van versnelde netwerken op [ondersteunde instanties van virtuele machines](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), waaronder de meeste virtuele machines van Azure met twee of meer vcpu's.

Maak een nieuw AKS-cluster met een proximity-plaatsings groep:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Voer de volgende opdracht uit en sla de geretourneerde ID op:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

De opdracht produceert uitvoer, die de *id-* waarde bevat die u nodig hebt voor aanstaande cli-opdrachten:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Gebruik de resource-ID voor de Proximity-plaatsings groep voor de waarde *myPPGResourceID* in de onderstaande opdracht:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial node pool
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Een proximity-plaatsings groep toevoegen aan een bestaand cluster

U kunt een proximity-plaatsings groep toevoegen aan een bestaand cluster door een nieuwe knooppunt groep te maken. Vervolgens kunt u eventueel bestaande werk belastingen naar de nieuwe knooppunt groep migreren en vervolgens de oorspronkelijke knooppunt groep verwijderen.

Gebruik dezelfde plaatsings groep die u eerder hebt gemaakt. Dit zorgt ervoor dat agent knooppunten in beide knooppunt groepen in uw AKS-cluster fysiek zich in hetzelfde Data Center bevinden.

Gebruik de resource-ID uit de plaatsings groep die u eerder hebt gemaakt en voeg een nieuwe knooppunt groep toe met de [`az aks nodepool add`][az-aks-nodepool-add] opdracht:

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>Opruimen

Als u het cluster wilt verwijderen, gebruikt u de [`az group delete`][az-group-delete] opdracht om de resource groep AKS te verwijderen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [proximity placement groups][proximity-placement-groups].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: /virtual-machines/windows/co-location.md#proximity-placement-groups/virtual-machines/linux/co-location
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete

