---
title: Meerdere knooppunt groepen gebruiken in azure Kubernetes service (AKS)
description: Meer informatie over het maken en beheren van meerdere knooppunt groepen voor een cluster in azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/22/2020
ms.author: mlearned
ms.openlocfilehash: bbfb65c31bf6fd46cc18c9eee66086afbbff1d5f
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157971"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Meerdere knooppunt groepen maken en beheren voor een cluster in azure Kubernetes service (AKS)

In azure Kubernetes service (AKS) worden knoop punten van dezelfde configuratie samen in *knooppunt groepen*gegroepeerd. Deze knooppunt groepen bevatten de onderliggende virtuele machines waarop uw toepassingen worden uitgevoerd. Het eerste aantal knoop punten en hun grootte (SKU) worden gedefinieerd wanneer u een AKS-cluster maakt, waarmee een *standaard knooppunt groep*wordt gemaakt. Als u toepassingen wilt ondersteunen die verschillende reken-of opslag vereisten hebben, kunt u extra knooppunt groepen maken. Gebruik bijvoorbeeld deze extra knooppunt groepen om Gpu's te bieden voor computerintensieve toepassingen, of om toegang te krijgen tot krachtige SSD-opslag.

> [!NOTE]
> Deze functie biedt meer controle over het maken en beheren van meerdere knooppunt groepen. Als gevolg hiervan zijn afzonderlijke opdrachten vereist voor maken/bijwerken/verwijderen. Voorheen cluster bewerkingen via `az aks create` of `az aks update` de managedCluster-API gebruikt en waren de enige optie om uw besturings vlak en een groep met één knoop punt te wijzigen. Deze functie biedt een afzonderlijke bewerking die is ingesteld voor agent groepen via de agent pool-API en waarvoor het gebruik van de `az aks nodepool` opdracht moet worden gebruikt om bewerkingen uit te voeren op een afzonderlijke knooppunt groep.

In dit artikel wordt beschreven hoe u meerdere knooppunt groepen kunt maken en beheren in een AKS-cluster.

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.76 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die ondersteuning bieden voor meerdere knooppunt groepen:

* Zie [quota's, beperkingen voor de grootte van virtuele machines en beschik baarheid van regio's in azure Kubernetes service (AKS)][quotas-skus-regions].
* U kunt de standaard-knooppunt groep (First) niet verwijderen.
* De invoeg toepassing voor het routeren van HTTP-toepassingen kan niet worden gebruikt.
* Het AKS-cluster moet het standaard SKU-load balancer gebruiken om meerdere knooppunt groepen te kunnen gebruiken. de functie wordt niet ondersteund met Basic SKU load balancers.
* Het AKS-cluster moet virtuele-machine schaal sets gebruiken voor de knoop punten.
* De naam van een knooppunt groep mag alleen kleine letters bevatten en moet beginnen met een kleine letter. Voor Linux-knooppunt Pools moet de lengte tussen 1 en 12 tekens lang zijn voor Windows-knooppunt groepen. de lengte moet tussen 1 en 6 tekens lang zijn.
* Alle knooppunt groepen moeten zich in hetzelfde vnet en subnet bevinden.
* Bij het maken van meerdere knooppunt groepen tijdens het maken van een cluster, moeten alle Kubernetes-versies die worden gebruikt door de knooppunt groepen, overeenkomen met de versieset voor het besturings vlak. Dit kan worden bijgewerkt nadat het cluster is ingericht met behulp van de bewerkingen per knooppunt groep.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Om aan de slag te gaan, maakt u een AKS-cluster met één knooppunt groep. In het volgende voor beeld wordt de opdracht [AZ Group Create][az-group-create] gebruikt voor het maken van een resource groep met de naam *myResourceGroup* in de regio *eastus* . Er wordt een AKS-cluster met de naam *myAKSCluster* gemaakt met behulp van de opdracht [AZ AKS Create][az-aks-create] . Een *--kubernetes-versie* van *1.15.7* wordt gebruikt om te laten zien hoe u een knooppunt groep in een volgende stap bijwerkt. U kunt elke [ondersteunde versie van Kubernetes][supported-versions]opgeven.

> [!NOTE]
> De *Basic* Load Balancer SKU wordt **niet ondersteund** wanneer meerdere knooppunt groepen worden gebruikt. Standaard worden AKS-clusters gemaakt met de *Standard* Load Balancer SKU van Azure CLI en Azure Portal.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --kubernetes-version 1.15.7 \
    --load-balancer-sku standard
```

Het duurt een paar minuten om het cluster te maken.

> [!NOTE]
> Om ervoor te zorgen dat uw cluster betrouwbaar werkt, moet u ten minste twee knoop punten in de standaard knooppunt groep uitvoeren, aangezien essentiële systeem services worden uitgevoerd in deze knooppunt groep.

Wanneer het cluster gereed is, gebruikt u de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] om de cluster referenties te verkrijgen voor gebruik met `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Een knooppunt groep toevoegen

Het cluster dat u in de vorige stap hebt gemaakt, heeft een groep met één knoop punt. We gaan een tweede groep knoop punten toevoegen met behulp van de opdracht [AZ AKS nodepool add][az-aks-nodepool-add] . In het volgende voor beeld wordt een knooppunt groep gemaakt met de naam *mynodepool* die *drie* knoop punten uitvoert:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> De naam van een knooppunt groep moet beginnen met een kleine letter en mag alleen alfanumerieke tekens bevatten. Voor Linux-knooppunt Pools moet de lengte tussen 1 en 12 tekens lang zijn voor Windows-knooppunt groepen. de lengte moet tussen 1 en 6 tekens lang zijn.

Als u de status van de knooppunt groepen wilt weer geven, gebruikt u de opdracht [AZ AKS node pool List][az-aks-nodepool-list] en geeft u de resource groep en de naam van het cluster op:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

In de volgende voorbeeld uitvoer ziet u dat *mynodepool* is gemaakt met drie knoop punten in de knooppunt groep. Wanneer het AKS-cluster in de vorige stap is gemaakt, is een standaard *nodepool1* gemaakt met het aantal knoop punten *2*.

```console
$ az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.5",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Als er geen *VmSize* is opgegeven wanneer u een knooppunt groep toevoegt, is de standaard grootte *Standard_DS2_v3* voor Windows-knooppunt groepen en *Standard_DS2_v2* voor Linux-knooppunt groepen. Als er geen *OrchestratorVersion* is opgegeven, wordt standaard dezelfde versie gebruikt als het besturings vlak.

## <a name="upgrade-a-node-pool"></a>Een knooppunt groep upgraden

> [!NOTE]
> Upgrade-en schaal bewerkingen op een cluster of knooppunt groep kunnen niet tegelijkertijd plaatsvinden, als er een fout wordt geretourneerd. In plaats daarvan moet elk bewerkings type worden voltooid voor de doel resource vóór de volgende aanvraag op dezelfde resource. Meer informatie hierover vindt u in onze [probleemoplossings handleiding](https://aka.ms/aks-pending-upgrade).

Wanneer uw AKS-cluster oorspronkelijk in de eerste stap is gemaakt, is er een `--kubernetes-version` van *1.15.7* opgegeven. Hiermee stelt u de Kubernetes-versie in voor zowel het besturings vlak als de standaard knooppunt groep. Met de opdrachten in deze sectie wordt uitgelegd hoe u één specifieke knooppunt groep bijwerkt.

De relatie tussen het upgraden van de Kubernetes-versie van het besturings vlak en de knooppunt groep wordt uitgelegd in de [volgende sectie](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> De versie van de installatie kopie van het besturings systeem van de knooppunt groep is gekoppeld aan de Kubernetes-versie van het cluster. U kunt upgrades van de installatie kopie van het besturings systeem downloaden na een upgrade van een cluster.

Omdat er in dit voor beeld twee knooppunt groepen zijn, moeten we [AZ AKS nodepool upgrade][az-aks-nodepool-upgrade] gebruiken om een knooppunt groep bij te werken. We gaan de *mynodepool* upgraden naar Kubernetes *1.15.7*. Gebruik de opdracht [AZ AKS nodepool upgrade][az-aks-nodepool-upgrade] om de knooppunt groep bij te werken, zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

Vermeld opnieuw de status van uw knooppunt groepen met de opdracht [AZ AKS node pool List][az-aks-nodepool-list] . In het volgende voor beeld ziet u dat *mynodepool* zich in de *upgrade* status bevindt op *1.15.7*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Het duurt enkele minuten om de knoop punten bij te werken naar de opgegeven versie.

Als best practice moet u alle knooppunt groepen in een AKS-cluster upgraden naar dezelfde Kubernetes-versie. Het standaard gedrag van `az aks upgrade` is het upgraden van alle knooppunt groepen samen met het besturings vlak om deze uitlijning te vervolledigen. Met de mogelijkheid om afzonderlijke knooppunt groepen bij te werken, kunt u een rolling upgrade uitvoeren en een Peul plannen tussen knooppunt groepen om de actieve tijds duur van de toepassing binnen de hierboven vermelde beperkingen te hand haven.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Een cluster besturings vlak upgraden met meerdere knooppunt groepen

> [!NOTE]
> Kubernetes maakt gebruik van het standaard versie beheer schema van [semantische versie](https://semver.org/) . Het versie nummer wordt weer gegeven als *x. y. z*, waarbij *x* de primaire versie is, *y* de secundaire versie en *z* de versie van de patch. In versie *1.12.6*is bijvoorbeeld 1 de primaire versie, 12 de secundaire versie en 6 de versie van de patch. De Kubernetes-versie van het besturings vlak en de eerste knooppunt groep worden ingesteld tijdens het maken van het cluster. Voor alle extra knooppunt groepen wordt de Kubernetes-versie ingesteld wanneer ze aan het cluster worden toegevoegd. De Kubernetes-versies kunnen verschillen tussen de knooppunt Pools en tussen een knooppunt groep en het besturings vlak.

Een AKS-cluster heeft twee cluster resource objecten waaraan Kubernetes-versies zijn gekoppeld.

1. Een Kubernetes-versie van het cluster besturings vlak.
2. Een knooppunt groep met een Kubernetes-versie.

Een besturings vlak wordt toegewezen aan een of meer knooppunt groepen. Het gedrag van een upgrade bewerking is afhankelijk van welke Azure CLI-opdracht wordt gebruikt.

Voor het bijwerken van een AKS-besturings vlak is het gebruik van `az aks upgrade`vereist. Hiermee worden de besturings vlak versie en alle knooppunt Pools in het cluster bijgewerkt. 

Als u de opdracht `az aks upgrade` met de `--control-plane-only`-vlag geeft, wordt alleen het cluster besturings vlak bijgewerkt. Geen van de gekoppelde knooppunt Pools in het cluster is gewijzigd.

Voor het upgraden van afzonderlijke knooppunt groepen is `az aks nodepool upgrade`vereist. Hiermee wordt alleen de doel knooppunt groep met de opgegeven Kubernetes-versie bijgewerkt

### <a name="validation-rules-for-upgrades"></a>Validatie regels voor upgrades

De geldige Kubernetes-upgrades voor de besturings vlak-en knooppunt groepen van een cluster worden gevalideerd door de volgende sets regels.

* Regels voor geldige versies voor het upgraden van knooppunt groepen:
   * De versie van de knooppunt groep moet dezelfde *primaire* versie hebben als het besturings vlak.
   * De *secundaire* versie van de knooppunt groep moet binnen twee *secundaire* versies van de besturings vlak versie zijn.
   * De versie van de knooppunt groep mag niet groter zijn dan de versie van het besturings element `major.minor.patch`.

* Regels voor het verzenden van een upgrade bewerking:
   * U kunt de Kubernetes-versie van het besturings vlak of de groep met knooppunt groepen niet verlagen.
   * Als er geen Kubernetes-versie van een knooppunt groep wordt opgegeven, is het gedrag afhankelijk van de gebruikte client. De declaratie in Resource Manager-sjablonen gaat terug naar de bestaande versie die is gedefinieerd voor de knooppunt groep als deze wordt gebruikt, als geen is ingesteld, wordt de versie van het besturings element gebruikt om terug te vallen.
   * U kunt een besturings vlak of een knooppunt groep op een bepaald moment bijwerken of schalen, u kunt niet meerdere bewerkingen tegelijkertijd verzenden op één besturings vlak of resource van een groep knoop punten.

## <a name="scale-a-node-pool-manually"></a>Een knooppunt groep hand matig schalen

Wanneer de werk belasting van uw toepassing wordt gewijzigd, moet u mogelijk het aantal knoop punten in een knooppunt groep schalen. Het aantal knoop punten kan omhoog of omlaag worden geschaald.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Als u het aantal knoop punten in een knooppunt groep wilt schalen, gebruikt u de opdracht [AZ AKS knoop punten Scale][az-aks-nodepool-scale] . In het volgende voor beeld wordt het aantal knoop punten in *mynodepool* naar *5*geschaald:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Vermeld opnieuw de status van uw knooppunt groepen met de opdracht [AZ AKS node pool List][az-aks-nodepool-list] . In het volgende voor beeld ziet u dat *mynodepool* zich in de *schaal* status bevindt met een nieuw aantal van *5* knoop punten:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Het duurt enkele minuten voordat de schaal bewerking is voltooid.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Een specifieke knooppunt groep automatisch schalen door de automatische cluster schaalr in te scha kelen

AKS biedt een afzonderlijke functie voor het automatisch schalen van knooppunt groepen met een functie die de automatische [cluster schaalr](cluster-autoscaler.md)wordt genoemd. Deze functie kan worden ingeschakeld per knooppunt groep met unieke minimum-en maximum schaal aantallen per knooppunt groep. Meer informatie over [het gebruik van de cluster-automatische schaal functie per knooppunt groep](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Een knooppunt groep verwijderen

Als u een pool niet meer nodig hebt, kunt u deze verwijderen en de onderliggende VM-knoop punten verwijderen. Als u een knooppunt groep wilt verwijderen, gebruikt u de opdracht [AZ AKS node pool delete][az-aks-nodepool-delete] en geeft u de naam van de knooppunt groep op. In het volgende voor beeld worden de *mynoodepool* die zijn gemaakt in de vorige stappen, verwijderd:

> [!CAUTION]
> Er zijn geen herstel opties voor gegevens verlies die kunnen optreden wanneer u een knooppunt groep verwijdert. Als er geen peulen kunnen worden gepland voor andere knooppunt groepen, zijn deze toepassingen niet beschikbaar. Zorg ervoor dat u geen knooppunt groep verwijdert wanneer in-use-toepassingen geen gegevens back-ups hebben of de mogelijkheid om uit te voeren op andere knooppunt groepen in uw cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

In de volgende voorbeeld uitvoer van de opdracht [AZ AKS node pool List][az-aks-nodepool-list] wordt aangegeven dat *Mynodepool* de status *verwijderen* heeft:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Het duurt enkele minuten om de knoop punten en de knooppunt groep te verwijderen.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Een VM-grootte voor een knooppunt groep opgeven

In de vorige voor beelden voor het maken van een knooppunt groep is een standaard VM-grootte gebruikt voor de knoop punten die in het cluster zijn gemaakt. Een veelvoorkomend scenario is het maken van knooppunt groepen met verschillende VM-grootten en-mogelijkheden. U kunt bijvoorbeeld een knooppunt groep maken die knoop punten bevat met grote hoeveel heden CPU of geheugen, of een knooppunt groep die GPU-ondersteuning biedt. In de volgende stap maakt u [gebruik van taints en verdragen](#schedule-pods-using-taints-and-tolerations) om de Kubernetes scheduler te laten zien hoe de toegang tot het peul kan worden beperkt op deze knoop punten.

In het volgende voor beeld maakt u een op GPU gebaseerde knooppunt groep die gebruikmaakt van de *Standard_NC6* VM-grootte. Deze Vm's worden aangedreven door de NVIDIA Tesla K80-kaart. Zie [grootten voor virtuele Linux-machines in azure][vm-sizes]voor meer informatie over de beschik bare VM-grootten.

Maak een knooppunt groep met de opdracht [AZ AKS node pool add][az-aks-nodepool-add] opnieuw. Geef deze keer de naam *gpunodepool*op en gebruik de para meter `--node-vm-size` om de *Standard_NC6* grootte op te geven:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

In de volgende voorbeeld uitvoer van de opdracht [AZ AKS node pool List][az-aks-nodepool-list] wordt aangegeven dat *Gpunodepool* knoop punten *maakt* met de opgegeven *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Het duurt enkele minuten voordat de *gpunodepool* is gemaakt.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Planning van peulen met behulp van taints en verdragen

U hebt nu twee knooppunt groepen in uw cluster: de standaard groep van het knoop punt dat oorspronkelijk is gemaakt en de op GPU gebaseerde knooppunt groep. Gebruik de opdracht [kubectl Get nodes][kubectl-get] om de knoop punten in uw cluster weer te geven. In de volgende voorbeeld uitvoer ziet u de knoop punten:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

De Kubernetes scheduler kan taints en verdragen gebruiken om te beperken welke workloads op knoop punten kunnen worden uitgevoerd.

* Een **Taint** wordt toegepast op een knoop punt dat aangeeft dat alleen een specifiek peul kan worden gepland.
* Vervolgens wordt er een **verdragen** toegepast op een pod waarmee de Taint van een knoop punt kunnen worden *toegestaan* .

Zie [Best Practices for Advanced scheduler-functies in AKS][taints-tolerations] voor meer informatie over het gebruik van geavanceerde Kubernetes-functies.

In dit voor beeld past u een Taint toe op uw GPU-knoop punt met behulp van de opdracht--node-taints. Geef de naam van uw op GPU gebaseerde knoop punt op uit de uitvoer van de vorige `kubectl get nodes` opdracht. De Taint wordt toegepast als *sleutel: waarde* en vervolgens een plannings optie. In het volgende voor beeld wordt het combi natie *SKU = GPU* gebruikt en definieert u de mogelijkheid om het *schema* niet te gebruiken:

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

In het volgende voor beeld van een YAML-manifest wordt met behulp van een tolerantie toegestaan dat de Kubernetes scheduler een NGINX pod op het op GPU gebaseerde knoop punt uitvoert. Zie [Gpu's gebruiken voor computerintensieve werk belastingen op AKS][gpu-cluster]voor een meer geschikt, maar tijdrovende voor beeld om een tensor flow-taak uit te voeren op basis van de MNIST-gegevensset.

Maak een bestand met de naam `gpu-toleration.yaml` en kopieer het in het volgende voor beeld YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Plan de Pod met de opdracht `kubectl apply -f gpu-toleration.yaml`:

```console
kubectl apply -f gpu-toleration.yaml
```

Het duurt een paar seconden om de Pod te plannen en de NGINX-installatie kopie op te halen. Gebruik de [kubectl pod opdracht beschrijven][kubectl-describe] om de status van de pod weer te geven. In de volgende verkorte voorbeeld uitvoer ziet u de *SKU = GPU:* de verdragen waarbij geen schema wordt toegepast. In de sectie Events heeft de scheduler de pod toegewezen aan het knoop punt *AKS-gpunodepool-28993262-vmss000000* op basis van GPU:

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Alleen een van de peulen waarvoor deze Taint is toegepast, kan worden gepland op knoop punten in *gpunodepool*. Elk ander pod zou worden gepland in de *nodepool1* -knooppunt groep. Als u extra knooppunt groepen maakt, kunt u extra taints en toleranties gebruiken om te beperken wat er op deze knooppunt bronnen kan worden gepland.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Knooppunt Pools beheren met een resource manager-sjabloon

Wanneer u een Azure Resource Manager sjabloon gebruikt om resources te maken en te beheren, kunt u de instellingen in uw sjabloon doorgaans bijwerken en opnieuw implementeren om de resource bij te werken. Met knooppunt Pools in AKS kan het oorspronkelijke knooppunt groeps profiel niet worden bijgewerkt nadat het AKS-cluster is gemaakt. Dit gedrag houdt in dat u een bestaande resource manager-sjabloon niet kunt bijwerken, een wijziging kunt aanbrengen in de knooppunt groepen en opnieuw moet implementeren. In plaats daarvan moet u een afzonderlijke resource manager-sjabloon maken waarmee alleen de knooppunt groepen voor een bestaand AKS-cluster worden bijgewerkt.

Maak een sjabloon, zoals `aks-agentpools.json` en plak het volgende voorbeeld manifest. Met deze voorbeeld sjabloon worden de volgende instellingen geconfigureerd:

* Hiermee werkt u de *Linux* -knooppunt groep met de naam *myagentpool* bij om drie knoop punten uit te voeren.
* Hiermee stelt u de knoop punten in de knooppunt groep in op het uitvoeren van Kubernetes-versie *1.15.7*.
* Hiermee definieert u de knooppunt grootte als *Standard_DS2_v2*.

Bewerk deze waarden als u wilt dat er knooppunt groepen worden bijgewerkt, toegevoegd of verwijderd:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Implementeer deze sjabloon met de opdracht [AZ Group Deployment Create][az-group-deployment-create] , zoals wordt weer gegeven in het volgende voor beeld. U wordt gevraagd om de bestaande naam en locatie van het AKS-cluster:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> U kunt een tag toevoegen aan de knooppunt groep door de eigenschap *tag* in de sjabloon toe te voegen, zoals wordt weer gegeven in het volgende voor beeld.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Het kan een paar minuten duren voordat u uw AKS-cluster bijwerkt, afhankelijk van de instellingen van de knooppunt groep en de bewerkingen die u in uw Resource Manager-sjabloon definieert.

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>Een openbaar IP-adres per knoop punt in een knooppunt groep toewijzen

> [!WARNING]
> Tijdens de preview van het toewijzen van een openbaar IP-adres per knoop punt, kan het niet worden gebruikt met de *Standard load BALANCER SKU in AKS* vanwege mogelijke Load Balancer regels die conflicteren met de inrichting van de virtuele machine. Als gevolg van deze beperking worden Windows-agent groepen niet ondersteund met deze preview-functie. In de preview-versie moet u de *Basic Load BALANCER SKU* gebruiken als u een openbaar IP-adres per knoop punt moet toewijzen.

AKS-knoop punten vereisen geen eigen open bare IP-adressen voor communicatie. Het kan echter voor komen dat knoop punten in een knooppunt groep hun eigen open bare IP-adressen hebben. Een voor beeld is gaming, waarbij een-console een directe verbinding moet maken met een virtuele machine in de cloud om hops te minimaliseren. Dit kan worden bereikt door te registreren voor een afzonderlijke preview-functie, open bare IP-adres (preview) van het knoop punt.

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Nadat de registratie is voltooid, implementeert u een Azure Resource Manager-sjabloon na dezelfde instructies als [hierboven](#manage-node-pools-using-a-resource-manager-template) en voegt u de eigenschap booleaanse waarde `enableNodePublicIP` toe aan agentPoolProfiles. Stel de waarde in op `true` zoals deze standaard is ingesteld als `false` indien niet opgegeven. Dit is een alleen-maken eigenschap en vereist een minimale API-versie van 2019-06-01. Dit kan worden toegepast op zowel Linux-als Windows-knooppunt groepen.

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel hebt u een AKS-cluster gemaakt dat op GPU gebaseerde knoop punten bevat. U kunt de *gpunodepool*of het hele AKS-cluster verwijderen om onnodige kosten te verminderen.

Als u de op GPU gebaseerde knooppunt groep wilt verwijderen, gebruikt u de opdracht [AZ AKS nodepool delete][az-aks-nodepool-delete] , zoals in het volgende voor beeld wordt weer gegeven:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Als u het cluster zelf wilt verwijderen, gebruikt u de opdracht [AZ Group delete][az-group-delete] om de resource groep AKS te verwijderen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u meerdere knooppunt groepen kunt maken en beheren in een AKS-cluster. Zie [Aanbevolen procedures voor geavanceerde functies van scheduler in AKS][operator-best-practices-advanced-scheduler]voor meer informatie over het beheren van de verschillende knooppunt groepen.

Zie [een Windows Server-container maken in AKS][aks-windows]om Windows Server-container knooppunt groepen te maken en te gebruiken.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[quotas-skus-regions]: quotas-skus-regions.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
