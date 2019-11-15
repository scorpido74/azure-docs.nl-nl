---
title: Architectuur van herstel na nood geval voor fysieke server in Azure Site Recovery
description: Dit artikel bevat een overzicht van de onderdelen en architectuur die worden gebruikt tijdens nood herstel van on-premises fysieke servers naar Azure met de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 23e8e4f9a092e871e62da27c8bf0c58a3bb8eb5b
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084690"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Architectuur van fysieke server naar Azure voor herstel na nood gevallen

In dit artikel worden de architectuur en processen beschreven die worden gebruikt bij replicatie, failover en herstel van fysieke Windows-en Linux-servers tussen een on-premises site en Azure, met behulp van de [Azure site Recovery](site-recovery-overview.md) -service.


## <a name="architectural-components"></a>Architectuuronderdelen

De volgende tabel en afbeelding bieden een weer gave op hoog niveau van de onderdelen die worden gebruikt voor de replicatie van de fysieke server naar Azure.  

**Onderdeel** | **Vereiste** | **Details**
--- | --- | ---
**Azure** | Een Azure-abonnement en een Azure-netwerk. | Gerepliceerde gegevens van on-premises fysieke machines worden opgeslagen in azure Managed disks. Azure-Vm's worden gemaakt met de gerepliceerde gegevens wanneer u een failover van on-premises naar Azure uitvoert. De Azure-VM's maken verbinding met het virtuele Azure-netwerk wanneer ze worden gemaakt.
**Configuratieserver** | Er wordt één on-premises fysieke machine of VMware-VM geïmplementeerd om alle on-premises Site Recovery onderdelen uit te voeren. De VM voert de configuratie server, de proces server en de hoofddoel server uit. | De configuratieserver coördineert de communicatie tussen on-premises en Azure, en beheert de gegevensreplicatie.
 **Processerver**:  | Standaard geïnstalleerd in combi natie met de configuratie server. | Fungeert als replicatiegateway. Dit onderdeel ontvangt replicatiegegevens, optimaliseert de gegevens met caching, compressie en codering, en verzendt ze naar de Azure-opslag.<br/><br/> De proces server installeert ook de Mobility-service op servers die u wilt repliceren.<br/><br/> Naarmate uw implementatie groeit, kunt u extra, afzonderlijke proces servers toevoegen om grotere volumes van replicatie verkeer af te handelen.
 **Hoofddoelserver** | Standaard geïnstalleerd in combi natie met de configuratie server. | Hier worden de replicatiegegevens tijdens de failback vanuit Azure afgehandeld.<br/><br/> Voor grote implementaties kunt u een extra, afzonderlijke Master doel server toevoegen voor failback.
**Gerepliceerde servers** | De Mobility-service wordt geïnstalleerd op elke server die u repliceert. | U wordt aangeraden automatische installatie vanaf de proces server toe te staan. U kunt de service ook hand matig installeren of een geautomatiseerde implementatie methode gebruiken, zoals System Center Configuration Manager.

**Fysiek naar Azure-architectuur**

![Onderdelen](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replicatieproces

1. U stelt de implementatie in, met inbegrip van on-premises en Azure-onderdelen. In de Recovery Services kluis geeft u de replicatie bron en het doel op, stelt u de configuratie server in, maakt u een replicatie beleid en schakelt u replicatie in.
2. Computers repliceren op basis van het replicatie beleid en een eerste kopie van de server gegevens wordt gerepliceerd naar Azure Storage.
3. Wanneer de initiële replicatie is voltooid, begint de replicatie van Delta wijzigingen naar Azure. Bijgehouden wijzigingen voor een machine worden opgeslagen in een .hrl-bestand.
    - Computers communiceren met de configuratie server op poort HTTPS 443 inkomend voor replicatie beheer.
    - Computers verzenden replicatie gegevens naar de proces server op poort HTTPS 9443 inkomend (kan worden gewijzigd).
    - De configuratieserver coördineert het replicatiebeheer met Azure via de uitgaande HTTPS-poort 443.
    - De processerver ontvangt gegevens van de bronmachines, optimaliseert en versleutelt deze gegevens en verzendt ze naar Azure Storage via de uitgaande poort 443.
    - Als u multi-VM-consistentie inschakelt, communiceren machines in de replicatiegroep met elkaar via poort 20004. Meerdere VM’s worden gebruikt als u meerdere machines groepeert in de replicatiegroepen die crashconsistent en app-consistent herstelpunten delen bij failover. Dit is handig als machines dezelfde workload gebruiken en consistent moeten zijn.
4. Verkeer wordt via internet gerepliceerd naar de openbare eindpunten van Azure Storage. U kunt ook [openbare peering](../expressroute/expressroute-circuit-peerings.md#publicpeering) van Azure ExpressRoute gebruiken. Het repliceren van verkeer via een site-naar-site-VPN van een on-premises site naar Azure wordt niet ondersteund.


**Fysiek naar Azure-replicatie proces**

![Replicatieproces](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Failover- en failbackproces

Nadat de replicatie is ingesteld en u een herstel analyse voor nood gevallen (testfailover) hebt uitgevoerd om te controleren of alles werkt zoals verwacht, kunt u failover en failback uitvoeren zoals u dat nodig hebt. Opmerking:

- Geplande failover wordt niet ondersteund.
- U moet een failback uitvoeren naar een on-premises virtuele VMware-machine. Dit betekent dat u een on-premises VMware-infra structuur nodig hebt, zelfs wanneer u on-premises fysieke servers naar Azure repliceert.
- U kunt een failover uitvoeren voor een enkele machine of herstel plannen maken om meerdere computers samen te werken.
- Wanneer u een failover uitvoert, worden Azure-Vm's gemaakt op basis van gerepliceerde gegevens in azure Storage.
- Nadat u de eerste failover hebt geactiveerd, voert u deze uit om toegang te krijgen tot de workload vanuit de Azure-VM.
- Als uw primaire on-premises site weer beschikbaar is, kunt u een failback uitvoeren.
- U moet een infra structuur voor failback instellen, waaronder:
    - **Tijdelijke proces server in azure**: als u een failback van Azure wilt uitvoeren, stelt u een Azure vm in om te fungeren als een proces server voor het afhandelen van replicatie vanuit Azure. U kunt deze virtuele machine verwijderen wanneer de failback is voltooid.
    - **VPN-verbinding**: als u een failback wilt uitvoeren, hebt u een VPN-verbinding (of Azure ExpressRoute) nodig van het Azure-netwerk naar de on-premises site.
    - **Afzonderlijke hoofddoel server**: de Master doel server die is geïnstalleerd met de configuratie server, op de on-premises VMware-VM, verwerkt de failback. Als u echter grote hoeveel heden verkeer wilt herstellen, moet u een afzonderlijke on-premises hoofddoel server voor dit doel instellen.
    - **Failbackbeleid**: als u wilt repliceren naar de on-premises site, hebt u een failbackbeleid nodig. Deze is automatisch gemaakt bij het maken van uw replicatie beleid van on-premises naar Azure.
    - **VMware-infra structuur**: u hebt een VMware-infra structuur nodig voor failback. U kunt geen failback uitvoeren naar een fysieke server.
- Nadat de onderdelen zijn geïmplementeerd, vindt failback plaats in drie fasen:
    - Fase 1: Beveilig de Azure Vm's opnieuw, zodat ze worden gerepliceerd van Azure naar de on-premises virtuele VMware-machines.
    - Fase 2: een failover uitvoeren naar de on-premises site.
    - Fase 3: nadat de workloads zijn hersteld, schakelt u de replicatie opnieuw in.

**VMware-failback van Azure**

![Failback](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Volgende stappen

Volg [deze zelf studie om de](physical-azure-disaster-recovery.md) replicatie van de fysieke server naar Azure in te scha kelen.
