---
title: Inleiding
description: Meer informatie over de functies en voordelen van Azure VMware Solution voor het implementeren en beheren van VMware-workloads in Azure.
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: 3d6801a6cdec7600cc6003711a08e6af8c86483e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005051"
---
# <a name="what-is-azure-vmware-solution-preview"></a>Wat is Azure VMware Solution Preview?

Met Azure VMware Solution beschikt u over privéclouds in Azure. De privécloud bevat vSphere-clusters, gebouwd op basis van een toegewezen bare-metal Azure-infrastructuur. U kunt privécloudclusters schalen van 3 naar 16 hosts, met de mogelijkheid om meerdere clusters in één privécloud te hebben. Alle privéclouds zijn ingericht met vCenter Server, vSAN, vSphere en NSX-T. U kunt workloads migreren uit uw on-premises omgevingen, nieuwe virtuele machines maken of implementeren en Azure-services gebruiken vanuit uw privéclouds.

Azure VMware Solution is een VMware-gevalideerde oplossing met voortdurende validatie van en testen op verbeteringen en upgrades. De infrastructuur en software van de privécloud worden beheerd en onderhouden door Microsoft, zodat u zich kunt richten op het ontwikkelen en uitvoeren van workloads in uw privéclouds.

In het volgende diagram wordt de verdeling weergegeven tussen privéclouds en VNets in Azure, Azure-services en on-premises omgevingen. Netwerktoegang van privéclouds naar Azure-services of VNets biedt SLA-gestuurde integratie van Azure-service-eindpunten. Privécloud-toegang vanuit on-premises omgevingen maakt gebruik van ExpressRoute Global Reach voor een privé- en beveiligde verbinding.

![Afbeelding van nabijheid van Azure VMware Solution-privécloud ten opzichte van Azure en on-premises](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Hosts, clusters en privéclouds

Azure VMware Solution-privéclouds en -clusters zijn opgebouwd uit een bare-metal, hypergeconvergeerde Azure-infrastructuurhost. De high-end-hosts hebben 576 GB RAM en dubbele Intel 18-core, 2,3GHz-processors. De HE hosts hebben twee vSAN-schijfgroepen met een totale raw vSAN-capaciteitslaag van 15,36 TB (SSD) en een cachelaag van 3,2 TB (NVMe).

Nieuwe privéclouds worden geïmplementeerd via Azure Portal of Azure CLI.

## <a name="networking"></a>Netwerken

Wanneer een privécloud wordt geïmplementeerd, worden privénetwerken voor beheer, provisioning en vMotion gemaakt. Deze privénetwerken worden gebruikt voor toegang tot vCenter en NSX-T Manager en voor vMotion of implementatie van virtuele machines. Alle privénetwerken zijn toegankelijk via een VNet in Azure of vanuit on-premises omgevingen. ExpressRoute Global Reach wordt gebruikt om privéclouds te verbinden met on-premises omgevingen en voor deze verbinding is een VNet met een ExpressRoute-circuit in uw abonnement vereist.

Toegang tot internet- en Azure-services wordt ingericht wanneer een privécloud wordt geïmplementeerd. De toegang wordt verschaft, zodat VM's op productieworkloadnetwerken Azure- of internetservices kunnen gebruiken. Internettoegang is standaard uitgeschakeld voor nieuwe privéclouds en kan op elk gewenst moment worden ingeschakeld of uitgeschakeld.

Voor meer informatie over netwerken en interconnectiviteit raadpleegt u het artikel [Networking-concepten](concepts-networking.md).

## <a name="access-and-security"></a>Toegang en beveiliging

Voor verbeterde beveiliging maken Azure VMware Solution-privéclouds gebruik van op vSphere-rollen gebaseerd toegangsbeheer. vSphere SSO LDAP-mogelijkheden kunnen worden geïntegreerd met Azure Active Directory. Zie het artikel [Toegang en identiteitsconcepten](concepts-identity.md) voor meer informatie over identiteit en bevoegdheden.

vSAN data-at-rest encryptie is standaard ingeschakeld en wordt gebruikt om vSAN-datastorebeveiliging te bieden. Dit wordt gedetailleerd beschreven in het artikel [Opslagconcepten](concepts-storage.md).

## <a name="host-and-software-lifecycle-maintenance"></a>Onderhoud van de levenscyclus van host en software

Regelmatige upgrades van de Azure VMware Solution-privécloud en VMware-software zorgen ervoor dat de nieuwste beveiligings-, stabiliteits- en functiesets worden uitgevoerd in uw privéclouds. Meer informatie over platformonderhoud en -upgrades vindt u in het artikel [Upgradeconcepten](concepts-upgrades.md).

## <a name="monitoring-your-private-cloud"></a>De privécloud bewaken

U kunt [Logboeken in Azure Monitor](../azure-monitor/overview.md) gebruiken om logboeken te verzamelen op uw virtuele machines die worden uitgevoerd in de privécloud van uw Azure VMware Solution. U kunt [de MMA-agent downloaden en installeren](../azure-monitor/platform/log-analytics-agent.md#installation-options) op virtuele Linux- en Windows-machines die worden uitgevoerd in uw Azure VMware Solution-privéclouds, met behulp van dezelfde query's die u op uw on-premises VM's uitvoert. U kunt dezelfde query's uitvoeren die normaal gesproken alleen op uw virtuele machines worden uitgevoerd. Zie [query's schrijven](../azure-monitor/log-query/log-query-overview.md#how-can-i-learn-how-to-write-queries)voor meer informatie over het maken van query's.

## <a name="next-steps"></a>Volgende stappen

De volgende stap is het leren van de belangrijkste [privécloud- en clusterconcepten](concepts-private-clouds-clusters.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
