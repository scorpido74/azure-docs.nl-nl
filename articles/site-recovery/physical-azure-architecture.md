---
title: Herstelarchitectuur voor herstel van fysieke servers in Azure-siteherstel
description: In dit artikel vindt u een overzicht van onderdelen en architectuur die worden gebruikt bij het herstel van on-premises fysieke servers naar Azure met de Azure Site Recovery-service.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 089d981284986a2b6eb0ee7f1dbd401fc7ce4fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162834"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Architectuur van herstel na noodgevallen van fysieke server naar Azure

In dit artikel worden de architectuur en processen beschreven die worden gebruikt wanneer u fysieke Windows- en Linux-servers nabootst, mislukt en herstelt tussen een on-premises site en Azure, met behulp van de [Azure Site Recovery-service.](site-recovery-overview.md)

## <a name="architectural-components"></a>Architectuuronderdelen

De volgende tabel en afbeelding biedt een weergave op hoog niveau van de componenten die worden gebruikt voor fysieke serverreplicatie naar Azure.

| **Component** | **Vereiste** | **Details** |
| --- | --- | --- |
| **Azure** | Een Azure-abonnement en een Azure-netwerk. | Gerepliceerde gegevens van on-premises fysieke machines worden opgeslagen in door Azure beheerde schijven. Azure VM's worden gemaakt met de gerepliceerde gegevens wanneer u een failover uitvoert van on-premises naar Azure. De Azure-VM's maken verbinding met het virtuele Azure-netwerk wanneer ze worden gemaakt. |
| **Processerver** | Standaard geïnstalleerd in combinatie met de configuratieserver. | Fungeert als replicatiegateway. Dit onderdeel ontvangt replicatiegegevens, optimaliseert de gegevens met caching, compressie en codering, en verzendt ze naar de Azure-opslag.<br/><br/> De processerver installeert ook de Mobiliteitsservice op servers die u wilt repliceren.<br/><br/> Naarmate uw implementatie toeneemt, u extra, afzonderlijke processervers toevoegen om grotere hoeveelheden replicatieverkeer te verwerken. |
| **Hoofddoelserver** | Standaard geïnstalleerd in combinatie met de configuratieserver. | Hiermee verwerkt u replicatiegegevens tijdens fail back vanuit Azure.<br/><br/> Voor grote implementaties u een extra, afzonderlijke hoofddoelserver toevoegen voor failback. |
| **Gerepliceerde servers** | De Mobiliteitsservice is geïnstalleerd op elke server die u repliceert. | Wij raden u aan automatische installatie vanaf de processerver toe te staan. U de service ook handmatig installeren of een geautomatiseerde implementatiemethode gebruiken, zoals Configuratiebeheer. |

**Fysieke naar Azure-architectuur**

![Onderdelen](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Replicatieproces

1. U stelt de implementatie in, inclusief on-premises en Azure-componenten. In de kluis Recovery Services geeft u de replicatiebron en het doel op, stelt u de configuratieserver in, maakt u een replicatiebeleid en schakelt u replicatie in.
1. Machines repliceren met behulp van het replicatiebeleid en een eerste kopie van de servergegevens wordt gerepliceerd naar Azure-opslag.
1. Nadat de eerste replicatie is voltooid, wordt de replicatie van deltawijzigingen in Azure gestart. Bijgehouden wijzigingen voor een machine worden in een bestand bewaard met de _.hrl-extensie._
   - Machines communiceren met de configuratieserver op HTTPS-poort 443 inkomende, voor replicatiebeheer.
   - Machines verzenden replicatiegegevens naar de processerver op HTTPS-poort 9443 inkomende (kan worden gewijzigd).
   - De configuratieserver orkestreert replicatiebeheer met Azure via HTTPS-poort 443-uitgaand.
   - De processerver ontvangt gegevens van bronmachines, optimaliseert en versleutelt deze en stuurt deze naar Azure-opslag via HTTPS-poort 443-uitgaand.
   - Als u multi-VM-consistentie inschakelt, communiceren machines in de replicatiegroep met elkaar via poort 20004. Meerdere VM’s worden gebruikt als u meerdere machines groepeert in de replicatiegroepen die crashconsistent en app-consistent herstelpunten delen bij failover. Deze groepen zijn handig als machines dezelfde werkbelasting uitvoeren en consistent moeten zijn.
1. Verkeer wordt via internet gerepliceerd naar de openbare eindpunten van Azure Storage. U kunt ook [openbare peering](../expressroute/about-public-peering.md) van Azure ExpressRoute gebruiken.

   > [!NOTE]
   > Replicatie wordt niet ondersteund via een site-to-site VPN vanaf een on-premises site of Azure ExpressRoute [private peering.](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)

**Replicatieproces van fysiek naar Azure**

![Replicatieproces](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Failover- en failbackproces

Nadat replicatie is ingesteld, u een noodherstelboor (testfailover) uitvoeren om te controleren of alles werkt zoals verwacht. Dan u mislukken over en niet terug als dat nodig is. Houd rekening met de volgende items:

- Geplande failover wordt niet ondersteund.
- Niet terug naar een on-premises VMware VM is noodzakelijk. U hebt een on-premises VMware-infrastructuur nodig, zelfs wanneer u on-premises fysieke servers repliceert naar Azure.
- U faalt boven één machine of maakt herstelplannen om samen over meerdere machines te falen.
- Wanneer u een failover uitvoert, worden Azure VM's gemaakt op basis van gerepliceerde gegevens in Azure-opslag.
- Nadat de eerste failover is geactiveerd, verbindt u u deze om toegang te krijgen tot de werkbelasting van de Azure VM.
- Als uw primaire on-premises site weer beschikbaar is, kunt u een failback uitvoeren.
- Stel een failback-infrastructuur in die het als:
  - **Tijdelijke processerver in Azure:** Als u niet terugwilt van Azure, stelt u een Azure VM in om als processerver te fungeren om replicatie vanuit Azure te verwerken. U deze vm verwijderen nadat fail back-finishes zijn voltooid.
  - **VPN-verbinding**: Om terug te mislukken, hebt u een VPN-verbinding (of Azure ExpressRoute) nodig van het Azure-netwerk naar de on-premises site.
  - **Afzonderlijke hoofddoelserver:** Standaard wordt de fail back afgehandeld door de hoofddoelserver die is geïnstalleerd met de configuratieserver op de on-premises VMware VM. Als u grote hoeveelheden verkeer moet terugwerken, moet u een afzonderlijke on-premises hoofddoelserver instellen.
  - **Failbackbeleid**: als u wilt repliceren naar de on-premises site, hebt u een failbackbeleid nodig. Het beleid is automatisch gemaakt toen u uw replicatiebeleid maakte van on-premises naar Azure.
  - **VMware-infrastructuur**: Om terug te falen, hebt u een VMware-infrastructuur nodig. U kunt geen failback uitvoeren naar een fysieke server.
- Nadat de componenten op hun plaats zijn, treedt de terugval in drie fasen op:
  - **Fase 1:** Bescherm de Azure VM's opnieuw zodat ze vanuit Azure worden gerepliceerd naar de on-premises Vm's vmware.
  - **Fase 2**: Voer een failover naar de on-premises site.
  - **Fase 3**: Nadat workloads zijn mislukt, schakelt u replicatie opnieuw in.

**VMware-failback van Azure**

![Failback](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>Volgende stappen

Als u noodherstel voor fysieke servers voor Azure wilt instellen, raadpleegt u de [handleiding](physical-azure-disaster-recovery.md).
