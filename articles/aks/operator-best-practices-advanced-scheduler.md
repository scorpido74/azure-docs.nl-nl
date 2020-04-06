---
title: Aanbevolen procedures voor plannerfuncties
titleSuffix: Azure Kubernetes Service
description: Lees de best practices voor de clusteroperator voor het gebruik van geavanceerde schedulerfuncties zoals taints en toleranties, knooppuntselecties en affiniteit, of interpodaffiniteit en anti-affiniteit in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 47b2d78f7dc831c4314c4215f5e0a9e17f75f0dc
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668364"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor geavanceerde schedulerfuncties in Azure Kubernetes Service (AKS)

Terwijl u clusters beheert in Azure Kubernetes Service (AKS), moet u vaak teams en workloads isoleren. De Kubernetes scheduler biedt geavanceerde functies waarmee u bepalen welke pods op bepaalde knooppunten kunnen worden gepland, of hoe multipod-toepassingen op de juiste manier over het cluster kunnen worden verdeeld. 

Dit best practices-artikel richt zich op geavanceerde Kubernetes-planningsfuncties voor clusteroperators. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Taints en toleranties gebruiken om te beperken welke pods op knooppunten kunnen worden gepland
> * Geef de voorkeur aan pods om te worden uitgevoerd op bepaalde knooppunten met knooppuntenselectors of knooppuntaffiniteit
> * Pods splitsen of groeperen met affiniteit tussen pod of anti-affiniteit

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Speciale knooppunten bieden met behulp van taints en toleranties

**Richtlijnen voor aanbevolen procedures** - Beperk de toegang voor resource-intensieve toepassingen, zoals invallencontrollers, tot specifieke knooppunten. Houd knooppuntresources beschikbaar voor workloads die dit vereisen en sta het plannen van andere workloads op de knooppunten niet toe.

Wanneer u uw AKS-cluster maakt, u knooppunten implementeren met GPU-ondersteuning of een groot aantal krachtige CPU's. Deze knooppunten worden vaak gebruikt voor grote gegevensverwerkingsworkloads zoals machine learning (ML) of kunstmatige intelligentie (AI). Aangezien dit type hardware doorgaans een dure knooppuntbron is om te implementeren, beperkt u de workloads die op deze knooppunten kunnen worden gepland. U in plaats daarvan enkele knooppunten in het cluster wijden om invallende services uit te voeren en andere workloads te voorkomen.

Deze ondersteuning voor verschillende knooppunten wordt geleverd door meerdere knooppuntgroepen te gebruiken. Een AKS-cluster biedt een of meer knooppuntgroepen.

De Kubernetes scheduler kan taints en toleranties gebruiken om te beperken welke workloads op knooppunten kunnen worden uitgevoerd.

* Er wordt een **taint** toegepast op een knooppunt dat aangeeft dat er alleen specifieke pods op kunnen worden gepland.
* Een **tolerantie** wordt vervolgens toegepast op een pod die hen in staat stelt om een node's taint *tolereren.*

Wanneer u een pod implementeert in een AKS-cluster, plant Kubernetes alleen pods op knooppunten waar een tolerantie is uitgelijnd met de taint. Stel bijvoorbeeld dat u een knooppuntgroep in uw AKS-cluster hebt voor knooppunten met GPU-ondersteuning. U definieert naam, zoals *gpu,* vervolgens een waarde voor het plannen. Als u deze waarde instelt op *NoSchedule,* kan de Kubernetes-planner geen pods op het knooppunt plannen als de pod niet de juiste tolerantie definieert.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Als een smet op knooppunten wordt toegepast, definieert u vervolgens een tolerantie in de podspecificatie die planning op de knooppunten mogelijk maakt. In het volgende `sku: gpu` `effect: NoSchedule` voorbeeld wordt de smet gedefinieerd die in de vorige stap op het knooppunt wordt toegepast:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Wanneer deze pod wordt geïmplementeerd, `kubectl apply -f gpu-toleration.yaml`zoals het gebruik , kan Kubernetes de pod op de knooppunten met de aangebrachte gegevens plannen. Met deze logische isolatie u de toegang tot bronnen binnen een cluster beheren.

Wanneer u taints toepast, werkt u samen met uw toepassingsontwikkelaars en -eigenaren om hen in staat te stellen de vereiste toleranties in hun implementaties te definiëren.

Zie voor meer informatie over smetten en verdraagbaarheid [het toepassen van taints en toleranties.][k8s-taints-tolerations]

Zie [Meerdere knooppuntgroepen maken en beheren voor een cluster in AKS voor][use-multiple-node-pools]meer informatie over het gebruik van meerdere knooppuntgroepen in AKS.

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Gedrag van bedorvenen en verdraagbaarheid in AKS

Wanneer u een knooppuntgroep in AKS upgradet, volgen taints en toleranties een vast patroon wanneer ze worden toegepast op nieuwe knooppunten:

- **Standaardclusters die virtuele machineschaalsets gebruiken**
  - Stel dat u een cluster met twee *nodes* hebt - knooppunt1 en *knooppunt2*. U upgradet het knooppuntzwembad.
  - Er worden twee extra knooppunten gemaakt, *knooppunt3* en *knooppunt4*en de taints worden respectievelijk doorgegeven.
  - De oorspronkelijke *node1* en *node2* worden verwijderd.

- **Ondersteuning voor clusters zonder ondersteuning voor virtuele machineschaalsets**
  - Nogmaals, laten we aannemen dat u een cluster met twee node - *node1* en *node2*. Wanneer u een upgrade uitvoert, wordt een extra knooppunt *(knooppunt3)* gemaakt.
  - De taints van *node1* worden toegepast op *node3,* dan wordt *node1* vervolgens verwijderd.
  - Een ander nieuw knooppunt wordt gemaakt (genaamd *node1*, sinds de vorige *node1* is verwijderd), en de *node2* taints worden toegepast op het nieuwe *knooppunt1*. Vervolgens wordt *node2* verwijderd.
  - In wezen *wordt node1* *knooppunt3*, en *knooppunt2* wordt *knooppunt1*.

Wanneer u een knooppuntzwembad in AKS schaalt, worden de smetten en toleranties niet door het ontwerp overgedragen.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Podplanning beheren met knooppuntselecties en affiniteit

**Richtlijnen voor aanbevolen procedures** - Beheer de planning van pods op knooppunten met knooppunten met knooppunten, node-affiniteit of affiniteit tussen groepen. Met deze instellingen kan de Kubernetes-planner de workloads logisch isoleren, bijvoorbeeld door hardware in het knooppunt.

Taints en toleranties worden gebruikt om middelen logisch te isoleren met een harde cut-off - als de pod de smet van een knooppunt niet tolereert, is het niet gepland op het knooppunt. Een alternatieve benadering is het gebruik van knooppuntselectors. U labelt knooppunten, zoals het aangeven van lokaal gekoppelde SSD-opslag of een grote hoeveelheid geheugen, en definieert vervolgens in de podspecificatie een knooppuntkiezer. Kubernetes plant deze pods vervolgens op een overeenkomend knooppunt. In tegenstelling tot toleranties kunnen pods zonder overeenkomende knooppuntkiezer worden gepland op gelabelde knooppunten. Met dit gedrag kunnen ongebruikte resources op de knooppunten worden verbruikt, maar wordt prioriteit gegeven aan pods die de overeenkomende knooppuntkiezer definiëren.

Laten we eens kijken naar een voorbeeld van knooppunten met een hoge hoeveelheid geheugen. Deze knooppunten kunnen de voorkeur geven aan pods die een hoge hoeveelheid geheugen aanvragen. Om ervoor te zorgen dat de resources niet inactief zitten, kunnen er ook andere pods worden uitgevoerd.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

Een podspecificatie voegt `nodeSelector` vervolgens de eigenschap toe om een knooppuntkiezer te definiëren die overeenkomt met de labelset op een knooppunt:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
    nodeSelector:
      hardware: highmem
```

Wanneer u deze planningsopties gebruikt, werkt u samen met uw toepassingsontwikkelaars en -eigenaren om hen in staat te stellen hun podspecificaties correct te definiëren.

Zie [Pods toewijzen aan knooppunten][k8s-node-selector]voor meer informatie over het gebruik van knooppunten.

### <a name="node-affinity"></a>Node affiniteit

Een knooppuntkiezer is een basismanier om pods toe te wijzen aan een bepaald knooppunt. Meer flexibiliteit is beschikbaar met behulp van *node affiniteit*. Met node-affiniteit bepaalt u wat er gebeurt als de pod niet kan worden gekoppeld aan een knooppunt. U *vereisen* dat Kubernetes scheduler overeenkomt met een pod met een gelabelde host. U ook *de voorkeur geven aan* een overeenkomst, maar toestaan dat de pod op een andere host wordt gepland als er geen overeenkomst beschikbaar is.

In het volgende voorbeeld wordt de nodeaffiniteit ingesteld *op vereistDuringSchedulingIgnoredDuringExecution*. Deze affiniteit vereist dat het Kubernetes-schema een knooppunt met een bijpassend label gebruikt. Als er geen knooppunt beschikbaar is, moet de pod wachten tot de planning is voortgezet. Als u wilt dat de pod op een ander knooppunt wordt gepland, u in plaats daarvan de waarde instellen op *de voorkeurDuringScheduledIgnoreDuringExecution:*

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

Het gedeelte *IgnoredDuringExecution* van de instelling geeft aan dat als de nodelabels worden gewijzigd, de pod niet uit het knooppunt mag worden verwijderd. De Kubernetes scheduler gebruikt alleen de bijgewerkte nodelabels voor nieuwe pods die worden gepland, niet pods die al op de knooppunten zijn gepland.

Zie [Affiniteit en antiaffiniteit][k8s-affinity]voor meer informatie.

### <a name="inter-pod-affinity-and-anti-affinity"></a>Affiniteit tussen pod en anti-affiniteit

Een laatste benadering voor de Kubernetes scheduler om workloads logisch te isoleren is het gebruik van inter-pod affiniteit of anti-affiniteit. De instellingen definiëren dat pods *niet moeten* worden gepland op een knooppunt met een bestaande overeenkomende pod of dat ze *moeten* worden gepland. Standaard probeert de Kubernetes-planner meerdere pods in een replicaset tussen knooppunten te plannen. U meer specifieke regels rond dit gedrag definiëren.

Een goed voorbeeld is een webtoepassing die ook een Azure-cache voor Redis gebruikt. U podanti-affiniteitregels gebruiken om te vragen dat de Kubernetes-planner replica's over knooppunten distribueert. U vervolgens affiniteitsregels gebruiken om ervoor te zorgen dat elke web-app-component op dezelfde host is gepland als een bijbehorende cache. De verdeling van pods over knooppunten ziet eruit als het volgende voorbeeld:

| **Knooppunt 1** | **Knooppunt 2** | **Knooppunt 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

Dit voorbeeld is een complexere implementatie dan het gebruik van knooppuntselecties of node-affiniteit. De implementatie geeft u controle over hoe Kubernetes pods op knooppunten plant en bronnen logisch kan isoleren. Zie Pods met dezelfde bron zoeken [op hetzelfde knooppunt][k8s-pod-affinity]voor een volledig voorbeeld van deze webtoepassing met Azure Cache voor Redis.

## <a name="next-steps"></a>Volgende stappen

Dit artikel richtte zich op geavanceerde Kubernetes scheduler-functies. Zie de volgende aanbevolen procedures voor meer informatie over clusterbewerkingen in AKS:

* [Multitenancy en clusterisolatie][aks-best-practices-scheduler]
* [Basisfuncties kubernetes scheduler][aks-best-practices-scheduler]
* [Verificatie en autorisatie][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
