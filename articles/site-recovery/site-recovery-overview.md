---
title: Over Azure Site Recovery
description: Dit artikel biedt een overzicht van de Azure Site Recovery-service en somt de implementatiescenario’s voor herstel na noodgeval en implementatie.
ms.topic: overview
ms.date: 03/17/2020
ms.custom: MVC
ms.openlocfilehash: ee387682e935522b37a5b4f9f32e53712bfb9150
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80067546"
---
# <a name="about-site-recovery"></a>Site Recovery

Welkom bij de Azure Site Recovery-service! Dit artikel biedt een kort overzicht van de service.

Als organisatie moet u een strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) gebruiken om uw gegevens veilig te houden en uw apps en workloads online, wanneer geplande en ongeplande storingen optreden.

Azure Recovery Services draagt bij aan uw BCDR-strategie:

- **Site Recovery-service**: Site Recovery helpt de bedrijfscontinuïteit te waarborgen door zakelijke apps en workloads actief te houden tijdens onderbrekingen. Met Site Recovery worden workloads die worden uitgevoerd op fysieke en virtuele machines (VM’s), gerepliceerd van een primaire site naar een secundaire locatie. Wanneer een onderbreking optreedt op de primaire site, voert u een failover uit naar de secundaire locatie, waar u vervolgens toegang hebt tot de apps. Als de primaire locatie weer actief is, kunt u een failback naar deze site uitvoeren.
- **Backup-service**: de [Azure backup](/azure/backup/) -service houdt uw gegevens veilig en herstelbaar.

Met Site Recovery kunt u replicatie beheren voor:

- Azure-VM's die worden gerepliceerd tussen Azure-regio's.
- On-premises Vm's, Azure Stack Vm's en fysieke servers.

## <a name="what-does-site-recovery-provide"></a>Wat biedt Site Recovery?

**Functie** | **Details**
--- | ---
**Eenvoudige BCDR-oplossing** | Met Site Recovery kunt u replicatie, failover en failback instellen en beheren vanaf één locatie in de Azure portal.
**Replicatie van Azure-VM’s** | U kunt herstel na noodgevallen instellen voor Azure-VM’s van een primaire regio naar een secundaire regio.
**Replicatie van on-premises VM’s** | U kunt on-premises VM's en fysieke servers repliceren naar Azure of naar een secundair on-premises datacenter. Hierdoor kunt u een secundair datacenter goedkoper en eenvoudiger onderhouden.
**Replicatie van workloads** | U kunt elke workload repliceren die wordt uitgevoerd op ondersteunde Azure-VM's, on-premises Hyper-V- en VMware-VM's, en fysieke Windows-/Linux-servers.
**Gegevenstolerantie** | Site Recovery organiseert de replicatie zonder toepassings gegevens te onderscheppen. Wanneer u repliceert naar Azure, worden gegevens opgeslagen in Azure Storage met alle bijbehorende tolerantie. Wanneer failover optreedt, worden er Azure-VM’s gemaakt op basis van de gerepliceerde gegevens.
**RTO- en RPO-doelen** | Zorg dat de beoogde hersteltijden (RTO) en beoogde herstelpunten (RPO) binnen de organisatielimieten blijven. Site Recovery biedt continue replicatie voor Azure-VM's en VMware-VM's en een lage replicatiefrequentie van slechts 30 seconden voor Hyper-V. U kunt beoogde hersteltijden verder beperken door integratie met [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Apps consistent houden bij failover** | U kunt repliceren via herstelpunten met toepassingsconsistente momentopnamen. Met deze momentopnamen kunt u schijfgegevens, alle gegevens in het geheugen en alle lopende transacties vastleggen.
**Testen zonder onderbreking** | U kunt eenvoudig noodhersteloefeningen uitvoeren zonder dat dit van invloed is op de doorlopende replicatie.
**Flexibele failovers** | U kunt geplande failovers uitvoeren voor verwachte storingen met geen gegevens verlies. Of ongeplande failovers met mini maal gegevens verlies, afhankelijk van de replicatie frequentie, voor onverwachte rampen. U kunt weer terug naar uw primaire site wanneer deze weer beschikbaar is.
**Aangepaste herstelplannen** | Met herstel plannen kunt u de failover en het herstel aanpassen van toepassingen met meerdere lagen die op meerdere Vm's worden uitgevoerd. U groepeert computers in een herstelplan en kunt optioneel ook scripts en handmatige acties toevoegen. Herstelplannen kunnen worden geïntegreerd met Azure Automation-runbooks.
**BCDR-integratie** | Met Site Recovery kunt u integreren met andere BCDR-technologieën. U kunt Site Recovery bijvoorbeeld gebruiken om de SQL Server-back-end van zakelijke workloads te beveiligen. Hierbij wordt ook systeemeigen ondersteuning geboden voor SQL Server AlwaysOn voor het beheren van failovers van beschikbaarheidsgroepen.
**Azure Automation-integratie** | Een uitgebreide Azure Automation-bibliotheek met toepassingsspecifieke scripts die klaar zijn voor gebruik en kunnen worden gedownload en geïntegreerd met Site Recovery.
**Netwerkintegratie** | Site Recovery integreert met Azure voor toepassings netwerk beheer. Zo kunt u bijvoorbeeld IP-adressen reserveren, load balancers configureren en Azure-Traffic Manager gebruiken voor efficiënte netwerk overschakelingen.

## <a name="what-can-i-replicate"></a>Wat kan ik repliceren?

**Geboden** | **Details**
--- | ---
**Replicatiescenario's** | Repliceer VM's van de ene Azure-regio naar de andere.<br/><br/>  Repliceer on-premises VMware-VM's, Hyper-V-VM's, fysieke servers (Windows en Linux), Azure Stack-VM's naar Azure.<br/><br/> AWS Windows-instanties repliceren naar Azure.<br/><br/> Repliceer on-premises VMware-VM’s, Hyper-V-VM’s die worden beheerd met System Center VMM, en fysieke servers naar een secundaire site.
**Regio's** | Bekijk [ondersteunde regio's](https://azure.microsoft.com/regions/services/) voor Site Recovery. |
**Gerepliceerde machines** | Lees de replicatievereisten voor [Azure VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)-replicatie, [on-premises virtuele VMware-machines en fysieke servers](vmware-physical-azure-support-matrix.md#replicated-machines), en [on-premises virtuele Hyper-V-machines](hyper-v-azure-support-matrix.md#replicated-vms).
**Workloads** | U kunt iedere workload repliceren die wordt uitgevoerd op een computer waarvoor replicatie wordt ondersteund. En het Site Recovery team heeft app-specifieke tests voor een [aantal apps](site-recovery-workload.md#workload-summary).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ondersteuning van werk belastingen](site-recovery-workload.md).
- Aan de slag met [Azure-VM-replicatie tussen regio's](azure-to-azure-quickstart.md).
