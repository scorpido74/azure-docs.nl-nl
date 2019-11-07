---
title: Veelgestelde vragen over herstel na nood gevallen voor Azure VM met Azure Site Recovery
description: In dit artikel vindt u antwoorden op veelgestelde vragen over herstel na nood gevallen van virtuele Azure-machines naar een andere Azure-regio met Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 04/29/2019
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 5ed501a9f11e790bcc2196d57c6479beb54f1a17
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621073"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Veelgestelde vragen: nood herstel voor Azure naar Azure

In dit artikel vindt u antwoorden op veelgestelde vragen over herstel na nood gevallen van virtuele Azure-machines naar een andere Azure-regio met behulp van [site Recovery](site-recovery-overview.md). 


## <a name="general"></a>Algemeen

### <a name="how-is-site-recovery-priced"></a>Hoe wordt Site Recovery geprijsd?
Bekijk de [Azure site Recovery prijs](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) gegevens.
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Hoe werkt de gratis laag voor Azure Site Recovery-werk?
Elk exemplaar dat wordt beschermd met Azure Site Recovery, wordt de eerste 31 dagen gratis beschermd. Vanaf de 32e dag wordt de bescherming voor het exemplaar in rekening gebracht tegen de bovenstaande tarieven.
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Worden er gedurende de eerste 31 dagen andere Azure-kosten in rekening gebracht?
Ja, hoewel Azure Site Recovery gratis is gedurende de eerste 31 dagen van een beschermd exemplaar, worden er mogelijk kosten in rekening gebracht voor Azure Storage, opslagtransacties en gegevensoverdracht. Voor een herstelde virtuele machine worden mogelijk ook Azure-rekenkosten in rekening gebracht. Bekijk [hier](https://azure.microsoft.com/pricing/details/site-recovery) de volledige informatie over de prijzen

### <a name="where-can-i-find-best-practices-for-azure-vm-disaster-recovery"></a>Waar vind ik aanbevolen procedures voor herstel na nood gevallen voor Azure VM? 
1. [Meer informatie over Azure-naar-Azure-architectuur](azure-to-azure-architecture.md)
2. [Controleer de ondersteunde en niet-ondersteunde configuraties](azure-to-azure-support-matrix.md)
3. [Herstel na nood geval instellen voor virtuele Azure-machines](azure-to-azure-how-to-enable-replication.md)
4. [Een testfailover uitvoeren](azure-to-azure-tutorial-dr-drill.md)
5. [Failover en failback naar de primaire regio](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-guaranteed-in-the-target-region"></a>Hoe is de capaciteit gegarandeerd in de doel regio?
Het Site Recovery-team werkt samen met het Azure capacity management-team om voldoende infrastructuur capaciteit te plannen en om ervoor te zorgen dat Vm's die door Site Recovery worden beveiligd, de doel regio worden geïmplementeerd wanneer de failover wordt gestart.

## <a name="replication"></a>Replicatie

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Kan ik Vm's die zijn ingeschakeld via Azure Disk Encryption repliceren?

Ja, Site Recovery ondersteunt nood herstel van Vm's met behulp van de functie voor Azure Disk Encryption (ADE) ingeschakeld. Wanneer u replicatie inschakelt, worden alle vereiste schijf versleutelings sleutels en-geheimen gekopieerd van de bron regio naar de doel regio in de gebruikers context. Als u niet de juiste machtiging hebt, kan een kant-en-klaar script aan de beveiligings beheerder worden door gegeven om de sleutels en geheimen te kopiëren.

- Site Recovery ondersteunt ADE voor virtuele Azure-machines waarop Windows wordt uitgevoerd.
- Site Recovery biedt ondersteuning voor ADE versie 0,1, met een schema met Azure Active Directory (AAD) en versie 1,1, zonder AAD. [Meer informatie](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schemata).
- ADE versie 1,1, de virtuele Windows-machines moeten worden gebruikt voor beheerde schijven.
- [Meer informatie](azure-to-azure-how-to-enable-replication-ade-vms.md) over het inschakelen van replicatie voor versleutelde vm's.



### <a name="can-i-replicate-vms-to-another-subscription"></a>Kan ik Vm's repliceren naar een ander abonnement?
Ja, u kunt virtuele Azure-machines repliceren naar een ander abonnement binnen dezelfde Azure AD-Tenant.
Het configureren van DR [via abonnementen](https://azure.microsoft.com/blog/cross-subscription-dr) is eenvoudig. U kunt een ander abonnement selecteren op het moment van replicatie.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Kan ik zone-vastgemaakte virtuele Azure-machines repliceren naar een andere regio?
Ja, u kunt door een [zone vastgemaakte vm's repliceren](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) naar een andere regio.

### <a name="can-i-exclude-disks"></a>Kan ik schijven uitsluiten?

Ja, u kunt schijven op het moment van beveiliging uitsluiten met behulp van Power shell. Raadpleeg voor meer informatie [artikel](azure-to-azure-exclude-disks.md)

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Kan ik nieuwe schijven toevoegen aan gerepliceerde Vm's en replicatie inschakelen?

Ja, dit wordt ondersteund voor virtuele Azure-machines met beheerde schijven. Wanneer u een nieuwe schijf toevoegt aan een virtuele machine van Azure waarvoor replicatie is ingeschakeld, wordt voor de replicatie status voor de virtuele machine een waarschuwing weer gegeven, met een opmerking die aangeeft dat een of meer schijven op de virtuele machine beschikbaar zijn voor beveiliging. U kunt replicatie inschakelen voor toegevoegde schijven.
- Als u beveiliging inschakelt voor de toegevoegde schijven, verdwijnt de waarschuwing na de initiële replicatie.
- Als u ervoor kiest geen replicatie voor de schijf in te scha kelen, kunt u de waarschuwing negeren.
- Wanneer u een failover hebt uitgevoerd voor een virtuele machine waaraan u een schijf toevoegt en replicatie inschakelt, worden de schijven weer gegeven die beschikbaar zijn voor herstel. Als een virtuele machine bijvoorbeeld één schijf heeft en u een nieuwe toevoegt, ziet u dat het replicatie punt bestaat uit ' 1 van 2 schijven ' voor replicatie punten die zijn gemaakt vóór het toevoegen van de schijf.

Site Recovery biedt geen ondersteuning voor ' hot remove ' van een schijf van een gerepliceerde VM. Als u een VM-schijf verwijdert, moet u de replicatie voor de virtuele machine uitschakelen en opnieuw inschakelen.


### <a name="how-often-can-i-replicate-to-azure"></a>Hoe vaak kan ik repliceren naar Azure?
Replicatie is doorlopend wanneer u virtuele Azure-machines naar een andere Azure-regio repliceert. Zie de [Azure-to-Azure-replicatie architectuur](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process)voor meer informatie.

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Kan ik virtuele machines binnen een regio repliceren? Ik heb dit nodig om Vm's te migreren.
U kunt geen Azure-naar-Azure-oplossing voor nood herstel gebruiken om Vm's binnen een regio te repliceren.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Kan ik Vm's repliceren naar een Azure-regio?
Met Site Recovery kunt u virtuele machines repliceren en herstellen tussen twee regio's binnen hetzelfde geografische cluster. Geografische clusters worden gedefinieerd met gegevens latentie en soevereiniteit. Zie de [ondersteunings matrix](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)voor de site Recovery regio voor meer informatie.

### <a name="does-site-recovery-require-internet-connectivity"></a>Is Site Recovery Internet verbinding vereist?

Nee, Site Recovery vereist geen Internet verbinding. Maar hiervoor is toegang tot Site Recovery-Url's en IP-adresbereiken vereist, zoals vermeld in [dit artikel](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>Kan ik de toepassing met een afzonderlijke resource groep voor afzonderlijke lagen repliceren?
Ja, u kunt de toepassing repliceren en de configuratie voor herstel na nood gevallen ook in een afzonderlijke resource groep laten staan.
Als u bijvoorbeeld een toepassing met elke lagen-app, DB en web in een afzonderlijke resource groep hebt, klikt u op de [wizard replicatie](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) drie keer per om alle lagen te beveiligen. Site Recovery worden deze drie lagen in drie verschillende resource groepen gerepliceerd.

## <a name="replication-policy"></a>Beleid voor replicatie

### <a name="what-is-a-replication-policy"></a>Wat is een replicatie beleid?
Hiermee worden de instellingen voor de Bewaar geschiedenis van herstel punten en de frequentie van app-consistente moment opnamen gedefinieerd. Azure Site Recovery maakt standaard een nieuw replicatie beleid met de standaard instellingen van:

* 24 uur voor de Bewaar geschiedenis van herstel punten.
* 60 minuten voor de frequentie van app-consistente moment opnamen.

[Meer informatie](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Wat is een crash-consistent herstel punt?
Een crash-consistent herstel punt staat voor de gegevens op de schijf alsof de virtuele machine is vastgelopen of het netsnoer is opgehaald van de server op het moment dat de moment opname werd gemaakt. Het bevat geen items die zich in het geheugen bevond toen de moment opname werd gemaakt.

Vandaag kunnen de meeste toepassingen goed worden hersteld met crash-consistente moment opnamen. Een crash-consistent herstel punt is doorgaans voldoende voor geen database besturingssystemen en-toepassingen, zoals bestands servers, DHCP-servers en afdruk servers.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Wat is de frequentie van crash-consistente herstel punten genereren?
Site Recovery maakt om de vijf minuten een crash-consistent herstel punt.

### <a name="what-is-an-application-consistent-recovery-point"></a>Wat is een toepassings consistent herstel punt?
Toepassings consistente herstel punten worden gemaakt op basis van toepassings consistente moment opnamen. Toepassings consistente herstel punten nemen dezelfde gegevens op als crash consistente moment opnamen, met toevoeging van alle gegevens in het geheugen en alle trans acties die worden uitgevoerd.
Vanwege hun extra inhoud zijn toepassings consistente moment opnamen het meest betrokken en kunnen ze het langst worden uitgevoerd. We raden toepassings consistente herstel punten aan voor database besturingssystemen en-toepassingen, zoals SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Wat is de impact van toepassings consistente herstel punten op de prestaties van toepassingen?
Als u toepassings consistente herstel punten overweegt, worden alle gegevens in het geheugen vastgelegd en wordt het Framework op Windows vereist om de toepassing stil te leggen. Als de werk belasting al veel bezig is, kan dit echter zeer vaak gevolgen hebben voor de prestaties. Normaal gesp roken is het niet raadzaam lage frequentie te gebruiken voor app-consistente herstel punten voor niet-data base-workloads en zelfs voor een Data Base-workload van 1 uur.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Wat is de minimale frequentie voor het genereren van toepassings consistente herstel punten?
Site Recovery kunt een toepassings consistent herstel punt maken met een minimale frequentie van 1 uur.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hoe worden herstel punten gegenereerd en opgeslagen?
Om te begrijpen hoe Site Recovery herstel punten genereert, gaan we een voor beeld van een replicatie beleid met een Bewaar periode voor herstel punten van 24 uur en een app-consistente frequentie momentopname van 1 uur.

Site Recovery maakt om de vijf minuten een crash-consistent herstel punt. De gebruiker kan deze frequentie niet wijzigen. Voor het afgelopen uur heeft de gebruiker 12 crash-consistente punten en één toepassings consistente punt om uit te kiezen. Naarmate de tijd vordert, Site Recovery alle herstel punten na het laatste uur weghalen en slechts één herstel punt per uur opslaat.

De volgende scherm afbeelding illustreert het voor beeld. In de scherm afbeelding:

1. Bij een tijd van minder dan het laatste uur, zijn er herstel punten met een frequentie van 5 minuten.
2. Voor een tijd voorbij het laatste uur, Site Recovery slechts één herstel punt behouden.

   ![Lijst met gegenereerde herstel punten](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Hoe ver terug kan ik herstellen?
Het oudste herstel punt dat u kunt gebruiken, is 72 uur.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Wat gebeurt er als er een replicatie beleid van 24 uur is en een probleem verhindert dat Site Recovery meer dan 24 uur herstel punten genereert? Gaan mijn vorige herstel punten verloren?
Nee, Site Recovery blijven al uw vorige herstel punten bewaard. Afhankelijk van de Bewaar periode van het herstel punt, 24 uur in dit geval, Site Recovery vervangen alleen oudste punt als er nieuwe punten worden gegenereerd. In dit geval, omdat er geen nieuw herstel punt wordt gegenereerd als gevolg van een probleem, blijven alle oude punten intact zodra we het Bewaar periode bereiken.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Hoe kan ik het replicatie beleid wijzigen nadat de replicatie is ingeschakeld op een VM?
Ga naar **site Recovery kluis** > **Site Recovery infra structuur** > **replicatie beleid**. Selecteer het beleid dat u wilt bewerken en sla de wijzigingen op. Elke wijziging is ook van toepassing op alle bestaande replicaties.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Zijn alle herstel punten een volledige kopie van de virtuele machine of een differentieel exemplaar?
Het eerste herstel punt dat wordt gegenereerd, heeft de volledige kopie. Eventuele opeenvolgende herstel punten bevatten Delta wijzigingen.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Verhoogt de Bewaar periode van herstel punten de opslag kosten?
Ja. Als u de retentie periode van 24 uur tot 72 uur verhoogt, worden de herstel punten door Site Recovery voor een extra 48 uur opgeslagen. De toegevoegde tijd maakt kosten voor opslag. Als één herstel punt bijvoorbeeld Delta wijzigingen van 10 GB heeft en de kosten per GB $0,16 per maand bedragen, zouden de extra kosten $1,6 * 48 per maand zijn.

## <a name="multi-vm-consistency"></a>Multi-VM-consistentie

### <a name="what-is-multi-vm-consistency"></a>Wat is consistentie tussen meerdere VM'S?
Dit betekent dat het herstel punt consistent is op alle gerepliceerde virtuele machines.
Site Recovery voorziet in een optie met ' multi-VM-consistentie ', die, wanneer u deze selecteert, een replicatie groep maakt om alle computers samen te repliceren die deel uitmaken van de groep.
Alle virtuele machines hebben gedeelde crash-consistente en toepassings consistente herstel punten wanneer er een failover wordt uitgevoerd.
Door loop de zelf studie om [multi-VM-consistentie in te scha kelen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Kan ik een failover voor één virtuele machine in een multi-VM-consistentie replicatie groep?
Als u de optie ' multi-VM-consistentie ' selecteert, wordt aangegeven dat de toepassing afhankelijk is van alle virtuele machines binnen een groep. Daarom is failover van één virtuele machine niet toegestaan.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Hoeveel virtuele machines kan ik repliceren als onderdeel van een multi-VM-consistentie replicatie groep?
U kunt 16 virtuele machines samen repliceren in een replicatie groep.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>Wanneer moet ik de consistentie tussen meerdere VM'S inschakelen?
Omdat het CPU-intensief is, kan het inschakelen van multi-VM-consistentie invloed hebben op de prestaties van de werk belasting. Deze moet alleen worden gebruikt als computers dezelfde werk belasting uitvoeren en u consistentie op meerdere computers nodig hebt. Als u bijvoorbeeld twee SQL Server exemplaren en twee webservers in een toepassing hebt, moet u alleen multi-VM-consistentie hebben voor de SQL Server exemplaren.


## <a name="failover"></a>Failover

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Hoe is de capaciteit gegarandeerd in de doel regio voor virtuele Azure-machines?
Het Site Recovery-team werkt samen met het Azure capacity management-team om te plannen voor voldoende infrastructuur capaciteit, om ervoor te zorgen dat Vm's die zijn ingeschakeld voor herstel na nood gevallen, in de doel regio worden geïmplementeerd wanneer een failover wordt gestart.

### <a name="is-failover-automatic"></a>Vindt failover automatisch plaats?

Failover wordt niet automatisch uitgevoerd. U start failovers met één klik in de portal of u kunt [Power shell](azure-to-azure-powershell.md) gebruiken om een failover te activeren.

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>Kan ik een openbaar IP-adres behouden na een failover?

Het open bare IP-adres van de productie toepassing kan na een failover niet worden behouden.
- Voor werk belastingen die zijn opgenomen als onderdeel van het failover-proces, moet een open bare Azure IP-resource worden toegewezen die beschikbaar is in de doel regio.
- U kunt dit hand matig doen of het automatiseren met een herstel plan.
- Meer informatie over het [instellen van open bare IP-adressen na een failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Kan ik een privé-IP-adres behouden tijdens een failover?
Ja, u kunt een privé-IP-adres gebruiken. Wanneer u herstel na nood gevallen voor virtuele Azure-machines inschakelt, maakt Site Recovery doel resources op basis van bron bron instellingen. -Voor virtuele Azure-machines die zijn geconfigureerd met statische IP-adressen, probeert Site Recovery hetzelfde IP-adres in te richten voor de doel-VM als deze niet in gebruik is.
Meer informatie over het [bewaren van IP-adressen tijdens failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-why-is-the-server-assigned-a-new-ip-address"></a>Waarom is er na een failover een nieuw IP-adres toegewezen aan de server?

Site Recovery probeert het IP-adres op te geven op het moment van de failover. Als het adres van een andere virtuele machine wordt bereikt, Site Recovery het volgende beschik bare IP-adres als doel instellen.
Meer informatie over het [instellen van netwerk toewijzing en IP-adres sering voor VNets](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Wat zijn de **meest recente (laagste RPO)** herstel punten?
De **nieuwste (laagste RPO)** optie verwerkt eerst alle gegevens die zijn verzonden naar de site Recovery-service, om een herstel punt te maken voor elke virtuele machine voordat er een failover wordt uitgevoerd. Deze optie biedt de laagste Recovery Point Objective (RPO), omdat de VM die is gemaakt na een failover, alle gegevens die worden gerepliceerd naar Site Recovery wanneer de failover werd geactiveerd.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>De **meest recente (laagste RPO)** herstel punten hebben invloed op de FAILOVER-RTO?
Ja. Site Recovery alle in behandeling zijnde gegevens verwerkt voordat de failover wordt uitgevoerd. deze optie heeft daarom een hogere herstel tijd (RTO) in vergelijking met andere opties.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Wat betekent de **meest recente verwerkte** optie in herstel punten?
De **laatst verwerkte** optie mislukt alle virtuele machines in het plan naar het laatste herstel punt dat site Recovery verwerkt. Als u het meest recente herstel punt voor een specifieke virtuele machine wilt zien, controleert u de **meest recente herstel punten** in de VM-instellingen. Deze optie biedt een laag RTO, omdat er geen tijd wordt besteed aan het verwerken van niet-verwerkte gegevens.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Wat gebeurt er als mijn primaire regio een onverwachte onderbreking ondervindt?
U kunt een failover activeren na de storing. Site Recovery hebt geen verbinding nodig van de primaire regio om de failover uit te voeren.

### <a name="what-is-a-rto-of-a-vm-failover-"></a>Wat is een RTO van een VM-failover?
Site Recovery heeft een [RTO-sla van twee uur](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). De meeste tijd Site Recovery failover van virtuele machines binnen enkele minuten. U kunt de RTO berekenen door te gaan naar de failover-taken die de tijd weer geven die nodig is om de virtuele machine te openen. Raadpleeg de sectie voor het herstel plan RTO.

## <a name="recovery-plans"></a>Herstel plannen

### <a name="what-is-a-recovery-plan"></a>Wat is een herstel plan?
Een herstel plan in Site Recovery is het herstel van failover van Vm's. Zo kunt u de herstel bewerking consistent maken, herhaalbaar en geautomatiseerd. Een herstel plan heeft betrekking op de volgende behoeften voor de gebruiker:

- Een groep virtuele machines definiëren die samen een failover uitvoeren
- De afhankelijkheden tussen virtuele machines definiëren zodat de toepassing nauw keurig is
- Het herstel automatiseren samen met aangepaste hand matige acties voor het uitvoeren van andere taken dan de failover van virtuele machines

[Meer informatie](site-recovery-create-recovery-plans.md) over herstel plannen.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Hoe wordt sequentiëren in een herstel plan behaald?

In een herstel plan kunt u meerdere groepen maken om sequentiëren te krijgen. Voor elke groep wordt een failover in één keer uitgevoerd. Vm's die deel uitmaken van dezelfde groep, worden gezamenlijk uitgevoerd, gevolgd door een andere groep. Zie [over herstel plannen](recovery-plan-overview.md#model-apps)voor meer informatie over het model leren van een toepassing met behulp van een herstel plan.

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hoe kan ik de RTO van een herstel plan vinden?
Als u de RTO van een herstel plan wilt controleren, voert u een testfailover uit voor het herstel plan en gaat u naar **site Recovery taken**.
In het volgende voor beeld duurde de taak met de naam SAPTestRecoveryPlan acht minuten en 59 seconden voor de failover van alle virtuele machines en het uitvoeren van opgegeven acties.

![Lijst met Site Recovery taken](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Kan ik Automation-runbooks toevoegen aan het herstel plan?
Ja, u kunt Azure Automation runbooks integreren in uw herstel plan. [Meer informatie](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Opnieuw beveiligen en failback

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Na een failover van de primaire regio naar een nood herstel regio, worden de Vm's in een DR-regio automatisch beveiligd?
Nee. Wanneer u virtuele Azure-machines van de ene regio naar de andere doorwerkt, worden de Vm's in de DR-regio in een [niet](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) -beveiligde status opgestart. Als u een failback wilt uitvoeren voor de virtuele machines naar de primaire regio, moet u de virtuele machines in de secundaire regio opnieuw [beveiligen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) .

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Wordt op het moment van de herbeveiliging de volledige gegevens van de secundaire regio naar de primaire regio Site Recovery gerepliceerd?
Dit is afhankelijk van de situatie. Als er bijvoorbeeld sprake is van een VM van de bron regio, worden alleen de wijzigingen tussen de bron schijf en de doel schijf gesynchroniseerd. Site Recovery de verschillen berekent door de schijven te vergelijken en vervolgens de gegevens over te zetten. Dit proces duurt meestal enkele uren. Zie voor meer informatie over wat er gebeurt tijdens de herbeveiliging, [de fout bij het opnieuw beveiligen van virtuele Azure-machines naar de primaire regio]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Hoe lang duurt het om een failback uit te voeren?
Na het opnieuw beveiligen is de hoeveelheid tijd voor failback doorgaans vergelijkbaar met de tijd die nodig was voor failover van de primaire regio naar een secundaire regio.

## <a name="capacity"></a>Capaciteit

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Hoe is de capaciteit gegarandeerd in de doel regio voor virtuele Azure-machines?
Het Site Recovery-team werkt samen met het Azure capacity management-team om te plannen voor voldoende infrastructuur capaciteit, om ervoor te zorgen dat de virtuele machines die zijn ingeschakeld voor herstel na nood gevallen, in de doel regio kunnen worden geïmplementeerd wanneer een failover wordt gestart.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Werkt Site Recovery met gereserveerde instanties?
Ja, u kunt [reserve-exemplaren](https://azure.microsoft.com/pricing/reserved-vm-instances/) kopen in het gebied voor herstel na nood gevallen en site Recovery failover-bewerkingen worden gebruikt. </br> Er is geen aanvullende configuratie nodig.


## <a name="security"></a>Beveiliging

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Worden er replicatiegegevens verzonden naar de Site Recovery-service?
Nee, Site Recovery worden geen gerepliceerde gegevens onderschept en bevatten geen informatie over wat er op uw Vm's wordt uitgevoerd. Alleen de metagegevens die nodig zijn om replicatie en failover te organiseren, worden naar de Site Recovery-service verzonden.  
Site Recovery is ISO 27001:2013, 27018, HIPAA, DPA gecertificeerd en is het proces van SOC2-en FedRAMP JAB-evaluaties.

### <a name="does-site-recovery-encrypt-replication"></a>Wordt replicatie met Site Recovery versleuteld?
Ja, zowel versleuteling als transit en [versleuteling op rest in azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen
* [Bekijk](azure-to-azure-support-matrix.md) de ondersteunings vereisten.
* [Instellen](azure-to-azure-tutorial-enable-replication.md) Replicatie van Azure naar Azure.
- Als u vragen hebt nadat u dit artikel hebt gelezen, plaatst u het in het [Azure Recovery Services-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
