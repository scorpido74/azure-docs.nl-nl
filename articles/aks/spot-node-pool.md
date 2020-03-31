---
title: Preview - Een pool van een spotknooppunt toevoegen aan een AKS-cluster (Azure Kubernetes Service)
description: Meer informatie over het toevoegen van een pool van een spotknooppunt aan een AKS-cluster (Azure Kubernetes Service).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.author: zarhoads
ms.openlocfilehash: 466ad7c88547b6676ba0ae263b74d14059322f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622045"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Preview - Een pool van een spotknooppunt toevoegen aan een AKS-cluster (Azure Kubernetes Service)

Een spot node zwembad is een knooppunt zwembad ondersteund door een [plek virtuele machine schaal ingesteld.][vmss-spot] Met behulp van spotVM's voor knooppunten met uw AKS-cluster u profiteren van onbenutte capaciteit in Azure tegen aanzienlijke kostenbesparingen. De hoeveelheid beschikbare onbenutte capaciteit varieert op basis van vele factoren, waaronder knooppuntgrootte, regio en tijdstip van de dag.

Bij het implementeren van een pool van een spotknooppunt wijst Azure de steunknooppunten toe als er capaciteit beschikbaar is. Maar er is geen SLA voor de spot knooppunten. Een spotschaalset die de pool met het hoofdknooppunt steunt, wordt geïmplementeerd in één foutdomein en biedt geen hoge beschikbaarheidsgaranties. Op elk moment dat Azure de capaciteit terug nodig heeft, wordt de Azure-infrastructuur op zoek naar steunknooppunten uitdeinen.

Spotknooppunten zijn ideaal voor workloads die onderbrekingen, vroegtijdige beëindigingen of uitzettingen kunnen verwerken. Workloads zoals batchverwerkingstaken, ontwikkel- en testomgevingen en grote compute-workloads kunnen bijvoorbeeld goede kandidaten zijn die kunnen worden gepland op een pool met een plekknooppunt.

In dit artikel voegt u een secundaire pool van een subspotknooppunt toe aan een bestaand AKS-cluster (Azure Kubernetes Service).

In dit artikel wordt uitgegaan van een basiskennis van Kubernetes- en Azure Load Balancer-concepten. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service-cluster (AKS)][kubernetes-concepts] voor meer informatie.

Deze functie is momenteel beschikbaar als preview-product.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

Wanneer u een cluster maakt om een pool met een steunknooppunt te gebruiken, moet dat cluster ook virtuele machineschaalsets gebruiken voor knooppuntgroepen en de *standaard* SKU-loadbalancer. U moet ook een extra knooppuntgroep toevoegen nadat u uw cluster hebt gemaakt om een pool met een puntknooppunt te gebruiken. Het toevoegen van een extra knooppuntgroep wordt in een latere stap behandeld, maar u moet eerst een voorbeeldfunctie inschakelen.

> [!IMPORTANT]
> AKS preview-functies zijn self-service, opt-in. Ze worden verstrekt om feedback en bugs van onze community te verzamelen. In preview zijn deze functies niet bedoeld voor productiegebruik. Functies in openbare preview vallen onder 'best effort' ondersteuning. Hulp van de AKS technische ondersteuning teams is alleen beschikbaar tijdens kantooruren Pacific timezone (PST) alleen. Zie voor meer informatie de volgende ondersteuningsartikelen:
>
> * [AKS-ondersteuningsbeleid][aks-support-policies]
> * [Veelgestelde vragen over Azure Support][aks-faq]

### <a name="register-spotpoolpreview-preview-feature"></a>Voorbeeldfunctie spotpoolpreview registreren

Als u een AKS-cluster wilt maken dat een pool van een steunknooppunt gebruikt, moet u de *spotpoolpreview-functievlag* inschakelen op uw abonnement. Deze functie biedt de nieuwste serviceverbeteringen bij het configureren van een cluster.

> [!CAUTION]
> Wanneer u een functie registreert voor een abonnement, u deze functie momenteel niet ongedaan maken. Nadat u een aantal voorbeeldfuncties hebt ingeschakeld, kunnen standaardinstellingen worden gebruikt voor alle AKS-clusters die vervolgens in het abonnement zijn gemaakt. Schakel geen voorbeeldfuncties in op productieabonnementen. Gebruik een apart abonnement om preview-functies te testen en feedback te verzamelen.

Registreer de *spotpoolpreview* feature flag met de opdracht [az-functieregister][az-feature-register] zoals in het volgende voorbeeld wordt weergegeven:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

Het duurt een paar minuten voordat de status *geregistreerd wordt weergegeven.* U de registratiestatus controleren met de opdracht [AZ Feature List:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de *microsoft.ContainerService-resourceprovider* met de opdracht [AZ-providerregister:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>Aks-preview CLI-extensie installeren

Als u een AKS-cluster wilt maken dat een pool van een steunknooppunt gebruikt, hebt u de *AKS-Preview CLI-extensieversie* 0.4.32 of hoger nodig. Installeer de *aks-preview Azure CLI-extensie* met de opdracht [Toevoegen van AZ-extensie][az-extension-add] en controleer vervolgens op beschikbare updates met de opdracht [update van AZ-extensie:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert met een pool van een steunknooppunt:

* Een pool van een puntknooppunt kan niet de standaardknooppuntgroep van het cluster zijn. Een zwembad met een hoekknooppunt kan alleen worden gebruikt voor een secundair zwembad.
* U niet een plek knooppunt zwembad upgraden omdat plek knooppunt zwembaden kan niet cordon en afvoer garanderen. U moet uw bestaande pool van het vlekknooppunt vervangen door een nieuwe om bewerkingen uit te voeren, zoals het upgraden van de Kubernetes-versie. Als u een pool van een plekknooppunt wilt vervangen, maakt u een nieuwe pool voor het knooppunt met een andere versie van Kubernetes, wacht u tot de status *gereed*is en verwijdert u vervolgens de oude knooppuntgroep.
* Het controlevlak en knooppuntzwembaden kunnen niet tegelijkertijd worden geüpgraded. U moet ze afzonderlijk upgraden of de pool van het steunknooppunt verwijderen om het controlevlak en de resterende knooppuntgroepen tegelijkertijd te upgraden.
* Een pool met een spotknooppunt moet virtuele machineschaalsets gebruiken.
* U ScaleSetPriority of SpotMaxPrice na het maken niet wijzigen.
* Bij het instellen van SpotMaxPrice moet de waarde -1 of een positieve waarde met maximaal vijf decimalen zijn.
* Een spot node pool zal het label *kubernetes.azure.com/scalesetpriority:spot*hebben, de taint *kubernetes.azure.com/scalesetpriority=spot:NoSchedule*, en systeempods hebben anti-affiniteit.
* U moet een [overeenkomstige tolerantie][spot-toleration] toevoegen om workloads te plannen op een pool van een puntknooppunt.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Een pool van een puntknooppunt toevoegen aan een AKS-cluster

U moet een pool van een puntknooppunt toevoegen aan een bestaand cluster waar meerdere knooppuntgroepen zijn ingeschakeld. Meer details over het maken van een AKS-cluster met meerdere knooppuntpools zijn [hier][use-multiple-node-pools]beschikbaar.

Maak een knooppuntpool met de [add-to-poule van AZ Aks Nodepool.][az-aks-nodepool-add]
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Standaard maakt u een knooppuntgroep met een *prioriteit* van *Regelmatig* in uw AKS-cluster wanneer u een cluster maakt met meerdere knooppuntgroepen. Met de bovenstaande opdracht wordt een extra knooppuntgroep toegevoegd aan een bestaand AKS-cluster met een *prioriteit* van *Spot.* De *prioriteit* van *Spot* maakt het knooppunt zwembad een plek knooppunt zwembad. De parameter *uitzetting-beleid* is ingesteld op *Verwijderen* in het bovenstaande voorbeeld, de standaardwaarde. Wanneer u het [uitzettingsbeleid][eviction-policy] instelt *op Verwijderen,* worden knooppunten in de onderliggende schaalset van de knooppuntgroep verwijderd wanneer ze worden verwijderd. U het uitzettingsbeleid ook instellen op *Detoewijzing*. Wanneer u het uitzettingsbeleid instelt op *Detoewijzen,* worden knooppunten in de onderliggende schaalset ingesteld op de status met stop-dealbij uitzetting. Knooppunten in de status met stopbereik tellen mee voor uw rekenquotum en kunnen problemen veroorzaken met clusterschaling of upgraden. De *waarden voor prioriteit* en *uitzettingsbeleid* kunnen alleen worden ingesteld tijdens het maken van knooppuntpool. Deze waarden kunnen niet later worden bijgewerkt.

De opdracht maakt ook de [cluster autoscaler][cluster-autoscaler], die wordt aanbevolen om te gebruiken met spot knooppunt zwembaden. Op basis van de workloads die in uw cluster worden uitgevoerd, schaalt de clusterautoscaler het aantal knooppunten in de knooppuntgroep op en schaalt deze op. Voor groepen met een puntknooppunt schaalt de clusterautoscaler het aantal knooppunten op na een uitzetting als er nog extra knooppunten nodig zijn. Als u het maximum aantal knooppunten wijzigt dat een knooppuntgroep `maxCount` kan hebben, moet u ook de waarde aanpassen die is gekoppeld aan de clusterautoscaler. Als u geen clusterautoscaler gebruikt, zal de spotpool bij uitzetting uiteindelijk tot nul dalen en een handmatige bewerking vereisen om extra spotknooppunten te ontvangen.

> [!Important]
> Plan alleen workloads op knooppuntgroepen ter plaatse die onderbrekingen kunnen verwerken, zoals batchverwerkingstaken en testomgevingen. Het wordt aanbevolen dat u [taints en toleranties][taints-tolerations] op uw spotknooppuntpool instelt om ervoor te zorgen dat alleen workloads die knooppuntuitzettingen kunnen verwerken, worden gepland op een pool van een plekknooppunt. De bovenstaande opdracht ny default voegt bijvoorbeeld een smet van *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* toe, zodat alleen pods met een bijbehorende tolerantie op dit knooppunt worden gepland.

## <a name="verify-the-spot-node-pool"></a>Controleer de pool van het knooppunt

Ga als volgt te werk om te controleren of uw knooppuntgroep is toegevoegd als een pool van een knooppunt:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

*ScaleSetPrioriteit* bevestigen is *Spot*.

Als u wilt dat een pod wordt uitgevoerd op een puntknooppunt, voegt u een tolerantie toe die overeenkomt met de smet die op uw plaatsknooppunt wordt toegepast. In het volgende voorbeeld wordt een gedeelte van een yaml-bestand weergegeven dat een tolerantie definieert die overeenkomt met een *kubernetes.azure.com/scalesetpriority=spot:NoSchedule-taint* die in de vorige stap is gebruikt.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Wanneer een pod met deze tolerantie wordt geïmplementeerd, kan Kubernetes de pod met succes plannen op de knooppunten met de aangebrachte smet.

## <a name="max-price-for-a-spot-pool"></a>Max prijs voor een spot pool
[De prijzen voor spotexemplaren zijn variabel,][pricing-spot]gebaseerd op regio en SKU. Zie prijzen voor [Linux][pricing-linux] en [Windows][pricing-windows]voor meer informatie.

Met variabele prijzen hebt u de optie om een maximale prijs in te stellen, in Amerikaanse dollars (USD), met maximaal 5 decimalen. De waarde *0,98765* is bijvoorbeeld een maximale prijs van $ 0,98765 USD per uur. Als u de maximale prijs instelt op *-1,* wordt de instantie niet uitgezet op basis van de prijs. De prijs voor de instantie is de huidige prijs voor Spot of de prijs voor een standaardexemplaar, ongeacht wat minder is, zolang er capaciteit en quotum beschikbaar is.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een pool van een puntknooppunt toevoegt aan een AKS-cluster. Zie [Aanbevolen procedures voor geavanceerde plannerfuncties in AKS][operator-best-practices-advanced-scheduler]voor meer informatie over het beheren van pods in knooppuntgroepen.

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deploy-create]: /cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-template-deploy]: ../azure-resource-manager/templates/deploy-cli.md#deployment-scope
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md