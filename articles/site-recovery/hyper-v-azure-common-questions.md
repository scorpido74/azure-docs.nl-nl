---
title: Veelgestelde vragen voor hyper-v-noodherstel met Azure Site Recovery
description: In dit artikel worden veelgestelde vragen over het instellen van noodherstel voor on-premises Hyper-V Vm's naar Azure samengevat met behulp van de Azure Site Recovery-site.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: 7c5f55fbea67567ddf7a2afa6a61f6c76568d829
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498207"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Algemene vragen - Herstel na noodgevallen van Hyper-V naar Azure

In dit artikel vindt u antwoorden op veelgestelde vragen die we zien bij het repliceren van on-premises Hyper-V Vm's naar Azure. 

## <a name="general"></a>Algemeen

### <a name="how-is-site-recovery-priced"></a>Hoe wordt siteherstel geprijsd?
Bekijk de [prijsdetails van Azure Site Recovery.](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="how-do-i-pay-for-azure-vms"></a>Hoe betaal ik voor Azure VM's?
Tijdens de replicatie worden gegevens gerepliceerd naar Azure-opslag en betaalt u geen VM-wijzigingen. Wanneer u een failover uitvoert naar Azure, maakt Site Recovery automatisch azure IaaS-virtuele machines. Daarna worden er kosten in rekening gebracht voor de rekenbronnen die u in Azure gebruikt.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Is er een verschil in kosten bij het repliceren naar General Purpose v2-opslagaccount?

U ziet doorgaans een toename van de transactiekosten voor GPv2-opslagaccounts, omdat Azure Site Recovery veel transacties is. [Lees meer](../storage/common/storage-account-upgrade.md#pricing-and-billing) om de verandering te schatten.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Wat heb ik nodig in Hyper-V om replicatie te orkestreren met Site Recovery?

Wat u voor de Hyper-V-hostserver nodig hebt, is afhankelijk van het implementatiescenario. Controleer de lijst met vereisten voor Hyper-V in:

* [Replicating Hyper-V VMs (without VMM) to Azure (Virtuele Hyper-V-machines (zonder VMM) repliceren naar Azure)](site-recovery-hyper-v-site-to-azure.md)
* [Replicating Hyper-V VMs (with VMM) to Azure (Virtuele Hyper-V-machines (met VMM) repliceren naar Azure)](site-recovery-vmm-to-azure.md)
* [Replicating Hyper-V VMs to a secondary datacenter (Virtuele Hyper-V-machines repliceren naar een secundair datacenter)](site-recovery-vmm-to-vmm.md)
* Als u reliceert naar een secundair datacenter, lees dan over [ondersteunde gastbesturingssystemen voor Hyper-V VM's.](https://technet.microsoft.com/library/mt126277.aspx)
* Als u een repliceren naar Azure uitvoert, ondersteunt Site Recovery alle gastbesturingssystemen die [worden ondersteund door Azure.](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kan ik VM's beveiligen wanneer Hyper-V wordt uitgevoerd op een besturingssysteem van een client?
Nee, virtuele machines moeten zich bevinden op een Hyper-V-hostserver die wordt uitgevoerd op een ondersteunde Windows-servercomputer. Als u een clientcomputer moet beschermen, u deze repliceren als een fysieke machine naar [Azure](site-recovery-vmware-to-azure.md) of een [secundair datacenter.](site-recovery-vmware-to-vmware.md)

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Moeten Hyper-V-hosts zich in VMM-clouds benemen?
Als u wilt repliceren naar een secundair datacenter, moeten Hyper-V VM's zich bevinden op Hyper-V hosts-servers in een VMM-cloud. Als u wilt repliceren naar Azure, u VM's repliceren met of zonder VMM-clouds. [Lees meer](tutorial-hyper-v-to-azure.md) over Hyper-V-replicatie naar Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kan ik virtuele machines van de tweede generatie Hyper-V repliceren naar Azure?
Ja. Site Recovery wordt tijdens failover omgezet van generatie 2 naar generatie 1. Bij failback wordt de machine teruggezet naar generatie 2. [Lees meer](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Kan ik Site Recovery met VMM implementeren als ik maar één VMM-server heb?

Ja. U VM's repliceren in Hyper-V-servers in de VMM-cloud naar Azure, of u repliceren tussen VMM-clouds op dezelfde server. Voor on-premises tot on-premises replicatie raden we u aan een VMM-server te hebben op zowel de primaire als de secundaire sites. 

### <a name="what-do-i-need-in-azure"></a>Wat heb ik nodig in Azure?
U hebt een Azure-abonnement, een Vault van Recovery Services, een opslagaccount en een virtueel netwerk nodig. De kluis, het opslagaccount en het netwerk moeten zich in dezelfde regio bevinden.

### <a name="what-azure-storage-account-do-i-need"></a>Welk Azure-opslagaccount heb ik nodig?
Je hebt een LRS- of GRS-opslagaccount nodig. GRS wordt aanbevolen, omdat de gegevens dan flexibel zijn te gebruiken als er sprake is van regionale uitval of als de primaire regio niet kan worden hersteld. Premium opslag wordt ondersteund.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Heeft mijn Azure-account machtigingen nodig om VM's te maken?
Als u een abonnementsbeheerder bent, hebt u de replicatiemachtigingen die u nodig hebt. Als u dat niet bent, hebt u machtigingen nodig om een Azure VM te maken in de brongroep en het virtuele netwerk dat u opgeeft wanneer u Siteherstel configureert en machtigingen om naar het geselecteerde opslagaccount te schrijven. [Meer informatie](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Worden replicatiegegevens verzonden naar Site recovery?
Nee, Site recovery onderschept geen gerepliceerde gegevens en bevat geen informatie over wat er op uw VM's wordt uitgevoerd. Replicatiegegevens worden uitgewisseld tussen Hyper-V-hosts en Azure-opslag. Met Site Recovery kunnen deze gegevens niet worden onderschept. Alleen de metagegevens die nodig zijn om replicatie en failover te organiseren, worden naar de Site Recovery-service verzonden.  

Site Recovery is ISO 27001:2013, 27018, HIPAA, DPA gecertificeerd, en is in het proces van SOC2 en FedRAMP JAB beoordelingen.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Kunnen we on-premises metadata binnen een geografische regio houden?
Ja. Wanneer u een kluis in een regio maakt, zorgen we ervoor dat alle metagegevens die door Site Recovery worden gebruikt, binnen de geografische grens van die regio blijven.

### <a name="does-site-recovery-encrypt-replication"></a>Wordt replicatie met Site Recovery versleuteld?
Ja, zowel versleuteling-in-transit als [versleuteling in Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) worden ondersteund.


## <a name="deployment"></a>Implementatie

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>Wat kan ik doen met Hyper-V naar Azure-replicatie?

- **Disaster recovery**: U volledig herstel na noodgevallen instellen. In dit scenario repliceert u on-premises Hyper-V VM's naar Azure-opslag:
    - U VM's repliceren naar Azure. Als uw on-premises infrastructuur niet beschikbaar is, wordt u niet overgelaten aan Azure.
    - Wanneer u niet meer hebt, worden Azure VM's gemaakt met behulp van de gerepliceerde gegevens. U hebt toegang tot apps en workloads op de Azure VM's.
    - Wanneer uw on-premises datacenter weer beschikbaar is, u weer van Azure naar uw on-premises site.
- **Migratie:** U Siteherstel gebruiken om on-premises Hyper-V VM's te migreren naar Azure-opslag. Vervolgens mislukt u van on-premises naar Azure. Na failover zijn uw apps en workloads beschikbaar en worden ze uitgevoerd op Azure VM's.


### <a name="what-do-i-need-on-premises"></a>Wat heb ik on-premises nodig?

U hebt een of meer VM's nodig die worden uitgevoerd op een of meer zelfstandige of geclusterde Hyper-V-hosts. U ook VM's repliceren die worden uitgevoerd op hosts die worden beheerd door System Center Virtual Machine Manager (VMM).
- Als u vmm niet uitvoert, verzamelt u tijdens de implementatie van Site Recovery Hyper-V-hosts en clusters in Hyper-V-sites. U installeert de Site Recovery Agents (Azure Site Recovery Provider and Recovery Services agent) op elke Hyper-V host.
- Als Hyper-V-hosts zich in een VMM-cloud bevinden, orkestreert u replicatie in VMM. U installeert de Site Recovery Provider op de VMM-server en de agent Recovery Services op elke Hyper-V-host. U brengt een kaart tussen VMM-logische/VM-netwerken en Azure VNets.
- [Meer informatie](hyper-v-azure-architecture.md) over Hyper-V naar Azure-architectuur.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Kan ik VM's repliceren die zich op een Hyper-V-cluster bevinden?

Ja, Site recovery ondersteunt geclusterde Hyper-V-hosts. Opmerking:

- Alle knooppunten van het cluster moeten worden geregistreerd op dezelfde kluis.
- Als u VMM niet gebruikt, moeten alle Hyper-V-hosts in het cluster worden toegevoegd aan dezelfde Hyper-V-site.
- U installeert de azure site recovery provider en recovery services-agent op elke Hyper-V-host in het cluster en voegt elke host toe aan een Hyper-V-site.
- Er hoeven geen specifieke stappen te worden uitgevoerd op het cluster.
- Als u het hulpprogramma Implementatieplanner voor Hyper-V uitvoert, verzamelt het hulpprogramma de profielgegevens van het knooppunt dat wordt uitgevoerd en waar de VM wordt uitgevoerd. De tool kan geen gegevens verzamelen van een knooppunt dat is uitgeschakeld, maar het zal dat knooppunt bijhouden. Nadat het knooppunt is ingeschakeld, begint het hulpprogramma met het verzamelen van de VM-profielgegevens (als de VM deel uitmaakt van de lijst met profielvm's en op het knooppunt wordt uitgevoerd).
- Als een VM op een Hyper-V-host in een siteherstelkluis migreert naar een andere Hyper-V-host in hetzelfde cluster of naar een zelfstandige host, wordt de replicatie voor de VM niet beïnvloed. De Hyper-V-host moet aan [de vereisten](hyper-v-azure-support-matrix.md#on-premises-servers)voldoen en worden geconfigureerd in een siteherstelkluis. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Kan ik VM's beveiligen wanneer Hyper-V wordt uitgevoerd op een besturingssysteem van een client?
Nee, virtuele machines moeten zich bevinden op een Hyper-V-hostserver die wordt uitgevoerd op een ondersteunde Windows-servercomputer. Als u een clientcomputer moet beschermen, u [deze als fysieke machine repliceren](physical-azure-disaster-recovery.md) naar Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Kan ik virtuele machines van de tweede generatie Hyper-V repliceren naar Azure?
Ja. Site Recovery wordt tijdens failover omgezet van generatie 2 naar generatie 1. Bij failback wordt de machine teruggezet naar generatie 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Kan ik siteherstelscenario's automatiseren met een SDK?
Ja. U kunt Site Recovery-werkstromen automatiseren met de Rest API-, PowerShell- of Azure-SDK. Momenteel ondersteunde scenario's voor het repliceren van Hyper-V naar Azure met PowerShell:

- [Hyper-V repliceren zonder VMM met PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Hyper-V repliceren met VMM met Powershell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replicatie

### <a name="where-do-on-premises-vms-replicate-to"></a>Waar repliceren on-premises VM's naar?
Gegevens worden gerepliceerd naar Azure-opslag. Wanneer u een failover uitvoert, maakt Site Recovery automatisch Azure VM's vanuit het opslagaccount.

### <a name="what-apps-can-i-replicate"></a>Welke apps kan ik repliceren?
U elke app of workload met een Hyper-V VM repliceren die voldoet aan [de replicatievereisten.](hyper-v-azure-support-matrix.md#replicated-vms) Siteherstel biedt ondersteuning voor toepassingsbewuste replicatie, zodat apps kunnen worden mislukt en niet meer in een intelligente status kunnen worden geplaatst. Site Recovery integreert met Microsoft-toepassingen zoals SharePoint, Exchange, Dynamics, SQL Server en Active Directory en werkt nauw samen met toonaangevende leveranciers, waaronder Oracle, SAP, IBM en Red Hat. Lees [hier](site-recovery-workload.md) meer informatie over workloadbeveiliging.

### <a name="whats-the-replication-process"></a>Wat is het replicatieproces?

1. Wanneer de eerste replicatie wordt geactiveerd, wordt een Hyper-V VM-momentopname gemaakt.
2. Virtuele harde schijven op de VM worden één voor één gerepliceerd, totdat ze allemaal naar Azure worden gekopieerd. Dit kan even duren, afhankelijk van de VM-grootte en de netwerkbandbreedte. Meer informatie over het vergroten van de netwerkbandbreedte.
3. Als schijfwijzigingen optreden terwijl de eerste replicatie wordt uitgevoerd, worden de wijzigingen bij de Hyper-V Replica Replication Tracker bijhoudt als Hyper-V-replicatielogboeken (.hrl). Deze logboekbestanden bevinden zich in dezelfde map als de schijven. Elke schijf heeft een bijbehorend .hrl-bestand dat naar secundaire opslag wordt verzonden. De momentopname- en logboekbestanden nemen schijfbronnen in beslag terwijl de eerste replicatie wordt uitgevoerd.
4. Als de initiële replicatie is voltooid, wordt de VM-momentopname verwijderd.
5. Alle schijfwijzigingen in het logboek worden gesynchroniseerd en samengevoegd tot de bovenliggende schijf.
6. Nadat de eerste replicatie is voltooid, wordt de beveiliging afronden op de taak van de virtuele machine uitgevoerd. Het configureert netwerk- en andere instellingen voor postreplicatie, zodat de VM is beveiligd.
7. In dit stadium u de VM-instellingen controleren om te controleren of deze klaar is voor failover. U een noodherstelboor (testfailover) uitvoeren voor de VM, om te controleren of deze niet meer is zoals verwacht.
8. Na de eerste replicatie begint deltareplicatie, in overeenstemming met het replicatiebeleid.
9. Wijzigingen worden geregistreerd .hrl-bestanden. Elke schijf die voor replicatie is geconfigureerd, heeft een bijbehorend .hrl-bestand.
10. Het logboek wordt verzonden naar het opslagaccount van de klant. Wanneer een logboek wordt doorgeleid naar Azure, worden de wijzigingen in de primaire schijf bijgehouden in een ander logboekbestand, in dezelfde map.
11. Tijdens zowel initiële als deltareplicatie u de VM in de Azure-portal controleren.

[Meer informatie](hyper-v-azure-architecture.md#replication-process) over het replicatieproces.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Kan ik repliceren naar Azure met een site-to-site VPN?

Siteherstel repliceert gegevens van on-premises naar Azure-opslag via een openbaar eindpunt of met ExpressRoute Microsoft-peering. Replicatie via een site-to-site VPN-netwerk wordt niet ondersteund.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Kan ik repliceren naar Azure met ExpressRoute?

Ja, ExpressRoute kan worden gebruikt om VM's te repliceren naar Azure. Siteherstel repliceert gegevens naar een Azure Storage-account via een openbaar eindpunt en u moet [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) instellen voor siteherstelreplicatie. Nadat VM's niet zijn overgegaan naar een virtueel Azure-netwerk, u deze openen met behulp van [private peering.](../expressroute/expressroute-circuit-peerings.md#privatepeering)


### <a name="why-cant-i-replicate-over-vpn"></a>Waarom kan ik niet repliceren via VPN?

Wanneer u renueert naar Azure, bereikt replicatieverkeer de openbare eindpunten van een Azure Storage-account. Zo u alleen repliceren via het openbare internet met ExpressRoute (Microsoft peering), en VPN werkt niet. 

### <a name="what-are-the-replicated-vm-requirements"></a>Wat zijn de gerepliceerde VM-vereisten?

Voor replicatie moet een Hyper-V VM een ondersteund besturingssysteem draaien. Bovendien moet de VM voldoen aan de vereisten voor Azure VM's. [Meer informatie](hyper-v-azure-support-matrix.md#replicated-vms) in de ondersteuningsmatrix.

### <a name="how-often-can-i-replicate-to-azure"></a>Hoe vaak kan ik repliceren naar Azure?

Hyper-V VM's kunnen elke 30 seconden worden gerepliceerd (behalve voor premium opslag), 5 minuten of 15 minuten.

### <a name="can-i-extend-replication"></a>Kan ik de replicatie verlengen?
Uitgebreide of gekoppelde replicatie wordt niet ondersteund. Vraag deze functie aan in [het feedbackforum](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Kan ik een offline initiële replicatie doen?
Nee, dit wordt niet ondersteund. Vraag deze functie aan in het [feedbackforum.](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)

### <a name="can-i-exclude-disks"></a>Kan ik schijven uitsluiten?
Ja, u schijven uitsluiten van replicatie. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Kan ik VM's repliceren met dynamische schijven?
Dynamische schijven kunnen worden gerepliceerd. De schijf van het besturingssysteem moet een basisschijf zijn.



## <a name="security"></a>Beveiliging

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Welke toegang heeft Site Recovery nodig voor Hyper-V-hosts

Site recovery heeft toegang nodig tot Hyper-V hosts om de VM's die u selecteert te repliceren. Site Recovery installeert het volgende op Hyper-V hosts:

- Als u Geen VMM uitvoert, worden de Azure Site Recovery Provider en Recovery Services-agent op elke host geïnstalleerd.
- Als u VMM uitvoert, wordt de agent Recovery Services op elke host geïnstalleerd. De provider draait op de VMM-server.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>Wat installeert Site Recovery op Hyper-V VM's?

Site recovery installeert niet expliciet iets op Hyper-V VM's ingeschakeld voor replicatie.




## <a name="failover-and-failback"></a>Failover en failback


### <a name="how-do-i-fail-over-to-azure"></a>Hoe kan ik falen naar Azure?

U kunt een geplande of niet-geplande failover uitvoeren van on-premises virtuele Hyper-V-machines naar Azure.

- Als u een geplande failover uitvoert, worden de virtuele bronmachines afgesloten om gegevensverlies te voorkomen.
- U een ongeplande failover uitvoeren als uw primaire site niet toegankelijk is.
- U kunt een failover van één machine uitvoeren of herstelplannen maken om de failover van meerdere virtuele machines te coördineren.
- Failover bestaat uit twee delen:
    - Nadat de eerste fase van failover is voltooid, moet u de gemaakte replica VM's in Azure kunnen zien. U kunt een openbaar IP-adres toewijzen aan de VM, indien nodig.
    - Vervolgens verbindt u de failover om toegang te krijgen tot de werkbelasting van de replica Azure VM.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hoe krijg ik toegang tot Azure VM's na failover?
Na failover hebt u toegang tot Azure VM's via een beveiligde internetverbinding, via een site-to-site VPN of via Azure ExpressRoute. Je moet een aantal dingen voorbereiden om verbinding te maken. [Meer informatie](failover-failback-overview.md#connect-to-azure-after-failover).

### <a name="is-failed-over-data-resilient"></a>Is mislukt over gegevens veerkrachtig?
Azure is ontworpen voor herstelbaarheid. Siteherstel is ontworpen voor failover naar een secundair Azure-datacenter, in overeenstemming met de Azure SLA. Wanneer er een failover plaatsvindt, zorgen we ervoor dat uw metagegevens en kluizen binnen dezelfde geografische regio blijven die u voor uw kluis hebt gekozen.

### <a name="is-failover-automatic"></a>Vindt failover automatisch plaats?
[Failover](site-recovery-failover.md) is niet automatisch. U initieert failovers met één klik in de portal of u [PowerShell](/powershell/module/az.recoveryservices) gebruiken om een failover te activeren.

### <a name="how-do-i-fail-back"></a>Hoe kan ik terugfalen?

Nadat uw on-premises infrastructuur weer operationeel is, u weer mislukken. Failback treedt op in drie fasen:

1. U start een geplande failover van Azure naar de on-premises site met behulp van een paar verschillende opties:

    - Downtime minimaliseren: als u deze optie gebruikt, synchroniseert Site recovery gegevens voordat de failover mislukt. Het controleert op gewijzigde gegevensblokken en downloadt ze naar de on-premises site, terwijl de Azure VM blijft draaien, waardoor downtime wordt geminimaliseerd. Wanneer u handmatig opgeeft dat de failover moet worden voltooid, wordt de Azure VM afgesloten, worden eventuele definitieve deltawijzigingen gekopieerd en wordt de failover gestart.
    - Volledige download: Met deze optie worden gegevens gesynchroniseerd tijdens failover. Met deze optie wordt de hele schijf gedownload. Het is sneller omdat er geen checksums worden berekend, maar er is meer downtime. Gebruik deze optie als u de replica Azure VM's al enige tijd uitvoert of als de on-premises VM is verwijderd.

2. U ervoor kiezen om terug te gaan naar dezelfde vm of naar een alternatieve vm. U opgeven dat Siteherstel de VM moet maken als deze nog niet bestaat.
3. Nadat de eerste synchronisatie is voltooid, selecteert u de failover te voltooien. Nadat deze is voltooid, u zich aanmelden bij de on-premises VM om te controleren of alles werkt zoals verwacht. In de Azure-portal u zien dat de Azure VM's zijn gestopt.
4. U verbindt de failover om te voltooien en krijgt opnieuw toegang tot de werkbelasting van de on-premises VM.
5. Nadat workloads zijn mislukt, schakelt u reverse replication in, zodat de on-premises VM's opnieuw worden gerepliceerd naar Azure.

### <a name="can-i-fail-back-to-a-different-location"></a>Kan ik niet terug naar een andere locatie?
Ja, als u niet naar Azure bent gegaan, u niet terug naar een andere locatie als de oorspronkelijke locatie niet beschikbaar is. [Meer informatie](hyper-v-azure-failback.md#fail-back-to-an-alternate-location).
