---
title: Veelgestelde vragen over Azure Red Hat open Shift | Microsoft Docs
description: Hier vindt u antwoorden op veelgestelde vragen over Microsoft Azure Red Hat open Shift
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: 2b4e78db9f3aa3a8f678212c7fcd1b97ed4834b1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75378208"
---
# <a name="azure-red-hat-openshift-faq"></a>Veelgestelde vragen over Azure Red Hat open Shift

In dit artikel vindt u veelgestelde vragen over Microsoft Azure Red Hat open SHIFT.

## <a name="which-azure-regions-are-supported"></a>Welke Azure-regio's worden ondersteund?

Zie [ondersteunde resources](supported-resources.md#azure-regions) voor een lijst met wereld wijde regio's waar Azure Red Hat open Shift wordt ondersteund.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Kan ik een cluster implementeren in een bestaand virtueel netwerk?

Nee. Maar u kunt een Azure Red Hat open Shift-cluster verbinden met een bestaand VNET via peering. Zie [verbinding maken met een virtueel netwerk van een cluster met een bestaand virtueel netwerk](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) voor meer informatie.

## <a name="what-cluster-operations-are-available"></a>Welke cluster bewerkingen zijn beschikbaar?

U kunt het aantal reken knooppunten alleen omhoog of omlaag schalen. Er zijn geen andere wijzigingen toegestaan aan de `Microsoft.ContainerService/openShiftManagedClusters` resource nadat deze is gemaakt. Het maximum aantal reken knooppunten is beperkt tot 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Welke groottes voor virtuele machines kan ik gebruiken?

Zie [grootten van Azure Red Hat open Shift](supported-resources.md#virtual-machine-sizes) voor een lijst met grootten voor virtuele machines die u kunt gebruiken met een Azure Red Hat open Shift-cluster.

## <a name="is-data-on-my-cluster-encrypted"></a>Versleutelde gegevens op mijn cluster?

Standaard is versleuteling in rust. Op het Azure Storage-platform worden uw gegevens automatisch versleuteld voordat deze persistent worden gemaakt en worden de gegevens gedecodeerd voordat ze worden opgehaald. Zie [Azure Storage service versleuteling voor gegevens in rust](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) voor meer informatie.

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Kan ik Prometheus/Grafana gebruiken voor het bewaken van mijn toepassingen?

Ja, u kunt Prometheus in uw naam ruimte implementeren en toepassingen in uw naam ruimte controleren.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Kan ik Prometheus/Grafana gebruiken voor het bewaken van metrische gegevens met betrekking tot de status en capaciteit van het cluster?

Nee, niet op huidige tijd.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Is het docker-REGI ster extern beschikbaar, zodat ik hulpprogram ma's zoals Jenkins kan gebruiken?

Het docker-REGI ster is beschikbaar via `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` echter, er is geen krachtige garantie voor de duurzaamheid van opslag. U kunt ook [Azure container Registry](https://azure.microsoft.com/services/container-registry/)gebruiken.

## <a name="is-cross-namespace-networking-supported"></a>Wordt meerdere naam ruimte netwerken ondersteund?

Klanten en afzonderlijke project beheerders kunnen meerdere naam ruimte netwerken (inclusief het weigeren) aanpassen per project met behulp van `NetworkPolicy`-objecten.

## <a name="can-an-admin-manage-users-and-quotas"></a>Kan een beheerder gebruikers en quota's beheren?

Ja. Een Azure Red Hat open Shift-beheerder kan gebruikers en quota's beheren naast toegang tot alle door de gebruiker gemaakte projecten.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Kan ik een cluster beperken tot bepaalde Azure AD-gebruikers?

Ja. U kunt beperken welke Azure AD-gebruikers zich kunnen aanmelden bij een cluster door de Azure AD-toepassing te configureren. Zie [How to: uw app beperken tot een set gebruikers](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users) voor meer informatie.

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Kan een cluster reken knooppunten hebben in meerdere Azure-regio's?

Nee. Alle knoop punten in een open Shift-cluster van Azure Red Hat moeten afkomstig zijn uit dezelfde Azure-regio.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Zijn de Master-en infrastructuur knooppunten opabstracted als ze zijn met Azure Kubernetes service (AKS)?

Nee. Alle resources, inclusief de cluster Master, worden uitgevoerd in uw klant abonnement. Deze typen resources worden in een alleen-lezen resource groep geplaatst.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Is open Service Broker voor Azure (OSBA) ondersteund?

Ja. U kunt OSBA gebruiken met Azure Red Hat open SHIFT. Zie [Service Broker voor Azure openen](https://github.com/Azure/open-service-broker-azure#openshift-project-template) voor meer informatie.

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Ik probeer te koppelen aan een virtueel netwerk in een ander abonnement, maar `Failed to get vnet CIDR` fout.

Zorg er in het abonnement met het virtuele netwerk voor dat u `Microsoft.ContainerService` provider registreert bij `az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Wat is het onderhouds proces van Azure Red Hat open Shift (ARO)?

Er zijn drie soorten onderhoud voor ARO: upgrades, back-ups maken en herstellen van etcd-gegevens en door de Cloud provider geïnitieerd onderhoud.

+ Upgrades zijn onder andere software-upgrades en CVEs. CVE herstel vindt plaats bij het opstarten door `yum update` uit te voeren en biedt onmiddellijke beperking.  Op parallelle manier wordt een nieuwe installatie kopie gemaakt voor het maken van een nieuw cluster.

+ Het maken en beheren van etcd-gegevens is een geautomatiseerd proces waarvoor mogelijk downtime van het cluster nodig is, afhankelijk van de actie. Als de etcd-data base wordt hersteld vanuit een back-up, zal er downtime zijn. Er wordt een back-up van etcd elk uur gemaakt en de laatste 6 uur van de back-ups worden bewaard.

+ Door de Cloud provider geïnitieerd onderhoud omvat netwerk-, opslag-en regionale storingen. Het onderhoud is afhankelijk van de Cloud provider en is afhankelijk van door de provider geleverde updates.

## <a name="what-is-the-general-upgrade-process"></a>Wat is het algemene upgrade proces?

Het uitvoeren van een upgrade moet een veilig proces zijn om uit te voeren en Cluster Services niet te onderbreken. De SRE kan het upgrade proces activeren wanneer er nieuwe versies beschikbaar zijn of als CVEs openstaan.

Beschik bare updates worden getest in een omgeving met fase ring en vervolgens toegepast op productie clusters. Wanneer u dit toepast, wordt er tijdelijk een nieuw knoop punt toegevoegd en worden de knoop punten op een roteerende manier bijgewerkt zodat het aantal replica aantallen behouden blijft. Volg de aanbevolen procedures om ervoor te zorgen dat er mini maal geen uitval tijd is.

Afhankelijk van de ernst van de in behandeling zijnde upgrade of update, kan het proces verschillen als de updates mogelijk snel worden toegepast om de bloot stelling van de service aan een CVE te beperken. Een nieuwe installatie kopie wordt asynchroon, getest en uitgerold als een cluster upgrade. Behalve dat is er geen verschil tussen nood herstel en gepland onderhoud. Gepland onderhoud is niet voorgepland met de klant.

U kunt meldingen verzenden via ICM en e-mail als communicatie met de klant is vereist.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>Hoe zit het met nood en geplande onderhouds Vensters?

Er wordt geen onderscheid gemaakt tussen de twee soorten onderhoud. Onze teams zijn beschikbaar 24/7/365 en gebruiken geen traditionele, geplande ' out-of-uren ' onderhouds Vensters.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Hoe wordt het hosten van het besturings systeem en open Shift-software bijgewerkt?

Het besturings systeem van de host en open Shift-software worden bijgewerkt via onze algemene upgrade en installatie kopie-buildproces.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Wat is het proces om het bijgewerkte knoop punt opnieuw op te starten?

Dit moet worden afgehandeld als onderdeel van een upgrade.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Worden gegevens die zijn opgeslagen in etcd versleuteld op ARO?

Het is niet versleuteld op het niveau van de etcd. De optie om deze in te scha kelen, wordt momenteel niet ondersteund. Open Shift ondersteunt deze functie, maar technische inspanningen zijn vereist om deze op de weg kaart te maken. De gegevens worden versleuteld op schijf niveau. Zie [gegevens versleutelen in de Data Store-laag](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) voor meer informatie.

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Kunnen Logboeken van onderliggende Vm's worden gestreamd naar een logboek analyse systeem van klanten?

Syslog, docker-logboeken, logboeken en dmesg worden verwerkt door de beheerde service en worden niet blootgesteld aan klanten.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Hoe kan een klant toegang krijgen tot metrische gegevens, zoals CPU/geheugen op knooppunt niveau, om actie te ondernemen om te schalen, problemen op te lossen, enzovoort. Ik kan `kubectl top` niet uitvoeren op een ARO-cluster.

Klanten hebben toegang tot de metrische gegevens van de CPU/het geheugen op knooppunt niveau met behulp van de opdracht `oc adm top nodes` of `kubectl top nodes` met de beheerder clusterrole van de klant.  Klanten hebben ook toegang tot de metrische gegevens van de CPU/het geheugen van `pods` met de opdracht `oc adm top pods` of `kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Wat is de standaard configuratie van de pod Scheduler voor ARO?

ARO maakt gebruik van de standaard planner die wordt geleverd in open SHIFT. Er zijn een aantal extra mechanismen die niet worden ondersteund in ARO. Raadpleeg de documentatie voor de [standaard planner](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) en de [hoofd planner](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json) voor meer informatie.

Geavanceerde/aangepaste planning wordt momenteel niet ondersteund. Raadpleeg de [plannings documentatie](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) voor meer informatie.

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Als de implementatie wordt geschaald, hoe kunnen Azure-fout domeinen worden toegewezen aan de pod-plaatsing om ervoor te zorgen dat alle peulen voor een service niet worden uitgesp aard door een storing in één fout domein?

Er zijn standaard vijf fout domeinen bij het gebruik van schaal sets voor virtuele machines in Azure. Elk exemplaar van de virtuele machine in een schaalset wordt in een van deze fout domeinen geplaatst. Dit zorgt ervoor dat toepassingen die zijn geïmplementeerd op de reken knooppunten in een cluster, in afzonderlijke fout domeinen worden geplaatst.

Raadpleeg [het juiste aantal fout domeinen voor de schaalset voor virtuele machines](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) voor meer informatie.

## <a name="is-there-a-way-to-manage-pod-placement"></a>Is er een manier om pod-plaatsing te beheren?

Klanten hebben de mogelijkheid om knoop punten op te halen en labels te bekijken als de beheerder van de klant.  Dit biedt een manier om een virtuele machine in de schaalset te richten.

Let op wanneer u specifieke labels gebruikt:

- De hostnaam mag niet worden gebruikt. De hostnaam wordt vaak met upgrades en updates gedraaid en wordt gegarandeerd gewijzigd.

- Als de klant een aanvraag voor specifieke labels of een implementatie strategie heeft, kan dit worden bereikt, maar is er technische inspanningen nodig en wordt deze niet ondersteund.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Wat is het maximum aantal peulen in een ARO-cluster?  Wat is het maximum aantal peulen per knoop punt in ARO?

 Azure Red Hat open Shift 3,11 heeft een limiet van 50-pod per knoop punt met [Aro met een limiet van 20 reken knooppunten](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available), zodat het maximum aantal peulingen in een Aro-cluster wordt gecaps tot 50 * 20 = 1000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Kunnen we IP-bereiken voor implementatie op het privé-VNET opgeven, waardoor er geen conflict is met andere bedrijfs VNETs zodra het peerend is?

Azure Red Hat open Shift ondersteunt VNET-peering en stelt de klant in staat om een VNET te leveren aan de peer en een VNET-CIDR waarin het open Shift-netwerk werkt.

Het VNET dat door ARO is gemaakt, wordt beveiligd en accepteert geen configuratie wijzigingen. Het VNET dat wordt beheerd door de klant en zich in het abonnement bevindt.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Bevindt het cluster zich in een klant abonnement? 

De door Azure beheerde toepassing bevindt zich in een vergrendelde resource groep met het abonnement van de klant. De klant kan objecten in die RG weer geven, maar niet wijzigen.

## <a name="is-the-sdn-module-configurable"></a>Kan de SDN-module worden geconfigureerd?

SDN is open Shift-OVS-networkpolicy en kan niet worden geconfigureerd.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Welke UNIX-rechten (in IaaS) zijn beschikbaar voor modellen/infra structuur/app-knoop punten?

Niet van toepassing op deze aanbieding. Toegang tot knoop punt is niet toegestaan.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Welke OCP-rechten hebben we? Cluster-beheerder? Project-beheerder?

Zie overzicht van Azure Red Hat open SHIFT [Cluster Administration](https://docs.openshift.com/aro/admin_guide/index.html)voor meer informatie.

## <a name="which-kind-of-federation-with-ldap"></a>Welk type Federatie met LDAP?

Dit wordt bereikt via Azure AD-integratie. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Is er een element in ARO gedeeld met andere klanten? Of is alles onafhankelijk?

Elk Azure Red Hat open Shift-cluster is toegewezen aan een bepaalde klant en valt binnen het abonnement van de klant. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Kunnen we een permanente opslag oplossing kiezen, zoals OCS? 

Er zijn twee opslag klassen beschikbaar om te selecteren uit: Azure disk en Azure file.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Hoe wordt een cluster bijgewerkt (inclusief belang rijke en minder jarigen als gevolg van beveiligings problemen)?

Zie [Wat is het algemene upgrade proces?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Welke Azure Load Balancer wordt gebruikt door ARO?  Is it Standard of Basic en kan het worden geconfigureerd?

ARO maakt gebruik van standaard Azure Load Balancer en kan niet worden geconfigureerd.

## <a name="can-aro-use-netapp-based-storage"></a>Kan ARO opslag gebruiken?

Op het moment van de enige ondersteunde opslag opties zijn Azure disk en Azure File Storage-klassen. 


