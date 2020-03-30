---
title: Veelgestelde vragen over VMware-herstel na noodgevallen met Azure Site Recovery
description: Antwoorden op veelgestelde vragen over noodherstel van on-premises Vm's vMware naar Azure met Azure Site Recovery.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: ae16138ae44262f53a8f9948d6287f0acf621244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240022"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Algemene vragen over replicatie van VMware naar Azure

In dit artikel worden veelgestelde vragen beantwoord die mogelijk naar boven komen wanneer u noodherstel van on-premises Virtuele VMware-virtuele machines (VM's) implementeert in Azure.

## <a name="general"></a>Algemeen

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>Wat heb ik nodig voor VMware VM disaster recovery?

[Meer informatie over de onderdelen die betrokken zijn](vmware-azure-architecture.md) bij noodherstel van VMware VM's.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Kan ik Siteherstel gebruiken om Vm's van VMware te migreren naar Azure?

Ja. Naast het gebruik van Site Recovery om volledige disaster recovery voor VMware VM's in te stellen, u siteherstel ook gebruiken om on-premises VMware VM's naar Azure te migreren. In dit scenario repliceert u on-premises Vm's vMware naar Azure Storage. Vervolgens mislukt u van on-premises naar Azure. Na failover zijn uw apps en workloads beschikbaar en worden ze uitgevoerd op Azure VM's. Het proces is als het instellen van volledig herstel na noodgevallen, behalve dat u bij een migratie niet terugfalen vanuit Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Heeft mijn Azure-account machtigingen nodig om VM's te maken?

Als u een abonnementsbeheerder bent, hebt u de replicatiemachtigingen die u nodig hebt. Als u geen beheerder bent, hebt u machtigingen nodig om deze acties uit te voeren:

- Maak een Azure VM in de brongroep en het virtuele netwerk dat u opgeeft wanneer u Siteherstel configureert.
- Schrijf naar het geselecteerde opslagaccount of beheerde schijf op basis van uw configuratie.

[Meer informatie](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) over vereiste machtigingen.

### <a name="what-applications-can-i-replicate"></a>Welke toepassingen kan ik repliceren?

U elke app of werkbelasting repliceren die wordt uitgevoerd op een VMware-vm die voldoet aan de [replicatievereisten.](vmware-physical-azure-support-matrix.md#replicated-machines)

- Siteherstel ondersteunt toepassingsbewuste replicatie, zodat apps kunnen worden mislukt en niet meer naar een intelligente status kunnen worden teruggestuurd.
- Site recovery integreert met Microsoft-toepassingen zoals SharePoint, Exchange, Dynamics, SQL Server en Active Directory. Het werkt ook nauw samen met toonaangevende leveranciers, waaronder Oracle, SAP, IBM en Red Hat.

Lees [hier](site-recovery-workload.md) meer informatie over workloadbeveiliging.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Kan ik een gast-OS-serverlicentie op Azure gebruiken?

Ja, klanten van Microsoft Software Assurance kunnen [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) gebruiken om te besparen op licentiekosten voor Windows Server-machines die zijn gemigreerd naar Azure of om Azure te gebruiken voor herstel na noodgevallen.

## <a name="security"></a>Beveiliging

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Welke toegang tot VMware-servers heeft Site Recovery nodig?

Site Recovery moet toegang hebben tot de VMware-servers om het volgende te kunnen doen:

- Stel een VMware VM in waarop de configuratieserver siteherstel wordt uitgevoerd.
- Detecteer automatisch VM's voor replicatie.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Welke toegang tot VMware VM's heeft Site Recovery nodig?

- Als u wilt repliceren, moet de Service Site Recovery Mobility zijn geïnstalleerd en uitgevoerd om te repliceren. U het hulpprogramma handmatig implementeren of u opgeven dat Siteherstel een push-installatie van de service doet wanneer u replicatie voor een virtuele machine inschakelt.
- Tijdens de replicatie communiceren VM's als volgt met Site Recovery:
    - VM's communiceren met de configuratieserver op HTTPS-poort 443 voor replicatiebeheer.
    - VM's sturen replicatiegegevens naar de processerver op HTTPS-poort 9443. (Deze instelling kan worden gewijzigd.)
    - Als u multi-VM-consistentie inschakelt, communiceren VM's met elkaar via poort 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Worden replicatiegegevens verzonden naar Site recovery?

Nee, Site recovery onderschept geen gerepliceerde gegevens en bevat geen informatie over wat er op uw VM's wordt uitgevoerd. Replicatiegegevens worden uitgewisseld tussen VMware-hypervisors en Azure Storage. Met Site Recovery kunnen deze gegevens niet worden onderschept. Alleen de metagegevens die nodig zijn om replicatie en failover te organiseren, worden naar de Site Recovery-service verzonden.

Site Recovery is gecertificeerd voor ISO 27001:2013 en 27018, HIPAA en DPA. Het is in het proces van SOC2 en FedRAMP JAB beoordelingen.

## <a name="pricing"></a>Prijzen

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Hoe bereken ik geschatte kosten voor VMware disaster recovery?

Gebruik de [prijscalculator](https://aka.ms/asr_pricing_calculator) om de kosten te schatten tijdens het gebruik van Site Recovery.

Voer voor een gedetailleerde schatting van de kosten de implementatieplannertool voor [VMware](https://aka.ms/siterecovery_deployment_planner) uit en gebruikt het [kostenschattingsrapport](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Is er een verschil in kosten tussen repliceren naar opslag of rechtstreeks naar beheerde schijven?

Beheerde schijven worden iets anders in rekening gebracht dan opslagaccounts. [Meer informatie](https://azure.microsoft.com/pricing/details/managed-disks/) over prijzen voor beheerde schijven.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Is er een verschil in kosten bij het repliceren naar General Purpose v2-opslagaccount?

U ziet doorgaans een toename van de transactiekosten voor GPv2-opslagaccounts, omdat Azure Site Recovery veel transacties is. [Lees meer](../storage/common/storage-account-upgrade.md#pricing-and-billing) om de verandering te schatten.

## <a name="mobility-service"></a>Mobility-service

### <a name="where-can-i-find-the-mobility-service-installers"></a>Waar vind ik de Mobility service installateurs?

De installatieprogramma's bevinden zich in de map %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository op de configuratieserver.

## <a name="how-do-i-install-the-mobility-service"></a>Hoe installeer ik de Mobiliteitsservice?

Installeer de service op elke vm die u wilt repliceren op een van de verschillende methoden:

- [Push-installatie](vmware-physical-mobility-service-overview.md#push-installation)
- [Handmatige installatie](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) van de gebruikersinterface of PowerShell
- Implementatie met behulp van een implementatietool zoals [Configuratiebeheer](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Managed Disks

### <a name="where-does-site-recovery-replicate-data-to"></a>Waar worden siteherstelgegevens gerepliceerd?

Site recovery repliceert on-premises Vm's vmware en fysieke servers naar beheerde schijven in Azure.

- De siteherstelprocesserver schrijft replicatielogboeken naar een cacheopslagaccount in het doelgebied.
- Deze logboeken worden gebruikt om herstelpunten te maken op door Azure beheerde schijven met voorvoegsel van **asrseeddisk.**
- Wanneer er een failover optreedt, wordt het herstelpunt dat u selecteert gebruikt om een nieuw doelbeheerde schijf te maken. Deze beheerde schijf is gekoppeld aan de VM in Azure.
- VM's die eerder zijn gerepliceerd naar een opslagaccount (vóór maart 2019) worden niet beïnvloed.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Kan ik nieuwe machines repliceren naar opslagaccounts?

Nee. Vanaf maart 2019 u in de Azure-portal alleen repliceren naar azure-beheerde schijven.

Replicatie van nieuwe VM's naar een opslagaccount is alleen beschikbaar met PowerShell of de REST API (versie 2018-01-10 of 2016-08-10).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Wat zijn de voordelen van repliceren naar beheerde schijven?

[Meer informatie over hoe](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site recovery vereenvoudigt disaster recovery met beheerde schijven.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Kan ik het type beheerde schijf wijzigen nadat een machine is beveiligd?

Ja, u [eenvoudig het type beheerde schijf wijzigen](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) voor lopende replicaties. Voordat u het type wijzigt, moet u ervoor zorgen dat er geen URL voor gedeelde toegangshandtekeningen wordt gegenereerd op de beheerde schijf:

1. Ga naar de **beheerschijfbron** op de Azure-portal en controleer of u een URL-banner met een gedeelde toegangshandtekening op het **hoofdoverzicht** hebt.
1. Als de banner aanwezig is, selecteert u deze om de lopende export te annuleren.
1. Wijzig het type schijf binnen enkele minuten. Als u het type beheerde schijf wijzigt, wacht u tot er nieuwe herstelpunten worden gegenereerd door Azure Site Recovery.
1. Gebruik de nieuwe herstelpunten voor een testfailover of failover in de toekomst.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Kan ik replicatie overschakelen van beheerde schijven naar onbeheerde schijven?

Nee. Overschakelen van beheerd naar onbeheerd wordt niet ondersteund.

## <a name="replication"></a>Replicatie

### <a name="what-are-the-replicated-vm-requirements"></a>Wat zijn de gerepliceerde VM-vereisten?

[Meer informatie](vmware-physical-azure-support-matrix.md#replicated-machines) over ondersteuningsvereisten voor Vm's en fysieke servers van VMware.

### <a name="how-often-can-i-replicate-to-azure"></a>Hoe vaak kan ik repliceren naar Azure?

Replicatie is continu bij het repliceren van Vms vmware naar Azure.

### <a name="can-i-extend-replication"></a>Kan ik de replicatie verlengen?

Uitgebreide of gekoppelde replicatie wordt niet ondersteund. Vraag deze functie aan in het [feedbackforum.](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)

### <a name="can-i-do-an-offline-initial-replication"></a>Kan ik een offline initiële replicatie doen?

Offline replicatie wordt niet ondersteund. Vraag deze functie aan in het [feedbackforum.](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)

### <a name="what-is-asrseeddisk"></a>Wat is asrseeddisk?

Voor elke bronschijf worden gegevens gerepliceerd naar een beheerde schijf in Azure. Deze schijf heeft het voorvoegsel van **asrseeddisk**. Het slaat de kopie van de bronschijf en alle momentopnamen van het herstelpunt op.

### <a name="can-i-exclude-disks-from-replication"></a>Kan ik schijven uitsluiten van replicatie?

Ja, u schijven uitsluiten.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Kan ik VM's repliceren met dynamische schijven?

Dynamische schijven kunnen worden gerepliceerd. De schijf van het besturingssysteem moet een basisschijf zijn.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Als ik replicatiegroepen gebruik voor consistentie met meerdere vm's, kan ik dan een nieuwe vm toevoegen aan een bestaande replicatiegroep?

Ja, u nieuwe VM's toevoegen aan een bestaande replicatiegroep wanneer u replicatie voor deze gegevens inschakelt. Echter:

- U een vm niet aan een bestaande replicatiegroep toevoegen nadat de replicatie is begonnen.
- U geen replicatiegroep maken voor bestaande VM's.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Kan ik VM's wijzigen die repliceren door schijven toe te voegen of te wijzigen?

Voor VMware-replicatie naar Azure u de schijfgrootte van bron-VM's wijzigen. Als u nieuwe schijven wilt toevoegen, moet u de schijf toevoegen en de beveiliging van de virtuele machine opnieuw inschakelen.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Kan ik on-premises machines migreren naar een nieuwe vCenter-server zonder dat dit gevolgen heeft voor de voortdurende replicatie?

Nee. Een wijziging van VMware Vcenter of migratie heeft gevolgen voor de lopende replicatie. Stel Siteherstel in met de nieuwe vCenterServer en schakel opnieuw replicatie voor machines in.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Kan ik repliceren naar een cache- of doelopslagaccount waarop een virtueel netwerk (met Azure Firewalls) is geconfigureerd?

Nee, Siteherstel biedt geen ondersteuning voor replicatie naar Azure Storage op virtuele netwerken.

## <a name="component-upgrade"></a>Componentupgrade

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Mijn versie van de mobiliteitsserviceagent of configuratieserver is oud en mijn upgrade is mislukt. Wat moet ik doen?

Site Recovery volgt het N-4-ondersteuningsmodel. [Meer informatie](https://aka.ms/asr_support_statement) over hoe u upgraden van zeer oude versies.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Waar vind ik de releasenotes en update rollups voor Azure Site Recovery?

[Meer informatie over nieuwe updates](site-recovery-whats-new.md)en [ophaalinformatie.](service-updates-how-to.md)

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Waar kan ik upgrade-informatie voor noodherstel naar Azure vinden?

[Meer informatie over upgraden](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Moet ik voor elke upgrade bronmachines opnieuw opstarten?

Een reboot wordt aanbevolen, maar niet verplicht voor elke upgrade. [Meer informatie](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Configuratieserver

### <a name="what-does-the-configuration-server-do"></a>Wat doet de configuratieserver?

De configuratieserver voert de on-premises siteherstelcomponenten uit, waaronder:

- De configuratieserver zelf. De server coördineert de communicatie tussen on-premises componenten en Azure en beheert gegevensreplicatie.
- De processerver, die fungeert als replicatiegateway. Deze server:
    1. Ontvangt replicatiegegevens.
    2. Optimaliseert de gegevens met caching, compressie en versleuteling.
    3. Hiermee verzendt u de gegevens naar Azure Storage.
  De processerver doet ook een push-installatie van de Mobility Service op VM's en voert automatische detectie uit van on-premises Vms vMware.
- De hoofddoelserver, die replicatiegegevens verwerkt tijdens failback vanuit Azure.

[Meer informatie](vmware-azure-architecture.md) over de onderdelen en processen van de configuratieserver.

### <a name="where-do-i-set-up-the-configuration-server"></a>Waar kan ik de configuratieserver instellen?

U hebt één, zeer beschikbare, on-premises VMware VM nodig voor de configuratieserver. Installeer de configuratieserver op een fysieke machine voor herstel van de fysieke server.

### <a name="what-do-i-need-for-the-configuration-server"></a>Wat heb ik nodig voor de configuratieserver?

Bekijk de [voorwaarden](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Kan ik de configuratieserver handmatig instellen in plaats van een sjabloon te gebruiken?

We raden u aan [de VM van de configuratieserver te maken](vmware-azure-deploy-configuration-server.md) met behulp van de nieuwste versie van de OVF-sjabloon (Open Virtualization Format). Als u de sjabloon niet gebruiken (bijvoorbeeld als u geen toegang hebt tot de VMware-server), [downloadt u](physical-azure-set-up-source.md) het installatiebestand van de portal en stelt u de configuratieserver in.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Kan een configuratieserver repliceren naar meer dan één regio?

Nee. Als u wilt repliceren naar meer dan één regio, hebt u in elke regio een configuratieserver nodig.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Kan ik een configuratieserver hosten in Azure?

Hoewel dit mogelijk is, moet de Azure VM waarop de configuratieserver wordt uitgevoerd, communiceren met uw on-premises VMware-infrastructuur en VM's. Deze communicatie voegt latentie toe en heeft gevolgen voor de voortdurende replicatie.

### <a name="how-do-i-update-the-configuration-server"></a>Hoe werk ik de configuratieserver bij?

[Meer informatie over](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) het bijwerken van de configuratieserver.

- U vindt de meest recente update-informatie op de [pagina met Azure-updates.](https://azure.microsoft.com/updates/?product=site-recovery)
- U de nieuwste versie downloaden van de portal. U ook de nieuwste versie van de configuratieserver rechtstreeks downloaden vanuit het [Microsoft Download Center.](https://aka.ms/asrconfigurationserver)
- Als uw versie meer dan vier versies ouder is dan de huidige versie, raadpleegt u het [ondersteuningsoverzicht](https://aka.ms/asr_support_statement) voor upgraderichtlijnen.

### <a name="should-i-back-up-the-configuration-server"></a>Moet ik een back-up maken van de configuratieserver?

We raden u aan regelmatig geplande back-ups van de configuratieserver te maken.

- Voor een succesvolle failback moet de terug-ingevallen VM in de configuratieserverdatabase bestaan.
- De configuratieserver moet actief zijn en in een verbonden status zijn.
- [Meer informatie](vmware-azure-manage-configuration-server.md) over algemene configuratieserverbeheertaken.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Kan ik MySQL handmatig downloaden en installeren wanneer ik de configuratieserver instel?

Ja. Download MySQL en plaats deze in de map C:\Temp\ASRSetup. Installeer het vervolgens handmatig. Wanneer u de vm van de configuratieserver instelt en de voorwaarden accepteert, wordt MySQL weergegeven als **Al geïnstalleerd** in Downloaden **en installeren.**

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Kan ik mysql niet downloaden, maar laat site recovery het installeren?

Ja. Download het MySQL-installatieprogramma en plaats deze in de map C:\Temp\ASRSetup. Wanneer u de vm van de configuratieserver instelt, accepteert u de voorwaarden en selecteert u **Downloaden en installeren.** De portal gebruikt de installer die u hebt toegevoegd om MySQL te installeren.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Kan ik de configuratieserver VM voor iets anders gebruiken?

Nee. Gebruik de VM alleen voor de configuratieserver.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Kan ik een configuratieserver klonen en deze gebruiken voor orkestratie?

Nee. Stel een nieuwe configuratieserver in om registratieproblemen te voorkomen.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Kan ik de kluis wijzigen waarin de configuratieserver is geregistreerd?

Nee. Nadat een kluis is gekoppeld aan de configuratieserver, kan deze niet worden gewijzigd. [Meer informatie](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) over het registreren van een configuratieserver met een andere kluis.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Kan ik dezelfde configuratieserver gebruiken voor noodherstel van zowel VMware VM's als fysieke servers?

Ja, maar houd er rekening mee dat de fysieke machine alleen kan worden mislukt naar een Vm van VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Waar kan ik de wachtwoordzin voor de configuratieserver downloaden?

[Meer informatie over](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) het downloaden van de wachtwoordzin.

### <a name="where-can-i-download-vault-registration-keys"></a>Waar kan ik kluisregistratiesleutels downloaden?

Selecteer **configuratieservers** in **siteherstelinfrastructuur** > **beheren**in de kluis Recovery Services . Selecteer vervolgens in **Servers**de **registratiesleutel downloaden** om het bestand met vault-referenties te downloaden.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Kan één configuratieserver worden gebruikt om meerdere vCenter-exemplaren te beschermen?

Ja, één configuratieserver kan VM's in meerdere vCenters beveiligen.  Er is geen limiet aan het aantal vCenter-exemplaren dat aan de configuratieserver kan worden toegevoegd, maar de limieten voor hoeveel VM's een enkele configuratieserver kan beveiligen, zijn wel van toepassing.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Kan één configuratieserver meerdere clusters binnen vCenter beveiligen?

Ja, Azure Site Recovery kan VM's in verschillende clusters beveiligen.

## <a name="process-server"></a>Processerver

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Waarom kan ik de processerver niet selecteren wanneer ik replicatie inschakel?

Updates in versies 9.24 en later geven nu de status van de processerver weer [wanneer u replicatie inschakelt.](vmware-azure-enable-replication.md#enable-replication) Deze functie helpt om proces-serverbeperking te voorkomen en het gebruik van ongezonde processervers te minimaliseren.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Hoe werk ik de processerver bij naar versie 9.24 of hoger voor nauwkeurige gezondheidsinformatie?

Vanaf [versie 9.24](service-updates-how-to.md#links-to-currently-supported-update-rollups)zijn er meer waarschuwingen toegevoegd om de status van de processerver aan te geven. [Werk uw siteherstelonderdelen bij naar versie 9.24 of hoger,](service-updates-how-to.md#links-to-currently-supported-update-rollups) zodat alle waarschuwingen worden gegenereerd.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Hoe kan ik zorgen voor een hoge beschikbaarheid van de processerver?

Door meer dan één processerver te configureren, biedt het ontwerp flexibiliteit om beveiligde machines van een ongezonde processerver naar werkprocesserver te verplaatsen. Verplaatsing van een machine van de ene processerver naar de andere moet expliciet/handmatig worden gestart via de gedefinieerde stappen hier: [het verplaatsen van VM's tussen processervers.](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)

## <a name="failover-and-failback"></a>Failover en failback

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Kan ik de on-premises processerver gebruiken voor failback?

We raden u ten zeerste aan een processerver in Azure te maken voor failback-doeleinden, om latende gegevensoverdracht te voorkomen. Als u het bron-VM-netwerk hebt gescheiden van het Azure-netwerk in de configuratieserver, is het bovendien essentieel om de processerver te gebruiken die in Azure is gemaakt voor failback.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Kan ik het IP-adres bij failover houden?

Ja, u het IP-adres bij failover houden. Controleer of u het doel-IP-adres opgeeft in de **compute- en netwerkinstellingen** voor de vm voordat u deze mislukt. Sluit ook machines af op het moment van failover om IP-adresconflicten tijdens failback te voorkomen.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Kan ik de doel-VM-grootte of VM-type wijzigen voordat de failover wordt mislukt?

Ja, u het type of de grootte van de virtuele machine op elk gewenst moment wijzigen voordat de mislukte mislukt. Gebruik in de portal de **compute- en netwerkinstellingen** voor de gerepliceerde vm.

### <a name="how-far-back-can-i-recover"></a>Hoe ver terug kan ik herstellen?

Voor VMware naar Azure is het oudste herstelpunt dat u gebruiken 72 uur.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hoe krijg ik toegang tot Azure VM's na failover?

Na failover hebt u toegang tot Azure VM's via een beveiligde internetverbinding, via een site-to-site VPN of via Azure ExpressRoute. Om verbinding te maken, moet u verschillende dingen voorbereiden. [Meer informatie](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Is mislukte gegevens bestand?

Azure is ontworpen voor herstelbaarheid. Siteherstel is ontworpen voor failover naar een secundair Azure-datacenter, zoals vereist door de SLA (Azure service-level agreement). Wanneer er een failover plaatsvindt, zorgen we ervoor dat uw metagegevens en kluizen in dezelfde geografische regio blijven als u voor uw kluis hebt gekozen.

### <a name="is-failover-automatic"></a>Vindt failover automatisch plaats?

[Failover](site-recovery-failover.md) is niet automatisch. U start een failover door één selectie in de portal te maken of u [PowerShell](/powershell/module/az.recoveryservices) gebruiken om een failover te activeren.

### <a name="can-i-fail-back-to-a-different-location"></a>Kan ik niet terug naar een andere locatie?

Ja. Als u niet overgaat naar Azure, u niet terug naar een andere locatie als de oorspronkelijke locatie niet beschikbaar is. [Meer informatie](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Waarom heb ik een VPN of ExpressRoute nodig met private peering om terug te mislukken?

Wanneer u niet terugkomt van Azure, worden gegevens uit Azure teruggekopieerd naar uw on-premises VM en is privétoegang vereist.


## <a name="automation-and-scripting"></a>Automatisering en scripting

### <a name="can-i-set-up-replication-with-scripting"></a>Kan ik replicatie instellen met scripting?

Ja. U siteherstelworkflows automatiseren met de Rest API, PowerShell of de Azure SDK. [Meer informatie](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Prestaties en capaciteit

### <a name="can-i-throttle-replication-bandwidth"></a>Kan ik de replicatiebandbreedte beperken?

Ja. [Meer informatie](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Volgende stappen

- [Bekijk](vmware-physical-azure-support-matrix.md) de ondersteuningsvereisten.
- [Instellen](vmware-azure-tutorial.md) VMware naar Azure-replicatie.
