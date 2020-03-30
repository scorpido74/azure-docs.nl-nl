---
title: De clusterautoscaler gebruiken in Azure Kubernetes Service (AKS)
description: Meer informatie over het gebruik van de clusterautoscaler om uw cluster automatisch te schalen om te voldoen aan toepassingsvereisten in een AKS-cluster (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 0b94865d81afc56c24d470012c668662f003a1b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596246"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Een cluster automatisch schalen om te voldoen aan de toepassingsvereisten op Azure Kubernetes Service (AKS)

Als u wilt voldoen aan de toepassingsvereisten in Azure Kubernetes Service (AKS), moet u mogelijk het aantal knooppunten aanpassen waarop uw workloads worden uitgevoerd. De component clusterautoscaler kan kijken naar pods in uw cluster die niet kunnen worden gepland vanwege resourcebeperkingen. Wanneer problemen worden gedetecteerd, wordt het aantal knooppunten in een knooppuntgroep verhoogd om aan de vraag naar toepassingen te voldoen. Knooppunten worden ook regelmatig gecontroleerd op een gebrek aan lopende pods, waarbij het aantal knooppunten vervolgens naar behoefte is afgenomen. Met deze mogelijkheid u het aantal knooppunten in uw AKS-cluster automatisch opschalen of omlaag, zodat u een efficiënt, kosteneffectief cluster uitvoeren.

In dit artikel ziet u hoe u de clusterautoscaler in een AKS-cluster inschakelt en beheert.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel moet u de Azure CLI-versie 2.0.76 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die de clusterautoscaler gebruiken:

* De invoegtoepassing HTTP-toepassingroutering kan niet worden gebruikt.

## <a name="about-the-cluster-autoscaler"></a>Informatie over de clusterautoscaler

Om zich aan te passen aan veranderende toepassingseisen, zoals tussen de werkdag en 's avonds of in het weekend, hebben clusters vaak een manier nodig om automatisch te schalen. AKS-clusters kunnen op twee manieren worden geschaald:

* De **clusterautoscaler** kijkt naar pods die niet kunnen worden gepland op knooppunten vanwege resourcebeperkingen. Het cluster verhoogt dan automatisch het aantal knooppunten.
* De **horizontale pod autoscaler** gebruikt de Metrics Server in een Kubernetes-cluster om de bronvraag van pods te controleren. Als een toepassing meer resources nodig heeft, wordt het aantal pods automatisch verhoogd om aan de vraag te voldoen.

![De clusterautoscaler en horizontale pod autoscaler werken vaak samen om de vereiste toepassingsvereisten te ondersteunen](media/autoscaler/cluster-autoscaler.png)

Zowel de horizontale pod autoscaler als cluster autoscaler kunnen ook het aantal pods en knooppunten verlagen als dat nodig is. De clusterautoscaler vermindert het aantal knooppunten wanneer er gedurende een bepaalde periode ongebruikte capaciteit is geweest. Pods op een knooppunt dat door de clusterautoscaler moet worden verwijderd, zijn veilig elders in het cluster gepland. De clusterautoscaler kan mogelijk niet worden geschaald als pods niet kunnen worden verplaatst, zoals in de volgende situaties:

* Een pod wordt rechtstreeks gemaakt en wordt niet ondersteund door een controllerobject, zoals een implementatie- of replicaset.
* Een pod disruption budget (PDB) is te beperkend en staat niet toe dat het aantal pods onder een bepaalde drempel waardestreep valt.
* Een pod maakt gebruik van knooppuntselectors of anti-affiniteit die niet kunnen worden gehonoreerd als deze op een ander knooppunt zijn gepland.

Zie [Welke typen pods kunnen voorkomen dat de clusterautoscaler een knooppunt verwijdert voor][autoscaler-scaledown] meer informatie over hoe de clusterautoscaler mogelijk niet kan worden geschaald?

De clusterautoscaler gebruikt opstartparameters voor zaken als tijdsintervallen tussen schaalgebeurtenissen en resourcedrempels. Deze parameters worden gedefinieerd door het Azure-platform en worden momenteel niet weergegeven om u aan te passen. Zie Wat zijn de parameters van de [clusterautoscaler?][autoscaler-parameters]

De autoscalers van het cluster en de horizontale pod kunnen samenwerken en worden vaak beide geïmplementeerd in een cluster. In combinatie is de horizontale pod autoscaler gericht op het uitvoeren van het aantal pods dat nodig is om te voldoen aan de vraag naar toepassingen. De clusterautoscaler is gericht op het uitvoeren van het aantal knooppunten dat nodig is om de geplande pods te ondersteunen.

> [!NOTE]
> Handmatig schalen is uitgeschakeld wanneer u de clusterautoscaler gebruikt. Laat de clusterautoscaler het vereiste aantal knooppunten bepalen. Als u het cluster handmatig wilt schalen, [schakelt u de clusterautoscaler uit.](#disable-the-cluster-autoscaler)

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Een AKS-cluster maken en de clusterautoscaler inschakelen

Als u een AKS-cluster wilt maken, gebruikt u de opdracht [az aks create.][az-aks-create] Als u de clusterautoscaler inschakelt en configureert op de knooppuntgroep voor het cluster, gebruikt u de parameter *--enable-cluster-autoscaler* en geeft u een knooppunt op *--min-telling* en *--max-telling*.

> [!IMPORTANT]
> De clusterautoscaler is een Kubernetes-component. Hoewel het AKS-cluster een virtuele machineschaalset voor de knooppunten gebruikt, schakelt u instellingen voor schaalsetautomatisch in de Azure-portal of met azure cli niet handmatig in of bewerken deze uit. Laat de Kubernetes cluster autoscaler de vereiste schaal-instellingen beheren. Zie [Kan ik de AKS-resources in de brongroep knooppunt wijzigen voor](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group) meer informatie?

In het volgende voorbeeld wordt een AKS-cluster gemaakt met één knooppuntgroep die wordt ondersteund door een virtuele machineschaalset. Het maakt ook de clusterautoscaler op de knooppuntgroep voor het cluster mogelijk en stelt minimaal *1* en maximaal *3* knooppunten in:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Het duurt een paar minuten om het cluster te maken en de instellingen van de clusterautoscaler te configureren.

## <a name="change-the-cluster-autoscaler-settings"></a>De instellingen voor clusterautoscaler wijzigen

> [!IMPORTANT]
> Als u meerdere knooppuntgroepen in uw AKS-cluster hebt, gaat u naar de [automatische schaal met sectie meerdere agentgroepen.](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled) Voor clusters met meerdere agentgroepen is gebruik van de `az aks nodepool` opdrachtset vereist om de specifieke eigenschappen van de knooppuntgroep te wijzigen in plaats van `az aks`.

In de vorige stap om een AKS-cluster te maken of een bestaande knooppuntgroep bij te werken, is het minimumaantal knooppuntgroepen van de clusterautoscaler ingesteld op *1*en is het maximale aantal nodes ingesteld op *3*. Als uw toepassing wijzigingen aanvraagt, moet u mogelijk het aantal clusterautoscalernode aanpassen.

Als u het aantal knooppunten wilt wijzigen, gebruikt u de opdracht [az aks-update.][az-aks-update]

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Het bovenstaande voorbeeld werkt clusterautoscaler op de single node pool in *myAKSCluster* bij tot een minimum van *1* en maximaal *5* knooppunten.

> [!NOTE]
> U geen hoger minimumknooppuntaantal instellen dan momenteel is ingesteld voor de knooppuntgroep. Als u bijvoorbeeld min-telling op *1*hebt ingesteld, u het aantal minen niet bijwerken naar *3*.

Controleer de prestaties van uw toepassingen en services en pas het aantal clusterautoscalernode's aan de vereiste prestaties aan.

## <a name="using-the-autoscaler-profile"></a>Het autoscalerprofiel gebruiken

U ook gedetailleerdere details van de clusterautoscaler configureren door de standaardwaarden in het clusterbrede autoscalerprofiel te wijzigen. Een scale down-gebeurtenis vindt bijvoorbeeld plaats nadat knooppunten na 10 minuten onderworden gebruikt. Als u workloads had die elke 15 minuten worden uitgevoerd, u het autoscaler-profiel wijzigen om na 15 of 20 minuten onder gebruikte knooppunten te schalen. Wanneer u de clusterautoscaler inschakelt, wordt een standaardprofiel gebruikt, tenzij u verschillende instellingen opgeeft. Het clusterautoscalerprofiel heeft de volgende instellingen die u bijwerken:

| Instelling                          | Beschrijving                                                                              | Standaardwaarde |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| scan-interval                    | Hoe vaak cluster opnieuw wordt geëvalueerd voor op- of opschalen                                    | 10 seconden    |
| scale-down-delay-after-add       | Hoe lang na het opschalen van die schaal naar beneden evaluatie hervat                               | 10 minuten    |
| scale-down-delay-after-delete    | Hoe lang na het verwijderen van het knooppunt dat de schaal down evaluatie hervat                          | scan-interval |
| scale-down-delay-after-failure   | Hoe lang na het afbreken van de fout die schaal down evaluatie hervat                     | 3 minuten     |
| schaal-down-onnodige tijd         | Hoe lang een knooppunt overbodig moet zijn voordat het in aanmerking komt voor schaalomlaag                  | 10 minuten    |
| schaal-down-unready-time          | Hoe lang een onklaar knooppunt overbodig moet zijn voordat het in aanmerking komt voor schaalomlaag         | 20 minuten    |
| schaal-down-usage-drempel | Gebruiksniveau van knooppunt, gedefinieerd als som van de gevraagde resources gedeeld door capaciteit, waaronder een knooppunt kan worden beschouwd voor schaalomlaag | 0.5 |
| max-graceful-termination-sec     | Maximaal aantal seconden dat de clusterautoscaler wacht op podbeëindiging wanneer het een knooppunt probeert af te schalen. | 600 seconden   |

> [!IMPORTANT]
> Het clusterautoscalerprofiel is van invloed op alle knooppuntgroepen die de clusterautoscaler gebruiken. U geen autoscalerprofiel per knooppuntgroep instellen.

### <a name="install-aks-preview-cli-extension"></a>Aks-preview CLI-extensie installeren

Als u het instellingenprofiel van de clusterautoscaler wilt instellen, hebt u de *AKS-Preview CLI-extensieversie* 0.4.30 of hoger nodig. Installeer de *aks-preview Azure CLI-extensie* met de opdracht [Toevoegen van AZ-extensie][az-extension-add] en controleer vervolgens op beschikbare updates met de opdracht [update van AZ-extensie:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Het clusterautoscalerprofiel instellen op een bestaand AKS-cluster

Gebruik de opdracht [az aks-update][az-aks-update] met de parameter *cluster-autoscaler-profile* om het clusterautoscalerprofiel op uw cluster in te stellen. In het volgende voorbeeld wordt de intervalinstelling voor scan geconfigureerd als 30s in het profiel.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Wanneer u de clusterautoscaler inschakelt op knooppuntgroepen in het cluster, gebruiken deze clusters ook het clusterautoscalerprofiel. Bijvoorbeeld:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> Wanneer u het clusterautoscalerprofiel instelt, worden bestaande knooppuntgroepen met de ingeschakelde clusterautoscaler onmiddellijk met het profiel gebruikt.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Het clusterautoscalerprofiel instellen bij het maken van een AKS-cluster

U ook de parameter *cluster-autoscaler-profile* gebruiken wanneer u uw cluster maakt. Bijvoorbeeld:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

Met de bovenstaande opdracht wordt een AKS-cluster gemaakt en wordt het scaninterval gedefinieerd als 30 seconden voor het clusterbrede autoscalerprofiel. De opdracht maakt ook de clusterautoscaler op de eerste knooppuntgroep, stelt het minimumaantal knooppunten in op 1 en het maximale aantal nodes op 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Clusterautoscaler-profiel opnieuw instellen naar standaardwaarden

Gebruik de opdracht [az aks-update][az-aks-update] om het clusterautoscaler-profiel op uw cluster opnieuw in te stellen.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>De clusterautoscaler uitschakelen

Als u de clusterautoscaler niet meer wilt gebruiken, u deze uitschakelen met de opdracht [az aks-update,][az-aks-update] waarbij de parameter *--disable-cluster-autoscaler* wordt opgegeven. Knooppunten worden niet verwijderd wanneer de clusterautoscaler is uitgeschakeld.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

U uw cluster handmatig schalen nadat u de clusterautoscaler hebt uitgeschakeld met de opdracht [az aks-schaal.][az-aks-scale] Als u de automatische schaal van de horizontale pod gebruikt, blijft deze functie worden uitgevoerd met de clusterautoscaler uitgeschakeld, maar kunnen pods uiteindelijk niet worden gepland als alle knooppuntbronnen worden gebruikt.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Een uitgeschakelde clusterautoscaler opnieuw inschakelen

Als u de clusterautoscaler opnieuw wilt inschakelen op een bestaand cluster, u deze opnieuw inschakelen met de opdracht [az aks-update,][az-aks-update] waarbij de parameters *--enable-cluster-autoscaler*, *--min-count*en *-max-count-* worden opgegeven.

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Clusterautoscaler-logboeken en -status ophalen

Als u autoscaler-gebeurtenissen wilt diagnosticeren en debuggen, kunnen logboeken en status worden opgehaald uit de autoscaler-invoegtoepassing.

AKS beheert de clusterautoscaler namens u en voert deze uit in het beheerde controlevlak. Hoofdknooppuntlogboeken moeten worden geconfigureerd om als resultaat te worden bekeken.

Voer de volgende stappen uit om logboeken te configureren die van de clusterautoscaler naar Log Analytics moeten worden gepusht.

1. Stel een regel in voor diagnostische logboeken om cluster-autoscaler-logboeken naar Log Analytics te pushen. [Instructies worden hier beschreven,](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs)zorg `cluster-autoscaler` ervoor dat u het selectievakje voor bij het selecteren van opties voor "Logs".
1. Klik via de Azure-portal op de sectie 'Logboeken' op uw cluster.
1. Voer de volgende voorbeeldquery in in Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

U ziet logboeken die vergelijkbaar zijn met het volgende voorbeeld, zolang er logboeken zijn die u ophalen.

![Logboekanalyselogboeken](media/autoscaler/autoscaler-logs.png)

De clusterautoscaler zal ook de status van `cluster-autoscaler-status`de status uitschrijven naar een configmap met de naam . Als u deze logboeken `kubectl` wilt ophalen, voert u de volgende opdracht uit. Er wordt een status gerapporteerd voor elke knooppuntgroep die is geconfigureerd met de clusterautoscaler.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Lees de veelgestelde vragen over het [Kubernetes/autoscaler GitHub-project](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why)voor meer informatie over wat er is vastgelegd in de autoscaler.

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>De clusterautoscaler gebruiken met meerdere knooppuntgroepen ingeschakeld

De clusterautoscaler kan samen met [meerdere knooppuntpools](use-multiple-node-pools.md) worden gebruikt. Volg dat document voor meer informatie over het inschakelen van meerdere knooppuntgroepen en het toevoegen van extra knooppuntgroepen aan een bestaand cluster. Wanneer u beide functies samen gebruikt, schakelt u de clusterautoscaler in op elke afzonderlijke knooppuntgroep in het cluster en u unieke regels voor automatisch schalen aan elk cluster doorgeven.

De onderstaande opdracht gaat ervan uit dat u de [eerste instructies](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) eerder in dit document hebt gevolgd en dat u het maximale aantal van een bestaande knooppuntgroep wilt bijwerken van *3* naar *5*. Gebruik de opdracht [az aks nodepool update][az-aks-nodepool-update] om de instellingen van een bestaande knooppuntgroep bij te werken.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

De clusterautoscaler kan worden uitgeschakeld met [de az aks-nodepool-update][az-aks-nodepool-update] en het passeren van de `--disable-cluster-autoscaler` parameter.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Als u de clusterautoscaler opnieuw wilt inschakelen op een bestaand cluster, u deze opnieuw inschakelen met de opdracht [az aks-nodepool-update,][az-aks-nodepool-update] waarbij de parameters *--enable-cluster-autoscaler*, *--min-count*en *-max-count* worden opgegeven.

## <a name="next-steps"></a>Volgende stappen

In dit artikel ziet u hoe u het aantal AKS-knooppunten automatisch schalen. U de automatische weegschaal van de horizontale pod ook gebruiken om automatisch het aantal pods aan te passen waarop uw toepassing wordt uitgevoerd. Zie [Toepassingen schalen in AKS][aks-scale-apps]voor stappen voor het gebruik van de automatische podautoscaler.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
