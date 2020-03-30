---
title: Architectuur-Hyper-V-noodherstel naar een secundaire site met Azure Site Recovery
description: In dit artikel vindt u een overzicht van de architectuur voor noodherstel van on-premises Hyper-V Vm's naar een secundaire VMM-site van het systeemcentrum met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 3e81e353d2912f56a932ce118a0424e45e758df7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133014"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Architectuur - Hyper-V-replicatie naar een secundaire site

In dit artikel worden de onderdelen en processen beschreven die zijn betrokken bij het repliceren van on-premises Hyper-V-VM's (virtuele machines) in System Center VMM-clouds (Virtual Machine Manager) naar een secundaire VMM-site met behulp van de [Azure Site Recovery](site-recovery-overview.md)-service in Azure Portal.
a

## <a name="architectural-components"></a>Architectuuronderdelen

De volgende tabel en afbeelding bieden een weergave op hoog niveau van de componenten die worden gebruikt voor Hyper-V-replicatie naar een secundaire site.

**Component** | **Vereiste** | **Details**
--- | --- | ---
**Azure** | Azure-abonnement | U maakt een Recovery Services-kluis in het Azure-abonnement om de replicatie tussen VMM-locaties te organiseren en te beheren.
**VMM-server** | U hebt een primaire en secundaire VMM-locatie nodig. | Aanbevolen is een VMM-beheerserver op de primaire site en een op de secundaire site.
**Hyper-V-server** |  U hebt een of meer Hyper-V-hostservers nodig die zich bevinden in de primaire en secundaire VMM-clouds. | Gegevens worden tussen de primaire en secundaire Hyper-V-hostservers via het LAN of VPN gerepliceerd met behulp van Kerberos of verificatie via certificaat.  
**Virtuele Hyper-V-machines** | Op Hyper-V-hostserver. | De bronhostserver moet ten minste één VM hebben die u wilt repliceren.

**On-premises naar on-premises architectuur**

![On-premises naar on-premises](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Replicatieproces

1. Wanneer de eerste replicatie wordt geactiveerd, wordt een [Hyper-V VM-momentopname](https://technet.microsoft.com/library/dd560637.aspx) gemaakt.
2. Virtuele harde schijven op de VM worden één voor één gerepliceerd naar de secundaire locatie.
3. Als schijfwijzigingen optreden terwijl de eerste replicatie wordt uitgevoerd, worden de wijzigingen bij de Hyper-V Replica Replication Tracker bijhoudt als Hyper-V-replicatielogboeken (.hrl). Deze logboekbestanden bevinden zich in dezelfde map als de schijven. Elke schijf heeft een bijbehorend .hrl-bestand dat naar de secundaire locatie wordt verzonden. De momentopname- en logboekbestanden nemen schijfbronnen in beslag terwijl de eerste replicatie wordt uitgevoerd.
4. Wanneer de eerste replicatie is voltooid, wordt de VM-momentopname verwijderd en wordt deltareplicatie gestart.
5. Verschillen in het logboek worden gesynchroniseerd en samengevoegd op de bovenliggende schijf.


## <a name="failover-and-failback-process"></a>Failover- en failbackproces

- U kunt een failover van één machine uitvoeren of herstelplannen maken om de failover van meerdere virtuele machines te coördineren.
- U kunt een geplande of niet-geplande failover uitvoeren tussen on-premises sites. Als u een geplande failover uitvoert, worden de virtuele bronmachines afgesloten om gegevensverlies te voorkomen.
    - Als u een ongeplande failover uitvoert naar een secundaire site, worden de failovermachines op de secundaire locatie niet beschermd.
    - Als u een geplande failover hebt uitgevoerd, zijn de machines op de secundaire locatie na de failover beveiligd.
- Nadat de eerste failover wordt uitgevoerd, verbindt u deze om toegang te krijgen tot de werkbelasting van de replica-VM.
- Wanneer de primaire locatie weer beschikbaar is, u weer mislukken.
    - U start omgekeerde replicatie om te beginnen met repliceren van de secundaire site naar de primaire site. Omgekeerde replicatie geeft de virtuele machines een beveiligde status, maar het secundaire datacenter is nog steeds de actieve locatie.
    - Als u de primaire site weer de actieve locatie wilt maken, start u een geplande failover van de secundaire site naar de primaire site, gevolgd door nog een omgekeerde replicatie.



## <a name="next-steps"></a>Volgende stappen


Volg [deze zelfstudie](hyper-v-vmm-disaster-recovery.md) om Hyper-V-replicatie tussen VMM-clouds in te schakelen.
