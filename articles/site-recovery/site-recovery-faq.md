---
title: Algemene vragen over de Azure Site Recovery-service
description: In dit artikel worden populaire algemene vragen over Azure Site Recovery besproken.
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: raynew
ms.openlocfilehash: a9d0ae4a6e60a72bbb1148aca1a75c44506b2e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257678"
---
# <a name="general-questions-about-azure-site-recovery"></a>Algemene vragen over Azure Site Recovery

In dit artikel worden veelgestelde vragen over Azure Site Recovery samengevat. Voor specifieke scenario's bekijk deze artikelen

- [Vragen over Azure VM disaster recovery naar Azure](azure-to-azure-common-questions.md)
- [Vragen over VMware VM disaster recovery naar Azure](vmware-azure-common-questions.md)
- [Vragen over hyper-v VM-noodherstel naar Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Algemeen

### <a name="what-does-site-recovery-do"></a>Wat doet Site Recovery?
Site Recovery draagt bij aan uw BCDR-strategie (Business Continuity and Disaster Recovery), door replicatie van Azure VM's tussen regio's, on-premises virtuele machines en fysieke servers naar Azure te orkestreren en te automatiseren naar een secundair datacenter. [Meer informatie](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Kan ik een virtuele machine met een Docker-schijf beveiligen?

Nee, dit is een niet-ondersteund scenario.

## <a name="service-providers"></a>Dienstverleners

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Ik ben een dienstverlener. Werkt Site Recovery voor speciale en gedeelde infrastructuurmodellen?
Ja, Site Recovery werkt voor zowel toegewezen als gedeelde infrastructuurmodellen.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Wordt de identiteit van mijn tenant voor een serviceprovider gedeeld met de Site Recovery-service?
Nee. De identiteit van de huurder blijft anoniem. Uw tenants hebben geen toegang nodig tot de Site Recovery-portal. Alleen de beheerder van de serviceprovider communiceert met de portal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Gaan tenanttoepassingsgegevens ooit naar Azure?
Bij replicatie tussen sites van serviceproviders worden er nooit toepassingsgegevens naar Azure overgezet. Gegevens worden versleuteld tijdens het transport en worden rechtstreeks tussen de sites van de serviceprovider gerepliceerd.

Als u naar Azure repliceert, worden er toepassingsgegevens verzonden naar Azure-opslag, maar niet naar de Site Recovery-service. Gegevens worden versleuteld tijdens het transport en blijven versleuteld in Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Ontvangen mijn tenants een factuur voor Azure-services?
Nee. Azure-services worden rechtstreeks aan de serviceprovider gefactureerd. Serviceproviders zijn zelf verantwoordelijk voor het genereren van facturen voor hun tenants.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Als ik naar Azure repliceer, moet ik dan altijd een virtuele machine in Azure uitvoeren?
Nee, gegevens worden gerepliceerd naar Azure-opslag in uw abonnement. Wanneer u een failovertest (details voor DR) of een werkelijke failover uitvoert, maakt Site Recovery automatisch virtuele machines in uw abonnement.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Wordt er gezorgd voor isolatie op tenantniveau wanneer ik naar Azure repliceer?
Ja.

### <a name="what-platforms-do-you-currently-support"></a>Welke platforms worden er momenteel ondersteund?
We ondersteunen Azure Pack, Cloud Platform System en System Center-gebaseerde (2012 en hogere) implementaties. [Meer informatie](https://technet.microsoft.com/library/dn850370.aspx) over Azure Pack- en Site Recovery-integratie.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Worden implementaties van één Azure Pack en één VMM-server ondersteund?
Ja, u virtuele Hyper-V-machines repliceren naar Azure of tussen serviceprovidersites.  Houd er rekening mee dat als u tussen serviceprovidersites repliceert, Azure runbook-integratie niet beschikbaar is.

## <a name="pricing"></a>Prijzen

### <a name="where-can-i-find-pricing-information"></a>Waar vind ik prijsinformatie?
Bekijk [de prijsdetails siteherstel.](https://azure.microsoft.com/pricing/details/site-recovery/)


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Hoe kan ik geschatte kosten berekenen tijdens het gebruik van Site Recovery?

U de [prijscalculator](https://aka.ms/asr_pricing_calculator) gebruiken om de kosten te schatten tijdens het gebruik van Site Recovery.

Voer voor een gedetailleerde schatting van de kosten de hulpprogramma voor implementatieplanner voor [VMware](https://aka.ms/siterecovery_deployment_planner) of [Hyper-V](https://aka.ms/asr-deployment-planner)uit en gebruikt het [kostenschattingsrapport](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Beheerde schijven worden nu gebruikt om Vm's en fysieke servers te repliceren. Maak ik extra kosten voor het cacheopslagaccount met beheerde schijven?

Nee, er zijn geen extra kosten voor cache. Wanneer u repartliceert naar het standaardopslagaccount, maakt deze cacheopslag deel uit van hetzelfde doelopslagaccount.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Ik ben al meer dan een maand azure-siteherstelgebruiker. Krijg ik nog steeds de eerste 31 dagen gratis voor elk beveiligd exemplaar?

Ja. Elke beveiligde instantie brengt de eerste 31 dagen geen kosten met azure-siteherstel met zich mee. Als u bijvoorbeeld de afgelopen 6 maanden 10 exemplaren hebt beschermd en een 11e exemplaar aansluit op Azure Site Recovery, zijn er de eerste 31 dagen geen kosten verbonden voor de 11e instantie. In de eerste 10 exemplaren worden nog steeds kosten in rekening gebracht voor Azure Site Recovery, omdat ze langer dan 31 dagen zijn beveiligd.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Zal ik gedurende de eerste 31 dagen andere Azure-kosten maken?

Ja, hoewel Site Recovery gratis is tijdens de eerste 31 dagen van een beveiligde instantie, u kosten in rekening brengen voor Azure Storage, opslagtransacties en gegevensoverdracht. Een herstelde virtuele machine kan ook azure-rekenkosten in rekening brengen.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Zijn er kosten verbonden aan het uitvoeren van noodhersteloefeningen/testfailover?

Er zijn geen aparte kosten voor DR-boor. Er worden rekenkosten berekend nadat de VM is gemaakt na de failover van de test.



## <a name="security"></a>Beveiliging

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Worden er replicatiegegevens verzonden naar de Site Recovery-service?
Nee, Site recovery onderschept geen gerepliceerde gegevens en bevat geen informatie over wat er op uw virtuele machines of fysieke servers wordt uitgevoerd.
Er worden alleen replicatiegegevens uitgewisseld tussen uw on-premises Hyper-V-hosts, VMware-hypervisors of fysieke servers en de Azure-opslag of uw secundaire site. Met Site Recovery kunnen deze gegevens niet worden onderschept. Alleen de metagegevens die nodig zijn om replicatie en failover te organiseren, worden naar de Site Recovery-service verzonden.  

Site Recovery is ISO 27001:2013, 27018, HIPAA, DPA gecertificeerd, en is in het proces van SOC2 en FedRAMP JAB beoordelingen.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Om nalevingsredenen moeten zelfs onze on-premises metadata binnen dezelfde geografische regio blijven. Kan Site Recovery ons helpen?
Ja. Wanneer u een kluis siteherstel in een regio maakt, zorgen we ervoor dat alle metagegevens die we nodig hebben om replicatie en failover in te schakelen en te orkestreren, binnen de geografische grens van die regio blijven.

### <a name="does-site-recovery-encrypt-replication"></a>Wordt replicatie met Site Recovery versleuteld?
Voor virtuele machines en fysieke servers wordt het repliceren tussen on-premises sites versleuteling-in-transit ondersteund. Voor virtuele machines en fysieke servers die naar Azure worden gerepliceerd, worden zowel versleuteling-in-transit als [versleuteling-rust (in Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) ondersteund.

### <a name="how-can-i-enforce-tls-12-on-all-on-premises-azure-site-recovery-components"></a>Hoe kan ik TLS 1.2 afdwingen voor alle on-premises Azure Site Recovery-componenten?
Mobiliteitsagents die op de gerepliceerde items zijn geïnstalleerd, communiceren alleen met Process Server op TLS 1.2. De communicatie van Configuration Server naar Azure en van Process Server naar Azure kan echter op TLS 1.1 of 1.0 zijn. Volg de [richtlijnen](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) om TLS 1.2 af te dwingen op alle configuratieservers en processervers die door u zijn ingesteld.


## <a name="disaster-recovery"></a>Herstel na noodgeval

### <a name="what-can-site-recovery-protect"></a>Wat kan Site Recovery beschermen?
* **Azure VM's:** Site recovery kan elke werkbelasting repliceren die wordt uitgevoerd op een ondersteunde Azure VM
* **Hyper-V virtuele machines**: Site Recovery kan elke workload die op een Hyper-V VM wordt uitgevoerd beschermen.
* **Fysieke servers**: Site Recovery kan fysieke servers met Windows of Linux beschermen.
* **Virtuele VMware-machines**: Site Recovery kan elke werkbelasting die in een VMware VM wordt uitgevoerd, beschermen.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Welke workloads kan ik met Site Recovery beveiligen?
U Siteherstel gebruiken om de meeste workloads te beschermen die op een ondersteunde vm of fysieke server worden uitgevoerd. Siteherstel biedt ondersteuning voor toepassingsbewuste replicatie, zodat apps kunnen worden hersteld naar een intelligente status. Het integreert met Microsoft-toepassingen zoals SharePoint, Exchange, Dynamics, SQL Server en Active Directory en werkt nauw samen met toonaangevende leveranciers, waaronder Oracle, SAP, IBM en Red Hat. Lees [hier](site-recovery-workload.md) meer informatie over workloadbeveiliging.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Kan ik met Site Recovery herstel na noodgeval voor mijn filialen beheren?
Ja. Wanneer u Site recovery gebruikt om replicatie en failover in uw filialen te orkestreren, krijgt u een uniforme orkestratie en weergave van al uw kantoorworkloads op een centrale locatie. Vanuit het hoofdkantoor kunt u eenvoudig failovers uitvoeren en herstel na noodgeval beheren voor alle filialen. Het is niet nodig de afzonderlijke filialen te bezoeken.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Wordt disaster recovery ondersteund voor Azure VM's?

Ja, Siteherstel ondersteunt een ramp voor Azure VM's tussen Azure-regio's. [Bekijk veelgestelde vragen](azure-to-azure-common-questions.md) over azure VM-noodherstel.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Wordt disaster recovery ondersteund voor VMware VM's?

Ja, Site Recovery ondersteunt noodherstel van on-premises Vm's vmware. [Bekijk veelgestelde vragen](vmware-azure-common-questions.md) voor noodherstel van Vm's vmware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Wordt disaster recovery ondersteund voor Hyper-V VM's?
Ja, Site Recovery ondersteunt noodherstel van on-premises Hyper-V VM's. [Bekijk veelgestelde vragen](hyper-v-azure-common-questions.md) voor noodherstel van Hyper-V VM's.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>Wordt disaster recovery ondersteund voor fysieke servers?
Ja, Site Recovery ondersteunt noodherstel van on-premises fysieke servers waarop Windows en Linux worden uitgevoerd naar Azure of naar een secundaire site. Meer informatie over vereisten voor noodherstel naar [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)en naar[een secundaire site](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Houd er rekening mee dat fysieke servers worden uitgevoerd als VM's in Azure na een failover. Failback van Azure naar een on-premises fysieke server wordt momenteel niet ondersteund. U alleen niet terug naar een VMware virtuele machine.





## <a name="replication"></a>Replicatie

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Kan ik repliceren via een site-to-site VPN naar Azure?
Azure Site Recovery repliceert gegevens naar een Azure-opslagaccount of beheerde schijven, via een openbaar eindpunt. Replicatie gaat niet via een site-to-site VPN. 

### <a name="why-cant-i-replicate-over-vpn"></a>Waarom kan ik niet repliceren via VPN?

Wanneer u renueert naar Azure, bereikt replicatieverkeer de openbare eindpunten van een Azure-opslag. Zo u alleen repliceren via het openbare internet of via ExpressRoute (Microsoft peering of een bestaande openbare peering).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Kan ik Riverbed SteelHeads gebruiken voor replicatie?

Onze partner, Riverbed, biedt gedetailleerde richtlijnen voor het werken met Azure Site Recovery. Bekijk hun [oplossingsgids.](https://community.riverbed.com/s/article/DOC-4627)

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Kan ik ExpressRoute gebruiken om virtuele machines te repliceren naar Azure?
Ja, [ExpressRoute kan worden gebruikt](concepts-expressroute-with-site-recovery.md) om on-premises virtuele machines te repliceren naar Azure.

- Azure Site Recovery repliceert gegevens naar een Azure Storage via een openbaar eindpunt. U moet [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) instellen of een bestaande [openbare peering](../expressroute/about-public-peering.md) (afgeschaft voor nieuwe circuits) gebruiken om ExpressRoute te gebruiken voor replicatie van siteherstel.
- Microsoft-peering is het aanbevolen routeringsdomein voor replicatie.
- Replicatie wordt niet ondersteund via private peering.
- Als u VMware-machines of fysieke machines beschermt, moet u ervoor zorgen dat ook aan de [netwerkvereisten](vmware-azure-configuration-server-requirements.md#network-requirements) voor configuratieserver wordt voldaan. Connectiviteit met specifieke URL's is vereist door Configuration Server voor orkestratie van siteherstelreplicatie. ExpressRoute kan niet worden gebruikt voor deze connectiviteit.
- Nadat de virtuele machines zijn mislukt naar een virtueel Azure-netwerk, u ze openen met behulp van de [instelling voor privé-peering](../expressroute/expressroute-circuit-peerings.md#privatepeering) met het virtuele Azure-netwerk.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Als ik repliceer naar Azure, welk type opslagaccount of beheerde schijf heb ik dan nodig?

Je hebt een LRS of GRS opslag nodig. GRS wordt aanbevolen, omdat de gegevens dan flexibel zijn te gebruiken als er sprake is van regionale uitval of als de primaire regio niet kan worden hersteld. Het account moet zich in dezelfde regio bevinden als de Recovery Services-kluis. Premium-opslag wordt ondersteund voor VMware VM, Hyper-V VM en fysieke serverreplicatie wanneer u Siterecovery implementeert in de Azure-portal. Beheerde schijven ondersteunen alleen LRS.

### <a name="how-often-can-i-replicate-data"></a>Hoe vaak kan ik gegevens repliceren?
* **Hyper-V:** Hyper-V VM's kunnen elke 30 seconden worden gerepliceerd (behalve voor premium opslag), vijf minuten of 15 minuten.
* **Azure VM's, VMware VM's, fysieke servers:** Een replicatiefrequentie is hier niet relevant. Replicatie is continu.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Kan ik de replicatie uitbreiden van bestaande herstelsite naar een andere tertiaire site?
Uitgebreide of gekoppelde replicatie wordt niet ondersteund. Vraag deze functie aan in [het feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Kan ik de eerste keer dat ik naar Azure repliceer, offline replicatie uitvoeren?
Nee, dit wordt niet ondersteund. Vraag deze functie aan in het [feedbackforum.](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)

### <a name="can-i-exclude-specific-disks-from-replication"></a>Kan ik bepaalde schijven uitsluiten van replicatie?
Dit wordt ondersteund wanneer u VMware VM's en Hyper-V VM's repliceert naar Azure, met behulp van de Azure-portal.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Kan ik virtuele machines repliceren met dynamische schijven?
Dynamische schijven worden ondersteund bij het repliceren van virtuele Hyper-V-machines en bij het repliceren van Vms en fysieke machines met VMware-vm's en fysieke machines naar Azure. De schijf van het besturingssysteem moet een basisschijf zijn.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Kan ik de bandbreedte die is toegewezen voor replicatieverkeer beperken?
Ja. U meer lezen over beperking bandbreedte in deze artikelen:

* [Capaciteitsplanning voor het repliceren van Vm's en fysieke servers](site-recovery-plan-capacity-vmware.md)
* [Capaciteitsplanning voor het repliceren van Hyper-V VM's naar Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Als ik niet overga naar Azure, hoe krijg ik dan toegang tot de Azure VM's na een failover?

U kunt de virtuele Azure-machines benaderen via een beveiligde internetverbinding, via een VPN tussen sites of via Azure ExpressRoute. Je moet een aantal dingen voor te bereiden om verbinding te maken. [Meer informatie](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Als ik niet overga naar Azure, hoe zorgt Azure er dan voor dat mijn gegevens veerkrachtig zijn?
Azure is ontworpen voor herstelbaarheid. Siteherstel is al ontworpen voor failover naar een secundair Azure-datacenter, in overeenstemming met de Azure SLA. Als dit gebeurt, zorgen we ervoor dat je metadata en kluizen binnen dezelfde geografische regio blijven die je voor je kluis hebt gekozen.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Als ik repliceer tussen twee datacenters, wat gebeurt er dan als mijn primaire datacenter een onverwachte storing ondervindt?
U kunt een ongeplande failover vanuit de secundaire site activeren. Site Recovery heeft geen connectiviteit vanuit de primaire site nodig om de failover uit te voeren.

### <a name="is-failover-automatic"></a>Vindt failover automatisch plaats?
Failover wordt niet automatisch uitgevoerd. U initieert failovers met één klik in de portal of u [Site Recovery PowerShell](/powershell/module/az.recoveryservices) gebruiken om een failover te activeren. Niet terugis een eenvoudige actie in de Site Recovery portal.

Als u wilt automatiseren, u on-premises Orchestrator of Operations Manager gebruiken om een fout van een virtuele machine te detecteren en vervolgens de failover activeren met behulp van de SDK.

* [Lees meer](site-recovery-create-recovery-plans.md) over herstelplannen.
* [Lees meer](site-recovery-failover.md) over failover.
* [Lees meer](site-recovery-failback-azure-to-vmware.md) over falende VMware VM's en fysieke servers

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Als mijn on-premises host niet reageert of crasht, kan ik dan niet terug naar een andere host?
Ja, u het alternatieve locatieherstel gebruiken om niet terug te gaan naar een andere host dan Azure.

* [Voor Virtuele Machines van VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Voor hyper-V virtuele machines](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kan ik siteherstelscenario's automatiseren met een SDK?
Ja. U kunt Site Recovery-werkstromen automatiseren met de Rest API-, PowerShell- of Azure-SDK. Momenteel ondersteunde scenario's voor het implementeren van siteherstel met PowerShell:

* [Hyper-V VM's in VMMs-clouds repliceren naar Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Hyper-V VM's zonder VMM repliceren naar Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [VMware repliceren naar Azure met PowerShell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Component/provider-upgrade

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Waar vind ik de release notes/update rollups van Site Recovery upgrades

[Meer informatie](site-recovery-whats-new.md) over nieuwe updates en [informatie over rollup.](service-updates-how-to.md)

## <a name="next-steps"></a>Volgende stappen
* Lees het [Site Recovery-overzicht](site-recovery-overview.md)

