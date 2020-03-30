---
title: Concepten - Beveiliging in Azure Kubernetes Services (AKS)
description: Meer informatie over beveiliging in Azure Kubernetes Service (AKS), inclusief hoofd- en knooppuntcommunicatie, netwerkbeleid en Kubernetes-geheimen.
services: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 7238e6cd7ab3625e2953a4408c82802d43372256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595940"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Beveiligingsconcepten voor toepassingen en clusters in Azure Kubernetes Service (AKS)

Om uw klantgegevens te beschermen terwijl u toepassingsworkloads uitvoert in Azure Kubernetes Service (AKS), is de beveiliging van uw cluster een belangrijke overweging. Kubernetes bevat beveiligingscomponenten zoals *netwerkbeleid* en *Geheimen.* Azure voegt vervolgens onderdelen toe, zoals netwerkbeveiligingsgroepen en georkestreerde clusterupgrades. Deze beveiligingscomponenten worden gecombineerd om ervoor te zorgen dat uw AKS-cluster de nieuwste beveiligingsupdates en Kubernetes-releases uitvoert, en met veilig podverkeer en toegang tot gevoelige referenties.

Dit artikel introduceert de kernconcepten die uw toepassingen beveiligen in AKS:

- [Beveiliging van hoofdcomponenten](#master-security)
- [Knooppuntbeveiliging](#node-security)
- [Clusterupgrades](#cluster-upgrades)
- [Netwerkbeveiliging](#network-security)
- [Kubernetes Secrets](#kubernetes-secrets)

## <a name="master-security"></a>Hoofdbeveiliging

In AKS maken de Hoofdcomponenten van Kubernetes deel uit van de beheerde service van Microsoft. Elk AKS-cluster heeft zijn eigen single-tenant, dedicated Kubernetes master om de API Server, Scheduler, enz. Deze master wordt beheerd en onderhouden door Microsoft.

Standaard gebruikt de Kubernetes API-server een openbaar IP-adres en een volledig gekwalificeerde domeinnaam (FQDN). U de toegang tot de API-server beheren met kubernetes-toegangsbesturingselementen voor rollen en Azure Active Directory. Zie [Azure AD-integratie met AKS][aks-aad]voor meer informatie.

## <a name="node-security"></a>Knooppuntbeveiliging

AKS-knooppunten zijn Azure virtuele machines die u beheert en onderhoudt. Linux-knooppunten draaien een geoptimaliseerde Ubuntu-distributie met behulp van de Moby-containerruntime. Windows Server-knooppunten (momenteel in preview in AKS) voeren een geoptimaliseerde Windows Server 2019-release uit en gebruiken ook de runtime van de Moby-container. Wanneer een AKS-cluster wordt gemaakt of opgeschaald, worden de knooppunten automatisch geïmplementeerd met de nieuwste beveiligingsupdates en -configuraties van het besturingssysteem.

Het Azure-platform past automatisch beveiligingspatches voor het besturingssysteem toe op Linux-knooppunten op een nachtelijke basis. Als een Besturingssysteem-beveiligingsupdate van Linux een opnieuw opstarten vereist, wordt die reboot niet automatisch uitgevoerd. U handmatig opnieuw opstarten van de Linux-knooppunten, of een gemeenschappelijke aanpak is het gebruik [van Kured][kured], een open-source reboot daemon voor Kubernetes. Kured wordt uitgevoerd als een [DaemonSet][aks-daemonsets] en controleert elk knooppunt op de aanwezigheid van een bestand dat aangeeft dat een reboot vereist is. Reboots worden beheerd in het cluster met behulp van hetzelfde [cordon- en afvoerproces](#cordon-and-drain) als een clusterupgrade.

Voor Windows Server-knooppunten (momenteel in preview in AKS) wordt de laatste updates niet automatisch uitgevoerd en toegepast voor Windows Server-knooppunten (momenteel in preview in AKS). Op een regelmatig schema rond de Windows Update-releasecyclus en uw eigen validatieproces moet u een upgrade uitvoeren op de Windows Server-knooppuntgroep(s) in uw AKS-cluster. Met dit upgradeproces worden knooppunten gemaakt waarmee de nieuwste Windows Server-afbeelding en -patches worden uitgevoerd en worden de oudere knooppunten verwijderd. Zie [Een knooppuntgroep in AKS upgraden][nodepool-upgrade]voor meer informatie over dit proces.

Knooppunten worden geïmplementeerd in een subnet van een privévirtueel netwerk, zonder dat openbare IP-adressen zijn toegewezen. Voor probleemoplossings- en beheerdoeleinden is SSH standaard ingeschakeld. Deze SSH-toegang is alleen beschikbaar via het interne IP-adres.

Om opslag te bieden, gebruiken de knooppunten Azure Managed Disks. Voor de meeste VM-nodeformaten zijn dit Premium-schijven die worden ondersteund door krachtige SSD's. De gegevens die op beheerde schijven zijn opgeslagen, worden automatisch versleuteld in rust binnen het Azure-platform. Om de redundantie te verbeteren, worden deze schijven ook veilig gerepliceerd in het Azure-datacenter.

Kubernetes-omgevingen, in AKS of elders, zijn momenteel niet helemaal veilig voor vijandig multi-tenant gebruik. Extra beveiligingsfuncties zoals *Pod Security Policies* of meer fijnmazige role-based access controls (RBAC) voor knooppunten maken exploits moeilijker. Echter, voor echte beveiliging bij het uitvoeren van vijandige multi-tenant workloads, een hypervisor is het enige niveau van beveiliging die u moet vertrouwen. Het beveiligingsdomein voor Kubernetes wordt het hele cluster, geen individueel knooppunt. Voor dit soort vijandige multi-tenant workloads moet u fysiek geïsoleerde clusters gebruiken. Zie [Aanbevolen procedures voor clusterisolatie in AKS][cluster-isolation]voor meer informatie over manieren om workloads te isoleren,

## <a name="cluster-upgrades"></a>Clusterupgrades

Voor beveiliging en naleving of voor het gebruik van de nieuwste functies biedt Azure hulpprogramma's om de upgrade van een AKS-cluster en -componenten te orkestreren. Deze upgrade orchestration omvat zowel de Kubernetes master en agent componenten. U een [lijst met beschikbare Kubernetes-versies](supported-kubernetes-versions.md) voor uw AKS-cluster bekijken. Als u het upgradeproces wilt starten, geeft u een van deze beschikbare versies op. Azure sluit vervolgens veilig elk AKS-knooppunt af en voert de upgrade uit.

### <a name="cordon-and-drain"></a>Cordon en afvoer

Tijdens het upgradeproces worden AKS-knooppunten individueel uit het cluster gesnoerd, zodat er geen nieuwe pods op zijn gepland. De knooppunten worden vervolgens als volgt afgevoerd en geüpgraded:

- Er wordt een nieuw knooppunt in het knooppuntzwembad geïmplementeerd. Dit knooppunt draait de nieuwste OS-afbeelding en patches.
- Een van de bestaande knooppunten is geïdentificeerd voor een upgrade. Pods op dit knooppunt worden op een elegante manier beëindigd en gepland op de andere knooppunten in de knooppuntgroep.
- Dit bestaande knooppunt wordt verwijderd uit het AKS-cluster.
- Het volgende knooppunt in het cluster wordt met hetzelfde proces afgezet en afgevoerd totdat alle knooppunten met succes zijn vervangen als onderdeel van het upgradeproces.

Zie [Een AKS-cluster upgraden][aks-upgrade-cluster]voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

Voor connectiviteit en beveiliging met on-premises netwerken u uw AKS-cluster implementeren in bestaande virtuele Azure-netwerksubnetten. Deze virtuele netwerken hebben mogelijk een Azure Site-to-Site VPN- of Express Route-verbinding terug naar uw on-premises netwerk. Kubernetes-ingress-controllers kunnen worden gedefinieerd met privé, interne IP-adressen, zodat services alleen toegankelijk zijn via deze interne netwerkverbinding.

### <a name="azure-network-security-groups"></a>Netwerkbeveiligingsgroepen in Azure

Als u de verkeersstroom in virtuele netwerken wilt filteren, gebruikt Azure groepsregels voor netwerkbeveiliging. Deze regels definiëren de bron- en bestemmings-IP-bereiken, poorten en protocollen die toegang tot bronnen zijn toegestaan of geweigerd. Er worden standaardregels gemaakt om TLS-verkeer toe te staan op de Kubernetes API-server. Terwijl u services maakt met load balancers, poorttoewijzingen of invallende routes, wijzigt AKS automatisch de netwerkbeveiligingsgroep om verkeer op de juiste manier te laten stromen.

## <a name="kubernetes-secrets"></a>Kubernetes Secrets

Een Kubernetes *Secret* wordt gebruikt om gevoelige gegevens in pods te injecteren, zoals toegangsreferenties of sleutels. U maakt eerst een geheim met behulp van de Kubernetes API. Wanneer u uw pod of implementatie definieert, kan een specifiek geheim worden aangevraagd. Geheimen worden alleen verstrekt aan knooppunten die een geplande pod die het vereist hebben, en het geheim wordt opgeslagen in *tmpfs,* niet geschreven naar schijf. Wanneer de laatste pod op een knooppunt waarvoor een geheim vereist is, wordt verwijderd uit de tmpfs van het knooppunt. Geheimen worden opgeslagen in een bepaalde naamruimte en zijn alleen toegankelijk via pods binnen dezelfde naamruimte.

Het gebruik van Geheimen vermindert de gevoelige informatie die wordt gedefinieerd in het pod- of serviceYAML-manifest. In plaats daarvan vraagt u het geheim dat is opgeslagen in Kubernetes API Server als onderdeel van uw YAML-manifest. Deze aanpak biedt alleen de specifieke pod toegang tot het geheim. Let op: de raw secret manifest bestanden bevat de geheime gegevens in base64 formaat (zie de [officiële documentatie][secret-risks] voor meer details). Daarom moet dit bestand worden behandeld als gevoelige informatie en nooit worden toegewezen aan bronbeheer.

## <a name="next-steps"></a>Volgende stappen

Zie [Een AKS-cluster upgraden][aks-upgrade-cluster]om aan de slag te gaan met het beveiligen van uw AKS-clusters.

Zie [Aanbevolen procedures voor clusterbeveiliging en -upgrades in AKS][operator-best-practices-cluster-security] en Aanbevolen procedures voor [podbeveiliging in AKS][developer-best-practices-pod-security]voor bijbehorende aanbevolen procedures.

Zie de volgende artikelen voor meer informatie over de belangrijkste Kubernetes- en AKS-concepten:

- [Kubernetes / AKS-clusters en workloads][aks-concepts-clusters-workloads]
- [Kubernetes / AKS-identiteit][aks-concepts-identity]
- [Kubernetes / AKS virtuele netwerken][aks-concepts-network]
- [Kubernetes / AKS-opslag][aks-concepts-storage]
- [Kubernetes / AKS-schaal][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
