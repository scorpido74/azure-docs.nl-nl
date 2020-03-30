---
title: Veelgestelde vragen voor Azure Kubernetes Service (AKS)
description: Antwoorden vinden op enkele van de veelgestelde vragen over Azure Kubernetes Service (AKS).
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: c4bb4328af5df7f729967c7b249847b2ab098770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497767"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Veelgestelde vragen over AKS (Azure Kubernetes Service)

In dit artikel worden veelgestelde vragen over Azure Kubernetes Service (AKS) beantwoord.

## <a name="which-azure-regions-currently-provide-aks"></a>Welke Azure-regio's bieden momenteel AKS?

Zie [AKS-regio's en beschikbaarheid][aks-regions]voor een volledige lijst met beschikbare regio's.

## <a name="does-aks-support-node-autoscaling"></a>Ondersteunt AKS automatisch schalen van knooppunts?

Ja, de mogelijkheid om agentknooppunten automatisch horizontaal te schalen in AKS is momenteel beschikbaar in preview. Zie [Een cluster automatisch schalen om te voldoen aan toepassingsvereisten in AKS][aks-cluster-autoscaler] voor instructies. AKS autoscaling is gebaseerd op de [Kubernetes autoscaler][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Kan ik AKS implementeren in mijn bestaande virtuele netwerk?

Ja, u een AKS-cluster implementeren in een bestaand virtueel netwerk met behulp van de [geavanceerde netwerkfunctie.][aks-advanced-networking]

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kan ik beperken wie toegang heeft tot de Kubernetes API-server?

Ja, u de toegang tot de Kubernetes API-server beperken met [API Server Authorized IP Ranges.][api-server-authorized-ip-ranges]

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Kan ik de Kubernetes API-server alleen toegankelijk maken binnen mijn virtuele netwerk?

Niet op dit moment, maar dit is gepland. U de voortgang bijhouden op de [AKS GitHub repo.][private-clusters-github-issue]

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Kan ik verschillende VM-formaten in één cluster hebben?

Ja, u verschillende virtuele machineformaten in uw AKS-cluster gebruiken door [meerdere knooppuntgroepen te][multi-node-pools]maken.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Worden beveiligingsupdates toegepast op AKS-agentknooppunten?

Azure past automatisch beveiligingspatches toe op de Linux-knooppunten in uw cluster volgens een nachtschema. U bent er echter verantwoordelijk voor dat deze Linux-knooppunten naar behoefte opnieuw worden opgestart. U hebt verschillende opties voor het opnieuw opstarten van knooppunten:

- Handmatig, via de Azure-portal of de Azure CLI.
- Door uw AKS-cluster te upgraden. Het cluster upgrades [cordon en drain nodes][cordon-drain] automatisch en breng vervolgens een nieuw knooppunt online met de nieuwste Ubuntu afbeelding en een nieuwe patch versie of een kleine Kubernetes versie. Zie [Een AKS-cluster upgraden][aks-upgrade]voor meer informatie.
- Door het gebruik van [Kured](https://github.com/weaveworks/kured), een open-source reboot daemon voor Kubernetes. Kured wordt uitgevoerd als een [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) en controleert elk knooppunt op de aanwezigheid van een bestand dat aangeeft dat een reboot vereist is. Over het cluster worden opnieuw opstarten van het besturingssysteem beheerd door hetzelfde [cordon- en afvoerproces][cordon-drain] als een clusterupgrade.

Zie [Beveiligings- en kernelupdates toepassen op knooppunten in AKS voor][node-updates-kured]meer informatie over het gebruik van kured.

### <a name="windows-server-nodes"></a>Windows Server-knooppunten

Voor Windows Server-knooppunten (momenteel in preview in AKS) wordt de laatste updates niet automatisch uitgevoerd en toegepast voor Windows Server-knooppunten (momenteel in preview in AKS). Op een regelmatig schema rond de Windows Update-releasecyclus en uw eigen validatieproces moet u een upgrade uitvoeren op het cluster en de Windows Server-knooppuntgroep(s) in uw AKS-cluster. Met dit upgradeproces worden knooppunten gemaakt waarmee de nieuwste Windows Server-afbeelding en -patches worden uitgevoerd en worden de oudere knooppunten verwijderd. Zie [Een knooppuntgroep in AKS upgraden][nodepool-upgrade]voor meer informatie over dit proces.

## <a name="why-are-two-resource-groups-created-with-aks"></a>Waarom zijn twee resourcegroepen gemaakt met AKS?

AKS bouwt voort op een aantal Azure-infrastructuurbronnen, waaronder virtuele machineschaalsets, virtuele netwerken en beheerde schijven. Hierdoor u veel van de kernmogelijkheden van het Azure-platform benutten binnen de beheerde Kubernetes-omgeving van AKS. De meeste azure-typen virtuele machines kunnen bijvoorbeeld rechtstreeks met AKS worden gebruikt en Azure-reserveringen kunnen worden gebruikt om automatisch kortingen op deze resources te ontvangen.

Om deze architectuur in te schakelen, omvat elke AKS-implementatie twee resourcegroepen:

1. U maakt de eerste resourcegroep. Deze groep bevat alleen de Kubernetes-servicebron. De AKS-resourceprovider maakt automatisch de tweede resourcegroep tijdens de implementatie. Een voorbeeld van de tweede resourcegroep is *MC_myResourceGroup_myAKSCluster_eastus*. Zie de volgende sectie voor informatie over het opgeven van de naam van deze tweede resourcegroep.
1. De tweede resourcegroep, bekend als de *brongroep knooppunt,* bevat alle infrastructuurbronnen die aan het cluster zijn gekoppeld. Deze bronnen omvatten de Vm's van het Kubernetes-knooppunt, virtuele netwerken en opslag. Standaard heeft de brongroep knooppunt een naam als *MC_myResourceGroup_myAKSCluster_eastus*. AKS verwijdert automatisch de knooppuntbron wanneer het cluster wordt verwijderd, dus het mag alleen worden gebruikt voor bronnen die de levenscyclus van het cluster delen.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Kan ik mijn eigen naam opgeven voor de AKS-knooppuntbrongroep?

Ja. Aks geeft standaard een naam aan de brongroep knooppunt *MC_resourcegroupname_clustername_location,* maar u ook uw eigen naam opgeven.

Als u uw eigen naam van de brongroep wilt opgeven, installeert u de versie *0.3.2* of hoger [van De Aks-Preview][aks-preview-cli] Azure CLI-extensie. Wanneer u een AKS-cluster maakt met de opdracht [az aks-maak,][az-aks-create] gebruikt u de parameter *--knooppuntbron-groep* en geeft u een naam op voor de resourcegroep. Als u [een Azure Resource Manager-sjabloon gebruikt][aks-rm-template] om een AKS-cluster te implementeren, u de naam van de brongroep definiëren met de eigenschap *nodeResourceGroup.*

* De secundaire brongroep wordt automatisch gemaakt door de Azure-resourceprovider in uw eigen abonnement.
* U alleen een aangepaste naam van de brongroep opgeven wanneer u het cluster maakt.

Houd er bij het werken met de brongroep knooppunt rekening mee dat u niet:

* Geef een bestaande resourcegroep op voor de knooppuntbrongroep.
* Geef een ander abonnement op voor de brongroep knooppunt.
* Wijzig de naam van de knooppuntbrongroep nadat het cluster is gemaakt.
* Geef namen op voor de beheerde resources in de brongroep knooppunt.
* Tags van beheerde resources in de brongroep knooppunt wijzigen of verwijderen. (Zie aanvullende informatie in de volgende sectie.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Kan ik tags en andere eigenschappen van de AKS-resources in de brongroep knooppunt wijzigen?

Als u door Azure gemaakte tags en andere resourceeigenschappen in de brongroep knooppunt wijzigt of verwijdert, u onverwachte resultaten krijgen, zoals schaling- en upgradefouten. Met AKS u aangepaste tags maken en wijzigen. U bijvoorbeeld aangepaste tags maken of wijzigen om een business unit of kostenplaats toe te wijzen. Door de resources onder de knooppuntbrongroep in het AKS-cluster te wijzigen, breekt u de doelstelling serviceniveau (SLO). Zie [Biedt AKS een service-level agreement voor](#does-aks-offer-a-service-level-agreement) meer informatie?

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Welke Kubernetes-toegangscontrollers ondersteunt AKS? Kunnen toegangscontrollers worden toegevoegd of verwijderd?

AKS ondersteunt de volgende [toelatingscontroleurs:][admission-controllers]

- *NaamruimteLevenscyclus*
- *LimitRanger LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass DefaultStorageClass DefaultStorageClass DefaultStorage*
- *DefaultTolerationSeconds DefaultTolerationSeconds DefaultTolerationSeconds DefaultTo*
- *MuterenDeWebhaak*
- *ValiderenToelatingWebhook*
- *Resourcequotum*

Momenteel u de lijst met toegangscontrollers in AKS niet wijzigen.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Is Azure Key Vault geïntegreerd met AKS?

AKS is momenteel niet native geïntegreerd met Azure Key Vault. Het [Azure Key Vault FlexVolume voor Kubernetes-project][keyvault-flexvolume] maakt echter directe integratie mogelijk van Kubernetes-pods naar Key Vault-geheimen.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kan ik Windows Server-containers uitvoeren op AKS?

Ja, Windows Server-containers zijn beschikbaar in preview. Als u Windows Server-containers in AKS wilt uitvoeren, maakt u een knooppuntgroep waarop Windows Server als gastbesturingssysteem wordt uitgevoerd. Windows Server-containers kunnen alleen Windows Server 2019 gebruiken. Zie [Een AKS-cluster maken met een Windows Server-knooppuntgroep][aks-windows-cli]om aan de slag te gaan.

Windows Server-ondersteuning voor knooppuntgroep bevat enkele beperkingen die deel uitmaken van het upstream Windows Server in Kubernetes-project. Zie [Windows Server-containers in AKS-beperkingen voor][aks-windows-limitations]meer informatie over deze beperkingen.

## <a name="does-aks-offer-a-service-level-agreement"></a>Biedt AKS een service-level overeenkomst?

In een service-level agreement (SLA) stemt de aanbieder ermee in de klant de kosten van de service te vergoeden als het gepubliceerde serviceniveau niet wordt gehaald. Aangezien AKS gratis is, zijn er geen kosten beschikbaar om te vergoeden, dus AKS heeft geen formele SLA. AKS streeft er echter naar om de beschikbaarheid van ten minste 99,5 procent voor de Kubernetes API-server te behouden.

Het is belangrijk om het onderscheid te herkennen tussen de beschikbaarheid van AKS-services, die verwijst naar uptime van het Kubernetes-controlevlak en de beschikbaarheid van uw specifieke workload die wordt uitgevoerd op Azure Virtual Machines. Hoewel het besturingsvlak mogelijk niet beschikbaar is als het besturingsvlak niet klaar is, kunnen uw clusterworkloads die op Azure VM's worden uitgevoerd, nog steeds functioneren. Gezien Azure VM's zijn betaalde resources worden ze ondersteund door een financiële SLA. Lees [hier voor meer informatie](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) over de Azure VM SLA en hoe u die beschikbaarheid vergroten met functies zoals [beschikbaarheidszones.][availability-zones]

## <a name="why-cant-i-set-maxpods-below-30"></a>Waarom kan ik maxPods niet onder de 30 instellen?

In AKS u `maxPods` de waarde instellen wanneer u het cluster maakt met behulp van de azure CLI- en Azure Resource Manager-sjablonen. Zowel Kubenet als Azure CNI vereisen echter een *minimumwaarde* (gevalideerd bij het maken van tijd):

| Netwerken | Minimum | Maximum |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet Kubenet | 30 | 110 |

Omdat AKS een beheerde service is, implementeren en beheren we add-ons en pods als onderdeel van het cluster. In het verleden konden `maxPods` gebruikers een waarde definiëren die lager was dan de waarde die de beheerde pods moeten uitvoeren (bijvoorbeeld 30). AKS berekent nu het minimum aantal pods met behulp van deze formule: ((maxPods of (maxPods * vm_count)) > beheerde add-on pods minimaal.

Gebruikers kunnen de minimale `maxPods` validatie niet overschrijven.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Kan ik Azure-reserveringskortingen toepassen op mijn AKS-agentknooppunten?

AKS-agentknooppunten worden gefactureerd als standaard virtuele Azure-machines, dus als u [Azure-reserveringen][reservation-discounts] hebt gekocht voor de VM-grootte die u in AKS gebruikt, worden deze kortingen automatisch toegepast.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Kan ik mijn cluster verplaatsen/migreren tussen Azure-tenants?

De `az aks update-credentials` opdracht kan worden gebruikt om een AKS-cluster tussen Azure-tenants te verplaatsen. Volg de instructies in [Kiezen om een serviceprincipal bij te werken of te maken](https://docs.microsoft.com/azure/aks/update-credentials) en werk vervolgens het [aks-cluster bij met nieuwe referenties.](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials)

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Kan ik mijn cluster verplaatsen/migreren tussen abonnementen?

Het verkeer van clusters tussen abonnementen wordt momenteel niet ondersteund.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Kan ik mijn AKS-clusters verplaatsen van het huidige azure-abonnement naar een ander azure? 

Het verplaatsen van uw AKS-cluster en de bijbehorende bronnen tussen Azure-abonnementen wordt niet ondersteund.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Waarom duurt het verwijderen van mijn cluster zo lang? 

De meeste clusters worden verwijderd op verzoek van de gebruiker; in sommige gevallen, vooral wanneer klanten hun eigen Resource Group meenemen of cross-RG-taken verwijderen, kan extra tijd in beslag nemen of mislukken. Als u een probleem hebt met verwijdert, controleer dan of u geen vergrendelingen op de RG hebt, of alle bronnen buiten de RG losstaan van de RG, enz.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Als ik pod / implementaties in de staat 'NodeLost' of 'Onbekend' kan ik nog steeds een upgrade van mijn cluster?

Dat kan, maar AKS raadt dit niet aan. Upgrades moeten idealiter worden uitgevoerd wanneer de status van het cluster bekend en gezond is.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Als ik een cluster heb met een of meer knooppunten in een niet-gezonde status of als ik ben afgesloten, kan ik dan een upgrade uitvoeren?

Nee, verwijder/verwijder alle knooppunten in een mislukte status of anderszins verwijderd uit het cluster voordat u een upgrade uitvoert.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Ik heb een cluster verwijderd, maar zie de fout`[Errno 11001] getaddrinfo failed` 

Meestal wordt dit veroorzaakt door gebruikers met een of meer Network Security Groups (NSGs) nog steeds in gebruik en gekoppeld aan het cluster.  Verwijder ze en probeer de delete opnieuw.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Ik liep een upgrade, maar nu mijn pods zijn in crash lussen, en gereedheid sondes mislukken?

Bevestig dat uw serviceprincipal niet is verlopen.  Zie: [AKS-serviceprincipal-](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) en [AKS-updatereferenties](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Mijn cluster werkte, maar plotseling niet kan voorzien LoadBalancers, monteren PVC's, enz.? 

Bevestig dat uw serviceprincipal niet is verlopen.  Zie: [AKS-serviceprincipal-](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) en [AKS-updatereferenties](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Kan ik de api's voor virtuele machineschaal gebruiken om handmatig te schalen?

Nee, schaalbewerkingen met behulp van de API's met virtuele machineschaalset worden niet ondersteund. Gebruik de AKS`az aks scale`API's ( ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Kan ik virtuele machineschaalsets gebruiken om handmatig te schalen naar 0 knooppunten?

Nee, schaalbewerkingen met behulp van de API's met virtuele machineschaalset worden niet ondersteund.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Kan ik al mijn VM's stoppen of de-toewijzen?

Hoewel AKS veerkrachtmechanismen heeft om een dergelijke config te weerstaan en ervan te herstellen, is dit geen aanbevolen configuratie.

## <a name="can-i-use-custom-vm-extensions"></a>Kan ik aangepaste VM-extensies gebruiken?

Geen ENKELE AKS is een beheerde service en manipulatie van de IaaS-bronnen wordt niet ondersteund. Om aangepaste componenten te installeren, enz. maak gebruik van de Kubernetes API's en -mechanismen. Maak bijvoorbeeld gebruik van DaemonSets om vereiste componenten te installeren.

<!-- LINKS - internal -->

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
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
