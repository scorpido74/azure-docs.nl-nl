---
title: Veelgestelde vragen voor Azure Red Hat OpenShift
description: Hier vindt u antwoorden op veelgestelde vragen over Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619471"
---
# <a name="azure-red-hat-openshift-faq"></a>Veelgestelde vragen over Azure Red Hat OpenShift

In dit artikel worden veelgestelde vragen (veelgestelde vragen) over Microsoft Azure Red Hat OpenShift beantwoord.

## <a name="which-azure-regions-are-supported"></a>Welke Azure-regio's worden ondersteund?

Zie [Ondersteunde resources](supported-resources.md#azure-regions) voor een lijst met globale regio's waar Azure Red Hat OpenShift wordt ondersteund.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Kan ik een cluster implementeren in een bestaand virtueel netwerk?

Nee. Maar u een Azure Red Hat OpenShift-cluster verbinden met een bestaand VNET via peering. Zie [Het virtuele netwerk van een cluster verbinden met een bestaand virtueel netwerk](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) voor meer informatie.

## <a name="what-cluster-operations-are-available"></a>Welke clusterbewerkingen zijn beschikbaar?

U alleen het aantal compute nodes op- of omlaag schalen. Er zijn geen andere wijzigingen `Microsoft.ContainerService/openShiftManagedClusters` toegestaan aan de resource na het maken. Het maximum aantal compute nodes is beperkt tot 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Welke virtuele machineformaten kan ik gebruiken?

Zie [Azure Red Hat OpenShift virtuele machineformaten](supported-resources.md#virtual-machine-sizes) voor een lijst met virtuele machineformaten die u gebruiken met een Azure Red Hat OpenShift-cluster.

## <a name="is-data-on-my-cluster-encrypted"></a>Zijn de gegevens op mijn cluster versleuteld?

Standaard is er encryptie in rust. Het Azure Storage-platform versleutelt uw gegevens automatisch voordat deze worden uitgevoerd en decodeert de gegevens voordat u deze ophaalt. Zie [Azure Storage Service Encryption voor gegevens in rust](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) voor meer informatie.

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Kan ik Prometheus/Grafana gebruiken om mijn applicaties te monitoren?

Ja, u Prometheus implementeren in uw naamruimte en toepassingen in uw naamruimte controleren.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Kan ik Prometheus/Grafana gebruiken om statistieken met betrekking tot clusterstatus en -capaciteit te controleren?

Nee, niet op dit moment.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Is het Docker-register extern beschikbaar, zodat ik tools zoals Jenkins kan gebruiken?

Het Docker-register `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` is echter verkrijgbaar bij een sterke garantie voor opslagduurzaamheid. U ook [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)gebruiken.

## <a name="is-cross-namespace-networking-supported"></a>Wordt cross-namespace-netwerken ondersteund?

Klant- en individuele projectbeheerders kunnen cross-namespace-netwerken (inclusief het weigeren `NetworkPolicy` ervan) per project aanpassen met behulp van objecten.

## <a name="can-an-admin-manage-users-and-quotas"></a>Kan een beheerder gebruikers en quota beheren?

Ja. Een Azure Red Hat OpenShift-beheerder kan gebruikers en quota beheren, naast toegang tot alle door gebruikers gemaakte projecten.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Kan ik een cluster beperken tot alleen bepaalde Azure AD-gebruikers?

Ja. U beperken welke Azure AD-gebruikers zich bij een cluster kunnen aanmelden door de Azure AD-toepassing te configureren. Zie Hoe: Uw app beperken tot een set gebruikers voor meer [informatie: Uw app beperken tot een reeks gebruikers](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>Kan ik gebruikers beperken om projecten te maken?

Ja. Meld u aan bij uw cluster als Azure Red Hat OpenShift-beheerder en voer deze opdracht uit:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Zie voor meer informatie de [OpenShift-documentatie](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)over het uitschakelen van zelfvoorziening .

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Kan een cluster rekenknooppunten hebben in meerdere Azure-regio's?

Nee. Alle knooppunten in een Azure Red Hat OpenShift-cluster moeten afkomstig zijn van dezelfde Azure-regio.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Worden master- en infrastructuurknooppunten verwijderd zoals ze zijn met Azure Kubernetes Service (AKS)?

Nee. Alle bronnen, inclusief de clustermaster, worden uitgevoerd in uw klantabonnement. Dit soort resources worden in een alleen-lezen brongroep geplaatst.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Wordt Open Service Broker for Azure (OSBA) ondersteund?

Ja. U OSBA gebruiken met Azure Red Hat OpenShift. Zie [Open Service Broker voor Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) voor meer informatie.

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Ik probeer te peer in een virtueel netwerk `Failed to get vnet CIDR` in een ander abonnement, maar het krijgen van fout.

Zorg er in het abonnement dat het `Microsoft.ContainerService` virtuele netwerk heeft, ervoor dat u provider registreert bij`az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Wat is het Azure Red Hat OpenShift (ARO) onderhoudsproces?

Er zijn drie soorten onderhoud voor ARO: upgrades, back-up en herstel van etcd-gegevens en door de cloudprovider geïnitieerd onderhoud.

+ Upgrades omvatten software-upgrades en CvE's. CVE-herstel vindt plaats `yum update` bij het opstarten door het uitvoeren van en voorziet in onmiddellijke mitigatie.  Tegelijkertijd zal een nieuwe image build worden gemaakt voor toekomstige cluster creëert.

+ Back-up en beheer van etcd-gegevens is een geautomatiseerd proces dat mogelijk clusterdowntime vereist, afhankelijk van de actie. Als de etcd-database wordt hersteld van een back-up zal er downtime zijn. We een back-up etcd per uur en behouden de laatste 6 uur van back-ups.

+ Door de cloudprovider geïnitieerd onderhoud omvat netwerk-, opslag- en regionale uitval. Het onderhoud is afhankelijk van de cloudprovider en is afhankelijk van door de provider geleverde updates.

## <a name="what-is-the-general-upgrade-process"></a>Wat is het algemene upgradeproces?

Het uitvoeren van een upgrade moet een veilig proces zijn om uit te voeren en mag clusterservices niet verstoren. De SRE kan het upgradeproces activeren wanneer er nieuwe versies beschikbaar zijn of CvE's uitstekend zijn.

Beschikbare updates worden getest in een faseomgeving en vervolgens toegepast op productieclusters. Wanneer toegepast, wordt een nieuw knooppunt tijdelijk toegevoegd en knooppunten worden bijgewerkt op een roterende manier, zodat pods replica tellingen behouden. Het volgen van best practices zorgt voor minimale tot geen downtime.

Afhankelijk van de ernst van de in behandeling zijnde upgrade of update kan het proces verschillen omdat de updates snel kunnen worden toegepast om de blootstelling van de service aan een CVE te beperken. Een nieuwe afbeelding wordt asynchroon gebouwd, getest en uitgerold als een clusterupgrade. Anders dan dat, is er geen verschil tussen nood- en gepland onderhoud. Gepland onderhoud is niet vooraf gepland met de klant.

Meldingen kunnen worden verzonden via ICM en e-mail als communicatie naar de klant nodig is.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>Hoe zit het met nood versus geplande onderhoudsramen?

We maken geen onderscheid tussen de twee soorten onderhoud. Onze teams zijn 24/7/365 beschikbaar en maken geen gebruik van traditionele geplande "out-of-hours" onderhoudsvensters.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Hoe worden het hostbesturingssysteem en OpenShift-software bijgewerkt?

Het hostbesturingssysteem en OpenShift-software worden bijgewerkt via ons algemene upgrade- en image-opbouwproces.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Wat is het proces om het bijgewerkte knooppunt opnieuw op te starten?

Dit moet worden behandeld als onderdeel van een upgrade.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Zijn gegevens opgeslagen in geë-geë-mogelijk versleuteld op ARO?

Het is niet versleuteld op het etsniveau. De optie om het in te schakelen wordt momenteel niet ondersteund. OpenShift ondersteunt deze functie, maar technische inspanningen zijn nodig om het op de routekaart te maken. De gegevens worden versleuteld op schijfniveau. Raadpleeg [Gegevens versleutelen bij Datastore Layer](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) voor meer informatie.

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Kunnen logboeken van onderliggende VM's worden gestreamd naar een analysesysteem voor klantenlogboeken?

Syslog, docker logs, journal, en dmesg worden behandeld door de managed service en worden niet blootgesteld aan klanten.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Hoe kan een klant toegang krijgen tot statistieken zoals CPU / geheugen op knooppuntniveau om actie te ondernemen om te schalen, problemen met de foutopsporing, enz. Ik kan niet `kubectl top` lijken te draaien op een ARO cluster.

Klanten hebben toegang tot de CPU/Memory-statistieken op `oc adm top nodes` `kubectl top nodes` knooppuntniveau met behulp van de opdracht of met de clusterrol voor klantbeheer.  Klanten hebben ook toegang tot `pods` de CPU/Memory metrics van met de opdracht `oc adm top pods` of`kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Wat is de standaardpodplannerconfiguratie voor ARO?

ARO gebruikt de standaardplanner die wordt verzonden in OpenShift. Er zijn een paar extra mechanismen die niet worden ondersteund in ARO. Raadpleeg [standaardplannerdocumentatie](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) en [hoofdplannerdocumentatie](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json) voor meer informatie.

Geavanceerde/aangepaste planning wordt momenteel niet ondersteund. Raadpleeg de [planningsdocumentatie](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html) voor meer informatie.

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Als we de implementatie opschalen, hoe worden Azure-foutdomeinen toegewezen aan podplaatsing om ervoor te zorgen dat alle pods voor een service niet worden uitgeschakeld door een fout in een enkel foutdomein?

Er zijn standaard vijf foutdomeinen bij het gebruik van virtuele machineschaalsets in Azure. Elke virtuele machine-instantie in een schaalset wordt in een van deze foutdomeinen geplaatst. Dit zorgt ervoor dat toepassingen die worden geïmplementeerd op de compute nodes in een cluster in afzonderlijke foutdomeinen worden geplaatst.

Raadpleeg [het kiezen van het juiste aantal foutdomeinen voor virtuele machineschaal ingesteld](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) voor meer details.

## <a name="is-there-a-way-to-manage-pod-placement"></a>Is er een manier om pod plaatsing te beheren?

Klanten hebben de mogelijkheid om knooppunten te krijgen en labels te bekijken als de klantbeheerder.  Dit biedt een manier om elke VM in de schaalset te targeten.

Voorzichtigheid is geboden bij het gebruik van specifieke etiketten:

- Hostname mag niet worden gebruikt. Hostname wordt vaak gedraaid met upgrades en updates en is gegarandeerd te veranderen.

- Als de klant een aanvraag heeft voor specifieke labels of een implementatiestrategie, kan dit worden bereikt, maar vereist het technische inspanningen en wordt deze vandaag niet ondersteund.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Wat is het maximum aantal pods in een ARO-cluster?Wat is het maximum aantal pods per knooppunt in ARO?

 Azure Red Hat OpenShift 3.11 heeft een limiet van 50 pod per node met ARO met een limiet van [20 compute node,](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)zodat het maximum aantal pods dat wordt ondersteund in een ARO-cluster wordt beperkt tot 50*20 = 1000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Kunnen we IP-bereiken opgeven voor implementatie op het particuliere VNET, waardoor botsingen met andere zakelijke VNET's worden vermeden die eenmaal zijn peered?

Azure Red Hat OpenShift ondersteunt VNET-peering en stelt de klant in staat om een VNET te bieden om mee te kijken en een VNET CIDR waarin het OpenShift-netwerk actief zal zijn.

De VNET die aro heeft gemaakt, wordt beschermd en accepteert geen configuratiewijzigingen. Het VNET dat wordt peered wordt gecontroleerd door de klant en woont in hun abonnement.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Heeft het cluster een klantabonnement? 

De Azure Managed Application wordt gebruikt in een vergrendelde resourcegroep met het klantabonnement. De klant kan objecten in die RG bekijken, maar niet wijzigen.

## <a name="is-the-sdn-module-configurable"></a>Is de SDN-module configureerbaar?

SDN is openshift-ovs-netwerkbeleid en is niet configureerbaar.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Welke UNIX-rechten (in IaaS) zijn beschikbaar voor Masters/Infra/App Nodes?

Niet van toepassing op dit aanbod. Toegang tot het knooppunt is verboden.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Welke OCP-rechten hebben we? Cluster-admin? Project-admin?

Zie het overzicht van Azure Red Hat [OpenShift-clusterbeheer](https://docs.openshift.com/aro/admin_guide/index.html)voor meer informatie .

## <a name="which-kind-of-federation-with-ldap"></a>Wat voor federatie met LDAP?

Dit zou worden bereikt via Azure AD-integratie. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Is er een element in ARO gedeeld met andere klanten? Of is alles onafhankelijk?

Elk Azure Red Hat OpenShift-cluster is gewijd aan een bepaalde klant en leeft binnen het abonnement van de klant. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Kunnen we kiezen voor een permanente opslagoplossing, zoals OCS? 

Er zijn twee opslagklassen beschikbaar om uit te kiezen: Azure Disk en Azure File.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Hoe wordt een cluster bijgewerkt (inclusief majors en minderjarigen als gevolg van kwetsbaarheden)?

Zie [Wat is het algemene upgradeproces?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Welke Azure Load balancer wordt gebruikt door ARO?Is het standaard of basic en is het configureerbaar?

ARO maakt gebruik van Standard Azure Load Balancer en is niet configureerbaar.

## <a name="can-aro-use-netapp-based-storage"></a>Kan ARO netapp-gebaseerde opslag gebruiken?

Op dit moment zijn de enige ondersteunde opslagopties Azure Disk en Azure File storage classes. 


