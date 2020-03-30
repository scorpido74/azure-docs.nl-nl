---
title: Aanbevolen procedures voor operatoren - Basisplannerfuncties in Azure Kubernetes Services (AKS)
description: Lees de aanbevolen procedures voor de clusteroperator voor het gebruik van basisplannerfuncties, zoals resourcequota en podonderbrekingsbudgetten in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cccc476a944b28d24c53a947e434d465c94f94ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126576"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor basisplannerfuncties in Azure Kubernetes Service (AKS)

Terwijl u clusters beheert in Azure Kubernetes Service (AKS), moet u vaak teams en workloads isoleren. De Kubernetes scheduler biedt functies waarmee u de distributie van compute resources beheren of de impact van onderhoudsgebeurtenissen beperken.

Dit best practices-artikel richt zich op basisfuncties voor Kubernetes-planning voor clusteroperators. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Resourcequota gebruiken om een vast aantal resources aan teams of workloads te verstrekken
> * De impact van gepland onderhoud beperken met behulp van pod-onderbrekingsbudgetten
> * Controleren op ontbrekende podresourceaanvragen `kube-advisor` en -limieten met het hulpprogramma

## <a name="enforce-resource-quotas"></a>Resourcequota afdwingen

**Richtlijnen voor beste praktijken** - Resourcequota plannen en toepassen op naamruimteniveau. Als pods geen resourceaanvragen en -limieten definiëren, weigert u de implementatie. Controleer het gebruik van resources en pas quota aan als dat nodig is.

Resourceaanvragen en -limieten worden in de podspecificatie geplaatst. Deze limieten worden gebruikt door de Kubernetes-planner tijdens de implementatieom een beschikbaar knooppunt in het cluster te vinden. Deze limieten en aanvragen werken op het niveau van de afzonderlijke pod. Zie Pod resource requests and limits definiëren voor meer informatie over het definiëren van deze [waarden.][resource-limits]

Als u een manier wilt bieden om resources te reserveren en te beperken voor een ontwikkelingsteam of project, moet u *resourcequota*gebruiken. Deze contingenten worden gedefinieerd op een naamruimte en kunnen worden gebruikt om quota's vast te stellen op de volgende basis:

* **Bereken resources,** zoals CPU en geheugen of GPU's.
* **Opslagbronnen**, inclusief het totale aantal volumes of de hoeveelheid schijfruimte voor een bepaalde opslagklasse.
* **Objecttelling**, zoals maximaal aantal geheimen, services of taken kunnen worden gemaakt.

Kubernetes overcommit er niet te veel middelen. Zodra het cumulatieve totaal van resourceaanvragen of -limieten het toegewezen quotum passeert, zijn er geen nieuwe implementaties meer geslaagd.

Wanneer u resourcequota definieert, moeten alle pods die in de naamruimte zijn gemaakt, limieten of aanvragen opgeven in hun podspecificaties. Als deze waarden niet worden verstrekt, u de implementatie weigeren. In plaats daarvan u [standaardaanvragen en limieten configureren voor een naamruimte.][configure-default-quotas]

In het volgende voorbeeld *yaml-manifest met denaam dev-app-team-quota.yaml* wordt een harde limiet ingesteld van in totaal *10* CPU's, *20Gi* geheugen en *10* pods:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Dit resourcequotum kan worden toegepast door de naamruimte op te geven, zoals *dev-apps:*

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Werk samen met uw toepassingsontwikkelaars en -eigenaren om hun behoeften te begrijpen en de juiste resourcequota toe te passen.

Zie [Resourcequota in Kubernetes][k8s-resource-quotas]voor meer informatie over beschikbare bronobjecten, scopes en prioriteiten.

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Beschikbaarheid plannen met podonderbrekingsbudgetten

**Richtlijnen voor aanbevolen procedures** - Als u de beschikbaarheid van toepassingen wilt behouden, definieert u Pod Disruption Budgetten (PDF's) om ervoor te zorgen dat er een minimum aantal pods beschikbaar is in het cluster.

Er zijn twee storende gebeurtenissen die ervoor zorgen dat pods worden verwijderd:

* *Onvrijwillige onderbrekingen* zijn gebeurtenissen die buiten de typische controle van de clusteroperator of toepassingseigenaar vallen.
  * Deze onvrijwillige onderbrekingen omvatten een hardwarestoring op de fysieke machine, een kernel panic of het verwijderen van een node VM
* *Vrijwillige onderbrekingen* zijn gebeurtenissen die worden aangevraagd door de clusterbeheerder of de eigenaar van de toepassing.
  * Deze vrijwillige onderbrekingen omvatten clusterupgrades, een bijgewerkte implementatiesjabloon of het per ongeluk verwijderen van een pod.

De onvrijwillige onderbrekingen kunnen worden beperkt door meerdere replica's van uw pods in een implementatie te gebruiken. Het uitvoeren van meerdere knooppunten in het AKS-cluster helpt ook bij deze onvrijwillige onderbrekingen. Voor vrijwillige onderbrekingen biedt Kubernetes *pod-onderbrekingsbudgetten* waarmee de clusteroperator een minimaal beschikbaar of maximaal onbeschikbaar aantal resources kan definiëren. Met deze podonderbrekingsbudgetten u plannen hoe implementaties of replicasets reageren wanneer een vrijwillige onderbrekingsgebeurtenis optreedt.

Als een cluster moet worden bijgewerkt of een implementatiesjabloon moet worden bijgewerkt, zorgt de Kubernetes-planner ervoor dat extra pods op andere knooppunten worden gepland voordat de vrijwillige onderbrekingsgebeurtenissen kunnen worden voortgezet. De planner wacht voordat een knooppunt opnieuw wordt opgestart totdat het gedefinieerde aantal pods is gepland op andere knooppunten in het cluster.

Laten we eens kijken naar een voorbeeld van een replica set met vijf pods die NGINX draaien. De pods in de replicaset `app: nginx-frontend`krijgen het label . Tijdens een vrijwillige onderbrekingsgebeurtenis, zoals een clusterupgrade, wilt u ervoor zorgen dat ten minste drie pods blijven worden uitgevoerd. In het volgende YAML-manifest voor een object *PodDisruptionBudget* worden de volgende vereisten gedefinieerd:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

U ook een percentage definiëren, zoals *60%,* waarmee u automatisch compenseren voor de replicaset die het aantal pods opschaalt.

U een maximum aantal niet-beschikbare exemplaren in een replicaset definiëren. Nogmaals, een percentage voor de maximale niet-beschikbare pods kan ook worden gedefinieerd. In het volgende YAML-manifest voor podverstoringswordt gedefinieerd dat niet meer dan twee pods in de replicaset niet beschikbaar zijn:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

Zodra het budget voor podverstoring is gedefinieerd, maakt u het in uw AKS-cluster zoals bij elk ander Kubernetes-object:

```console
kubectl apply -f nginx-pdb.yaml
```

Werk samen met uw toepassingsontwikkelaars en -eigenaren om hun behoeften te begrijpen en de juiste pod-onderbrekingsbudgetten toe te passen.

Zie [Een onderbrekingsbudget voor uw toepassing opgeven voor][k8s-pdbs]meer informatie over het gebruik van podonderbrekingsbudgetten.

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Controleer regelmatig op clusterproblemen met kube-adviseur

**Richtlijnen voor aanbevolen procedures** - Voer `kube-advisor` regelmatig de nieuwste versie van het open source-hulpprogramma uit om problemen in uw cluster op te sporen. Als u resourcequota toepast op een `kube-advisor` bestaand AKS-cluster, voert u eerst pods uit die geen resourceaanvragen en -limieten hebben gedefinieerd.

De [kube-advisor][kube-advisor] tool is een bijbehorend AKS open source project dat een Kubernetes cluster scant en rapporteert over problemen die het vindt. Een handige controle is om pods te identificeren die geen resourceaanvragen en -limieten hebben.

De kube-advisor tool kan rapporteren over resource request en beperkingen ontbreken in PodSpecs voor Windows-toepassingen en Linux-toepassingen, maar de kube-advisor tool zelf moet worden gepland op een Linux-pod. U een pod plannen om een knooppuntgroep met een specifiek besturingssysteem uit te voeren met behulp van een [knooppuntkiezer][k8s-node-selector] in de configuratie van de pod.

In een AKS-cluster met meerdere ontwikkelteams en -toepassingen kan het moeilijk zijn om pods bij te houden zonder dat deze resourceaanvragen en limieten zijn ingesteld. Als aanbevolen praktijk wordt `kube-advisor` u regelmatig uitgevoerd op uw AKS-clusters, vooral als u geen resourcequota toewijst aan naamruimten.

## <a name="next-steps"></a>Volgende stappen

Dit artikel richtte zich op de basisfuncties van Kubernetes scheduler. Zie de volgende aanbevolen procedures voor meer informatie over clusterbewerkingen in AKS:

* [Multitenancy en clusterisolatie][aks-best-practices-cluster-isolation]
* [Geavanceerde functies voor Kubernetes scheduler][aks-best-practices-advanced-scheduler]
* [Verificatie en autorisatie][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
