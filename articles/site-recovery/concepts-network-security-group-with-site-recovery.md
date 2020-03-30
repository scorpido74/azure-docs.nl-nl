---
title: Netwerkbeveiligingsgroepen met Azure-siteherstel | Microsoft Documenten
description: Beschrijft hoe u netwerkbeveiligingsgroepen gebruiken met Azure Site Recovery voor herstel en migratie na noodgevallen
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: eb5ba99133f5726c44164b0ba45b7ab5d94e292f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292366"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Netwerkbeveiligingsgroepen met Azure-siteherstel

Netwerkbeveiligingsgroepen worden gebruikt om netwerkverkeer te beperken tot bronnen in een virtueel netwerk. Een [Network Security Group (NSG)](../virtual-network/security-overview.md#network-security-groups) bevat een lijst met beveiligingsregels die binnenkomend of uitgaand netwerkverkeer toestaan of weigeren op basis van bron- of bestemmings-IP-adres, poort en protocol.

Onder het implementatiemodel ResourceManager kunnen NSG's worden gekoppeld aan subnetten of afzonderlijke netwerkinterfaces. Wanneer een NSG is gekoppeld aan een subnet, zijn de regels van toepassing op alle resources die zijn verbonden met het subnet. Het verkeer kan verder worden beperkt door ook een NSG te koppelen aan individuele netwerkinterfaces binnen een subnet dat al een bijbehorende NSG heeft.

In dit artikel wordt beschreven hoe u netwerkbeveiligingsgroepen gebruiken met Azure Site Recovery.

## <a name="using-network-security-groups"></a>Netwerkbeveiligingsgroepen gebruiken

Een individueel subnet kan nul of één bijbehorende NSG hebben. Een afzonderlijke netwerkinterface kan ook nul of één bijbehorende NSG hebben. U dus effectief een dubbele verkeersbeperking voor een virtuele machine hebben door eerst een NSG aan een subnet te koppelen en vervolgens een andere NSG aan de netwerkinterface van de VM. De toepassing van NSG-regels is in dit geval afhankelijk van de rijrichting en de prioriteit van de toegepaste beveiligingsregels.

Overweeg een eenvoudig voorbeeld met één virtuele machine als volgt:
-    De virtuele machine wordt in het **Contoso Subnet**geplaatst.
-    **Contoso Subnet** is gekoppeld aan **Subnet NSG**.
-    De VM-netwerkinterface is bovendien gekoppeld aan **VM NSG.**

![NSG met siteherstel](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

In dit voorbeeld wordt voor binnenkomend verkeer het subnet NSG eerst geëvalueerd. Verkeer dat via Subnet NSG is toegestaan, wordt vervolgens geëvalueerd door VM NSG. Het omgekeerde is van toepassing op uitgaand verkeer, waarbij VM NSG eerst wordt geëvalueerd. Alle verkeer toegestaan via VM NSG wordt vervolgens geëvalueerd door Subnet NSG.

Dit maakt het mogelijk voor gedetailleerde beveiligingsregel toepassing. U bijvoorbeeld binnenkomende internettoegang toestaan tot een paar vm's voor toepassingen (zoals frontend VM's) onder een subnet, maar de inkomende internettoegang beperken tot andere VM's (zoals database- en andere backend-VM's). In dit geval u een mildere regel hebben op het subnet NSG, waardoor internetverkeer wordt toegestaan en de toegang tot specifieke VM's wordt beperkt door de toegang op VM NSG te weigeren. Hetzelfde kan worden toegepast voor uitgaand verkeer.

Controleer bij het instellen van dergelijke NSG-configuraties of de juiste prioriteiten worden toegepast op de [beveiligingsregels.](../virtual-network/security-overview.md#security-rules) Regels worden verwerkt in volgorde van prioriteit, waarbij lagere getallen worden verwerkt vóór hogere getallen omdat lagere getallen een hogere prioriteit hebben. Zodra het verkeer overeenkomt met een regel, wordt de verwerking beëindigd. Daardoor worden regels met een lagere prioriteit (een hoger getal) die dezelfde kenmerken hebben als regels met een hogere prioriteit, niet verwerkt.

U ben er mogelijk niet altijd van op de hoogte wanneer netwerkbeveiligingsgroepen worden toegepast op zowel een netwerkinterface als een subnet. U de geaggregeerde regels die op een netwerkinterface worden toegepast controleren door de [effectieve beveiligingsregels](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) voor een netwerkinterface te bekijken. U de [IP-stroomverificatiemogelijkheid](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) ook gebruiken in [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) om te bepalen of communicatie is toegestaan van of vanuit een netwerkinterface. Deze functie vertelt u of communicatie is toegestaan en welke netwerkbeveiligingsregel verkeer toestaat of weigert.

## <a name="on-premises-to-azure-replication-with-nsg"></a>On-premises naar Azure-replicatie met NSG

Azure Site Recovery maakt disaster recovery en migratie naar Azure mogelijk voor on-premises [Virtuele Hyper-V-machines,](hyper-v-azure-architecture.md) [Virtuele VMware-machines](vmware-azure-architecture.md)en [fysieke servers](physical-azure-architecture.md). Voor alle on-premises naar Azure-scenario's worden replicatiegegevens verzonden naar en opgeslagen in een Azure Storage-account. Tijdens de replicatie betaalt u geen kosten voor virtuele machines. Wanneer u een failover uitvoert naar Azure, maakt Site Recovery automatisch azure IaaS-virtuele machines.

Zodra VM's zijn gemaakt na een failover naar Azure, kunnen NSG's worden gebruikt om netwerkverkeer te beperken tot het virtuele netwerk en VM's. Site Recovery maakt geen NSGs als onderdeel van de failover-bewerking. We raden u aan de vereiste Azure NSGs te maken voordat u een failover start. Vervolgens u NSGs automatisch koppelen aan mislukte VM's tijdens failover, met behulp van automatiseringsscripts met de krachtige [herstelplannen van](site-recovery-create-recovery-plans.md)Site Recovery.

Als de VM-configuratie na failover bijvoorbeeld vergelijkbaar is met het [voorbeeldscenario](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) dat hierboven wordt beschreven:
-    U **Contoso VNet** en **Contoso Subnet** maken als onderdeel van DR-planning op de doelazure-regio.
-    U ook zowel **Subnet NSG** als **VM NSG** maken en configureren als onderdeel van dezelfde DR-planning.
-    **Subnet NSG** kan dan direct worden gekoppeld aan **Contoso Subnet,** omdat zowel de NSG als het subnet al beschikbaar zijn.
-    **VM NSG** kan tijdens failover aan VM's worden gekoppeld met herstelplannen.

Zodra de NSG's zijn gemaakt en geconfigureerd, raden we u aan een [testfailover](site-recovery-test-failover-to-azure.md) uit te voeren om gescripte NSG-koppelingen en VM-connectiviteit na het failoveren te verifiëren.

## <a name="azure-to-azure-replication-with-nsg"></a>Azure naar Azure-replicatie met NSG

Azure Site Recovery maakt disaster recovery van [Azure virtuele machines](azure-to-azure-architecture.md)mogelijk. Wanneer replicatie voor Azure VM's wordt ingeschakeld, kan Site Recovery de virtuele replicanetwerken (inclusief subnetten en gateway-subnetten) op het doelgebied maken en de vereiste toewijzingen maken tussen de bron- en doelvirtuele netwerken. U ook vooraf de doelnetwerken en subnetten maken en hetzelfde gebruiken terwijl u replicatie inschakelt. Siteherstel maakt geen VM's op het doelAzure-gebied voordat [deze mislukt.](azure-to-azure-tutorial-failover-failback.md)

Controleer voor Azure VM-replicatie of de NSG-regels voor het bronazure-gebied [uitgaande connectiviteit](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) voor replicatieverkeer toestaan. U deze vereiste regels ook testen en verifiëren via dit [voorbeeld NSG-configuratie.](azure-to-azure-about-networking.md#example-nsg-configuration)

Site recovery maakt of repliceert geen SGS als onderdeel van de failover-bewerking. We raden u aan de vereiste NSG's te maken op de beoogde Azure-regio voordat u een failover start. Vervolgens u NSGs automatisch koppelen aan mislukte VM's tijdens failover, met behulp van automatiseringsscripts met de krachtige [herstelplannen van](site-recovery-create-recovery-plans.md)Site Recovery.

Rekening houdend met het [voorbeeldscenario](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) dat eerder is beschreven:
-    Siteherstel kan replica's van **Contoso VNet** en **Contoso Subnet** maken op het doelazure-gebied wanneer replicatie is ingeschakeld voor de VM.
-    U de gewenste replica's van **Subnet NSG** en **VM NSG** (met de naam bijvoorbeeld **Target Subnet NSG** en **Target VM NSG**) op de doelazure-regio maken, zodat er aanvullende regels voor het doelgebied nodig zijn.
-    **Target Subnet NSG** kan dan direct worden gekoppeld aan het subnet van het doelgebied, omdat zowel de NSG als het subnet al beschikbaar zijn.
-    **Target VM NSG** kan tijdens failover aan VM's worden gekoppeld met herstelplannen.

Zodra de NSG's zijn gemaakt en geconfigureerd, raden we u aan een [testfailover](azure-to-azure-tutorial-dr-drill.md) uit te voeren om gescripte NSG-koppelingen en VM-connectiviteit na het failoveren te verifiëren.

## <a name="next-steps"></a>Volgende stappen
-    Meer informatie over [netwerkbeveiligingsgroepen](../virtual-network/security-overview.md#network-security-groups).
-    Meer informatie over [NSG-beveiligingsregels](../virtual-network/security-overview.md#security-rules).
-    Meer informatie over [effectieve beveiligingsregels](../virtual-network/diagnose-network-traffic-filter-problem.md) voor een NSG.
-    Meer informatie over [herstelplannen](site-recovery-create-recovery-plans.md) om failover van toepassingen te automatiseren.
