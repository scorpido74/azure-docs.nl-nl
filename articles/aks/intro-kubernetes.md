---
title: Kennismaking met Azure Kubernetes Service
description: Ontdek de functies en voordelen van Azure Kubernetes Service voor het implementeren en beheren van toepassingen op basis van containers in Azure.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: overview
ms.date: 05/06/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: c98e67d174c3c8b7fc426e1dd2c5ebafd6760969
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932609"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) maakt het eenvoudig om een ​​beheerd Kubernetes-cluster in Azure te implementeren. AKS verkleint de complexiteit en de operationele overhead die gepaard gaan met het beheer van Kubernetes door veel van deze taken naar Azure over te hevelen. Azure handelt als een gehoste Kubernetes-service cruciale taken voor u af zoals statuscontrole en onderhoud. De Kubernetes-modellen worden beheerd door Azure. U beheert en onderhoudt alleen de agentknooppunten. Als een beheerde Kubernetes-service is AKS gratis. U betaalt alleen voor de agentknooppunten binnen uw clusters, niet voor de masters.

U kunt een AKS-cluster maken in de Azure Portal, met de Azure CLI, of met de sjabloongestuurde implementatieopties, zoals Resource Manager-sjablonen en Terraform. Wanneer u een AKS-cluster implementeert, worden de Kubernetes-master en alle knooppunten voor u geïmplementeerd en geconfigureerd. Extra functies zoals geavanceerd netwerken, Azure Active Directory-integratie en bewaking kunnen ook tijdens het implementatieproces worden geconfigureerd. Ondersteuning voor Windows Server-containers is momenteel beschikbaar als preview-versie in AKS.

Zie [Kubernetes core-concepten voor AKS][concepts-clusters-workloads]voor meer informatie over de basis principes van Kubernetes.

Om aan de slag te gaan, voltooit u de AKS Snelstartgids [in de Azure Portal][aks-portal] of [met de Azure cli][aks-cli].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="access-security-and-monitoring"></a>Toegang, beveiliging en bewaking

Voor verbeterde beveiliging en verbeterd beheer kunt u AKS integreren met Azure Active Directory en gebruik maken van op Kubernetes-rollen gebaseerd toegangsbeheer. U kunt ook de status van uw cluster en resources bewaken.

### <a name="identity-and-security-management"></a>Identiteits- en beveiligingsbeheer

AKS biedt ondersteuning voor het toegangs [beheer op basis van rollen (RBAC)][kubernetes-rbac]om de toegang tot cluster bronnen te beperken. Met RBAC kunt u de toegang instellen tot Kubernetes-resources en -naamruimten en kunt u de machtigingen voor deze bronnen instellen. U kunt een AKS-cluster ook configureren om te worden geïntegreerd met Azure Active Directory (AD). Met Azure AD-integratie kan Kubernetes-toegang worden geconfigureerd op basis van bestaande identiteiten en groepslidmaatschap. Aan uw bestaande Azure AD-gebruikers en -groepen kan toegang tot AKS-resources worden verleend met een geïntegreerde ervaring voor eenmalige aanmelding.

Zie voor meer informatie over identiteit de [toegangs-en identiteits opties voor AKS][concepts-identity].

Als u uw AKS-clusters wilt beveiligen, raadpleegt u [Azure Active Directory integreren met AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Geïntegreerde logboekregistratie en bewaking

Om inzicht te krijgen in hoe uw AKS-cluster en geïmplementeerde toepassingen functioneren, verzamelt Azure Monitor voor containerstatus metrische geheugen- en processorgegevens van containers, knooppunten en domeincontrollers. Er zijn container logboeken beschikbaar en u kunt ook [de Kubernetes-hoofd logboeken bekijken][aks-master-logs]. Deze bewakingsgegevens worden opgeslagen in een Log Analytics-werkruimte van Azure en zijn beschikbaar via Azure Portal, Azure CLI of een REST-eindpunt.

Zie [Azure Kubernetes service container Health controleren][container-health]voor meer informatie.

## <a name="clusters-and-nodes"></a>Clusters en knoop punten

AKS-knooppunten worden uitgevoerd op virtuele machines in Azure. U kunt opslag met knooppunten en pods verbinden, clusteronderdelen bijwerken en GPU's gebruiken. AKS ondersteunt Kubernetes-clusters die meerdere knooppunt groepen uitvoeren ter ondersteuning van gemengde besturings systemen en Windows Server-containers (momenteel als preview-versie). Linux-knoop punten voeren een aangepaste Ubuntu-installatie kopie van het besturings systeem uit en Windows Server-knoop punten voeren een aangepaste installatie kopie van Windows Server 2019-besturings systeem

### <a name="cluster-node-and-pod-scaling"></a>Clusterknooppunten en pods schalen

Naarmate de vraag naar resources verandert, kan het aantal clusterknooppunten of -pods waarop uw services worden uitgevoerd, automatisch omhoog of omlaag worden geschaald. U kunt zowel de horizontale automatische schaalaanpassing voor pods als de automatische schaalaanpassing voor clusters gebruiken. Met deze schalingsmethode kan het AKS-cluster automatisch worden aangepast aan de eisen, zodat alleen de benodigde resources worden uitgevoerd.

Zie [een Azure Kubernetes service-cluster (AKS) schalen][aks-scale]voor meer informatie.

### <a name="cluster-node-upgrades"></a>Clusterknooppuntupgrades

Azure Kubernetes Service biedt meerdere Kubernetes versies. Wanneer nieuwe versies beschikbaar komen in AKS, kan uw cluster worden bijgewerkt met behulp van Azure Portal of Azure-CLI. Tijdens het upgradeproces worden knooppunten zorgvuldig afgebakend en geleegd om onderbreking van actieve toepassingen te minimaliseren.

Zie [ondersteunde Kubernetes-versies in AKS][aks-supported versions]voor meer informatie over levenscyclus versies. Zie [een Azure Kubernetes service-cluster (AKS) bijwerken][aks-upgrade]voor stappen voor het uitvoeren van een upgrade.

### <a name="gpu-enabled-nodes"></a>GPU-knooppunten

AKS ondersteunt het maken van GPU-knooppuntpools. Azure biedt momenteel VM’s met een of meerdere GPU’s. GPU-VM's zijn ontworpen voor rekenintensieve, grafisch-intensieve en visualisatiewerkbelastingen.

Zie [GPU gebruiken op AKS][aks-gpu]voor meer informatie.

### <a name="storage-volume-support"></a>Opslagvolumeondersteuning

Ter ondersteuning van werkbelastingen kunt u opslagvolumes koppelen voor uw permanente gegevens. U kunt zowel statische als dynamische volumes gebruiken. Afhankelijk van het aantal verbonden pods waarmee de opslag moet worden gedeeld, kunt u opslag ondersteund door Azure Disks gebruiken voor toegang tot één pod, of opslag ondersteund door Azure Files voor toegang tot meerdere pods tegelijk.

Zie [opslag opties voor toepassingen in AKS][concepts-storage]voor meer informatie.

Ga aan de slag met dynamische permanente volumes met behulp van [Azure-schijven][azure-disk] of [Azure files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Virtual Networks en inkomend verkeer

Een AKS-cluster kan worden geïmplementeerd in een bestaand virtueel netwerk. In deze configuratie krijgt elke pod in het cluster een IP-adres toegewezen in het virtueel netwerk. De pod kan direct communiceren met andere pods in het cluster en met andere knooppunten in het virtueel netwerk. Pods kunnen ook verbinding maken met andere services in een gekoppeld virtueel netwerk en met on-premises netwerken via ExpressRoute en S2S VPN-verbindingen (site-to-site).

Zie [Network concepten for Applications in AKS][aks-networking](Engelstalig) voor meer informatie.

Zie [http-toepassings routering][aks-http-routing]om aan de slag te gaan met ingangs verkeer.

### <a name="ingress-with-http-application-routing"></a>Ingress met HTTP-toepassingsroutering

De invoegtoepassing voor HTTP-toepassingsroutering vergemakkelijkt de toegang tot toepassingen die in uw AKS-cluster zijn geïmplementeerd. Indien ingeschakeld, configureert de HTTP-toepassingsrouteringsoplossing een ingangscontroller in uw AKS-cluster. Wanneer er toepassingen worden geïmplementeerd, worden automatisch openbaar toegankelijke DNS-namen geconfigureerd. HTTP-toepassingsroutering configureert een DNS-zone en integreert deze met het AKS-cluster. Daarna kunt u inkomende Kubernetes-resources op de gebruikelijke manier implementeren.

Zie [http-toepassings routering][aks-http-routing]om aan de slag te gaan met ingangs verkeer.

## <a name="development-tooling-integration"></a>Integratie van ontwikkelingshulpprogramma’s

Kubernetes heeft een uitgebreid ecosysteem van hulpprogramma’s voor ontwikkeling en beheer, zoals Helm, Draft en de Kubernetes-extensie voor Visual Studio Code. Deze hulpprogramma's werken naadloos met AKS.

Bovendien biedt Azure Dev Spaces een snelle, iteratieve Kubernetes-ontwikkelervaring voor teams. Met een minimale configuratie kunt u containers rechtstreeks in AKS uitvoeren en debuggen. Zie [Azure dev Spaces][azure-dev-spaces]om aan de slag te gaan.

Het Azure DevOps-project biedt een eenvoudige oplossing voor het overbrengen van bestaande code en Git-opslagplaatsen naar Azure. Het DevOps-project maakt automatisch Azure-resources zoals AKS, een release-pipeline in Azure DevOps Services met een build-pipeline voor CI, stelt een release-pipeline in voor CD en maakt vervolgens een Azure Application Insights-resource voor bewaking.

Zie [Azure DevOps project][azure-devops](Engelstalig) voor meer informatie.

## <a name="docker-image-support-and-private-container-registry"></a>Ondersteuning voor Docker-installatiekopieën en registers voor persoonlijke containers

AKS ondersteunt de Docker-installatiekopie-indeling. Voor privéopslag van uw Docker-installatiekopieën kunt u AKS integreren met Azure Container Registry (ACR).

Zie [Azure container Registry][acr-docs]voor het maken van een archief met persoonlijke installatie kopieën.

## <a name="kubernetes-certification"></a>Kubernetes-certificering

De Azure Kubernetes Service (AKS) is door de CNCF gecertificeerd als compatibel met Kubernetes.

## <a name="regulatory-compliance"></a>Naleving van regelgeving

AKS (Azure Kubernetes Service) voldoet aan SOC, ISO, PCI DSS en HIPAA. Zie [overzicht van Microsoft Azure compatibiliteit][compliance-doc]voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het implementeren en beheren van AKS vindt u in de snelstartgids van Azure CLI.

> [!div class="nextstepaction"]
> [AKS Quick Start][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
