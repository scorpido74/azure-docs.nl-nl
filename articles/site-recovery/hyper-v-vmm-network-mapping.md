---
title: Over Hyper-V (met VMM) netwerktoewijzing met Site Recovery
description: Beschrijft hoe u netwerktoewijzing instelt voor noodherstel van Hyper V VM's (beheerd in VMM-clouds) naar Azure, met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 6b68b4c943ec96620427978c2309f27e1fb1f217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082562"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Netwerktoewijzing voorbereiden voor het herstel van Hyper VM-noodvoor spoednaar Azure


In dit artikel u inzicht krijgen in en voorbereiden op netwerktoewijzing wanneer u Hyper-V VM's in VMM-clouds (System Center Virtual Machine Manager) nabootst naar Azure of naar een secundaire site met behulp van de [Azure Site Recovery-service.](site-recovery-overview.md)


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Netwerktoewijzing voorbereiden voor replicatie naar Azure

Wanneer u re-replica's naar Azure, netwerktoewijzing smaps tussen VM-netwerken op een bron VMM-server, en doel Azure virtuele netwerken. Bij toewijzing gebeurt het volgende:
-  **Netwerkverbinding**:Zorgt ervoor dat gerepliceerde Azure VM's zijn verbonden met het toegewezen netwerk. Alle machines die op hetzelfde netwerk uitvallen, kunnen verbinding met elkaar maken, zelfs als ze niet zijn geslaagd in verschillende herstelplannen.
- **Netwerkgateway**: als een netwerkgateway is ingesteld op het doel Azure-netwerk, kunnen VM's verbinding maken met andere on-premises virtuele machines.

Netwerktoewijzing werkt als volgt:

- U brengt een vmm-vm-netwerk van een bron toewijzen aan een virtueel Azure-netwerk.
- Na failover Azure VM's in het bronnetwerk worden verbonden met het toegewezen virtuele doelnetwerk.
- Nieuwe VM's die zijn toegevoegd aan het vm-netwerk van de bron, zijn bij replicatie verbonden met het toegewezen Azure-netwerk.
- Als het doelnetwerk meerdere subnetten bevat en een van deze subnetten dezelfde naam heeft als het subnet waarin de virtuele bronmachine zich bevindt, wordt de gerepliceerde virtuele machine na een failover verbonden met dat doelsubnet.
- Als er geen doelsubnet met een overeenkomende naam bestaat, wordt de virtuele machine verbonden met het eerste subnet in het netwerk.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Netwerktoewijzing voorbereiden voor replicatie naar een secundaire site

Wanneer u re-replica's naar een secundaire site, netwerk toewijzing kaarten tussen VM-netwerken op een bron VMM-server, en VM-netwerken op een doel VMM-server. Bij toewijzing gebeurt het volgende:

- **Netwerkverbinding**— Verbindt VM's met geschikte netwerken na failover. De replica-VM wordt verbonden met het doelnetwerk dat is toegewezen aan het bronnetwerk.
- **Optimale VM-plaatsing**—Plaatst de replica VM's optimaal op Hyper-V-hostservers. Replica VM's worden geplaatst op hosts die toegang hebben tot de toegewezen VM-netwerken.
- **Geen netwerktoewijzing**:Als u geen netwerktoewijzing configureert, worden replicaVM's na een failover niet meer verbonden met VM-netwerken.

Netwerktoewijzing werkt als volgt:

- Netwerktoewijzing kan worden geconfigureerd tussen VM-netwerken op twee VMM-servers of op één VMM-server als twee sites door dezelfde server worden beheerd.
- Wanneer toewijzing correct is geconfigureerd en replicatie is ingeschakeld, wordt een VM op de primaire locatie verbonden met een netwerk en wordt de replica op de doellocatie verbonden met het toegewezen netwerk.
- Wanneer u een doel-VM-netwerk selecteert tijdens netwerktoewijzing in Site Recovery, worden de VMM-bronclouds die het bron-VM-netwerk gebruiken, weergegeven, samen met de beschikbare doelVM-netwerken op de doelclouds die worden gebruikt voor bescherming.
- Als het doelnetwerk meerdere subnetten heeft en een van die subnetten dezelfde naam heeft als het subnet waarop de virtuele bronmachine zich bevindt, wordt de replica-VM na failover verbonden met dat doelsubnet. Als er geen doelsubnet met een overeenkomende naam is, wordt de VM verbonden met het eerste subnet in het netwerk.

## <a name="example"></a>Voorbeeld

Hier is een voorbeeld om dit mechanisme te illustreren. Laten we een organisatie nemen met twee locaties in New York en Chicago.

**Locatie** | **VMM-server** | **VM-netwerken** | **Toegewezen aan**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | In kaart gebracht naar VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Niet toegewezen
Chicago | VMM-Chicago| VMNetwork1-Chicago | Toegewezen aan VMNetwork1-NewYork
 | | VMNetwork2-Chicago | Niet toegewezen

In dit voorbeeld:

- Wanneer een replica-VM wordt gemaakt voor elke VM die is verbonden met VMNetwork1-NewYork, wordt deze verbonden met VMNetwork1-Chicago.
- Wanneer een replica-VM wordt gemaakt voor VMNetwork2-NewYork of VMNetwork2-Chicago, wordt deze niet verbonden met een netwerk.

Zo worden VMM-clouds ingesteld in onze voorbeeldorganisatie en de logische netwerken die zijn gekoppeld aan de clouds.

### <a name="cloud-protection-settings"></a>Instellingen voor cloudbeveiliging

**Beveiligde cloud** | **Cloud beveiligen** | **Logisch netwerk (New York)**  
---|---|---
GoldCloud1 (GoldCloud1) | GoldCloud2 (GoldCloud2) |
SilverCloud1 (SilverCloud1)| SilverCloud2 (SilverCloud2) |
GoldCloud2 (GoldCloud2) | <p>N.v.t.</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 (SilverCloud2) | <p>N.v.t.</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Logische en VM-netwerkinstellingen

**Locatie** | **Logisch netwerk** | **Gekoppeld VM-netwerk**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Netwerkinstellingen targeten

Op basis van deze instellingen toont de volgende tabel de keuzes die beschikbaar zijn wanneer u het vm-netwerk van het doel selecteert.

**Selecteren** | **Beveiligde cloud** | **Cloud beveiligen** | **Doelnetwerk beschikbaar**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 (SilverCloud1) | SilverCloud2 (SilverCloud2) | Beschikbaar
 | GoldCloud1 (GoldCloud1) | GoldCloud2 (GoldCloud2) | Beschikbaar
VMNetwork2-Chicago | SilverCloud1 (SilverCloud1) | SilverCloud2 (SilverCloud2) | Niet beschikbaar
 | GoldCloud1 (GoldCloud1) | GoldCloud2 (GoldCloud2) | Beschikbaar


Als het doelnetwerk meerdere subnetten heeft en een van die subnetten dezelfde naam heeft als het subnet waarop de virtuele bronmachine zich bevindt, wordt de virtuele replica-machine na failover verbonden met dat doelsubnet. Als er geen doelsubnet met een overeenkomende naam bestaat, wordt de virtuele machine verbonden met het eerste subnet in het netwerk.


### <a name="failback-behavior"></a>Failbackgedrag

Om te zien wat er gebeurt in het geval van failback (reverse replication), laten we aannemen dat VMNetwork1-NewYork is toegewezen aan VMNetwork1-Chicago, met de volgende instellingen.


**Vm** | **Verbonden met VM-netwerk**
---|---
VM1 | VMNetwork1-netwerk
VM2 (replica van VM1) | VMNetwork1-Chicago

Met deze instellingen, laten we eens kijken wat er gebeurt in een paar mogelijke scenario's.

**Scenario** | **Resultaat**
---|---
Geen wijziging in de netwerkeigenschappen van VM-2 na failover. | VM-1 blijft verbonden met het bronnetwerk.
Netwerkeigenschappen van VM-2 worden gewijzigd na failover en worden losgekoppeld. | VM-1 is losgekoppeld.
Netwerkeigenschappen van VM-2 worden na failover gewijzigd en zijn verbonden met VMNetwork2-Chicago. | Als VMNetwork2-Chicago niet in kaart wordt gebracht, wordt de verbinding tussen VM-1 en 201 000 verbroken.
Netwerktoewijzing van VMNetwork1-Chicago is gewijzigd. | VM-1 wordt aangesloten op het netwerk dat nu is toegewezen aan VMNetwork1-Chicago.



## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over](hyper-v-vmm-networking.md) IP-adressering na failover naar een secundaire VMM-site.
- [Meer informatie over](concepts-on-premises-to-azure-networking.md) IP-adressering na failover naar Azure.
