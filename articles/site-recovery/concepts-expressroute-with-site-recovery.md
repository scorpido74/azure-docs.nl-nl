---
title: ExpressRoute gebruiken met Azure Site Recovery
description: Beschrijft hoe u Azure ExpressRoute gebruiken met de Azure Site Recovery-service voor herstel en migratie na noodgevallen.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: e4525bdc6165e8e736db5f539c764d25250cb248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258003"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute met Azure-siteherstel

Met Microsoft Azure ExpressRoute kunt u uw on-premises netwerken in de Microsoft Cloud uitbreiden via een persoonlijke verbinding die wordt gefaciliteerd door een connectiviteitsprovider. Met ExpressRoute kunt u verbindingen tot stand brengen met Microsoft Cloud-services, zoals Microsoft Azure, Office 365 en Dynamics 365.

In dit artikel wordt beschreven hoe u Azure ExpressRoute gebruiken met Azure Site Recovery voor herstel en migratie na noodgevallen.

## <a name="expressroute-circuits"></a>ExpressRoute-circuits

Een ExpressRoute-circuit vormt een logische verbinding tussen uw on-premises infrastructuur en Microsoft-cloudservices via een connectiviteitsprovider. U meerdere ExpressRoute-circuits bestellen. Elk circuit kan zich in dezelfde of verschillende regio's bevinden en kan via verschillende connectiviteitsproviders met uw locatie worden verbonden. Lees [hier](../expressroute/expressroute-circuit-peerings.md)meer over ExpressRoute-circuits.

Aan een ExpressRoute-circuit zijn meerdere routeringsdomeinen gekoppeld. Lees [hier](../expressroute/expressroute-circuit-peerings.md#peeringcompare)meer over en vergelijk Routeringsdomeinen van ExpressRoute.

## <a name="on-premises-to-azure-replication-with-expressroute"></a>On-premises naar Azure-replicatie met ExpressRoute

Azure Site Recovery maakt disaster recovery en migratie naar Azure mogelijk voor on-premises [Virtuele Hyper-V-machines,](hyper-v-azure-architecture.md) [Virtuele VMware-machines](vmware-azure-architecture.md)en [fysieke servers](physical-azure-architecture.md). Voor alle on-premises naar Azure-scenario's worden replicatiegegevens verzonden naar en opgeslagen in een Azure Storage-account. Tijdens de replicatie betaalt u geen kosten voor virtuele machines. Wanneer u een failover uitvoert naar Azure, maakt Site Recovery automatisch azure IaaS-virtuele machines.

Siteherstel repliceert gegevens naar een Azure Storage-account of replica Beheerde schijf op het doelAzure-gebied via een openbaar eindpunt. Als u ExpressRoute wilt gebruiken voor replicatieverkeer voor siteherstel, u [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) of een bestaande [openbare peering](../expressroute/about-public-peering.md) gebruiken (afgeschaft voor nieuwe creaties). Microsoft-peering is het aanbevolen routeringsdomein voor replicatie. Houd er rekening mee dat replicatie niet wordt ondersteund via privé-peering.

Controleer of ook aan de [netwerkvereisten](vmware-azure-configuration-server-requirements.md#network-requirements) voor configuratieserver is voldaan. Connectiviteit met specifieke URL's is vereist door Configuration Server voor orkestratie van siteherstelreplicatie. ExpressRoute kan niet worden gebruikt voor deze connectiviteit. 

Als u proxy on-premises gebruikt en ExpressRoute wilt gebruiken voor replicatieverkeer, moet u de proxybypasslijst configureren op de configuratieserver en processervers. Volg de onderstaande stappen:

- Download PsExec tool vanaf [hier](https://aka.ms/PsExec) om toegang te krijgen tot de gebruikerscontext van het systeem.
- Open Internet Explorer in de context van systeemgebruikers door de volgende opdrachtregel psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe" uit te voeren
- Proxy-instellingen toevoegen in IE
- Voeg in de bypasslijst de URL van Azure-opslag *.blob.core.windows.net

Dit zorgt ervoor dat alleen replicatieverkeer via ExpressRoute verloopt terwijl de communicatie via proxy kan verlopen.

Nadat virtuele machines of servers niet zijn overgegaan naar een virtueel Azure-netwerk, u ze openen met behulp van [private peering.](../expressroute/expressroute-circuit-peerings.md#privatepeering) 

Het gecombineerde scenario wordt weergegeven in ![het volgende diagram: On-premises-to-Azure met ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Azure-replicatie naar Azure met ExpressRoute

Azure Site Recovery maakt disaster recovery van [Azure virtuele machines](azure-to-azure-architecture.md)mogelijk. Afhankelijk van of uw Azure virtuele machines [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md)gebruiken, worden replicatiegegevens verzonden naar een Azure Storage-account of replica Managed Disk in het doelAzure-gebied. Hoewel de replicatieeindpunten openbaar zijn, wordt het replicatieverkeer voor Azure VM-replicatie standaard niet door het internet doorkruist, ongeacht in welk Azure-gebied het virtuele bronnetwerk bestaat. U de standaardsysteemroute van Azure voor het voorvoegsel 0.0.0.0/0 overschrijven met een [aangepaste route](../virtual-network/virtual-networks-udr-overview.md#custom-routes) en VM-verkeer omleiden naar een on-premises virtuele netwerk -toestel (NVA), maar deze configuratie wordt niet aanbevolen voor siteherstelreplicatie. Als u aangepaste routes gebruikt, moet u [een eindpunt voor virtuele netwerkservice maken](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) in uw virtuele netwerk voor 'Opslag' zodat het replicatieverkeer de Azure-grens niet verlaat.

Voor Azure VM-noodherstel is ExpressRoute standaard niet vereist voor replicatie. Nadat virtuele machines niet zijn overgelaten aan de doelregio Azure, hebt u er toegang toe met behulp van [private peering.](../expressroute/expressroute-circuit-peerings.md#privatepeering) Houd er rekening mee dat de prijzen voor gegevensoverdracht van toepassing zijn, ongeacht de wijze van gegevensreplicatie in Azure-regio's.

Als u ExpressRoute al gebruikt om verbinding te maken vanuit uw on-premises datacenter met de Azure VM's in het brongebied, u de ExpressRoute-connectiviteit herstellen in de failoverdoelregio. U hetzelfde ExpressRoute-circuit gebruiken om verbinding te maken met het doelgebied via een nieuwe virtuele netwerkverbinding of een apart ExpressRoute-circuit en -verbinding te gebruiken voor noodherstel. De verschillende mogelijke scenario's worden [hier](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)beschreven.

U virtuele Azure-machines repliceren naar elke Azure-regio binnen hetzelfde geografische cluster als [hier](../site-recovery/azure-to-azure-support-matrix.md#region-support)wordt beschreven. Als de gekozen doelazure-regio zich niet binnen dezelfde geopolitieke regio bevindt als de bron, moet u mogelijk ExpressRoute Premium inschakelen. Kijk voor meer informatie op [ExpressRoute-locaties](../expressroute/expressroute-locations.md) en [ExpressRoute-prijzen.](https://azure.microsoft.com/pricing/details/expressroute/)

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [ExpressRoute-circuits](../expressroute/expressroute-circuit-peerings.md).
- Meer informatie over [routeringsdomeinen van ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Meer informatie over [ExpressRoute-locaties](../expressroute/expressroute-locations.md).
- Meer informatie over noodherstel van [Azure virtuele machines met ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
