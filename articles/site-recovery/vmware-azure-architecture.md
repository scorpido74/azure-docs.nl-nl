---
title: Architectuur voor herstel na nood gevallen voor VMware VM in Azure Site Recovery
description: Dit artikel bevat een overzicht van de onderdelen en architectuur die worden gebruikt bij het instellen van herstel na nood gevallen van on-premises virtuele VMware-machines naar Azure met Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 8bfbc6783df4f902d25b2a4791708990a327edc8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663067"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Architectuur voor nood herstel van VMware naar Azure

In dit artikel worden de architectuur en processen beschreven die worden gebruikt bij het implementeren van herstel na nood gevallen, failover en herstel van virtuele VMware-machines (Vm's) tussen een on-premises VMware-site en Azure met behulp van de [Azure site Recovery](site-recovery-overview.md) -service.


## <a name="architectural-components"></a>Architectuuronderdelen

De volgende tabel en afbeelding bieden een weer gave op hoog niveau van de onderdelen die worden gebruikt voor VMware-herstel na nood gevallen naar Azure.

**Onderdeel** | **Vereiste** | **Details**
--- | --- | ---
**Azure** | Een Azure-abonnement, Azure Storage account voor cache, Managed disk en Azure Network. | Gerepliceerde gegevens van on-premises Vm's worden opgeslagen in azure Storage. Virtuele Azure-machines worden gemaakt met de gerepliceerde gegevens wanneer u een failover van on-premises naar Azure uitvoert. De Azure-VM's maken verbinding met het virtuele Azure-netwerk wanneer ze worden gemaakt.
**Computer van de configuratie server** | Één on-premises computer. We raden u aan deze uit te voeren als een VMware-VM die kan worden geïmplementeerd vanuit een gedownloade OVF-sjabloon.<br/><br/> Op de computer worden alle on-premises Site Recovery onderdelen uitgevoerd, waaronder de configuratie server, de proces server en de hoofddoel server. | **Configuratie server**: coördineert de communicatie tussen on-premises en Azure, en beheert de gegevens replicatie.<br/><br/> **Proces server**: standaard geïnstalleerd op de configuratie server. Het ontvangt replicatie gegevens; optimaliseert het met caching, compressie en versleuteling. en verzendt deze naar Azure Storage. De processerver installeert ook Azure Site Recovery Mobility Service op VM's die u wilt repliceren, en detecteert automatisch on-premises computers. Naarmate uw implementatie groeit, kunt u extra, afzonderlijke proces servers toevoegen om grotere volumes van replicatie verkeer af te handelen.<br/><br/> **Hoofddoel server**: standaard geïnstalleerd op de configuratie server. De replicatie gegevens worden verwerkt tijdens de failback vanuit Azure. Voor grote implementaties kunt u een extra, afzonderlijke Master doel server toevoegen voor failback.
**VMware-servers** | VMware-Vm's worden gehost op on-premises vSphere ESXi-servers. We raden aan dat een vCenter-Server de hosts beheert. | Tijdens Site Recovery implementatie voegt u VMware-servers toe aan de Recovery Services kluis.
**Gerepliceerde machines** | Mobility service is geïnstalleerd op elke virtuele VMware-machine die u repliceert. | U wordt aangeraden automatische installatie vanaf de proces server toe te staan. U kunt de service ook hand matig installeren of een geautomatiseerde implementatie methode gebruiken, zoals System Center Configuration Manager.

**VMware naar Azure-architectuur**

![Onderdelen](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>Replicatieproces

1. Wanneer u replicatie voor een virtuele machine inschakelt, begint de initiële replicatie naar Azure Storage met behulp van het opgegeven replicatie beleid. Houd rekening met het volgende:
    - Voor virtuele VMware-machines is de replicatie op blok niveau, bijna continu, met behulp van de Mobility Service-agent die op de virtuele machine wordt uitgevoerd.
    - Alle instellingen voor het replicatie beleid worden toegepast:
        - **RPO-drempel waarde**. Deze instelling heeft geen invloed op replicatie. Het helpt bij de bewaking. Er wordt een gebeurtenis gegenereerd en optioneel een e-mail verzonden als de huidige RPO de drempel waarde overschrijdt die u opgeeft.
        - **Bewaar periode van het herstel punt**. Met deze instelling geeft u op hoe ver terug in de tijd die u wilt door gaan wanneer er een onderbreking optreedt. De maximale Bewaar periode voor Premium-opslag is 24 uur. In de standaard opslag is 72 uur. 
        - **App-consistente moment opnamen**. De app-consistente moment opname kan elke 1 tot 12 uur duren, afhankelijk van de behoeften van uw app. Moment opnamen zijn standaard Azure Blob-moment opnamen. De Mobility-agent die wordt uitgevoerd op een VM, verzoekt een VSS-moment opname in overeenstemming met deze instelling en blad wijzers die als een toepassings consistent punt in de replicatie stroom wordt genoemd.

2. Verkeer wordt gerepliceerd naar open bare eind punten van Azure Storage via internet. U kunt ook Azure ExpressRoute gebruiken met [micro soft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Het repliceren van verkeer via een site-naar-site virtueel particulier netwerk (VPN) van een on-premises site naar Azure wordt niet ondersteund.
3. Wanneer de initiële replicatie is voltooid, begint de replicatie van Delta wijzigingen naar Azure. Bijgehouden wijzigingen voor een machine worden verzonden naar de proces server.
4. Communicatie gebeurt als volgt:

    - Vm's communiceren met de on-premises configuratie server op poort HTTPS 443 inkomend voor replicatie beheer.
    - De configuratie Server organiseert de replicatie met Azure via poort HTTPS 443 uitgaand.
    - Vm's verzenden replicatie gegevens naar de proces server (die wordt uitgevoerd op de computer van de configuratie server) op poort HTTPS 9443-binnenkomend. Deze poort kan worden gewijzigd.
    - De proces server ontvangt replicatie gegevens, optimaliseert en versleutelt deze en verzendt deze naar Azure Storage via poort 443 uitgaand.
5. De replicatie gegevens melden het eerste land in een cache-opslag account in Azure. Deze logboeken worden verwerkt en de gegevens worden opgeslagen in een Azure Managed disk (ook wel ASR-Seed-schijf genoemd). De herstel punten worden op deze schijf gemaakt.




**Replicatie proces van VMware naar Azure**

![Replicatieproces](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Failover- en failbackproces

Nadat de replicatie is ingesteld en u een nood herstel analyse (testfailover) uitvoert om te controleren of alles werkt zoals verwacht, kunt u failover en failback uitvoeren zoals u dat nodig hebt.

1. U voert een failover uit voor één computer of u maakt een herstel plan om meerdere Vm's tegelijk uit te voeren. Het voor deel van een herstel plan in plaats van de failover van één machine omvat:
    - U kunt app-afhankelijkheden model leren door alle virtuele machines in de app in één herstel plan op te nemen.
    - U kunt scripts, Azure-runbooks en pauzes voor hand matige acties toevoegen.
2. Nadat u de eerste failover hebt geactiveerd, voert u deze uit om toegang te krijgen tot de workload vanuit de Azure-VM.
3. Als uw primaire on-premises site weer beschikbaar is, kunt u voor bereidingen voor failback. Als u een failback wilt uitvoeren, moet u een failover-infra structuur instellen, waaronder:

    * **Tijdelijke proces server in azure**: als u een failback van Azure wilt uitvoeren, stelt u een Azure vm in om te fungeren als proces server voor het afhandelen van replicatie van Azure. U kunt deze virtuele machine verwijderen wanneer de failback is voltooid.
    * **VPN-verbinding**: als u een failback wilt uitvoeren, hebt u een VPN-verbinding (of ExpressRoute) van het Azure-netwerk nodig naar de on-premises site.
    * **Afzonderlijke hoofddoel server**: de Master doel server die is geïnstalleerd met de configuratie server op de on-premises virtuele VMWare-machine, verwerkt de failback. Als u grote hoeveel heden verkeer wilt herstellen, moet u voor dit doel een afzonderlijke on-premises Master doel server instellen.
    * **Failbackbeleid**: als u wilt repliceren naar de on-premises site, hebt u een failbackbeleid nodig. Dit beleid wordt automatisch gemaakt wanneer u een replicatie beleid maakt op basis van on-premises naar Azure.
4. Nadat de onderdelen zijn geïmplementeerd, vindt failback plaats in drie acties:

    - Fase 1: Beveilig de Azure Vm's opnieuw, zodat ze worden gerepliceerd van Azure naar de on-premises virtuele VMware-machines.
    -  Fase 2: een failover uitvoeren naar de on-premises site.
    - Fase 3: nadat de workloads zijn hersteld, schakelt u de replicatie voor de on-premises Vm's opnieuw in.
    
 
**VMware-failback van Azure**

![Failback](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Volgende stappen

Volg [deze zelf studie om de](vmware-azure-tutorial.md) replicatie van VMware naar Azure in te scha kelen.
