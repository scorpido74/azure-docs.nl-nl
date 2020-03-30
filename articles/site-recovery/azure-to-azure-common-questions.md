---
title: Veelgestelde vragen over Azure VM-noodherstel met Azure Site Recovery
description: In dit artikel worden veelgestelde vragen beantwoord over azure vm-noodherstel wanneer u Azure Site Recovery gebruikt.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 3d71301534d56ef8eca68951c8c9f9a1570b3a6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282253"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Veelgestelde vragen: Azure-to-Azure-herstel na noodgevallen

In dit artikel worden veelgestelde vragen beantwoord over noodherstel van Azure VM's naar een andere Azure-regio voor wanneer u [Azure Site Recovery gebruikt.](site-recovery-overview.md)

## <a name="general"></a>Algemeen

### <a name="how-is-site-recovery-priced"></a>Hoe wordt siteherstel geprijsd?

Bekijk de prijzen azure [siteherstel voor VM's](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Hoe werkt de gratis laag voor Azure Site Recovery?

Elk exemplaar dat is beveiligd met Azure Site Recovery is gratis voor de eerste 31 dagen van de bescherming. Na die periode is de beveiliging voor elk exemplaar aan de tarieven in [azure site recovery-prijzen voor Azure Virtual Machines.](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Zal ik gedurende de eerste 31 dagen andere Azure-kosten maken?

Ja. Hoewel Azure Site Recovery gratis is tijdens de eerste 31 dagen van een beveiligde instantie, u kosten in rekening brengen voor Azure Storage, opslagtransacties en gegevensoverdrachten. Een herstelde virtuele machine kan ook azure-rekenkosten in rekening brengen. Ontvang volledige details over de prijzen bij [Azure Site Recovery-prijzen](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Wat zijn de aanbevolen procedures voor azure virtual machines disaster recovery?

1. [Azure-naar-Azure-architectuur begrijpen](azure-to-azure-architecture.md)
1. [Bekijk de ondersteunde en niet-ondersteunde configuraties](azure-to-azure-support-matrix.md)
1. [Disaster recovery instellen voor Azure VM's](azure-to-azure-how-to-enable-replication.md)
1. [Een testfailover uitvoeren](azure-to-azure-tutorial-dr-drill.md)
1. [Fail over en fail terug naar de primaire regio](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Hoe wordt de capaciteit in de doelregio gewaarborgd?

Het team SiteHerstel en het Azure-capaciteitsbeheerteam plannen voor voldoende infrastructuurcapaciteit. Wanneer u een failover start, zorgen de teams er ook voor dat VM-exemplaren die worden beschermd door Site Recovery worden geïmplementeerd in de doelregio.

## <a name="replication"></a>Replicatie

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Kan ik VM's repliceren die zijn ingeschakeld via Azure-schijfversleuteling?

Ja. Site recovery ondersteunt disaster recovery van VM's waarmee Azure Disk Encryption is ingeschakeld. Wanneer u replicatie inschakelt, kopieert Azure alle vereiste schijfversleutelingssleutels en -geheimen van het brongebied naar het doelgebied in de gebruikerscontext. Als u niet over de juiste machtigingen beschikt, kan uw beveiligingsbeheerder een script gebruiken om de sleutels en geheimen te kopiëren.

- Siteherstel ondersteunt Azure Disk Encryption voor Azure VM's waarop Windows wordt uitgevoerd.
- Siteherstel ondersteunt Azure Disk Encryption versie 0.1, die een schema heeft waarvoor Azure Active Directory (Azure AD) vereist is. Siteherstel ondersteunt ook versie 1.1, waarvoor geen Azure AD nodig is. [Meer informatie over het extensieschema voor Azure-schijfversleuteling](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - Voor Azure Disk Encryption versie 1.1 moet u de Windows VM's gebruiken met beheerde schijven.
  - [Meer informatie](azure-to-azure-how-to-enable-replication-ade-vms.md) over het inschakelen van replicatie voor versleutelde VM's.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Kan ik virtuele machines repliceren naar een ander abonnement?

Ja, u Azure VM's repliceren naar een ander abonnement binnen dezelfde Azure AD-tenant.

Configureer herstel na noodgevallen [voor abonnementen](https://azure.microsoft.com/blog/cross-subscription-dr) door een ander abonnement te selecteren op het moment van replicatie.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Kan ik Azure VM's met zonevasten repliceren naar een andere regio?

Ja, u [vm's met zonevasten repliceren](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) naar een andere regio.

### <a name="can-i-exclude-disks"></a>Kan ik schijven uitsluiten?

Ja, u schijven op het moment van beveiliging uitsluiten met PowerShell. Zie voor meer informatie [hoe u schijven uitsluiten van replicatie](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Kan ik nieuwe schijven toevoegen aan gerepliceerde VM's en replicatie voor deze schijven inschakelen?

Ja, het toevoegen van nieuwe schijven aan gerepliceerde VM's en het inschakelen van replicatie voor deze schijven wordt ondersteund voor Azure VM's met beheerde schijven. Wanneer u een nieuwe schijf toevoegt aan een Azure VM die is ingeschakeld voor replicatie, wordt in de replicatiestatus van de VM een waarschuwing weergegeven. In die waarschuwing staat dat een of meer schijven op de VM beschikbaar zijn voor bescherming. U replicatie inschakelen voor toegevoegde schijven.

- Als u de beveiliging van de toegevoegde schijven inschakelt, verdwijnt de waarschuwing na de eerste replicatie.
- Als u replicatie voor de schijf niet inschakelt, u de waarschuwing verwijderen.
- Als u niet slaagt boven een vm waarop een toegevoegde schijf en replicatie zijn ingeschakeld, zijn er replicatiepunten. De replicatiepunten geven de schijven weer die beschikbaar zijn voor herstel.

Stel dat een vm één schijf heeft en u een nieuwe schijf toevoegt. Er is mogelijk een replicatiepunt gemaakt voordat u de schijf hebt toegevoegd. Dit replicatiepunt laat zien dat het bestaat uit '1 van de 2 schijven'.

Site herstel ondersteunt geen "hot remove" van een schijf van een gerepliceerde vm. Als u een VM-schijf verwijdert, moet u replicatie voor de virtuele machine uitschakelen en vervolgens opnieuw inschakelen.

### <a name="how-often-can-i-replicate-to-azure"></a>Hoe vaak kan ik repliceren naar Azure?

Replicatie is continu wanneer u Azure VM's repliceert naar een andere Azure-regio. Zie de [azure-to-Azure-replicatiearchitectuur voor](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process)meer informatie.

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Kan ik virtuele machines binnen een regio repliceren? Ik heb deze functionaliteit nodig om VM's te migreren.

U geen Azure-naar-Azure-schijfhersteloplossing gebruiken om VM's binnen een regio te repliceren.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Kan ik VM-exemplaren repliceren naar een Azure-regio?

Door Siteherstel te gebruiken, u VM's repliceren en herstellen tussen twee regio's binnen hetzelfde geografische cluster. Geografische clusters worden gedefinieerd met gegevenslatentie en soevereiniteit in het achterhoofd. Zie de [ondersteuningsmatrix siteherstelregio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)voor meer informatie .

### <a name="does-site-recovery-require-internet-connectivity"></a>Vereist Site Recovery internetverbinding?

Nee, siteherstel vereist geen internetverbinding. Maar het vereist wel toegang tot URL's en IP-bereiken voor siteherstel, zoals vermeld in [netwerken in Azure VM-noodherstel.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>Kan ik een toepassing repliceren met een afzonderlijke brongroep voor afzonderlijke lagen?

Ja, u de toepassing repliceren en de configuratie van het noodherstel ook in een afzonderlijke brongroep behouden.

Als uw toepassing bijvoorbeeld de toepassing, database en het web van elke laag in een afzonderlijke brongroep heeft, moet u de [replicatiewizard](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) drie keer selecteren om alle lagen te beschermen. Site recovery repliceert deze drie lagen in drie verschillende resourcegroepen.

## <a name="replication-policy"></a>Beleid voor replicatie

### <a name="what-is-a-replication-policy"></a>Wat is een replicatiebeleid?

Een replicatiebeleid definieert de instellingen voor de bewaargeschiedenis van herstelpunten. Het beleid definieert ook de frequentie van app-consistente momentopnamen. Azure Site Recovery maakt standaard een nieuw replicatiebeleid met standaardinstellingen van:

- 24 uur voor de retentiegeschiedenis van herstelpunten.
- 60 minuten voor de frequentie van app-consistente momentopnamen.

[Meer informatie over replicatie-instellingen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Wat is een crash-consistent herstelpunt?

Een crash-consistent herstelpunt heeft de gegevens op de schijf alsof u het netsnoer van de server hebt getrokken tijdens de momentopname. Het crash-consistent herstelpunt bevat niets dat in het geheugen stond toen de momentopname werd gemaakt.

Vandaag de dag kunnen de meeste toepassingen goed herstellen van crash-consistente snapshots. Een crashconsistent herstelpunt is meestal voldoende voor besturingssystemen en toepassingen zonder database, zoals bestandsservers, DHCP-servers en afdrukservers.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Wat is de frequentie van crash-consistent herstelpunt generatie?

Site Recovery creëert elke 5 minuten een crashconsistent herstelpunt.

### <a name="what-is-an-application-consistent-recovery-point"></a>Wat is een toepassingsconsistent herstelpunt?

Toepassingsconsistente herstelpunten worden gemaakt op basis van toepassingsconsistente momentopnamen. Toepassingsconsistente herstelpunten leggen dezelfde gegevens vast als momentopnamen voor crashconsistent, terwijl ze ook gegevens vastleggen in het geheugen en alle transacties in proces.

Vanwege hun extra inhoud zijn toepassingsconsistente snapshots het meest betrokken en nemen ze het langst. We raden toepassingsconsistente herstelpunten aan voor databasebesturingssystemen en toepassingen zoals SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Wat is de impact van toepassingsconsistente herstelpunten op de prestaties van toepassingen?

Toepassingsconsistente herstelpunten leggen alle gegevens vast in het geheugen en in het proces. Omdat herstelpunten die gegevens vastleggen, vereisen ze framework zoals Volume Shadow Copy Service op Windows om de toepassing te quiesce. Als het vastleggen vaak voorkomt, kan dit van invloed zijn op de prestaties wanneer de werkbelasting al bezet is. We raden u af om lage frequentie te gebruiken voor app-consistente herstelpunten voor niet-databaseworkloads. Zelfs voor database workload, 1 uur is genoeg.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Wat is de minimale frequentie van de generatie van toepassingsconsistente herstelpunten?

Site recovery kan een toepassingsconsistent herstelpunt maken met een minimale frequentie van 1 uur.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hoe worden herstelpunten gegenereerd en opgeslagen?

Als u wilt begrijpen hoe siteherstelherstelpunten genereert, u een voorbeeld van een replicatiebeleid bekijken. Dit replicatiebeleid heeft een herstelpunt met een bewaarvenster van 24 uur en een app-consistente frequentiemomentopname van 1 uur.

Site Recovery creëert elke 5 minuten een crashconsistent herstelpunt. Je deze frequentie niet veranderen. Voor het laatste uur u kiezen uit 12 crash-consistent punten en 1 app-consistent punt. Naarmate de tijd vordert, snoeit Site Recovery alle herstelpunten na het laatste uur en bespaart slechts 1 herstelpunt per uur.

De volgende schermafbeelding illustreert het voorbeeld. In de screenshot:

- In het afgelopen uur zijn er herstelpunten met een frequentie van 5 minuten.
- Na het afgelopen uur houdt Site Recovery slechts 1 herstelpunt.

   ![Lijst van gegenereerde herstelpunten](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Hoe ver terug kan ik herstellen?

Het oudste herstelpunt dat u gebruiken is 72 uur.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Ik heb een replicatiebeleid van 24 uur. Wat gebeurt er als een probleem voorkomt dat Site Recovery herstelpunten voor meer dan 24 uur genereert? Gaan mijn eerdere herstelpunten verloren?

Nee, Site Recovery houdt al uw eerdere herstelpunten. Afhankelijk van het retentievenster van de herstelpunten vervangt Site Recovery het oudste punt alleen als het nieuwe punten genereert. Vanwege het probleem kan Site Recovery geen nieuwe herstelpunten genereren. Totdat er nieuwe herstelpunten zijn, blijven alle oude punten over nadat u het venster van retentie hebt bereikt.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Hoe wijzig ik het replicatiebeleid nadat replicatie is ingeschakeld op een virtuele machine?

Ga naar het replicatiebeleid voor**herstelinfrastructuur** > van **siteherstel** > **van siteherstel .** Selecteer het beleid dat u wilt bewerken en sla de wijzigingen op. Elke wijziging is ook van toepassing op alle bestaande replicaties.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Zijn alle herstelpunten een volledige kopie van de VM of een differentieel?

Het eerste herstelpunt dat wordt gegenereerd, heeft de volledige kopie. Alle opeenvolgende herstelpunten hebben deltawijzigingen.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Verhoogt het verhogen van de bewaarperiode van herstelpunten de opslagkosten?

Ja, als u de bewaartermijn verhoogt van 24 uur naar 72 uur, slaat Site Recovery de herstelpunten nog eens 48 uur op. De extra tijd brengt opslagkosten met zich mee. Een enkel herstelpunt kan bijvoorbeeld deltawijzigingen van 10 GB hebben met een kosten per GB van $ 0,16 per maand. Extra kosten zou zijn $1.60 × 48 per maand.

## <a name="multi-vm-consistency"></a>Consistentie met meerdere vm's

### <a name="what-is-multi-vm-consistency"></a>Wat is multi-VM-consistentie?

Multi-VM-consistentie zorgt ervoor dat het herstelpunt consistent is voor alle gerepliceerde virtuele machines.

Siteherstel biedt een **consistentieoptie voor meerdere vm's,** waarmee een replicatiegroep van alle machines wordt gemaakt.

Wanneer u niet over de virtuele machines, zullen ze gedeelde crash-consistente en app-consistente herstelpunten.

Ga door de zelfstudie om [consistentie met meerdere VM's in](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm)te schakelen.

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Kan ik falen over één virtuele machine binnen een replicatiegroep met meerdere VM-consistenties?

Wanneer u de **consistentieoptie Multi-VM** selecteert, geeft u aan dat de toepassing afhankelijk is van alle virtuele machines binnen een groep. Single virtual machine failover is niet toegestaan.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Hoeveel virtuele machines kan ik repliceren als onderdeel van een replicatiegroep voor multi-VM-consistentie?

U 16 virtuele machines samen repliceren in een replicatiegroep.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Wanneer moet ik multi-VM-consistentie inschakelen?

Omdat multi-VM-consistentie CPU-intensief is, kan dit de prestaties van de werkbelasting beïnvloeden. Gebruik consistentie met meerdere VM's alleen als machines dezelfde werkbelasting uitvoeren en u consistentie nodig hebt voor meerdere machines. Als u bijvoorbeeld twee SQL Server-exemplaren en twee webservers in een toepassing hebt, moet u alleen multi-VM-consistentie hebben voor de SQL Server-exemplaren.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>u een reeds replicerende VM toevoegen aan een replicatiegroep?

U een vm toevoegen aan een nieuwe replicatiegroep terwijl u replicatie inschakelt. U ook een vm toevoegen aan een bestaande replicatiegroep terwijl u replicatie inschakelt. U echter geen reeds replicerende vm toevoegen aan een nieuwe replicatiegroep of bestaande replicatiegroep.

## <a name="failover"></a>Failover

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Hoe wordt de capaciteit in de doelregio voor Azure VM's gewaarborgd?

Het team SiteHerstel en het Azure-capaciteitsbeheerteam plannen voor voldoende infrastructuurcapaciteit. Wanneer u een failover start, zorgen de teams er ook voor dat VM-exemplaren die worden beschermd door Site Recovery worden geïmplementeerd in de doelregio.

### <a name="is-failover-automatic"></a>Vindt failover automatisch plaats?

Failover wordt niet automatisch uitgevoerd. U failovers starten met één klik in de portal of u [PowerShell](azure-to-azure-powershell.md) gebruiken om een failover te activeren.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>Kan ik een openbaar IP-adres bewaren na een failover?

U het openbare IP-adres van de productietoepassing niet behouden na een failover.

Wanneer u een werkbelasting weerbrengt als onderdeel van het failoverproces, moet u een openbare IP-bron van Azure toewijzen aan de werkbelasting. De openbare IP-bron van Azure moet beschikbaar zijn in de doelregio. U de openbare IP-bron van Azure handmatig toewijzen of u deze automatiseren met een herstelplan. Meer informatie over het [instellen van openbare IP-adressen na failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>Kan ik een privé-IP-adres bewaren tijdens een failover?

Ja, u een privé-IP-adres behouden. Wanneer u noodherstel voor Azure VM's inschakelt, maakt Site Recovery standaard doelbronnen op basis van bronbroninstellingen. Voor Azure Virtual Machines die zijn geconfigureerd met statische IP-adressen, probeert Site Recovery hetzelfde IP-adres voor de doel-vm in te richten als deze niet in gebruik is.
Meer informatie over [het behouden van IP-adressen tijdens failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Waarom krijgt de server na een failover een nieuw IP-adres toegewezen?

Site recovery probeert het IP-adres op te geven op het moment van failover. Als een andere virtuele machine dat adres inneemt, stelt Site Recovery het volgende beschikbare IP-adres in als doel.

Meer informatie over [het instellen van netwerktoewijzing en IP-adressering voor virtuele netwerken](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Wat zijn **laatste (laagste RPO)** herstelpunten?

De **laatste optie (laagste RPO)** verwerkt eerst alle gegevens die naar de siteherstel zijn verzonden. Nadat de service de gegevens heeft verwerkt, wordt voor elke vm een herstelpunt gemaakt voordat deze niet wordt overgedragen aan de VM. Deze optie biedt de laagste doelstelling voor herstelpunten (RPO). De VM die na failover is gemaakt, heeft alle gegevens gerepliceerd naar Site recovery vanaf het moment dat de failover werd geactiveerd.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Hebben **de laatste (laagste RPO)** herstelpunten een impact op failover RTO?

Ja. Site Recovery verwerkt alle gegevens in behandeling voordat deze niet is mislukt, dus deze optie heeft een hogere hersteltijddoelstelling (RTO) in vergelijking met andere opties.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Wat betekent **de laatste verwerkte** optie in herstelpunten?

De **laatst verwerkte** optie mislukt over alle VM's in het plan tot het laatste herstelpunt dat Site Recovery heeft verwerkt. Als u het nieuwste herstelpunt voor een specifieke virtuele machine wilt bekijken, schakelt u **De laatste herstelpunten** in de VM-instellingen in. Deze optie biedt een lage RTO, omdat er geen tijd wordt besteed aan het verwerken van onverwerkte gegevens.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Wat gebeurt er als mijn primaire regio een onverwachte storing ondervindt?

U een failover activeren na de storing. Site herstel heeft geen connectiviteit van de primaire regio nodig om de failover uit te kunnen doen.

### <a name="what-is-an-rto-of-a-vm-failover"></a>Wat is een RTO van een VM-failover?

Site Recovery heeft een [RTO SLA van 2 uur.](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) Echter, de meeste van de tijd, Site Recovery mislukt over virtuele machines binnen enkele minuten. U de RTO berekenen door naar de failovertaken te gaan, die de tijd laten zien die nodig was om de VM ter sprake te brengen. Raadpleeg voor Herstelplan RTO de volgende sectie.

## <a name="recovery-plans"></a>Herstelplannen

### <a name="what-is-a-recovery-plan"></a>Wat is een herstelplan?

Een herstelplan in Site Recovery orkestreert het failoverherstel van VM's. Het helpt het herstel consistent nauwkeurig, herhaalbaar en geautomatiseerd te maken. Een herstelplan voorkomt op de volgende behoeften:

- Een groep virtuele machines definiëren die samen mislukken
- Het definiëren van de afhankelijkheden tussen virtuele machines, zodat de toepassing komt nauwkeurig
- Het automatiseren van het herstel samen met aangepaste handmatige acties om andere taken dan de failover van virtuele machines te bereiken

Meer informatie [over het maken van herstelplannen](site-recovery-create-recovery-plans.md).

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Hoe wordt sequencing bereikt in een herstelplan?

In een herstelplan u meerdere groepen maken om sequencing te bereiken. Elke groep faalt in één keer. Virtuele machines die deel uitmaken van dezelfde groep mislukken samen, gevolgd door een andere groep. Zie [Over herstelplannen](recovery-plan-overview.md#model-apps)voor meer informatie over het modelleren van een toepassing met behulp van een herstelplan.

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hoe kan ik de RTO van een herstelplan vinden?

Als u de RTO van een herstelplan wilt controleren, doet u een testfailover voor het herstelplan en gaat u naar **sitehersteltaken.**
Zie in het volgende voorbeeld de taak **SAPTestRecoveryPlan**. De taak duurde 8 minuten en 59 seconden om te mislukken over alle virtuele machines en doen specifieke acties.

![Lijst met sitehersteltaken](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Kan ik automatiseringsloopboeken toevoegen aan het herstelplan?

Ja, u Azure Automation-runbooks integreren in uw herstelplan. Meer informatie over [het toevoegen van Azure Automation-runbooks](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Herbescherming en failback

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Ik faalde van de primaire regio naar een ramp herstel regio. Worden VM's in een DR-regio automatisch beschermd?

Nee. Wanneer u [niet meer dan](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) Azure VM's van de ene regio naar de andere, de VM's opstarten in de DR-regio in een onbeveiligde status. Als u de VM's naar het primaire gebied wilt terugsturen, moet u de VM's in het secundaire gebied [opnieuw beveiligen.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect)

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Repliceert Site Recovery op het moment van herbeveiliging volledige gegevens van het secundaire gebied naar de primaire regio?

Het hangt af van de situatie. Als de vm van het brongebied bestaat, worden alleen wijzigingen tussen de bronschijf en de doelschijf gesynchroniseerd. Site Recovery berekent de verschillen door de schijven te vergelijken en vervolgens de gegevens over te brengen. Dit proces duurt meestal een paar uur. Zie [Reprotect is mislukt voor Azure VM-exemplaren naar het primaire gebied voor](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)meer informatie over wat er tijdens de herbeveiliging gebeurt.

### <a name="how-much-time-does-it-take-to-fail-back"></a>Hoelang duurt het uitvoeren van een failback?

Na herbescherming neemt failback ongeveer evenveel tijd in beslag om van het primaire gebied naar een secundaire regio te mislukken.

## <a name="capacity"></a><a name="capacity"></a>Capaciteit

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Hoe wordt de capaciteit in de doelregio voor Azure VM's gewaarborgd?

Het team SiteHerstel en het Azure-capaciteitsbeheerteam plannen voor voldoende infrastructuurcapaciteit. Wanneer u een failover start, zorgen de teams er ook voor dat VM-exemplaren die worden beschermd door Site Recovery worden geïmplementeerd in de doelregio.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Werkt siteherstel met gereserveerde exemplaren?

Ja, u [gereserveerde Azure VM's](https://azure.microsoft.com/pricing/reserved-vm-instances/) kopen in het gebied van noodherstel en failoverbewerkingen voor siteherstel worden gebruikt. Er is geen extra configuratie nodig.

## <a name="security"></a>Beveiliging

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Worden er replicatiegegevens verzonden naar de Site Recovery-service?

Nee, Site herstel onderschept geen gerepliceerde gegevens en er is geen informatie over wat er op uw VM's wordt uitgevoerd. Alleen de metagegevens die nodig zijn om replicatie en failover te organiseren, worden naar de Site Recovery-service verzonden.

Site Recovery is ISO 27001:2013, 27018, HIPAA en DPA gecertificeerd. De dienst ondergaat SOC2 en FedRAMP JAB beoordelingen.

### <a name="does-site-recovery-encrypt-replication"></a>Wordt replicatie met Site Recovery versleuteld?

Ja, zowel versleuteling in transit als [versleuteling in rust in Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) worden ondersteund.

## <a name="next-steps"></a>Volgende stappen

- [Controleer azure-naar-Azure-ondersteuningsvereisten](azure-to-azure-support-matrix.md).
- [Azure-naar-Azure-replicatie instellen](azure-to-azure-tutorial-enable-replication.md).
- Als je vragen hebt na het lezen van dit artikel, plaats ze dan op het [Azure Recovery Services-forum.](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)
