---
title: VMware VM-noodherstelarchitectuur in Azure Site Recovery
description: Dit artikel bevat een overzicht van componenten en architectuur die worden gebruikt bij het instellen van noodherstel van on-premises VMware VM's naar Azure met Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 77b4dd4c0efbe6d03e64865f18c2c87614aaecb5
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632524"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware naar Azure-architectuur voor noodherstel

In dit artikel worden de architectuur en processen beschreven die worden gebruikt bij het implementeren van disaster recovery-replicatie, failover en herstel van Virtuele VM's (VMware) tussen een on-premises VMware-site en Azure met behulp van de [Azure Site Recovery-service.](site-recovery-overview.md)


## <a name="architectural-components"></a>Architectuuronderdelen

De volgende tabel en afbeelding bieden een weergave op hoog niveau van de componenten die worden gebruikt voor VMware-noodherstel naar Azure.

**Component** | **Vereiste** | **Details**
--- | --- | ---
**Azure** | Een Azure-abonnement, Azure Storage-account voor cache, Beheerde schijf en Azure-netwerk. | Gerepliceerde gegevens van on-premises VM's worden opgeslagen in Azure-opslag. Azure VM's worden gemaakt met de gerepliceerde gegevens wanneer u een failover uitvoert van on-premises naar Azure. De Azure-VM's maken verbinding met het virtuele Azure-netwerk wanneer ze worden gemaakt.
**Configuratieservermachine** | Eén on-premises machine. We raden u aan deze uit te voeren als een VMware-vm die kan worden geïmplementeerd op basis van een gedownloade OVF-sjabloon.<br/><br/> De machine voert alle on-premises siteherstelcomponenten uit, waaronder de configuratieserver, de processerver en de hoofddoelserver. | **Configuratieserver:** coördineert de communicatie tussen on-premises en Azure en beheert gegevensreplicatie.<br/><br/> **Processerver**: standaard geïnstalleerd op de configuratieserver. Het ontvangt replicatiegegevens; optimaliseert het met caching, compressie en versleuteling; en stuurt het naar Azure Storage. De processerver installeert ook Azure Site Recovery Mobility Service op VM's die u wilt repliceren, en detecteert automatisch on-premises computers. Naarmate uw implementatie toeneemt, u extra, afzonderlijke processervers toevoegen om grotere hoeveelheden replicatieverkeer te verwerken.<br/><br/> **Hoofddoelserver**: standaard geïnstalleerd op de configuratieserver. Het verwerkt replicatiegegevens tijdens failback vanuit Azure. Voor grote implementaties u een extra, afzonderlijke hoofddoelserver toevoegen voor failback.
**VMware-servers** | VMware VM's worden gehost op on-premises vSphere ESXi-servers. We raden een vCenter-server aan om de hosts te beheren. | Tijdens de implementatie van Site Recovery voegt u VMware-servers toe aan de kluis Recovery Services.
**Gerepliceerde machines** | Mobility Service is geïnstalleerd op elke VMware VM die u repliceert. | Wij raden u aan automatische installatie vanaf de processerver toe te staan. U de service ook handmatig installeren of een geautomatiseerde implementatiemethode gebruiken, zoals Configuratiebeheer.

**VMware naar Azure-architectuur**

![Onderdelen](./media/vmware-azure-architecture/arch-enhanced.png)


## <a name="replication-process"></a>Replicatieproces

1. Wanneer u replicatie inschakelt voor een virtuele machine, begint de eerste replicatie naar Azure-opslag met behulp van het opgegeven replicatiebeleid. Houd rekening met het volgende:
    - Voor VMware VM's is replicatie bijna continu op blokniveau en met behulp van de mobiliteitsserviceagent die op de VM wordt uitgevoerd.
    - Alle instellingen voor replicatiebeleid worden toegepast:
        - **RPO-drempelwaarde**. Deze instelling heeft geen invloed op replicatie. Het helpt bij het monitoren. Een gebeurtenis wordt verhoogd en eventueel een verzonden e-mail als de huidige RPO de door u opgegeven drempelwaarde overschrijdt.
        - **Herstelpunt behoud**. Met deze instelling geeft u aan hoe ver terug in de tijd u wilt gaan wanneer er een verstoring optreedt. Maximale retentie op premium opslag is 24 uur. Op standaard opslag is het 72 uur. 
        - **App-consistente momentopnamen**. App-consistente momentopname kan elke 1 tot 12 uur worden gemaakt, afhankelijk van de behoeften van uw app. Momentopnamen zijn standaard Azure blob snapshots. De mobiliteitsagent die op een VM wordt uitgevoerd, vraagt een VSS-momentopname aan in overeenstemming met deze instelling en bladwijzers die als consistent punt in de replicatiestream in de tijdstoepassing worden weergegeven.

2. Verkeer wordt via internet gerepliceerd naar openbare eindpunten voor Azure-opslag. U Azure ExpressRoute ook gebruiken met [Microsoft-peering.](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) Het repliceren van verkeer via een vpn (site-to-site virtual private network) van een on-premises site naar Azure wordt niet ondersteund.
3. De eerste replicatiebewerking zorgt ervoor dat volledige gegevens op de machine op het moment van inschakelen replicatie naar Azure worden verzonden. Nadat de eerste replicatie is voltooid, wordt de replicatie van deltawijzigingen in Azure gestart. Bijgehouden wijzigingen voor een machine worden naar de processerver verzonden.
4. Communicatie gebeurt als volgt:

    - VM's communiceren met de on-premises configuratieserver op poort HTTPS 443 inkomende, voor replicatiebeheer.
    - De configuratieserver orkestreert replicatie met Azure via poort HTTPS 443 outbound.
    - VM's verzenden replicatiegegevens naar de processerver (uitgevoerd op de configuratieservermachine) op poort HTTPS 9443 binnenkomend. Deze poort kan worden gewijzigd.
    - De processerver ontvangt replicatiegegevens, optimaliseert en versleutelt deze en stuurt deze naar Azure-opslag via poort 443-uitgaand.
5. De replicatiegegevens worden eerst in een cacheopslagaccount in Azure opgeslagen. Deze logboeken worden verwerkt en de gegevens worden opgeslagen in een Azure Managed Disk (asr seed disk genoemd). De herstelpunten worden op deze schijf gemaakt.

**VMware naar Azure-replicatieproces**

![Replicatieproces](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Opnieuw synchronisatieproces

1. Soms, tijdens de eerste replicatie of tijdens het overbrengen van deltawijzigingen, kunnen er problemen zijn met de netwerkconnectiviteit tussen de bronmachine om server te verwerken of tussen processerver naar Azure. Een van deze kan leiden tot fouten in de overdracht van gegevens naar Azure tijdelijk.
2. Om problemen met de gegevensintegriteit te voorkomen en de kosten voor gegevensoverdracht te minimaliseren, markeert Site Recovery een machine voor opnieuw synchronisatie.
3. Een machine kan ook worden gemarkeerd voor opnieuw synchronisatie in situaties zoals volgen om de consistentie van de bronmachine en gegevens die zijn opgeslagen in Azure te behouden
    - Als een machine force shut-down ondergaat
    - Als een machine configuratiewijzigingen ondergaat, zoals het formaat van de schijf (het wijzigen van de grootte van de schijf van 2 TB tot 4 TB)
4. Resynchronisatie stuurt alleen deltagegevens naar Azure. Gegevensoverdracht tussen on-premises en Azure door het berekenen van gegevensgegevens tussen de bronmachine en gegevens die in Azure zijn opgeslagen.
5. Standaard wordt opnieuw synchronisatie automatisch buiten kantooruren uitgevoerd. Als u niet wilt wachten op standaardsynchronisatie buiten de openingstijden, u een virtuele machine handmatig opnieuw synchroniseren. Ga hiervoor naar Azure-portal en selecteer de VM > **Opnieuw synchroniseren**.
6. Als standaardsynchronisatie buiten kantooruren mislukt en een handmatige interventie vereist is, wordt er een fout gegenereerd op de specifieke machine in azure-portal. U de fout oplossen en de resynchronisatie handmatig activeren.
7. Na voltooiing van de resynchronisatie wordt de replicatie van deltawijzigingen hervat.

## <a name="failover-and-failback-process"></a>Failover- en failbackproces

Nadat replicatie is ingesteld en u een noodherstelboor uitvoert (testfailover) om te controleren of alles werkt zoals verwacht, u failover en failback uitvoeren als dat nodig is.

1. U voert fail uit voor één machine of maakt een herstelplan dat niet meer dan meerdere VM's tegelijk is. Het voordeel van een herstelplan in plaats van single machine failover omvatten:
    - U app-afhankelijkheden modelleren door alle VM's in de app in één herstelplan op te nemen.
    - U scripts, Azure-runbooks en pauze toevoegen voor handmatige acties.
2. Nadat u de eerste failover hebt geactiveerd, verbindt u deze ertoe om toegang te krijgen tot de werkbelasting van de Azure VM.
3. Wanneer uw primaire on-premises site weer beschikbaar is, u zich voorbereiden op fail back. Als u niet terug wilt werken, moet u een failback-infrastructuur instellen, waaronder:

    * **Tijdelijke processerver in Azure:** Als u niet terugwilt van Azure, stelt u een Azure VM in om te fungeren als een processerver om replicatie vanuit Azure te verwerken. U kunt deze virtuele machine verwijderen wanneer de failback is voltooid.
    * **VPN-verbinding**: Om terug te mislukken, hebt u een VPN-verbinding (of ExpressRoute) nodig van het Azure-netwerk naar de on-premises site.
    * **Afzonderlijke hoofddoelserver:** standaard verwerkt de hoofddoelserver die is geïnstalleerd met de configuratieserver op de on-premises VMware VM failback. Als u grote hoeveelheden verkeer moet terugwerken, stelt u hiervoor een afzonderlijke on-premises hoofddoelserver in.
    * **Failbackbeleid**: als u wilt repliceren naar de on-premises site, hebt u een failbackbeleid nodig. Dit beleid wordt automatisch gemaakt wanneer u een replicatiebeleid maakt van on-premises naar Azure.
4. Nadat de componenten op hun plaats zijn, treedt failback op in drie acties:

    - Fase 1: Bescherm de Azure VM's opnieuw zodat ze vanuit Azure worden gerepliceerd naar de on-premises Vm's vmware.
    -  Fase 2: Voer een failover naar de on-premises site.
    - Fase 3: Nadat workloads zijn mislukt, u replicatie opnieuw inschakelen voor de on-premises VM's.
    
 
**VMware-failback van Azure**

![Failback](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Volgende stappen

Volg [deze zelfstudie](vmware-azure-tutorial.md) om VMware in te schakelen voor Azure-replicatie.
