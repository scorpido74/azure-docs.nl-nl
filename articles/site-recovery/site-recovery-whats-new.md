---
title: Nieuwe nieuwe in Azure Site Recovery
description: Biedt een overzicht van nieuwe functies en de nieuwste updates in de Azure Site Recovery-service.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: abb1592bcacf025e9a052d7a9222f6fb3d2b72d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257431"
---
# <a name="whats-new-in-site-recovery"></a>Nieuw in Site Recovery

De [Azure Site Recovery-service](site-recovery-overview.md) wordt voortdurend bijgewerkt en verbeterd. Om u te helpen up-to-date te blijven, biedt dit artikel u informatie over de nieuwste releases, nieuwe functies en nieuwe inhoud. Deze pagina wordt regelmatig bijgewerkt.

U meldingen van siteherstel-updates volgen en u abonneren in het [Azure-updatekanaal.](https://azure.microsoft.com/updates/?product=site-recovery)

## <a name="supported-updates"></a>Ondersteunde updates

Voor Site Recovery-componenten ondersteunen we N-4-versies, waarbij N de nieuwste versie is. Deze worden samengevat in de volgende tabel.

**Update** |  **Uniforme installatie** | **Eicellen van configuratieserver** | **Mobiliteitsservicemedewerker** | **Siteherstelprovider** | **Recovery Services-agent**
--- | --- | --- | --- | --- | ---
[Rollup 45](https://support.microsoft.com/help/4550047/) | 9.32.5487.1 | 5.1.5400.0 | 9.32.5487.1 | 5.1.5400.0 | 2.0.9165.0
[Rollup 43](https://support.microsoft.com/help/4537047/) | 9.31.5449.1 | 5.1.5300.0 | 9.31.5449.1 | 5.1.5300.0 | 2.0.9165.0
[Rollup 42](https://support.microsoft.com/help/4531426/) | 9.30.5407.1 | 5.1.5200.0 | 9.30.5407.1 | 5.1.5200.0 | 2.0.9165.0
[Rollup 41](https://support.microsoft.com/help/4528026/) | 9.29.5367.1 | 5.1.5000.0 | 9.29.5367.1 | 5.1.5000.0 | 2.0.9165.0
[Rollup 40](https://support.microsoft.com/help/4521530/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0

[Meer informatie](service-updates-how-to.md) over installatie en ondersteuning van updates.

> [!NOTE]
> Update rollup 44 wordt niet weergegeven in de tabel omdat er geen updates zijn opgenomen voor de siteherstelproviders en -agents.

## <a name="updates-march-2020"></a>Updates (maart 2020)

### <a name="update-rollup-45"></a>Rollup 45 bijwerken

[Update rollup 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) biedt de volgende updates:

**Update** | **Details**
--- | ---
**Providers en agents** | Updates voor siteherstelmedewerkers en providers zoals beschreven in de rollup.
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen zoals beschreven in de rollup.

## <a name="updates-january-2020"></a>Updates (januari 2020)

### <a name="update-rollup-44"></a>Rollup 44 bijwerken

[Update rollup 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Er waren geen updates voor de Site Recovery providers en agents.
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen zoals beschreven in de rollup.

### <a name="azure-vmware-disaster-recovery"></a>Azure VMware disaster recovery

Azure virtuele machines ondersteunen nu VM's voor versleuteling-at-rest met door de klant beheerde sleutels. [Meer informatie](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Rollup 43 bijwerken

[Update rollup 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Updates voor siteherstelmedewerkers en -providers (zoals beschreven in de rollup)
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup)


## <a name="updates-november-2019"></a>Updates (november 2019)

### <a name="update-rollup-42"></a>Rollup 42 bijwerken

[Update rollup 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Updates voor siteherstelmedewerkers en -providers (zoals beschreven in de rollup)
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup)


### <a name="azure-vm-disaster-recovery"></a>Azure VM-noodherstel

Nieuwe functies voor Azure VM-noodherstel worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**UEFI (UEFI)** | Site recovery ondersteunt nu disaster recovery voor Azure VM's met UEFI-gebaseerde opstartarchitectuur.
**Linux** | Site recovery ondersteunt nu Azure VM's met Linux met Azure Disk Encryption (ADE).
**Tweede generatie** | Alle generatie 2 Azure VM's worden nu ondersteund voor noodherstel.
**Regio's** | U nu disaster recovery inschakelen voor Azure VM's in de Noorse geo.

### <a name="vmware-to-azure-disaster-recovery"></a>Herstel na noodgevallen van VMware naar Azure

Nieuwe functies voor VMware voor Azure-herstel na noodgevallen worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**UEFI (UEFI)** | Site Recovery ondersteunt nu disaster recovery voor VMware VM's met UEFI-gebaseerde bootarchitectuur.<br/><br/> Ondersteunde besturingssystemen zijn Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, SLES 12 SP4, RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Update naar onderhoud stack update/SHA-2

Noteer het volgende voor noodherstel van Azure VM's naar een secundair gebied of on-premises VMware VM's of fysieke servers naar Azure:

- Vanaf versie 9.30.5407.1 van de Mobiliteitsservice-extensie (voor Azure VM's) en Mobility-serviceagent (voor VMware/fysieke machines) moeten sommige machinebesturingssystemen de update voor de servicestack en SHA-2 uitvoeren. Details worden weergegeven in de onderstaande tabel.
- Installeer de update en SHA-2 in overeenstemming met de gekoppelde KB. SHA-1 wordt niet meer ondersteund vanaf september 2019 en als sha-2-codeondertekening niet is ingeschakeld, wordt de agentextensie niet geïnstalleerd/geüupgradet zoals verwacht.
- Meer informatie over [sha-2-upgrade en vereisten.](https://aka.ms/SHA-2KB)

**Besturingssysteem** | **Azure VM** | **VMware VM/fysieke machine**
--- | --- | ---
**Windows 2008 R2 SP1** | [Update van de stapel service](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Update van de stapel service](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Update van de stapel service](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Update van de stapel service](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [Update van de stapel service](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Update van de stapel service](https://support.microsoft.com/help/4490628)<br/> [SHA-2.](https://support.microsoft.com/help/4474419)



## <a name="updates-october-2019"></a>Updates (oktober 2019)

### <a name="update-rollup-41"></a>Rollup bijwerken 41

[Update rollup 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Updates voor siteherstelmedewerkers en -providers (zoals beschreven in de rollup)
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup)



### <a name="azure-vm-disaster-recovery"></a>Azure VM-noodherstel

Nieuwe functies voor Azure VM-noodherstel worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Failoverinstellingen testen** | Wanneer u een testfailover instelt, u nu instellingen configureren voor de vm en het netwerk van de testfailover, inclusief IP-adres, NSG, interne laadbalans en het openbare IP-adres voor elke machine NIC. Deze instellingen zijn optioneel en wijzigen het huidige gedrag niet. Als u deze instellingen niet configureert, u een Azure VNet kiezen op het moment van de failover van de test. [Meer informatie](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Herstelplannen** | Herstelplannen zijn nu beperkt tot 100 VM's, om failover betrouwbaarheid te garanderen.

### <a name="vmware-to-azure-disaster-recovery"></a>Herstel na noodgevallen van VMware naar Azure

Nieuwe functies voor VMware voor Azure-herstel na noodgevallen worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Herstelplannen** | Herstelplannen zijn nu beperkt tot 100 VM's, om failover betrouwbaarheid te garanderen.


## <a name="updates-september-2019"></a>Updates (september 2019)

### <a name="update-rollup-40"></a>Rollup 40 bijwerken

[Update rollup 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Updates voor siteherstelmedewerkers en -providers (zoals beschreven in de rollup)
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup)




### <a name="azure-vm-disaster-recovery"></a>Azure VM-noodherstel

Nieuwe functies voor Azure VM-noodherstel worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Opruimen na failback** | Nadat site recovery niet is overgegaan naar het secundaire Azure en vervolgens niet terugkeert naar de primaire regio, worden machines in de secundaire regio automatisch geschoond. Het is niet nodig om VM's en NIC's handmatig te verwijderen.
**Testfailover behoudt IP-adres** | U nu het IP-adres van de bron-VM behouden tijdens een noodhersteloefening en een statisch IP-adres kiezen voor een testfailover.

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fysieke server disaster recovery

Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
Nieuwe processerverwaarschuwingen | We hebben nieuwe processerverwaarschuwingen toegevoegd. [Meer informatie](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Hyper-V disaster recovery

Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
Storage-account | Site Recovery ondersteunt nu het gebruik van opslagaccounts met firewall ingeschakeld voor Hyper-V naar Azure disaster recovery.  U opslagaccounts met firewalls selecteren als doelaccount of voor cacheopslag. Als u een firewallaccount gebruikt, moet u ervoor zorgen dat u de optie inschakelt om vertrouwde Microsoft-services toe te staan.<br/><br/> Dit wordt ondersteund voor Hyper-V VM's met of zonder System Center VMM.


## <a name="updates-august-2019"></a>Updates (augustus 2019)

### <a name="update-rollup-39"></a>Rollup 39 bijwerken

[Update rollup 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Updates voor siteherstelmedewerkers en -providers (zoals beschreven in de rollup)
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup)


### <a name="azure-vm-disaster-recovery"></a>Azure VM-noodherstel

Nieuwe functies voor Azure VM-noodherstel worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Versleuteling zonder Azure AD** | Versleuteling zonder Azure AD-app wordt nu ondersteund voor Azure VM-replicatie voor beheerde schijven met Windows.
**Netwerkbronnen voor failover** | Als u niet naar een andere regio gaat, u nu netwerkbroninstellingen (NSGs, load balancing, openbaar IP-adres) aan een VM koppelen.

## <a name="updates-july-2019"></a>Updates (juli 2019)

### <a name="update-rollup-38"></a>Rollup 38 bijwerken

[Update rollup 38](https://support.microsoft.com/help/4513507/) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Updates voor siteherstelmedewerkers en -providers (zoals beschreven in de rollup)
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup)


### <a name="general"></a>Algemeen

Site recovery ondersteunt nu het gebruik van v2-opslagaccounts voor algemeen gebruik voor cacheopslag of doelopslag. Voorheen werd alleen v1 ondersteund.

### <a name="vmware-to-azure-disaster-recovery"></a>Herstel na noodgevallen van VMware naar Azure

U nu schijven tot 8 TB repliceren wanneer u deze repliceert naar een Azure VM met beheerde schijven.


## <a name="updates-june-2019"></a>Updates (juni 2019)

### <a name="update-rollup-37"></a>Rollup 37 bijwerken

[Update rollup 37](https://support.microsoft.com/help/4508614/) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Updates voor siteherstelmedewerkers en -providers (zoals beschreven in de rollup)
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup)


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fysieke server disaster recovery

Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**GPT-partities** | Vanaf Update Rollup 37 (Mobility service versie 9.25.5241.1) worden maximaal vijf GPT-partities ondersteund in UEFI. Voorafgaand aan deze update werden er vier ondersteund.



## <a name="updates-may-2019"></a>Updates (mei 2019)

### <a name="update-rollup-36"></a>Rollup 36 bijwerken

[Update rollup 36](https://support.microsoft.com/help/4503156) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor siteherstelmedewerkers en -providers (zoals beschreven in de rollup)
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup)

### <a name="azure-vm-disaster-recovery"></a>Azure VM-noodherstel

Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Toegevoegde schijven repliceren** | Replicatie inschakelen voor gegevensschijven die zijn toegevoegd aan een Azure VM die al is ingeschakeld voor herstel na noodgevallen. [Meer informatie](azure-to-azure-enable-replication-added-disk.md).
**Automatische updates** | Wanneer u automatische updates configureert voor de mobiliteitsserviceextensie die wordt uitgevoerd op Azure VM's die zijn ingeschakeld voor noodherstel, u nu een bestaand automatiseringsaccount selecteren dat u wilt gebruiken in plaats van het standaardaccount te gebruiken dat door Site Recovery is gemaakt. [Meer informatie](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fysieke server disaster recovery

Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Serverbewaking verwerken** | Voor noodherstel van on-premises VM's en fysieke servers u problemen met processervers controleren en oplossen met verbeterde rapportage en waarschuwingen over de serverstatus. [Meer informatie](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Updates (maart 2019)

### <a name="update-rollup-35"></a>Rollup 35 bijwerken

[Update rollup 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor siteherstelmedewerkers en -providers (zoals beschreven in de rollup)
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup)

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fysieke server disaster recovery

Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Managed Disks** | Replicatie van on-premises Vm's vmware en fysieke servers is nu rechtstreeks naar beheerde schijven in Azure. On-premises gegevens worden verzonden naar een cacheopslagaccount in Azure en herstelpunten worden gemaakt in beheerde schijven op de doellocatie. Dit zorgt ervoor dat u niet meerdere doelopslagaccounts hoeft te beheren.
**Configuratieserver** | Site Recovery ondersteunt nu configuratieservers met meerdere NIC's. Voeg extra adapters toe aan de vm van de configuratieserver voordat u de configuratieserver in de kluis registreert. Als u daarna toevoegt, moet u de server opnieuw registreren in de kluis.


## <a name="updates-february-2019"></a>Updates (februari 2019)

### <a name="update-rollup-34"></a>Rollup 34 bijwerken

[Update rollup 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor siteherstelagents en providers (zoals beschreven in de rollup).
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup).


### <a name="update-rollup-33"></a>Rollup 33 bijwerken

[Update rollup 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor siteherstelagents en providers (zoals beschreven in de rollup).
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup).


### <a name="azure-vm-disaster-recovery"></a>Azure VM-noodherstel

Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Netwerktoewijzing** | Voor Azure VM-noodherstel u nu elk beschikbaar doelnetwerk gebruiken wanneer u replicatie inschakelt.
**Standard - SSD** | U nu disaster recovery instellen voor Azure VM's met [standaard SSD-schijven.](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)
**Opslagruimten direct** | U noodherstel instellen voor apps die worden uitgevoerd op Azure VM-apps met [behulp van Storage Spaces Direct](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) voor hoge beschikbaarheid.  Het gebruik van Storage Spaces Direct (S2D) samen met Site Recovery biedt uitgebreide bescherming van Azure VM-workloads. Met S2D u een gastcluster hosten in Azure. Dit is vooral handig wanneer een VM een kritieke toepassing host, zoals SAP ASCS-laag, SQL Server of scale-out bestandsserver.


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fysieke server disaster recovery

Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Linux BRTFS-bestandssysteem** | Site Recovery ondersteunt nu replicatie van Vm's vmware met het BRTFS-bestandssysteem. Replicatie wordt niet ondersteund als:<br/><br/>- Het subvolume van het BTRFS-bestandssysteem wordt gewijzigd nadat replicatie is inschakelt.<br/><br/>- Het bestandssysteem is verspreid over meerdere schijven.<br/><br/>- Het BTRFS-bestandssysteem ondersteunt RAID.
**Windows Server 2019** | Ondersteuning toegevoegd voor machines met Windows Server 2019.


## <a name="updates-january-2019"></a>Updates (januari 2019)


### <a name="accelerated-networking-azure-vms"></a>Versnelde netwerken (Azure VM's)

Versnelde netwerken maakt single root I/O-virtualisatie (SR-IOV) mogelijk voor een VM, waardoor de netwerkprestaties worden verbeterd. Wanneer u replicatie inschakelt voor een Azure VM, detecteert Site Recovery of versnelde netwerken is ingeschakeld. Als dit het is, configureert Site Recovery na failover automatisch versnelde netwerken op de doelreplica Azure VM, voor zowel [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) als [Linux.](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)

[Meer informatie](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Rollup 32 bijwerken

[Update rollup 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor siteherstelagents en providers (zoals beschreven in de rollup).
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup).

### <a name="azure-vm-disaster-recovery"></a>Azure VM-noodherstel

Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Linux Support** | Er is ondersteuning toegevoegd voor RedHat Workstation 6/7 en nieuwe kernelversies voor Ubuntu, Debian en SUSE.
**Opslagruimten direct** | Siteherstel ondersteunt Azure VM's met Storage Spaces Direct (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware VM's/fysieke servers disaster recovery

Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Linux Support** | Er is ondersteuning toegevoegd voor Redhat Enterprise Linux 7.6, RedHat Workstation 6/7, Oracle Linux 6.10 en Oracle Linux 7.6 en nieuwe kernelversies voor Ubuntu, Debian en SUSE.


### <a name="update-rollup-31"></a>Rollup 31 bijwerken

[Update rollup 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor siteherstelagents en providers (zoals beschreven in de rollup).
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup).

### <a name="vmware-vmsphysical-servers-replication"></a>VMware VM's/fysieke servers replicatie

Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Linux Support** |  Er is ondersteuning toegevoegd voor Oracle Linux 6.8, Oracle Linux 6.9 en Oracle Linux 7.0 met de Red Hat Compatible Kernel en voor de Unbreakable Enterprise Kernel (UEK) Release 5.
**Lvm** | Ondersteuning toegevoegd voor LVM- en LVM2-volumes.<br/><br/> De /boot directory op een schijfpartitie en op LVM volumes wordt nu ondersteund.
**Mappen** | Ondersteuning is toegevoegd voor deze mappen die zijn ingesteld als afzonderlijke partities of bestandssystemen die zich niet op dezelfde systeemschijf bevindt:<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Ondersteuning toegevoegd voor dynamische schijven.
**Failover** | Verbeterde failover tijd voor VMware VM's waar storvsc en vsbus zijn niet boot drivers.
**Ondersteuning voor UEFI** | Azure VM's ondersteunen geen opstarttype UEFI. U nu on-premises fysieke servers met UEFI migreren naar Azure met Site Recovery. Site recovery migreert de server door het opstarttype te converteren naar BIOS voordat de migratie wordt gedaan. Site Recovery ondersteunde deze conversie eerder alleen voor VM's. Ondersteuning is beschikbaar voor fysieke servers met Windows Server 2012 of hoger.

### <a name="azure-vm-disaster-recovery"></a>Azure VM-noodherstel

Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Linux Support** | Er is ondersteuning toegevoegd voor Oracle Linux 6.8, Oracle Linux 6.9 en Oracle Linux 7.0 met de Red Hat Compatible Kernel en voor de Unbreakable Enterprise Kernel (UEK) Release 5.
**Linux BRTFS-bestandssysteem** | Ondersteund voor Azure VM's.
**Azure VM's in beschikbaarheidszones** | U replicatie inschakelen naar een andere regio voor Azure VM's die zijn geïmplementeerd in beschikbaarheidszones. U nu replicatie inschakelen voor een Azure-vm en het doel voor failover instellen op één VM-exemplaar, een VM in een beschikbaarheidsset of een VM in een beschikbaarheidszone. De instelling heeft geen invloed op replicatie. [Lees](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) de aankondiging.
**Firewall-opslag (portal/PowerShell)** | Ondersteuning toegevoegd voor [opslagaccounts met firewalls](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> U Azure VM's met niet-beheerde schijven op opslagaccounts met firewalls repliceren naar een andere Azure-regio voor herstel na noodgevallen.<br/><br/> U opslagaccounts met firewalls gebruiken als doelopslagaccounts voor niet-beheerde schijven.<br/><br/> Ondersteund in portal en met powershell.

## <a name="updates-december-2018"></a>Updates (december 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Automatische updates voor de Mobiliteitsservice (Azure VM's)

Site Recovery heeft een optie toegevoegd voor automatische updates van de mobiliteitsserviceextensie. De extensie Mobility-service is geïnstalleerd op elke Azure VM die is gerepliceerd door Site Recovery. Wanneer u replicatie inschakelt, selecteert u of siteherstel updates voor de extensie wilt beheren.

Updates vereisen geen herstart van de vm en hebben geen invloed op replicatie. [Meer informatie](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Prijscalculator voor Azure VM-noodherstel

Disaster Recovery of Azure VM's brengt VM-licentiekosten en netwerk- en opslagkosten met zich mee. Azure biedt een [prijscalculator](https://aka.ms/a2a-cost-estimator) om u te helpen deze kosten te berekenen. Site Recovery biedt nu een [voorbeeld prijsschatting](https://aka.ms/a2a-cost-estimator) dat de prijzen van een monster implementatie op basis van een drie-tier app met behulp van zes VM's met 12 Standard HDD schijven en 6 Premium SSD schijven.

- Het voorbeeld gaat uit van een gegevenswijziging van 10 GB per dag voor standaard en 20 GB voor premium.
- Voor uw specifieke implementatie u de variabelen wijzigen om de kosten te schatten.
- U het aantal VM's, het aantal en het type beheerde schijven en de verwachte totale gegevenswijzigingssnelheid opgeven die voor de VM's wordt verwacht.
- Bovendien u een compressiefactor toepassen om de bandbreedtekosten te schatten.

[Lees](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) de aankondiging.


## <a name="updates-october-2018"></a>Updates (oktober 2018)

### <a name="update-rollup-30"></a>Rollup 30 bijwerken

[Update rollup 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor siteherstelagents en providers (zoals beschreven in de rollup).
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup).

### <a name="azure-vm-disaster-recovery"></a>Azure VM-noodherstel
Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Ondersteuning voor regio's** | Site Recovery-ondersteuning toegevoegd voor Australië Centraal 1 en Australia Central 2.
**Ondersteuning voor schijfversleuteling** | Ondersteuning toegevoegd voor noodherstel van Azure VM's die zijn versleuteld met Azure Disk Encryption (ADE) met de Azure AD-app. [Meer informatie](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Schijfuitsluiting** | Niet-geïninitialiseerde schijven worden nu automatisch uitgesloten tijdens Azure VM-replicatie.
**Opslag met firewall (PowerShell)** | Ondersteuning toegevoegd voor [opslagaccounts met firewalls](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> U Azure VM's met niet-beheerde schijven op opslagaccounts met firewalls repliceren naar een andere Azure-regio voor herstel na noodgevallen.<br/><br/> U opslagaccounts met firewalls gebruiken als doelopslagaccounts voor niet-beheerde schijven.<br/><br/> Alleen ondersteund met PowerShell.


### <a name="update-rollup-29"></a>Rollup bijwerken 29

[Update rollup 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor siteherstelagents en providers (zoals beschreven in de rollup).
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup).


## <a name="updates-august-2018"></a>Updates (augustus 2018)

### <a name="update-rollup-28"></a>Rollup bijwerken 28

[Update rollup 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor siteherstelagents en providers (zoals beschreven in de rollup).
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup).

### <a name="azure-vm-disaster-recovery"></a>Azure VM-noodherstel
Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Linux Support** | Ondersteuning toegevoegd voor RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/>
**Cloudondersteuning** | Ondersteunde disaster recovery voor Azure VM's in de Duitse cloud.
**Herstel van noodvoor noodgevallen met kruisabonnement** | Ondersteuning voor het repliceren van Azure VM's in een regio naar een andere regio in een ander abonnement, binnen dezelfde Azure Active Directory-tenant. [Meer informatie](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/physical server disaster recovery VMware VM/physical server disaster recovery VMware VM/physical server disaster recovery VMware
Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Linux Support** | Ondersteuning toegevoegd voor RedHat Enterprise Linux 6.10, CentOS 6.10.<br/><br/> Linux-gebaseerde VM's die de GUID-partitietabelstijl (GPT) gebruiken in de verouderde BIOS-compatibiliteitsmodus worden nu ondersteund. Bekijk de [veelgestelde vragen over Azure VM](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) voor meer informatie.
**Herstel na spoed na migratie** | Ondersteuning voor het inschakelen van noodherstel naar een secundaire regio voor een on-premises Vm vMware gemigreerd naar Azure, zonder dat u de Mobiliteitsservice op de VM hoeft te verwijderen voordat replicatie wordt ingeschakeld.
**Windows Server 2008** | Ondersteuning voor migrerende machines met Windows Server 2008 R2/2008 64-bits en 32-bits.<br/><br/> Alleen migratie (replicatie en failover). Failback wordt niet ondersteund.

## <a name="updates-july-2018"></a>Updates (juli 2018)

### <a name="update-rollup-27-july-2018"></a>Update rollup 27 (juli 2018)

[Update rollup 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor siteherstelagents en providers (zoals beschreven in de rollup).
**Oplossingen/verbeteringen van problemen** | Een aantal correcties en verbeteringen (zoals beschreven in de rollup).

### <a name="azure-vm-disaster-recovery"></a>Azure VM-noodherstel

Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Linux Support** | Ondersteuning toegevoegd voor Red Hat Enterprise Linux 7.5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/physical server disaster recovery VMware VM/physical server disaster recovery VMware VM/physical server disaster recovery VMware

Functies die deze maand zijn toegevoegd, worden samengevat in de tabel.

**Functie** | **Details**
--- | ---
**Linux Support** | Ondersteuning toegevoegd voor Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Volgende stappen

Blijf op de hoogte van onze updates op de pagina [Azure Updates.](https://azure.microsoft.com/updates/?product=site-recovery)
