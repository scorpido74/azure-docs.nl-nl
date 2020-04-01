---
title: Meerdere knooppuntgroepen gebruiken in Azure Kubernetes Service (AKS)
description: Meer informatie over het maken en beheren van meerdere knooppuntgroepen voor een cluster in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 607419787bc0bab243d6cc2b8cbaa0ec22921e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422316"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Meerdere knooppuntgroepen voor een cluster maken en beheren in Azure Kubernetes Service (AKS)

In Azure Kubernetes Service (AKS) worden knooppunten van dezelfde configuratie gegroepeerd in *knooppuntgroepen.* Deze knooppuntgroepen bevatten de onderliggende VM's waarop uw toepassingen worden uitgevoerd. Het beginaantal knooppunten en de grootte (SKU) worden gedefinieerd wanneer u een AKS-cluster maakt, waardoor een *standaardknooppuntgroep wordt gemaakt.* Als u toepassingen met verschillende reken- of opslagvereisten wilt ondersteunen, u extra knooppuntgroepen maken. Gebruik deze extra knooppuntgroepen bijvoorbeeld om GPU's te leveren voor computerintensieve toepassingen of toegang tot krachtige SSD-opslag.

> [!NOTE]
> Deze functie biedt meer controle over het maken en beheren van meerdere knooppuntgroepen. Als gevolg hiervan zijn afzonderlijke opdrachten vereist voor het maken/bijwerken/verwijderen. Voorheen clusterbewerkingen via `az aks create` of `az aks update` gebruikt en gebruikten de managedCluster API en waren de enige optie om uw besturingsvlak en één knooppuntgroep te wijzigen. Deze functie legt een afzonderlijke bewerkingsset voor agentgroepen bloot via `az aks nodepool` de agentPool-API en vereist het gebruik van de opdrachtset om bewerkingen uit te voeren op een afzonderlijke knooppuntgroep.

In dit artikel ziet u hoe u meerdere knooppuntgroepen in een AKS-cluster maakt en beheert.

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.2.0 of hoger installeren en configureren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][install-azure-cli].

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die meerdere knooppuntgroepen ondersteunen:

* Zie [Quota, beperkingen voor de grootte van virtuele machines en regiobeschikbaarheid in Azure Kubernetes Service (AKS)][quotas-skus-regions].
* U de groep systeemknooppuntsniet verwijderen, standaard de eerste knooppuntgroep.
* Het AKS-cluster moet de Standaard SKU-loadbalancer gebruiken om meerdere knooppuntgroepen te gebruiken, de functie wordt niet ondersteund met Basis-SKU-loadbalancers.
* Het AKS-cluster moet virtuele machineschaalsets voor de knooppunten gebruiken.
* De naam van een knooppuntgroep mag alleen alfanumerieke tekens voor kleine letters bevatten en moet beginnen met een kleine letter. Voor Linux-knooppuntpools moet de lengte tussen 1 en 12 tekens liggen, voor Windows-knooppuntgroepen moet de lengte tussen 1 en 6 tekens liggen.
* Alle knooppuntgroepen moeten zich in hetzelfde virtuele netwerk bevinden.
* Bij het maken van meerdere knooppuntgroepen in clustermaaktijd moeten alle Kubernetes-versies die door knooppuntgroepen worden gebruikt, overeenkomen met de versie die is ingesteld voor het beheervlak. Dit kan worden bijgewerkt nadat het cluster is ingericht met behulp van bewerkingen per knooppuntgroep.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Maak een AKS-cluster met één knooppuntgroep om aan de slag te gaan. In het volgende voorbeeld wordt de opdracht [Az-groep maken][az-group-create] gebruikt om een resourcegroep met de naam *myResourceGroup* in het *eastus-gebied* te maken. Vervolgens wordt een AKS-cluster met de naam *myAKSCluster* gemaakt met de opdracht [az aks create.][az-aks-create] Een *--kubernetes-versie* van *1.15.7* wordt gebruikt om te laten zien hoe u een knooppuntpool in een volgende stap bijwerken. U elke [ondersteunde Kubernetes-versie][supported-versions]opgeven.

> [!NOTE]
> De *Basic* load balancer SKU **wordt niet ondersteund** bij het gebruik van meerdere knooppuntgroepen. Standaard worden AKS-clusters gemaakt met de *SKU* voor standaardlastbalancer van Azure CLI en Azure-portal.

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
> Om ervoor te zorgen dat uw cluster betrouwbaar werkt, moet u ten minste twee (twee) knooppunten uitvoeren in de standaardknooppuntgroep, omdat essentiële systeemservices in deze knooppuntgroep worden uitgevoerd.

Wanneer het cluster klaar is, gebruikt u de opdracht [az aks-referenties][az-aks-get-credentials] om de clusterreferenties te krijgen voor gebruik met: `kubectl`

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Een knooppuntgroep toevoegen

Het cluster dat in de vorige stap is gemaakt, heeft één knooppuntgroep. Laten we een tweede knooppuntpool toevoegen met de opdracht [az aks nodepool toevoegen.][az-aks-nodepool-add] In het volgende voorbeeld wordt een knooppuntgroep met de naam *mynodepool* gemaakt met *drie* knooppunten:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> De naam van een knooppuntgroep moet beginnen met een kleine letter en kan alleen alfanumerieke tekens bevatten. Voor Linux-knooppuntpools moet de lengte tussen 1 en 12 tekens liggen, voor Windows-knooppuntgroepen moet de lengte tussen 1 en 6 tekens liggen.

Als u de status van uw knooppuntgroepen wilt bekijken, gebruikt u de opdracht lijst met az [aks-knooppuntgroepen][az-aks-nodepool-list] en geeft u de naam van uw brongroep en cluster op:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

In de volgende voorbeelduitvoerwordt weergegeven dat *mynodepool* is gemaakt met drie knooppunten in de knooppuntgroep. Toen het AKS-cluster in de vorige stap werd gemaakt, is een *standaardknooppunt1* gemaakt met een knooppunttelling van *2*.

```output
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
> Als er geen *VmSize* is opgegeven wanneer u een knooppuntgroep toevoegt, wordt de standaardgrootte *Standard_DS2_v3* voor Windows-knooppuntgroepen en *Standard_DS2_v2* voor Linux-knooppuntgroepen. Als er geen *OrchestratorVersion* is opgegeven, wordt deze standaard ingesteld op dezelfde versie als het besturingsvlak.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Een knooppuntgroep toevoegen met een uniek subnet (voorbeeld)

Een werkbelasting vereist mogelijk het splitsen van knooppunten van een cluster in afzonderlijke groepen voor logische isolatie. Deze isolatie kan worden ondersteund met afzonderlijke subnetten die zijn gewijd aan elke knooppuntgroep in het cluster. Dit kan voldoen aan vereisten, zoals het hebben van niet-aaneengesloten virtuele netwerkadresruimte om te splitsen over knooppuntgroepen.

#### <a name="limitations"></a>Beperkingen

* Alle subnetten die aan nodepools zijn toegewezen, moeten tot hetzelfde virtuele netwerk behoren.
* Systeempods moeten toegang hebben tot alle knooppunten in het cluster om kritieke functionaliteit te bieden, zoals DNS-resolutie via coreDNS.
* Toewijzing van een uniek subnet per knooppuntgroep is beperkt tot Azure CNI tijdens preview.
* Het gebruik van netwerkbeleid met een uniek subnet per knooppuntgroep wordt niet ondersteund tijdens preview.

Als u een knooppuntgroep met een speciaal subnet wilt maken, geeft u de subnetbron-id als extra parameter door bij het maken van een knooppuntgroep.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Een knooppuntgroep upgraden

> [!NOTE]
> Upgrade- en schaalbewerkingen op een cluster- of knooppuntgroep kunnen niet tegelijkertijd plaatsvinden als een fout wordt geretourneerd. In plaats daarvan moet elk bewerkingstype worden voltooid op de doelbron voordat de volgende aanvraag op dezelfde resource wordt ingediend. Lees hier meer over in onze [handleiding voor probleemoplossing.](https://aka.ms/aks-pending-upgrade)

Toen uw AKS-cluster in eerste instantie `--kubernetes-version` in de eerste stap werd gemaakt, werd een van *1.15.7* opgegeven. Hiermee wordt de Kubernetes-versie ingesteld voor zowel het besturingsvlak als de standaardknooppuntgroep. In de opdrachten in deze sectie wordt uitgelegd hoe u één specifieke knooppuntgroep upgraden.

De relatie tussen het upgraden van de Kubernetes-versie van het controlevlak en de knooppuntgroep wordt uitgelegd in het [onderstaande gedeelte.](#upgrade-a-cluster-control-plane-with-multiple-node-pools)

> [!NOTE]
> De afbeeldingsversie van node pool OS is gekoppeld aan de Kubernetes-versie van het cluster. U krijgt alleen os-afbeeldingupgrades, na een clusterupgrade.

Aangezien er twee knooppuntzwembaden in dit voorbeeld zijn, moeten we [az aks nodepool-upgrade][az-aks-nodepool-upgrade] gebruiken om een knooppuntzwembad te upgraden. Laten we de *mynodepool* upgraden naar Kubernetes *1.15.7*. Gebruik de [upgradeopdracht AZ AKS Nodepool][az-aks-nodepool-upgrade] om de knooppuntgroep te upgraden, zoals in het volgende voorbeeld wordt weergegeven:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

Vermeld de status van uw knooppuntpools opnieuw met de opdracht az [aks-knooppuntgroeplijst.][az-aks-nodepool-list] In het volgende voorbeeld wordt aangegeven dat *mynodepool* zich in de *status Upgraden* bevindt naar *1.15.7:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
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

Het duurt een paar minuten om de knooppunten te upgraden naar de opgegeven versie.

Als best practice moet u alle knooppuntgroepen in een AKS-cluster upgraden naar dezelfde Kubernetes-versie. Het standaardgedrag `az aks upgrade` van is het upgraden van alle knooppuntpools samen met het besturingsvlak om deze uitlijning te bereiken. Met de mogelijkheid om afzonderlijke knooppuntgroepen te upgraden, u een rolling upgrade uitvoeren en pods tussen knooppuntgroepen plannen om de uptime van de toepassing te behouden binnen de bovengenoemde beperkingen.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Een clustercontrolevlak upgraden met meerdere knooppuntgroepen

> [!NOTE]
> Kubernetes maakt gebruik van het standaard [semantische versieschema.](https://semver.org/) Het versienummer wordt uitgedrukt als *x.y.z*, waar *x* de belangrijkste versie is, *y* is de kleine versie, en *z* is de patchversie. Bijvoorbeeld, in versie *1.12.6*, 1 is de belangrijkste versie, 12 is de kleine versie, en 6 is de patch versie. De Kubernetes-versie van het besturingsvlak en de eerste knooppuntgroep worden ingesteld tijdens het maken van het cluster. Alle extra knooppuntpools hebben hun Kubernetes-versie ingesteld wanneer ze aan het cluster worden toegevoegd. De Kubernetes-versies kunnen verschillen tussen knooppuntpools en tussen een knooppuntgroep en het controlevlak.

Een AKS-cluster heeft twee clusterbronobjecten met Kubernetes-versies.

1. Een Cluster control plane Kubernetes versie.
2. Een knooppuntpool met een Kubernetes-versie.

Een controlevliegtuig wordt toegewezen aan één of meer knooppuntzwembaden. Het gedrag van een upgradebewerking is afhankelijk van de opdracht Azure CLI.

Voor het upgraden van `az aks upgrade`een AKS-besturingsvlak is gebruik vereist. Met deze opdracht worden de versie van het besturingsvlak en alle knooppuntgroepen in het cluster bijgewerkt.

Als u `az aks upgrade` de `--control-plane-only` opdracht uitgeeft met de vlag, worden alleen het clustercontrolevlak uitgevoerd. Geen van de bijbehorende knooppuntgroepen in het cluster wordt gewijzigd.

Voor het upgraden van `az aks nodepool upgrade`afzonderlijke knooppuntpools is gebruik nodig van . Met deze opdracht wordt alleen de doelknooppuntgroep bijgewerkt met de opgegeven Kubernetes-versie

### <a name="validation-rules-for-upgrades"></a>Validatieregels voor upgrades

De geldige Kubernetes-upgrades voor het controlevlak en knooppuntpools van een cluster worden gevalideerd door de volgende regels.

* Regels voor geldige versies om knooppuntgroepen te upgraden:
   * De node pool versie moet dezelfde *grote* versie hebben als het controlevlak.
   * De *secundaire* versie van de knooppuntgroep moet zich binnen twee *kleine* versies van de versie van het controlevlak begeven.
   * De versie van de knooppuntgroep `major.minor.patch` kan niet groter zijn dan de besturingsversie.

* Regels voor het indienen van een upgradebewerking:
   * U het besturingsvlak of een Kubernetes-versie van het knooppunt niet downgraden.
   * Als een Kubernetes-versie van een knooppuntgroep niet is opgegeven, is het gedrag afhankelijk van de client die wordt gebruikt. Declaratie in Resource Manager-sjablonen valt terug op de bestaande versie die is gedefinieerd voor de knooppuntgroep indien deze wordt gebruikt, als er geen is ingesteld, wordt de versie van het besturingsvlak gebruikt om op terug te vallen.
   * U een besturingsvlak of een knooppuntgroep op een bepaald moment upgraden of schalen, u niet meerdere bewerkingen tegelijk indienen op één controlevlak of knooppuntgroepbron.

## <a name="scale-a-node-pool-manually"></a>Een knooppuntgroep handmatig schalen

Als de werkbelasting van uw toepassing om verandering vraagt, moet u mogelijk het aantal knooppunten in een knooppuntgroep schalen. Het aantal knooppunten kan worden opgeschaald of omlaag.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Als u het aantal knooppunten in een knooppuntgroep wilt schalen, gebruikt u de opdracht de schaalschaal van [az aks-knooppunten.][az-aks-nodepool-scale] In het volgende voorbeeld wordt het aantal knooppunten in *mynodepool geschaald* naar *5:*

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Vermeld de status van uw knooppuntpools opnieuw met de opdracht az [aks-knooppuntgroeplijst.][az-aks-nodepool-list] In het volgende voorbeeld ziet u dat *mynodepool* zich in de *status Schalen bevindt* met een nieuw aantal van *5* knooppunten:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
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

Het duurt een paar minuten voordat de schaalbewerking is voltooid.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Een specifieke knooppuntgroep automatisch schalen door de clusterautoscaler in te schakelen

AKS biedt een aparte functie om knooppuntpools automatisch te schalen met een functie genaamd de [clusterautoscaler.](cluster-autoscaler.md) Deze functie kan per knooppuntgroep worden ingeschakeld met unieke minimum- en maximale schaaltellingen per knooppuntgroep. Meer informatie over het [gebruik van de clusterautoscaler per knooppuntgroep](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Een knooppuntgroep verwijderen

Als u geen pool meer nodig hebt, u deze verwijderen en de onderliggende VM-knooppunten verwijderen. Als u een knooppuntgroep wilt verwijderen, gebruikt u de opdracht az [aks-knooppuntgroep verwijderen][az-aks-nodepool-delete] en geeft u de naam van de knooppuntgroep op. In het volgende voorbeeld wordt de *mynoodepool* verwijderd die in de vorige stappen is gemaakt:

> [!CAUTION]
> Er zijn geen herstelopties voor gegevensverlies die kunnen optreden wanneer u een knooppuntgroep verwijdert. Als pods niet kunnen worden gepland op andere knooppuntgroepen, zijn deze toepassingen niet beschikbaar. Zorg ervoor dat u een knooppuntgroep niet verwijdert wanneer toepassingen in gebruik zijn geen back-ups van gegevens of de mogelijkheid om op andere knooppuntgroepen in uw cluster uit te voeren.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

In de volgende voorbeelduitvoer van de [opdracht az aks-knooppuntgroeplijst][az-aks-nodepool-list] wordt weergegeven dat *mynodepool* zich in de *status Verwijderen* bevindt:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
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

Het duurt een paar minuten om de knooppunten en de knooppuntgroep te verwijderen.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Een VM-grootte opgeven voor een knooppuntgroep

In de vorige voorbeelden om een knooppuntgroep te maken, werd een standaard VM-grootte gebruikt voor de knooppunten die in het cluster zijn gemaakt. Een veelvoorkomend scenario is dat u knooppuntgroepen maakt met verschillende VM-formaten en -mogelijkheden. U bijvoorbeeld een knooppuntgroep maken die knooppunten bevat met grote hoeveelheden CPU of geheugen, of een knooppuntgroep die GPU-ondersteuning biedt. In de volgende stap gebruikt u [taints en toleranties](#schedule-pods-using-taints-and-tolerations) om de Kubernetes-planner te vertellen hoe u de toegang tot pods die op deze knooppunten kunnen worden uitgevoerd, beperken.

Maak in het volgende voorbeeld een op GPU gebaseerde knooppuntgroep die de *Standard_NC6* VM-grootte gebruikt. Deze VM's worden aangedreven door de NVIDIA Tesla K80-kaart. Zie Grootte voor virtuele [Linux-machines in Azure voor][vm-sizes]informatie over beschikbare VM-formaten.

Maak opnieuw een knooppuntpool met de opdracht Az [AKS-knooppuntpool toevoegen.][az-aks-nodepool-add] Geef deze keer de naam *gpunodepool*op en gebruik de `--node-vm-size` parameter om de *Standard_NC6* grootte op te geven:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

In het volgende voorbeeld wordt vanuit de lijst met de [az aks-knooppuntenlijst][az-aks-nodepool-list] weergegeven dat *gpunodepool* *knooppunten maken* met de opgegeven *VmSize:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
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

Het duurt een paar minuten voordat de *gpunodepool* succesvol is gemaakt.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Pods plannen met taints en toleranties

U hebt nu twee knooppuntgroepen in uw cluster: de standaardknooppuntgroep die in eerste instantie is gemaakt en de op GPU gebaseerde knooppuntgroep. Gebruik de opdracht [kubectl get nodes][kubectl-get] om de knooppunten in uw cluster weer te geven. In de volgende voorbeelduitvoer worden de knooppunten weergegeven:

```console
kubectl get nodes
```

```output
NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

De Kubernetes scheduler kan taints en toleranties gebruiken om te beperken welke workloads op knooppunten kunnen worden uitgevoerd.

* Er wordt een **taint** toegepast op een knooppunt dat aangeeft dat er alleen specifieke pods op kunnen worden gepland.
* Een **tolerantie** wordt vervolgens toegepast op een pod die hen in staat stelt om een node's taint *tolereren.*

Zie [Aanbevolen procedures voor geavanceerde plannerfuncties in AKS][taints-tolerations] voor meer informatie over het gebruik van geavanceerde kubernetes-geplande functies

Breng in dit voorbeeld een taint toe op uw GPU-gebaseerde knooppunt met de opdracht --node-taints. Geef de naam van uw GPU-knooppunt op `kubectl get nodes` uit de uitvoer van de vorige opdracht. De taint wordt toegepast als een *sleutel:waarde* en vervolgens een planningsoptie. In het volgende voorbeeld wordt het *sku=gpu-paar* gebruikt en worden pods gedefinieerd die anders de *noschedule-mogelijkheid* hebben:

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Het volgende basisvoorbeeld YAML-manifest maakt gebruik van een tolerantie om de Kubernetes-planner in staat te stellen een NGINX-pod op het OP GPU gebaseerde knooppunt uit te voeren. Zie [GPU's gebruiken voor computerintensieve workloads op AKS][gpu-cluster]voor een geschikter, maar tijdsintensief voorbeeld om een Tensorflow-taak uit te voeren ten opzichte van de MNIST-gegevensset.

Maak een `gpu-toleration.yaml` bestand met de naam en kopie in het volgende voorbeeld YAML:

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

Plan de pod `kubectl apply -f gpu-toleration.yaml` met de opdracht:

```console
kubectl apply -f gpu-toleration.yaml
```

Het duurt een paar seconden om de pod te plannen en trek de NGINX-afbeelding. Gebruik de opdracht [kubectl describe pod][kubectl-describe] om de podstatus weer te geven. De volgende verkorte voorbeelduitvoer toont de *sku=gpu:NoSchedule-tolerantie* wordt toegepast. In de sectie gebeurtenissen heeft de planner de pod toegewezen aan het op *AKS-gpunodepool-28993262-vms000000* GPU-gebaseerde knooppunt:

```console
kubectl describe pod mypod
```

```output
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

Alleen pods die deze taint hebben toegepast, kunnen worden gepland op knooppunten in *gpunodepool.* Elke andere pod zou worden gepland in de *nodepool1* knooppunt zwembad. Als u extra knooppuntgroepen maakt, u extra taints en toleranties gebruiken om te beperken welke pods kunnen worden gepland op die knooppuntbronnen.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Een taint, label of tag opgeven voor een knooppuntgroep

Wanneer u een knooppuntgroep maakt, u taints, labels of tags toevoegen aan die knooppuntgroep. Wanneer u een taint, label of tag toevoegt, krijgen alle knooppunten binnen die knooppuntgroep ook die smet, het label of de tag.

Als u een knooppuntzwembad met een taint wilt maken, gebruikt u [az aks nodepool toevoegen.][az-aks-nodepool-add] Geef de naam *taintnp* op en gebruik de `--node-taints` parameter om *sku=gpu op te geven:NoSchedule* voor de taint.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

In het volgende voorbeeld dat wordt uitgevoerd vanuit de opdracht [az aks-knooppuntenlijst,][az-aks-nodepool-list] wordt aangegeven dat *taintnp* *knooppunten maken* met de opgegeven *nodeTaints:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  {
      "sku": "gpu:NoSchedule"
    },
    ...
  },
 ...
]
```

De taint-informatie is zichtbaar in Kubernetes voor het verwerken van planningsregels voor knooppunten.

U ook labels toevoegen aan een knooppuntgroep tijdens het maken van een knooppuntgroep. Labels die bij het knooppuntzwembad zijn ingesteld, worden toegevoegd aan elk knooppunt in het knooppuntzwembad. Deze [labels zijn zichtbaar in Kubernetes][kubernetes-labels] voor het verwerken van planningsregels voor knooppunten.

Als u een knooppuntpool met een label wilt maken, gebruikt u [az aks nodepool toevoegen][az-aks-nodepool-add]. Geef de *naamlabelnp* `--labels` op en gebruik de parameter om *dept=IT* en *costcenter=9999* voor labels op te geven.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Label kan alleen worden ingesteld voor knooppuntpools tijdens het maken van knooppuntgroepen. Labels moeten ook een sleutel/waardepaar zijn en een [geldige syntaxis][kubernetes-label-syntax]hebben.

In het volgende voorbeeld uitvoer van de az [aks nodepool lijst][az-aks-nodepool-list] opdracht blijkt dat *labelnp* is *Het maken van* knooppunten met de opgegeven *nodeLabels:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

U een Azure-tag toepassen op knooppuntgroepen in uw AKS-cluster. Tags die op een knooppuntgroep worden toegepast, worden toegepast op elk knooppunt binnen de knooppuntgroep en blijven bestaan door upgrades. Tags worden ook toegepast op nieuwe knooppunten die tijdens scale-outbewerkingen aan een knooppuntgroep zijn toegevoegd. Het toevoegen van een tag kan helpen bij taken zoals beleidstracking of kostenschatting.

Maak een knooppuntpool met de [add-to-poule van AZ Aks Nodepool.][az-aks-nodepool-add] Geef de *naamtagnodepool* `--tag` op en gebruik de parameter om *dept=IT* en *costcenter=9999* voor tags op te geven.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> U de `--tags` parameter ook gebruiken bij het gebruik van de opdracht AZ [Aks Nodepool update][az-aks-nodepool-update] en bij het maken van het cluster. Tijdens het maken `--tags` van het cluster past de parameter de tag toe op de eerste knooppuntgroep die met het cluster is gemaakt. Alle tagnamen moeten voldoen aan de beperkingen in [Tags gebruiken om uw Azure-resources te ordenen.][tag-limitation] Een knooppuntgroep bijwerken `--tags` met de parameter werkt eventuele bestaande tagwaarden bij en voegt nieuwe tags toe. Als uw knooppuntpool bijvoorbeeld *dept=IT* en *costcenter=9999* voor tags had en u deze hebt bijgewerkt met *team=dev* en *costcenter=111* voor tags, heeft u *dept=IT,* *costcenter=111*en *team=dev* voor tags.

In het volgende voorbeeld uitvoer van de az [aks nodepool lijst][az-aks-nodepool-list] opdracht blijkt dat *tagnodepool* *is Het maken van* knooppunten met de opgegeven *tag:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Knooppuntgroepen beheren met een sjabloon Resourcebeheer

Wanneer u een Azure Resource Manager-sjabloon gebruikt om resources te maken en te beheren, u de instellingen in uw sjabloon doorgaans bijwerken en opnieuw implementeren om de bron bij te werken. Met knooppuntgroepen in AKS kan het oorspronkelijke knooppuntgroepprofiel niet worden bijgewerkt nadat het AKS-cluster is gemaakt. Dit gedrag betekent dat u een bestaande resourcemanagersjabloon niet bijwerken, geen wijzigingen aanbrengen in de knooppuntgroepen en opnieuw implementeren. In plaats daarvan moet u een afzonderlijke resourcemanagersjabloon maken die alleen de knooppuntgroepen voor een bestaand AKS-cluster bijwerkt.

Maak een sjabloon `aks-agentpools.json` zoals en plak het volgende voorbeeldmanifest. Met deze voorbeeldsjabloon worden de volgende instellingen geconfigureerd:

* Werkt *Linux* de Linux-knooppuntpool met de naam *myagentpool* bij om drie knooppunten uit te voeren.
* Hiermee stelt u de knooppunten in de knooppuntgroep in om Kubernetes-versie *1.15.7*uit te voeren.
* Hiermee definieert u de grootte van het knooppunt als *Standard_DS2_v2*.

Bewerk deze waarden als nodig is om knooppuntgroepen bij te werken, toe te voegen of te verwijderen:

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

Implementeer deze sjabloon met de [opdracht Implementatie van az-groep,][az-group-deployment-create] zoals in het volgende voorbeeld wordt weergegeven. U wordt gevraagd om de bestaande AKS-clusternaam en -locatie:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> U een tag toevoegen aan uw knooppuntgroep door de eigenschap *tag* in de sjabloon toe te voegen, zoals in het volgende voorbeeld wordt weergegeven.
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

Het kan enkele minuten duren voordat uw AKS-cluster wordt bijgewerkt, afhankelijk van de instellingen en bewerkingen van de knooppuntgroep die u definieert in de sjabloon Resourcemanager.

## <a name="assign-a-public-ip-per-node-for-a-node-pool-preview"></a>Een openbaar IP per knooppunt toewijzen voor een knooppuntgroep (voorbeeld)

> [!WARNING]
> Tijdens de preview van het toewijzen van een openbaar IP per knooppunt, kan het niet worden gebruikt met de *Standard Load Balancer SKU in AKS* vanwege mogelijke regels voor load balancer die in strijd zijn met VM-provisioning. Als gevolg van deze beperking worden Windows-agentpools niet ondersteund met deze voorbeeldfunctie. Terwijl u in de preview zit, moet u de *Basic Load Balancer SKU* gebruiken als u een openbaar IP per knooppunt moet toewijzen.

AKS-knooppunten hebben geen eigen openbare IP-adressen nodig voor communicatie. Scenario's vereisen echter mogelijk knooppunten in een knooppuntgroep om hun eigen specifieke openbare IP-adressen te ontvangen. Een veelvoorkomend scenario is voor gamingworkloads, waarbij een console een directe verbinding moet maken met een virtuele cloudmachine om hop te minimaliseren. Dit scenario kan worden bereikt op AKS door u te registreren voor een preview-functie, Node Public IP (preview).

Registreer u voor de IP-functie Knooppunt openbaar door de volgende opdracht Azure CLI uit te geven.

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Na succesvolle registratie implementeert u een Azure [above](#manage-node-pools-using-a-resource-manager-template) Resource Manager-sjabloon volgens `enableNodePublicIP` dezelfde instructies als hierboven en voegt u de booleaanse eigenschap toe aan agentPoolProfiles. Stel de `true` waarde in op standaardinstelling als `false` of deze niet is opgegeven. 

Deze eigenschap is een eigenschap die alleen create-time is en een minimale API-versie van 2019-06-01 vereist. Dit kan worden toegepast op zowel Linux- als Windows-knooppuntgroepen.

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel hebt u een AKS-cluster gemaakt met op GPU gebaseerde knooppunten. Om onnodige kosten te verminderen, u de *gpunodepool*of het hele AKS-cluster verwijderen.

Als u de knooppuntgroep OP BASIS van GPU wilt verwijderen, gebruikt u de opdracht [verwijderen van AZ AKS-nodepool][az-aks-nodepool-delete] zoals weergegeven in het volgende voorbeeld:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Als u het cluster zelf wilt verwijderen, gebruikt u de opdracht verwijderen van de [AZ-groep][az-group-delete] om de AKS-brongroep te verwijderen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u meerdere knooppuntgroepen in een AKS-cluster maken en beheren. Zie [Aanbevolen procedures voor geavanceerde plannerfuncties in AKS][operator-best-practices-advanced-scheduler]voor meer informatie over het beheren van pods in knooppuntgroepen.

Zie [Een Windows Server-container maken in AKS][aks-windows]als u containerknooppuntgroepen van Windows Server wilt maken en gebruiken.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md