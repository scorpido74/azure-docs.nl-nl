---
title: Veelgestelde vragen over herstel na nood gevallen voor Azure VM met Azure Site Recovery
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure VM-nood herstel wanneer u Azure Site Recovery gebruikt.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 7d3bcc32dc8f1412a5adbc175a5f8618628bce83
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80547891"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Veelgestelde vragen: nood herstel voor Azure naar Azure

In dit artikel vindt u antwoorden op veelgestelde vragen over herstel na nood gevallen van virtuele Azure-machines naar een andere Azure-regio voor wanneer u [Azure site Recovery](site-recovery-overview.md)gebruikt.

## <a name="general"></a>Algemeen

### <a name="how-is-site-recovery-priced"></a>Hoe wordt Site Recovery geprijsd?

Bekijk [Azure site Recovery prijzen voor vm's](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Hoe werkt de gratis laag voor Azure Site Recovery?

Elk exemplaar dat wordt beveiligd met Azure Site Recovery is gedurende de eerste 31 dagen gratis beschermd. Na deze periode heeft de beveiliging voor elke instantie de tarieven van [Azure site Recovery prijzen voor Azure virtual machines](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Worden er gedurende de eerste 31 dagen andere Azure-kosten in rekening gebracht?

Ja. Hoewel Azure Site Recovery gratis is gedurende de eerste 31 dagen van een beschermd exemplaar, worden er mogelijk kosten in rekening gebracht voor Azure Storage, opslag transacties en gegevens overdrachten. Een herstelde virtuele machine kan ook Azure-reken kosten in rekening worden gebracht. Bekijk de volledige informatie over de prijzen van [Azure site Recovery prijzen](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Wat zijn de aanbevolen procedures voor Azure Virtual Machines herstel na nood gevallen?

1. [Meer informatie over Azure-naar-Azure-architectuur](azure-to-azure-architecture.md)
1. [Controleer de ondersteunde en niet-ondersteunde configuraties](azure-to-azure-support-matrix.md)
1. [Herstel na nood geval instellen voor virtuele Azure-machines](azure-to-azure-how-to-enable-replication.md)
1. [Een testfailover uitvoeren](azure-to-azure-tutorial-dr-drill.md)
1. [Failover en failback naar de primaire regio](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Hoe is de capaciteit gegarandeerd in de doel regio?

Het team van de Site Recovery-team en het Azure Capacity Management-abonnement voor voldoende infrastructuur capaciteit. Wanneer u een failover start, kunnen de teams er ook voor zorgen dat VM-exemplaren die worden beveiligd door Site Recovery worden geïmplementeerd in de doel regio.

## <a name="replication"></a>Replicatie

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Kan ik Vm's die zijn ingeschakeld via Azure Disk Encryption repliceren?

Ja. Site Recovery ondersteunt herstel na nood gevallen van Vm's waarvoor Azure Disk Encryption is ingeschakeld. Wanneer u replicatie inschakelt, kopieert Azure alle vereiste schijf versleutelings sleutels en geheimen van de bron regio naar de doel regio in de gebruikers context. Als u niet over de juiste machtigingen beschikt, kan uw beveiligings beheerder een script gebruiken om de sleutels en geheimen te kopiëren.

- Site Recovery ondersteunt Azure Disk Encryption voor virtuele Azure-machines waarop Windows wordt uitgevoerd.
- Site Recovery ondersteunt Azure Disk Encryption versie 0,1, die een schema heeft waarvoor Azure Active Directory (Azure AD) is vereist. Site Recovery biedt ook ondersteuning voor versie 1,1, waarvoor Azure AD niet is vereist. Meer [informatie over het extensie schema voor Azure Disk Encryption](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - Voor Azure Disk Encryption versie 1,1 moet u de virtuele Windows-machines met beheerde schijven gebruiken.
  - [Meer informatie](azure-to-azure-how-to-enable-replication-ade-vms.md) over het inschakelen van replicatie voor versleutelde vm's.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Kan ik virtuele machines repliceren naar een ander abonnement?

Ja, u kunt virtuele Azure-machines repliceren naar een ander abonnement binnen dezelfde Azure AD-Tenant.

Herstel na nood geval voor [abonnementen](https://azure.microsoft.com/blog/cross-subscription-dr) configureren door een ander abonnement te selecteren op het moment van replicatie.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Kan ik zone-vastgemaakte virtuele Azure-machines repliceren naar een andere regio?

Ja, u kunt door een [zone vastgemaakte vm's repliceren](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) naar een andere regio.

### <a name="can-i-exclude-disks"></a>Kan ik schijven uitsluiten?

Ja, u kunt schijven op het moment van beveiliging uitsluiten met behulp van Power shell. Zie [schijven uitsluiten van replicatie](azure-to-azure-exclude-disks.md)voor meer informatie.

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Kan ik nieuwe schijven toevoegen aan gerepliceerde Vm's en replicatie inschakelen?

Ja, het toevoegen van nieuwe schijven aan gerepliceerde Vm's en het inschakelen van replicatie voor ze wordt ondersteund voor virtuele Azure-machines met beheerde schijven. Wanneer u een nieuwe schijf toevoegt aan een virtuele machine van Azure waarvoor replicatie is ingeschakeld, wordt er een waarschuwing weer gegeven voor de replicatie status voor de virtuele machine. Met deze waarschuwing wordt aangegeven dat een of meer schijven op de virtuele machine beschikbaar zijn voor beveiliging. U kunt replicatie inschakelen voor toegevoegde schijven.

- Als u beveiliging inschakelt voor de toegevoegde schijven, verdwijnt de waarschuwing na de initiële replicatie.
- Als u geen replicatie inschakelt voor de schijf, kunt u de waarschuwing sluiten.
- Als u een failover van een VM met een toegevoegde schijf en replicatie hebt ingeschakeld, zijn er replicatie punten. Op de replicatie punten worden de schijven weer gegeven die beschikbaar zijn voor herstel.

Stel bijvoorbeeld dat een virtuele machine één schijf heeft en u een nieuwe hebt toegevoegd. Er is mogelijk een replicatie punt dat is gemaakt voordat u de schijf hebt toegevoegd. Dit replicatie punt geeft aan dat het bestaat uit "1 van 2 schijven".

Site Recovery biedt geen ondersteuning voor ' hot remove ' van een schijf van een gerepliceerde VM. Als u een VM-schijf verwijdert, moet u de replicatie voor de virtuele machine uitschakelen en opnieuw inschakelen.

### <a name="how-often-can-i-replicate-to-azure"></a>Hoe vaak kan ik repliceren naar Azure?

Replicatie is doorlopend wanneer u virtuele Azure-machines naar een andere Azure-regio repliceert. Zie de [Azure-to-Azure-replicatie architectuur](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process)voor meer informatie.

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Kan ik virtuele machines binnen een regio repliceren? Ik heb deze functionaliteit nodig om Vm's te migreren.

U kunt geen Azure-naar-Azure-oplossing voor schijf herstel gebruiken om Vm's binnen een regio te repliceren.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Kan ik VM-exemplaren repliceren naar een Azure-regio?

Met Site Recovery kunt u virtuele machines repliceren en herstellen tussen twee regio's binnen hetzelfde geografische cluster. Geografische clusters worden gedefinieerd met gegevens latentie en soevereiniteit. Zie de [ondersteunings matrix](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)voor de site Recovery regio voor meer informatie.

### <a name="does-site-recovery-require-internet-connectivity"></a>Is Site Recovery Internet verbinding vereist?

Nee, Site Recovery vereist geen Internet verbinding. Maar hiervoor is toegang tot Site Recovery-Url's en IP-adresbereiken vereist, zoals vermeld in [netwerken in azure VM nood herstel](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>Kan ik een toepassing repliceren die een afzonderlijke resource groep voor afzonderlijke lagen heeft?

Ja, u kunt de toepassing repliceren en de configuratie voor herstel na nood gevallen ook in een afzonderlijke resource groep laten staan.

Als uw toepassing bijvoorbeeld de toepassing, de data base en het web van elke laag in een afzonderlijke resource groep bevat, moet u de [wizard replicatie](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) drie keer selecteren om alle lagen te beveiligen. Site Recovery worden deze drie lagen gerepliceerd naar drie verschillende resource groepen.

## <a name="replication-policy"></a>Beleid voor replicatie

### <a name="what-is-a-replication-policy"></a>Wat is een replicatie beleid?

Een replicatie beleid definieert de instellingen voor de Bewaar geschiedenis van herstel punten. Het beleid definieert ook de frequentie van app-consistente moment opnamen. Azure Site Recovery maakt standaard een nieuw replicatie beleid met de standaard instellingen van:

- 24 uur voor de Bewaar geschiedenis van herstel punten.
- 60 minuten voor de frequentie van app-consistente moment opnamen.

Meer [informatie over replicatie-instellingen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Wat is een crash-consistent herstel punt?

Een crash consistent herstel punt heeft de gegevens op de schijf alsof u tijdens de moment opname het netsnoer van de server hebt opgehaald. Het crash-consistente herstel punt bevat geen items die zich in het geheugen bevonden toen de moment opname werd gemaakt.

Vandaag kunnen de meeste toepassingen goed worden hersteld met crash-consistente moment opnamen. Een crash-consistent herstel punt is doorgaans voldoende voor geen database besturingssystemen en-toepassingen, zoals bestands servers, DHCP-servers en afdruk servers.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Wat is de frequentie van crash-consistente herstel punten genereren?

Site Recovery maakt om de vijf minuten een crash-consistent herstel punt.

### <a name="what-is-an-application-consistent-recovery-point"></a>Wat is een toepassings consistent herstel punt?

Toepassings consistente herstel punten worden gemaakt op basis van toepassings consistente moment opnamen. Toepassings consistente herstel punten nemen dezelfde gegevens op als crash-consistente moment opnamen terwijl ook gegevens worden vastgelegd in het geheugen en alle trans acties die worden uitgevoerd.

Vanwege hun extra inhoud zijn toepassings consistente moment opnamen het meest betrokken en nemen ze het langst mee. We raden toepassings consistente herstel punten aan voor database besturingssystemen en-toepassingen, zoals SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Wat is de impact van toepassings consistente herstel punten op de prestaties van toepassingen?

Met toepassings consistente herstel punten worden alle gegevens in het geheugen en in verwerking vastgelegd. Omdat herstel punten die gegevens vastleggen, vereisen ze Framework als Volume Shadow Copy Service in Windows om de toepassing stil te leggen. Als het vastleggen veelvuldig is, kan dit van invloed zijn op de prestaties wanneer de werk belasting al bezet is. Het is niet raadzaam om lage frequentie te gebruiken voor app-consistente herstel punten voor niet-data base-workloads. Zelfs voor de werk belasting van de data base is 1 uur voldoende.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Wat is de minimale frequentie voor het genereren van toepassings consistente herstel punten?

Site Recovery kunt een toepassings consistent herstel punt maken met een minimale frequentie van 1 uur.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hoe worden herstel punten gegenereerd en opgeslagen?

Laten we een voor beeld van een replicatie beleid zien om te begrijpen hoe Site Recovery herstel punten genereert. Dit replicatie beleid heeft een herstel punt met een Bewaar periode van 24 uur en een app-consistente frequentie momentopname van 1 uur.

Site Recovery maakt om de vijf minuten een crash-consistent herstel punt. U kunt deze frequentie niet wijzigen. Voor het afgelopen uur kunt u kiezen uit 12 crash-consistente punten en één app-consistent punt. Na verloop van tijd worden Site Recovery alle herstel punten na het laatste uur verwijderd en wordt er slechts één herstel punt per uur opgeslagen.

De volgende scherm afbeelding illustreert het voor beeld. In de scherm afbeelding:

- In het afgelopen uur zijn er herstel punten met een frequentie van 5 minuten.
- Voorbij het afgelopen uur Site Recovery slechts één herstel punt behouden.

   ![Lijst met gegenereerde herstel punten](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Hoe ver terug kan ik herstellen?

Het oudste herstel punt dat u kunt gebruiken, is 72 uur.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Ik heb een replicatie beleid van 24 uur. Wat gebeurt er als een probleem verhindert dat Site Recovery meer dan 24 uur herstel punten genereert? Gaan mijn vorige herstel punten verloren?

Nee, Site Recovery blijven al uw vorige herstel punten bewaard. Afhankelijk van het retentie venster voor herstel punten wordt Site Recovery het oudste punt alleen vervangen als nieuwe punten worden gegenereerd. Vanwege het probleem kunnen Site Recovery geen nieuwe herstel punten genereren. Totdat er nieuwe herstel punten zijn, blijven alle oude punten aanwezig nadat u het venster van bewaren hebt bereikt.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Hoe kan ik het replicatie beleid wijzigen nadat de replicatie is ingeschakeld op een VM?

Ga naar **site Recovery kluis** > **site Recovery infrastructuur** > **beleid voor replicatie**. Selecteer het beleid dat u wilt bewerken en sla de wijzigingen op. Elke wijziging is ook van toepassing op alle bestaande replicaties.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Zijn alle herstel punten een volledige kopie van de virtuele machine of een differentieel exemplaar?

Het eerste herstel punt dat wordt gegenereerd, heeft de volledige kopie. Eventuele opeenvolgende herstel punten bevatten Delta wijzigingen.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Verhoogt de Bewaar periode van herstel punten de opslag kosten?

Ja, als u de retentie periode van 24 uur tot 72 uur verhoogt, worden de herstel punten door Site Recovery voor een extra 48 uur opgeslagen. De toegevoegde tijd maakt kosten voor opslag. Eén herstel punt kan bijvoorbeeld Delta wijzigingen van 10 GB hebben, met een kosten per GB van $0,16 per maand. Meer kosten zijn $1,60 × 48 per maand.

## <a name="multi-vm-consistency"></a>Multi-VM-consistentie

### <a name="what-is-multi-vm-consistency"></a>Wat is consistentie tussen meerdere VM'S?

Met consistentie tussen meerdere VM'S zorgt u ervoor dat het herstel punt consistent is op alle gerepliceerde virtuele machines.

Site Recovery biedt een optie voor de **consistentie van meerdere vm's** , waarmee een replicatie groep van alle computers wordt gemaakt.

Wanneer u een failover voor de virtuele machines maakt, hebben ze gedeelde crash-consistente en toepassings consistente herstel punten.

Door loop de zelf studie om [multi-VM-consistentie in te scha kelen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Kan ik een failover uitvoeren voor één virtuele machine in een multi-VM-consistentie replicatie groep?

Wanneer u de optie **consistentie van meerdere vm's** selecteert, weet u dat de toepassing afhankelijk is van alle virtuele machines binnen een groep. Failover van één virtuele machine is niet toegestaan.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Hoeveel virtuele machines kan ik repliceren als onderdeel van een multi-VM-consistentie replicatie groep?

U kunt 16 virtuele machines samen repliceren in een replicatie groep.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Wanneer moet ik de consistentie tussen meerdere VM'S inschakelen?

Omdat de consistentie tussen meerdere VM'S CPU-intensief is, kan dit van invloed zijn op de prestaties van de werk belasting. Gebruik alleen de consistentie tussen meerdere VM'S als op computers dezelfde werk belasting wordt uitgevoerd en u consistentie op meerdere computers nodig hebt. Als u bijvoorbeeld twee SQL Server exemplaren en twee webservers in een toepassing hebt, moet u alleen multi-VM-consistentie hebben voor de SQL Server exemplaren.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>Kunt u een al replicerende virtuele machine aan een replicatie groep toevoegen?

U kunt een virtuele machine toevoegen aan een nieuwe replicatie groep tijdens het inschakelen van replicatie. U kunt ook een virtuele machine toevoegen aan een bestaande replicatie groep tijdens het inschakelen van replicatie. U kunt echter geen reeds replicerende virtuele machine toevoegen aan een nieuwe replicatie groep of bestaande replicatie groep.

## <a name="failover"></a>Failover

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Hoe is de capaciteit gegarandeerd in de doel regio voor virtuele Azure-machines?

Het team van de Site Recovery-team en het Azure Capacity Management-abonnement voor voldoende infrastructuur capaciteit. Wanneer u een failover start, kunnen de teams er ook voor zorgen dat VM-exemplaren die worden beveiligd door Site Recovery worden geïmplementeerd in de doel regio.

### <a name="is-failover-automatic"></a>Vindt failover automatisch plaats?

Failover wordt niet automatisch uitgevoerd. U kunt failovers starten met één klik in de portal of u kunt [Power shell](azure-to-azure-powershell.md) gebruiken om een failover te activeren.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>Kan ik een openbaar IP-adres na een failover houden?

U kunt het open bare IP-adres van de productie toepassing niet hand haven na een failover.

Wanneer u een werk belasting ophaalt als onderdeel van het failover-proces, moet u een open bare Azure IP-resource toewijzen aan de workload. De open bare IP van Azure-resource moet beschikbaar zijn in de doel regio. U kunt de open bare Azure IP-resource hand matig toewijzen of u kunt deze automatiseren met een herstel plan. Meer informatie over het [instellen van open bare IP-adressen na een failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>Kan ik een privé-IP-adres houden tijdens een failover?

Ja, u kunt een privé-IP-adres gebruiken. Wanneer u herstel na nood gevallen voor virtuele Azure-machines inschakelt, maakt Site Recovery doel resources op basis van bron bron instellingen. Voor Azure Virtual Machines geconfigureerd met statische IP-adressen, Site Recovery probeert hetzelfde IP-adres in te richten voor de doel-VM als deze niet in gebruik is.
Meer informatie over het [bewaren van IP-adressen tijdens een failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Waarom is er na een failover een nieuw IP-adres toegewezen aan de server?

Site Recovery probeert het IP-adres op te geven op het moment van de failover. Als het adres van een andere virtuele machine wordt bereikt, Site Recovery het volgende beschik bare IP-adres als doel instellen.

Meer informatie over het [instellen van netwerk toewijzing en IP-adres sering voor virtuele netwerken](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Wat zijn de **meest recente (laagste RPO)** herstel punten?

De **nieuwste (laagste RPO)** optie verwerkt eerst alle gegevens die naar de site Recovery zijn verzonden. Nadat de gegevens zijn verwerkt door de service, maakt deze een herstel punt voor elke VM voordat failover wordt uitgevoerd naar de virtuele machine. Deze optie biedt de laagste Recovery Point Objective (RPO). De virtuele machine die is gemaakt na een failover, bevat alle gegevens die worden gerepliceerd naar Site Recovery vanaf het moment dat de failover werd geactiveerd.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>De **meest recente (laagste RPO)** herstel punten hebben invloed op de FAILOVER-RTO?

Ja. Site Recovery alle in behandeling zijnde gegevens verwerkt voordat de failover wordt uitgevoerd. deze optie heeft daarom een hogere herstel tijd (RTO) ten opzichte van andere opties.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Wat betekent de **meest recente verwerkte** optie in herstel punten?

De **meest recente verwerkings** optie mislukt via alle virtuele machines in het plan naar het laatste herstel punt dat site Recovery verwerkt. Als u het meest recente herstel punt voor een specifieke virtuele machine wilt zien, controleert u de **meest recente herstel punten** in de VM-instellingen. Deze optie biedt een laag RTO, omdat er geen tijd wordt besteed aan het verwerken van niet-verwerkte gegevens.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Wat gebeurt er als mijn primaire regio een onverwachte onderbreking ondervindt?

U kunt een failover activeren na de storing. Site Recovery hebt geen verbinding nodig van de primaire regio om de failover uit te voeren.

### <a name="what-is-an-rto-of-a-vm-failover"></a>Wat is een RTO van een VM-failover?

Site Recovery heeft een [RTO-sla van twee uur](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). De meeste tijd, Site Recovery failover van virtuele machines binnen enkele minuten. U kunt de RTO berekenen door te gaan naar de failover-taken, die de tijd weer geven die nodig is om de virtuele machine te openen. Raadpleeg de volgende sectie voor het RTO van het herstel plan.

## <a name="recovery-plans"></a>Herstelplannen

### <a name="what-is-a-recovery-plan"></a>Wat is een herstel plan?

Een herstel plan in Site Recovery is het herstel van failover van Vm's. Zo kunt u de herstel bewerking consistent maken, herhaalbaar en geautomatiseerd. Een herstel plan is gericht op de volgende behoeften:

- Een groep virtuele machines definiëren die samen een failover uitvoeren
- De afhankelijkheden tussen virtuele machines definiëren zodat de toepassing nauw keurig is
- Het herstel automatiseren samen met aangepaste hand matige acties voor het uitvoeren van andere taken dan de failover van virtuele machines

Meer informatie [over het maken van herstel plannen](site-recovery-create-recovery-plans.md).

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Hoe wordt sequentiëren in een herstel plan behaald?

In een herstel plan kunt u meerdere groepen maken om sequentiëren te krijgen. Voor elke groep wordt een failover in één keer uitgevoerd. Voor virtuele machines die deel uitmaken van dezelfde groep, wordt een failover uitgevoerd, gevolgd door een andere groep. Zie [over herstel plannen](recovery-plan-overview.md#model-apps)voor meer informatie over het model leren van een toepassing met behulp van een herstel plan.

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hoe kan ik de RTO van een herstel plan vinden?

Als u de RTO van een herstel plan wilt controleren, voert u een testfailover uit voor het herstel plan en gaat u naar **site Recovery taken**.
In het volgende voor beeld ziet u de taak **SAPTestRecoveryPlan**. De taak duurde acht minuten en 59 seconden voor de failover van alle virtuele machines en voert specifieke acties uit.

![Lijst met Site Recovery taken](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Kan ik Automation-runbooks toevoegen aan het herstel plan?

Ja, u kunt Azure Automation runbooks integreren in uw herstel plan. Meer informatie over het [toevoegen van Azure Automation runbooks](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Opnieuw beveiligen en failback

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Ik heb een failover van de primaire regio naar een nood herstel regio uitgevoerd. Worden Vm's in een DR-regio automatisch beveiligd?

Nee. Wanneer u virtuele Azure-machines van de ene regio naar de andere doorwerkt, worden de Vm's in de DR-regio in een [niet](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) -beveiligde status opgestart. Als u een failback wilt uitvoeren voor de virtuele machines naar de primaire regio, moet u de virtuele machines in de secundaire regio opnieuw [beveiligen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) .

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Wordt op het moment van de herbeveiliging de volledige gegevens van de secundaire regio naar de primaire regio Site Recovery gerepliceerd?

Dit is afhankelijk van de situatie. Als er een VM van de bron regio bestaat, worden alleen de wijzigingen tussen de bron schijf en de doel schijf gesynchroniseerd. Site Recovery de verschillen berekent door de schijven te vergelijken en vervolgens de gegevens over te zetten. Dit proces duurt meestal enkele uren. Zie voor meer informatie over wat er gebeurt tijdens de herbeveiliging, voor [het opnieuw beveiligen van mislukte Azure VM-instanties naar de primaire regio](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Hoelang duurt het uitvoeren van een failback?

Na de herbeveiliging neemt failback ongeveer dezelfde hoeveelheid tijd in beslag voor failover van de primaire regio naar een secundaire regio.

## <a name="capacity"></a><a name="capacity"></a>Capaciteit

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Hoe is de capaciteit gegarandeerd in de doel regio voor virtuele Azure-machines?

Het team van de Site Recovery-team en het Azure Capacity Management-abonnement voor voldoende infrastructuur capaciteit. Wanneer u een failover start, kunnen de teams er ook voor zorgen dat VM-exemplaren die worden beveiligd door Site Recovery worden geïmplementeerd in de doel regio.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Werkt Site Recovery met gereserveerde instanties?

Ja, u kunt [gereserveerde Azure-vm's](https://azure.microsoft.com/pricing/reserved-vm-instances/) kopen in het gebied voor herstel na nood gevallen en site Recovery failover-bewerkingen worden gebruikt. Er is geen aanvullende configuratie nodig.

## <a name="security"></a>Beveiliging

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Worden er replicatiegegevens verzonden naar de Site Recovery-service?

Nee, Site Recovery worden geen gerepliceerde gegevens onderschept en er is geen informatie over wat er op uw Vm's wordt uitgevoerd. Alleen de metagegevens die nodig zijn om replicatie en failover te organiseren, worden naar de Site Recovery-service verzonden.

Site Recovery is ISO 27001:2013, 27018, HIPAA en DPA Certified. De service ondergaat SOC2-en FedRAMP JAB-evaluaties.

### <a name="does-site-recovery-encrypt-replication"></a>Wordt replicatie met Site Recovery versleuteld?

Ja, de versleuteling van door Voer en [versleuteling op rest in azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen

- [Bekijk de vereisten voor Azure-naar-Azure-ondersteuning](azure-to-azure-support-matrix.md).
- [Azure-naar-Azure-replicatie instellen](azure-to-azure-tutorial-enable-replication.md).
- Als u vragen hebt nadat u dit artikel hebt gelezen, plaatst u het in het [Azure Recovery Services-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
