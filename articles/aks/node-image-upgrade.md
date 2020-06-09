---
title: Installatie kopieën van het AKS-knoop punt (Azure Kubernetes service) upgraden
description: Meer informatie over het bijwerken van de installatie kopieën op AKS-cluster knooppunten en-knooppunt groepen.
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 20379f6a1e87c7553d6567be5b50f22bbadb8db7
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/08/2020
ms.locfileid: "84514685"
---
# <a name="preview---azure-kubernetes-service-aks-node-image-upgrades"></a>Preview-upgrade van installatie kopie van knoop punt van Azure Kubernetes service (AKS)

AKS biedt ondersteuning voor het upgraden van installatie kopieën op een knoop punt zodat u up-to-date bent met de nieuwste besturings systemen en runtime-updates. AKS biedt een nieuwe installatie kopie per week met de nieuwste updates. het is dus handig om de installatie kopieën van uw knoop punt regel matig bij te werken voor de nieuwste functies, waaronder Linux-of Windows-patches. In dit artikel wordt beschreven hoe u installatie kopieën van AKS-cluster knooppunten bijwerkt en hoe u installatie kopieën van groeps knooppunten bijwerkt zonder de versie van Kubernetes bij te werken.

Als u wilt weten over de nieuwste installatie kopieën van AKS, raadpleegt u de [opmerkingen](https://github.com/Azure/AKS/releases) bij de release van AKS voor meer informatie.

Zie [een AKS-cluster upgraden][upgrade-cluster]voor informatie over het bijwerken van de Kubernetes-versie voor uw cluster.

## <a name="register-the-node-image-upgrade-preview-feature"></a>De preview-functie voor de upgrade van de knooppunt installatie kopie registreren

Als u de upgrade functie voor de knooppunt installatie kopie wilt gebruiken tijdens de preview-periode, moet u de functie registreren.

```azurecli
# Register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "NodeImageUpgradePreview"
```

Het duurt enkele minuten voordat de registratie is voltooid. Gebruik de volgende opdracht om te controleren of de functie is geregistreerd:

```azurecli
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodeImageUpgradePreview')].{Name:name,State:properties.state}"
```

Tijdens de preview-versie hebt u de *AKS-preview cli-* extensie nodig om de upgrade van de knooppunt installatie kopie te gebruiken. Gebruik de opdracht [AZ extension add] [AZ-extension-add] en controleer vervolgens of er beschik bare updates zijn met behulp van de opdracht [AZ extension update] [AZ-extension-update]:

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Alle knoop punten in alle knooppunt groepen upgraden

De upgrade van de knooppunt installatie kopie wordt uitgevoerd met `az aks upgrade` . Als u de installatie kopie van het knoop punt wilt bijwerken, gebruikt u de volgende opdracht:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Controleer tijdens de upgrade de status van de installatie kopie van het knoop punt met de volgende `kubectl` opdracht om de labels op te halen en de gegevens van de huidige knooppunt afbeelding te filteren:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Wanneer de upgrade is voltooid, gebruikt `az aks show` u om de bijgewerkte Details van de knooppunt groep op te halen. De huidige knooppunt afbeelding wordt weer gegeven in de `nodeImageVersion` eigenschap.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Een specifieke knooppunt groep upgraden

Het bijwerken van de installatie kopie op een knooppunt groep is vergelijkbaar met het bijwerken van de installatie kopie in een cluster.

Als u de installatie kopie van het besturings systeem van de knooppunt groep wilt bijwerken zonder een Kubernetes-cluster upgrade uit te voeren, gebruikt u de `--node-image-only` optie in het volgende voor beeld:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Controleer tijdens de upgrade de status van de installatie kopie van het knoop punt met de volgende `kubectl` opdracht om de labels op te halen en de gegevens van de huidige knooppunt afbeelding te filteren:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Wanneer de upgrade is voltooid, gebruikt `az aks nodepool show` u om de bijgewerkte Details van de knooppunt groep op te halen. De huidige knooppunt afbeelding wordt weer gegeven in de `nodeImageVersion` eigenschap.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Knooppunt installatie kopieën bijwerken met overspanning van knoop punt

Als u het upgrade proces van de knooppunt installatie kopie wilt versnellen, kunt u uw knooppunt installatie kopieën bijwerken met behulp van een aanpas bare overspannings waarde. AKS gebruikt standaard één extra knoop punt voor het configureren van upgrades.

Als u de snelheid van upgrades wilt verhogen, gebruikt u de `--max-surge` waarde voor het configureren van het aantal knoop punten dat moet worden gebruikt voor upgrades zodat ze sneller kunnen worden uitgevoerd. Zie de upgrade van het `--max-surge` [knoop punt piek aanpassen][max-surge]voor meer informatie over de verschillende instellingen.

Met de volgende opdracht stelt u de maximale piek waarde in voor het uitvoeren van een upgrade van een knooppunt installatie kopie:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Controleer tijdens de upgrade de status van de installatie kopie van het knoop punt met de volgende `kubectl` opdracht om de labels op te halen en de gegevens van de huidige knooppunt afbeelding te filteren:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Gebruiken `az aks nodepool show` om de bijgewerkte Details van de knooppunt groep op te halen. De huidige knooppunt afbeelding wordt weer gegeven in de `nodeImageVersion` eigenschap.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Volgende stappen

- Zie de [opmerkingen](https://github.com/Azure/AKS/releases) bij de release van AKS voor informatie over de nieuwste knooppunt installatie kopieën.
- Meer informatie over het bijwerken van de Kubernetes-versie met [een upgrade voor een AKS-cluster][upgrade-cluster].
- [Beveiligings-en kernel-updates Toep assen op Linux-knoop punten in azure Kubernetes service (AKS)][security-update]
- Meer informatie over meerdere knooppunt Pools en het upgraden van knooppunt Pools met het [maken en beheren van meerdere knooppunt groepen][use-multiple-node-pools].

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
