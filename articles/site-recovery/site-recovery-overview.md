---
title: Over Azure Site Recovery
description: Dit artikel biedt een overzicht van de Azure Site Recovery-service en somt de implementatiescenario’s voor herstel na noodgeval en implementatie.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: overview
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 3f38560c92e91925ab8a18924acdbb74f4b90e59
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814376"
---
# <a name="about-site-recovery"></a>Site Recovery

Welkom bij de Azure Site Recovery-service! Dit artikel biedt een kort overzicht van de service.

Als organisatie moet u een BCDR-strategie (Business Continunity and Disaster Recovery) gebruiken die ervoor zorgt dat uw gegevens zijn beveiligd, en uw apps en workloads actief blijven tijdens geplande en ongeplande onderbrekingen.

Azure Recovery Services dragen bij aan uw BCDR-strategie:

- **Site Recovery-service**: Site Recovery helpt de bedrijfscontinuïteit te waarborgen door zakelijke apps en workloads actief te houden tijdens onderbrekingen. Met Site Recovery worden workloads die worden uitgevoerd op fysieke en virtuele machines (VM’s), gerepliceerd van een primaire site naar een secundaire locatie. Wanneer een onderbreking optreedt op de primaire site, voert u een failover uit naar de secundaire locatie, waar u vervolgens toegang hebt tot de apps. Als de primaire locatie weer actief is, kunt u een failback naar deze site uitvoeren.  
- **Backup-service**: met de [Azure Backup](https://docs.microsoft.com/azure/backup/)-service zijn uw gegevens beveiligd en kunnen ze worden hersteld door back-ups te maken in Azure.

Met Site Recovery kunt u replicatie beheren voor:

- Azure-VM's die worden gerepliceerd tussen Azure-regio's.
- On-premises VM's, Azure Stack-VM's en fysieke servers.


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
**Flexibele failovers** | U kunt bij verwachte uitval geplande failovers uitvoeren zonder gegevensverlies en bij onverwachte noodsituaties ongeplande failovers uitvoeren met minimaal gegevensverlies (afhankelijk van de replicatiefrequentie). U kunt weer terug naar uw primaire site wanneer deze weer beschikbaar is.
**Aangepaste herstelplannen** | Met herstelplannen kunt u de failover en het herstel van toepassingen met meerdere lagen waarop meerdere VM’s worden uitgevoerd, aanpassen en aaneenschakelen. U groepeert computers in een herstelplan en kunt optioneel ook scripts en handmatige acties toevoegen. Herstelplannen kunnen worden geïntegreerd met Azure Automation-runbooks.
**BCDR-integratie** | Met Site Recovery kunt u integreren met andere BCDR-technologieën. U kunt Site Recovery bijvoorbeeld gebruiken om de SQL Server-back-end van zakelijke workloads te beveiligen. Hierbij wordt ook systeemeigen ondersteuning geboden voor SQL Server AlwaysOn voor het beheren van failovers van beschikbaarheidsgroepen.
**Azure Automation-integratie** | Een uitgebreide Azure Automation-bibliotheek met toepassingsspecifieke scripts die klaar zijn voor gebruik en kunnen worden gedownload en geïntegreerd met Site Recovery.
**Netwerkintegratie** | Site Recovery kan worden geïntegreerd met Azure voor eenvoudig toepassingsnetwerkbeheer, waaronder het reserveren van IP-adressen, het configureren van load balancers en het integreren van Azure Traffic Manager voor het efficiënt schakelen tussen netwerken.


## <a name="what-can-i-replicate"></a>Wat kan ik repliceren?

**Ondersteund** | **Details**
--- | ---
**Replicatiescenario's** | Repliceer VM's van de ene Azure-regio naar de andere.<br/><br/>  Repliceer on-premises VMware-VM's, Hyper-V-VM's, fysieke servers (Windows en Linux), Azure Stack-VM's naar Azure.<br/><br/> <br/><br/> AWS Windows-instanties repliceren naar Azure.<br/><br/> Repliceer on-premises VMware-VM’s, Hyper-V-VM’s die worden beheerd met System Center VMM, en fysieke servers naar een secundaire site.
**Regio's** | Bekijk [ondersteunde regio's](https://azure.microsoft.com/regions/services/) voor Site Recovery. |
**Gerepliceerde machines** | Lees de replicatievereisten voor [Azure VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)-replicatie, [on-premises virtuele VMware-machines en fysieke servers](vmware-physical-azure-support-matrix.md#replicated-machines), en [on-premises virtuele Hyper-V-machines](hyper-v-azure-support-matrix.md#replicated-vms).
**Workloads** | U kunt iedere workload repliceren die wordt uitgevoerd op een computer waarvoor replicatie wordt ondersteund. Daarnaast heeft het Site Recovery-team specifieke tests uitgevoerd voor een [aantal apps](site-recovery-workload.md#workload-summary).



## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [workloadondersteuning](site-recovery-workload.md).
* Aan de slag met [Azure-VM-replicatie tussen regio's](azure-to-azure-quickstart.md). 
