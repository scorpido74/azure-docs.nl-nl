---
title: Inleiding
description: Meer informatie over de functies en voor delen van de Azure VMware-oplossing (AVS) voor het implementeren en beheren van op VMware gebaseerde workloads in Azure.
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: e227b6c7c2f25fbf62f11c17dc62293e4fbd5267
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740330"
---
# <a name="what-is-azure-vmware-solution-avs-preview"></a>Wat is de preview-versie van Azure VMware Solution (AVS)?

Met de Azure VMware-oplossing (AVS) beschikt u over persoonlijke Clouds in Azure. De privécloud bevat vSphere-clusters, gebouwd op basis van een toegewezen bare-metal Azure-infra structuur. U kunt particuliere cloud clusters schalen van 3 naar 16 hosts, met de mogelijkheid om meerdere clusters in één privécloud te hebben. Alle persoonlijke Clouds worden ingericht met vCenter Server, VSAN, vSphere en NSX-T. U kunt workloads migreren uit uw on-premises omgevingen, nieuwe virtuele machines maken of implementeren en Azure-Services gebruiken vanuit uw persoonlijke Clouds.

AVS is een VMware-gevalideerde oplossing met een voortdurende validatie en het testen van verbeteringen en upgrades. De infra structuur en software van de privécloud worden beheerd en beheerd door micro soft, zodat u zich kunt richten op het ontwikkelen en uitvoeren van werk belastingen in uw privé-Clouds.

In het volgende diagram ziet u de belending tussen persoonlijke Clouds en VNets in azure, Azure-Services en on-premises omgevingen. Netwerk toegang van particuliere Clouds naar Azure-Services of VNets biedt SLA-aangedreven integratie van Azure-service-eind punten. Persoonlijke Cloud toegang vanuit on-premises omgevingen maakt gebruik van ExpressRoute Global Reach voor een privé-en beveiligde verbinding.

![Afbeelding van een persoonlijke Cloud-aangrenzing in Azure en on-premises](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Hosts, clusters en privé-Clouds

Persoonlijke Clouds en clusters van AVS zijn gebouwd op basis van een bare-metal, Hyper-geconvergeerde Azure-infrastructuur host. De high-end-hosts hebben 576 GB RAM-geheugen en Dual Intel 18 Core-processors van 2,3 GHz. De gast host heeft twee vSAN-diskgroups met een totale vSAN-capaciteits categorie van 15,36 TB (SSD) en een NVMe-vSAN-cache laag van 3,2 TB.

Nieuwe persoonlijke Clouds worden geïmplementeerd via de Azure Portal of Azure CLI.

## <a name="networking"></a>Netwerken

Wanneer een privécloud wordt geïmplementeerd, worden particuliere netwerken voor beheer, inrichting en vMotion gemaakt. Deze particuliere netwerken worden gebruikt voor toegang tot vCenter en NSX-T-beheer, en voor de installatie van de virtuele machine vMotion of implementatie. Alle particuliere netwerken zijn toegankelijk vanuit een VNet in azure of vanuit on-premises omgevingen. ExpressRoute Global Reach wordt gebruikt om persoonlijke Clouds te verbinden met on-premises omgevingen en deze verbinding vereist een VNet met een ExpressRoute-circuit in uw abonnement.

De toegang tot internet en Azure-Services worden ingericht wanneer er een privécloud wordt geïmplementeerd. De toegang wordt verschaft zodat Vm's op productie werkbelasting netwerken Azure-of op internet gebaseerde services kunnen gebruiken. Internet toegang is standaard uitgeschakeld voor nieuwe persoonlijke Clouds en kan op elk gewenst moment worden ingeschakeld of uitgeschakeld.

Zie het artikel over [netwerk concepten](concepts-networking.md) voor meer informatie over netwerk-en interconnectiviteit.

## <a name="access-and-security"></a>Toegang en beveiliging

Voor verbeterde beveiliging gebruiken AVS persoonlijke Clouds vSphere toegangs beheer op basis van rollen. vSphere SSO LDAP-mogelijkheden kunnen worden geïntegreerd met Azure Active Directory. Zie het artikel [toegangs-en identiteits concepten](concepts-identity.md) voor meer informatie over identiteit en bevoegdheden.

vSAN gegevens-at-rest-versleuteling is standaard ingeschakeld en wordt gebruikt om de beveiliging van vSAN Data Store te bieden. Dit wordt gedetailleerd beschreven in het artikel over [opslag concepten](concepts-storage.md) .

## <a name="host-and-software-lifecycle-maintenance"></a>Onderhoud van host-en software levenscyclus

Reguliere upgrades van de AVS-privécloud en VMware-software zorgen ervoor dat de nieuwste beveiligings-, stabiliteits-en functie sets worden uitgevoerd in uw privé-Clouds. Meer informatie over platform onderhoud en upgrades vindt u in het artikel over de [upgrade-concepten](concepts-upgrades.md) .

## <a name="monitoring-your-private-cloud"></a>De privécloud bewaken

U kunt [Logboeken in azure monitor](../azure-monitor/overview.md) gebruiken om logboeken te verzamelen op uw virtuele machines die worden uitgevoerd in de privécloud van uw AVS. U kunt [de MMA-agent downloaden en installeren](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) op virtuele Linux-en Windows-machines die worden uitgevoerd in uw AVS-persoonlijke Clouds, met behulp van dezelfde query's die u op uw on-premises vm's uitvoert. U kunt dezelfde query's uitvoeren die normaal gesp roken alleen op uw virtuele machines worden uitgevoerd. Zie [query's schrijven](../azure-monitor/log-query/log-query-overview.md#how-can-i-learn-how-to-write-queries)voor meer informatie over het maken van query's.

## <a name="next-steps"></a>Volgende stappen

De volgende stap is om de belangrijkste [persoonlijke Cloud-en cluster concepten](concepts-private-clouds-clusters.md)te leren.

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
