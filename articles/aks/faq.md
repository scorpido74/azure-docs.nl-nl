---
title: Veelgestelde vragen over Azure Kubernetes service (AKS)
description: Vind antwoorden op enkele veelgestelde vragen over Azure Kubernetes service (AKS).
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 10/02/2019
ms.author: mlearned
ms.openlocfilehash: ff44409180d61828c25980704c71b72965f6bd5d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75967428"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Veelgestelde vragen over Azure Kubernetes service (AKS)

Dit artikel behandelt Veelgestelde vragen over Azure Kubernetes service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Welke Azure-regio's bieden momenteel AKS?

Zie [AKS-regio's en beschik baarheid][aks-regions]voor een volledige lijst met beschik bare regio's.

## <a name="does-aks-support-node-autoscaling"></a>Biedt AKS ondersteuning voor automatisch schalen van knoop punten?

Ja, de mogelijkheid om agent knooppunten horizon taal automatisch te schalen in AKS is momenteel beschikbaar als preview-versie. Zie [een cluster automatisch schalen om te voldoen aan de toepassings vereisten in AKS][aks-cluster-autoscaler] voor instructies. AKS automatisch schalen is gebaseerd op de [Kubernetes autoscaler][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Kan ik AKS implementeren in mijn bestaande virtuele netwerk?

Ja, u kunt een AKS-cluster implementeren in een bestaand virtueel netwerk met behulp van de [geavanceerde netwerk functie][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kan ik de toegang tot de Kubernetes-API-server beperken?

Ja, u kunt de toegang tot de Kubernetes API-server beperken met behulp van door de [API server geautoriseerde IP-adresbereiken][api-server-authorized-ip-ranges].

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Kan ik de Kubernetes API-server alleen toegankelijk maken binnen mijn virtuele netwerk?

Op dit moment, maar dit is gepland. U kunt de voortgang volgen op de [AKS github opslag plaats][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Kan ik verschillende VM-grootten in één cluster hebben?

Ja, u kunt verschillende groottes van virtuele machines in uw AKS-cluster gebruiken door [meerdere knooppunt groepen][multi-node-pools]te maken.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Worden beveiligings updates toegepast op agent knooppunten van AKS?

Azure past automatisch beveiligings patches toe op de Linux-knoop punten in uw cluster volgens een nacht planning. U bent echter verantwoordelijk om ervoor te zorgen dat deze Linux-knoop punten zo nodig opnieuw worden opgestart. U hebt verschillende opties voor het opnieuw opstarten van knoop punten:

- Hand matig, via de Azure Portal of de Azure CLI.
- Door uw AKS-cluster bij te werken. Met het cluster worden automatisch de [Cordon-en afvoer knooppunten][cordon-drain] bijgewerkt en vervolgens een nieuw knoop punt online met de meest recente Ubuntu-installatie kopie en een nieuwe patch versie of een secundaire Kubernetes-versie. Zie [een AKS-cluster upgraden][aks-upgrade]voor meer informatie.
- Met behulp van [Kured](https://github.com/weaveworks/kured), een open source-daemon voor opnieuw opstarten voor Kubernetes. Kured wordt uitgevoerd als een [daemonset](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) en bewaakt elk knoop punt voor de aanwezigheid van een bestand dat aangeeft dat de computer opnieuw moet worden opgestart. In het hele cluster worden het besturings systeem opnieuw opgestart en wordt het proces voor het uitvoeren van een cluster upgrade uitgevoerd door hetzelfde [Cordon en een afwaterproces][cordon-drain] .

Zie [beveiliging en kernel-updates Toep assen op knoop punten in AKS][node-updates-kured]voor meer informatie over het gebruik van kured.

### <a name="windows-server-nodes"></a>Windows Server-knoop punten

Voor Windows Server-knoop punten (momenteel in de preview-versie van AKS) wordt Windows Update niet automatisch uitgevoerd en worden de nieuwste updates toegepast. U moet een upgrade uitvoeren op het cluster en de Windows Server-knooppunt groep (en) in uw AKS-cluster, volgens een regel matige planning rond de Windows Update release cyclus en uw eigen validatie proces. Dit upgrade proces maakt knoop punten waarop de nieuwste installatie kopie en patches van Windows Server worden uitgevoerd, waarna de oudere knoop punten worden verwijderd. Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor meer informatie over dit proces.

## <a name="why-are-two-resource-groups-created-with-aks"></a>Waarom zijn er twee resource groepen gemaakt met AKS?

AKS bouwt voort op een aantal Azure-infrastructuur resources, zoals schaal sets voor virtuele machines, virtuele netwerken en beheerde schijven. Op die manier kunt u veel van de kern mogelijkheden van het Azure-platform gebruiken binnen de beheerde Kubernetes-omgeving van AKS. De meeste typen van virtuele machines van Azure kunnen bijvoorbeeld rechtstreeks worden gebruikt met AKS en Azure Reservations kunnen worden gebruikt voor het automatisch ontvangen van kortingen op deze resources.

Om deze architectuur in te scha kelen, omvat elke AKS-implementatie twee resource groepen:

1. U maakt de eerste resource groep. Deze groep bevat alleen de Kubernetes-service resource. De resource provider AKS maakt automatisch de tweede resource groep tijdens de implementatie. Een voor beeld van de tweede resource groep is *MC_myResourceGroup_myAKSCluster_eastus*. Zie de volgende sectie voor meer informatie over het opgeven van de naam van deze tweede resource groep.
1. De tweede resource groep, ook wel de *resource groep knoop punt*genoemd, bevat alle infrastructuur resources die zijn gekoppeld aan het cluster. Deze resources omvatten de Kubernetes-knoop punt-Vm's, virtuele netwerken en opslag. De resource groep van het knoop punt heeft standaard een naam als *MC_myResourceGroup_myAKSCluster_eastus*. AKS verwijdert automatisch de knooppunt resource wanneer het cluster wordt verwijderd. dit moet daarom alleen worden gebruikt voor resources die de levens cyclus van het cluster delen.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Kan ik mijn eigen naam opgeven voor de resource groep van het AKS-knoop punt?

Ja. AKS krijgt standaard de naam van de knooppunt resource groep *MC_resourcegroupname_clustername_location*, maar u kunt ook uw eigen naam opgeven.

Als u de naam van uw eigen resource groep wilt opgeven, installeert u de [AKS-preview][aks-preview-cli] Azure cli-extensie versie *0.3.2* of hoger. Wanneer u een AKS-cluster maakt met behulp van de opdracht [AZ AKS Create][az-aks-create] , gebruikt u de para meter *--node-Resource-Group* en geeft u een naam op voor de resource groep. Als u [een Azure Resource Manager-sjabloon gebruikt][aks-rm-template] om een AKS-cluster te implementeren, kunt u de naam van de resource groep definiëren met behulp van de eigenschap *nodeResourceGroup* .

* De secundaire resource groep wordt automatisch gemaakt door de Azure-resource provider in uw eigen abonnement.
* U kunt alleen een aangepaste resource groeps naam opgeven wanneer u het cluster maakt.

Houd er bij het werken met de knooppunt resource groep voor dat u niet:

* Geef een bestaande resource groep op voor de knooppunt resource groep.
* Geef een ander abonnement op voor de knooppunt resource groep.
* Wijzig de naam van de resource groep van het knoop punt nadat het cluster is gemaakt.
* Geef namen voor de beheerde resources op in de resource groep van het knoop punt.
* Labels wijzigen of verwijderen van beheerde resources binnen de resource groep van het knoop punt. (Zie de volgende sectie voor meer informatie.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Kan ik tags en andere eigenschappen van de AKS-resources in de knooppunt resource groep wijzigen?

Als u door Azure gemaakte Tags en andere bron eigenschappen in de knooppunt resource groep wijzigt of verwijdert, kunt u onverwachte resultaten krijgen, zoals het schalen en upgraden van fouten. Met AKS kunt u aangepaste tags maken en wijzigen. Het is raadzaam om aangepaste labels te maken of te wijzigen, bijvoorbeeld om een bedrijfs eenheid of kosten plaats toe te wijzen. Door de resources in de resource groep node te wijzigen in het AKS-cluster, verbreekt u de serviceniveau doelstelling (SLO). Zie voor meer informatie [AKS biedt een service overeenkomst?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Welke Kubernetes-toegangs controllers ondersteunt AKS? Kunnen Admission controllers worden toegevoegd of verwijderd?

AKS ondersteunt de volgende [toegangs controllers][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

Op dit moment kunt u de lijst met toegangs controllers in AKS niet wijzigen.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Is Azure Key Vault geïntegreerd met AKS?

AKS is momenteel niet ingebouwd met Azure Key Vault. Met de [Azure Key Vault FlexVolume for Kubernetes-project][keyvault-flexvolume] kunt u echter rechtstreeks integratie van Kubernetes van Peul maken op Key Vault geheimen.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kan ik Windows Server-containers op AKS uitvoeren?

Ja, Windows Server-containers zijn beschikbaar als preview-versie. Als u Windows Server-containers in AKS wilt uitvoeren, maakt u een knooppunt groep die Windows Server als het gast besturingssysteem uitvoert. Windows Server-containers kunnen alleen Windows Server 2019 gebruiken. Zie [een AKS-cluster maken met een Windows Server-knooppunt groep][aks-windows-cli]om aan de slag te gaan.

Windows Server-ondersteuning voor de knooppunt groep bevat enkele beperkingen die deel uitmaken van de upstream-Windows-Server in Kubernetes-project. Zie [Windows Server-containers in AKS-beperkingen][aks-windows-limitations]voor meer informatie over deze beperkingen.

## <a name="does-aks-offer-a-service-level-agreement"></a>Biedt AKS een service overeenkomst?

In een Service Level Agreement (SLA) stemt de provider ermee in om de kosten van de service te vergoeden als niet aan het gepubliceerde service niveau wordt voldaan. Omdat AKS gratis is, zijn er geen kosten beschikbaar voor schadeloosstelling, dus AKS heeft geen formele SLA. AKS zoekt echter de beschik baarheid van ten minste 99,5 procent voor de Kubernetes-API-server.

Het is belang rijk dat u het onderscheid herkent tussen de beschik baarheid van de AKS-service, die verwijst naar de uptime van het Kubernetes-besturings vlak en de beschik baarheid van uw specifieke werk belasting die wordt uitgevoerd op Azure Virtual Machines. Hoewel het besturings vlak mogelijk niet beschikbaar is als het besturings element niet gereed is, kunnen de werk belastingen van uw cluster die worden uitgevoerd op virtuele machines van Azure, nog steeds functioneren. De gegeven Azure-Vm's zijn betaalde bronnen die door een financiële SLA worden ondersteund. Lees [hier meer informatie](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) over de sla van de Azure-VM en hoe u deze Beschik baarheid kunt verg Roten met functies als [Beschikbaarheidszones][availability-zones].

## <a name="why-cant-i-set-maxpods-below-30"></a>Waarom kan ik maxPods minder dan 30 instellen?

In AKS kunt u de `maxPods` waarde instellen wanneer u het cluster maakt met behulp van de Azure CLI-en Azure Resource Manager-sjablonen. Zowel Kubenet als Azure CNI vereisen echter een *minimum waarde* (gevalideerd tijdens de aanmaak):

| Networking | Minimum | Maximum |
| -- | :--: | :--: |
| Azure-CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Omdat AKS een beheerde service is, implementeren en beheren we invoeg toepassingen en peulen als onderdeel van het cluster. In het verleden kunnen gebruikers een `maxPods` waarde opgeven die lager is dan de waarde die het beheerde vereiste is om uit te voeren (bijvoorbeeld 30). AKS berekent nu het minimum aantal van het hoogste getal door gebruik te maken van de volgende formule: ((maxPods of (maxPods * vm_count)) > beheerde invoeg toepassing voor het minimum.

Gebruikers kunnen de minimale `maxPods` validatie niet overschrijven.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Kan ik Azure-reserverings kortingen Toep assen op mijn AKS-agent knooppunten?

AKS-agent knooppunten worden gefactureerd als standaard virtuele machines van Azure, dus als u [Azure-reserve ringen][reservation-discounts] hebt aangeschaft voor de VM-grootte die u in AKS gebruikt, worden deze kortingen automatisch toegepast.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Kan ik mijn cluster verplaatsen/migreren tussen Azure-tenants?

De opdracht `az aks update-credentials` kan worden gebruikt om een AKS-cluster te verplaatsen tussen Azure-tenants. Volg de instructies in [kiezen om een service-principal bij te werken of te maken](https://docs.microsoft.com/azure/aks/update-credentials) en [werk vervolgens AKS-cluster bij met nieuwe referenties](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Kan ik mijn cluster verplaatsen/migreren tussen abonnementen?

Het verplaatsen van clusters tussen abonnementen wordt momenteel niet ondersteund.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Kan ik mijn AKS-clusters van het huidige Azure-abonnement naar een andere verplaatsen? 

Het verplaatsen van uw AKS-cluster en de bijbehorende resources tussen Azure-abonnementen wordt niet ondersteund.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Waarom duurt het verwijderen van een cluster? 

De meeste clusters worden verwijderd bij de gebruikers aanvraag; in sommige gevallen, met name waarbij klanten hun eigen resource groep halen of het verwijderen van cross-RG taken, kan het langer duren of mislukken. Als u een probleem met verwijderingen ondervindt, controleert u of er geen vergren delingen zijn op de RG, waardoor bronnen buiten de RG worden ontkoppeling van de RG, enzovoort.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Als ik pod/Deployments heb in de status NodeLost of Unknown, kan ik mijn cluster nog steeds bijwerken?

Dit kan, maar AKS wordt dit niet aanbevolen. Upgrades moeten in het ideale geval worden uitgevoerd wanneer de status van het cluster bekend en in orde is.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Als ik een cluster met een of meer knoop punten met een slechte status of computer heb afgesloten, kan ik dan een upgrade uitvoeren?

Nee, verwijder alle knoop punten met de status mislukt of verwijder deze uit het cluster voordat u een upgrade uitvoert.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Ik heb een cluster verwijderd, maar zie de fout `[Errno 11001] getaddrinfo failed` 

Dit wordt meestal veroorzaakt door gebruikers die een of meer netwerk beveiligings groepen (Nsg's) nog in gebruik hebben en aan het cluster zijn gekoppeld.  Verwijder ze en probeer opnieuw te verwijderen.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Ik heb een upgrade uitgevoerd, maar nu zijn mijn peulen in de loop van een crash, en mislukt de gereedheids tests?

Controleer of uw Service-Principal niet is verlopen.  Zie de referenties van de [AKS-Service-Principal](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) en de AKS- [Update](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Mijn cluster werkte, maar kan plotseling geen LoadBalancers, Mount-Pvc's, enzovoort inrichten? 

Controleer of uw Service-Principal niet is verlopen.  Zie de referenties van de [AKS-Service-Principal](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) en de AKS- [Update](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Kan ik de Api's voor de schaalset van de virtuele machine gebruiken om hand matig te schalen?

Nee, schaal bewerkingen met behulp van de virtuele-machine Scale set-Api's worden niet ondersteund. De AKS-Api's (`az aks scale`) gebruiken.

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Kan ik de schaal sets van virtuele machines gebruiken om hand matig te schalen naar 0 knoop punten?

Nee, schaal bewerkingen met behulp van de virtuele-machine Scale set-Api's worden niet ondersteund.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Kan ik al mijn Vm's stoppen of de toewijzing ervan ongedaan maken?

Hoewel AKS de mechanismen voor flexibiliteit heeft om een dergelijke configuratie te verkrijgen en te herstellen, is dit geen aanbevolen configuratie.

## <a name="can-i-use-custom-vm-extensions"></a>Kan ik aangepaste VM-extensies gebruiken?

Geen AKS is een beheerde service en het bewerken van de IaaS-resources wordt niet ondersteund. Om aangepaste onderdelen, enzovoort, te installeren. Maak gebruik van de kubernetes-Api's en-mechanismen. Gebruik bijvoorbeeld DaemonSets om de vereiste onderdelen te installeren.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
