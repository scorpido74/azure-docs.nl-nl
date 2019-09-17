---
title: Wat is er nieuw in Azure Site Recovery
description: Biedt een samen vatting van de nieuwe functies die zijn geïntroduceerd in Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: raynew
ms.openlocfilehash: 867a2dc9739e839a6957190f93311a811556df15
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010250"
---
# <a name="whats-new-in-site-recovery"></a>Nieuw in Site Recovery

De [Azure site Recovery](site-recovery-overview.md) -service wordt voortdurend bijgewerkt en verbeterd. Om u te helpen up-to-date te blijven, geeft u in dit artikel informatie over de nieuwste releases, nieuwe functies en nieuwe inhoud. Deze pagina wordt regel matig bijgewerkt.

U kunt volgen en u abonneren op Site Recovery update meldingen in het [Azure-updates](https://azure.microsoft.com/updates/?product=site-recovery) kanaal.

## <a name="supported-updates"></a>Ondersteunde updates

Voor Site Recovery-onderdelen ondersteunen we N-4 versies, waarbij N de meest recente versie is van de release. Deze worden in de volgende tabel samenvatten.

**Update** |  **Unified Setup** | **Eicellen van de configuratie server** | **Mobility Service-agent** | **Site Recovery provider** | **Recovery Services-agent**
--- | --- | --- | --- | --- | ---
[Pakket 40](https://support.microsoft.com/help/4517283/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0
[Pakket 39](https://support.microsoft.com/help/4517283/) | 9.27.5308.1 | 5.1.4600.0 | 9.27.5308.1 | 5.1.4600.0 | 2.0.9165.0
[Pakket 38](https://support.microsoft.com/help/4513507/) | 9.26.5269.1 | 5.1.4500.0 | 9.26.5269.1 | 5.1.4500.0 | 2.0.9165.0
[Pakket 37](https://support.microsoft.com/help/4508614/) | 9.25.5241.1 | 5.1.4300.0 | 9.25.5241.1 | 5.1.4300.0 | 2.0.9163.0
[Pakket 36](https://support.microsoft.com/help/4503156/) | 9.24.5211.1 | 5.1.4150.0 | 9.24.5211.1 | 5.1.4150.0 | 2.0.9160.0 
        

Meer [informatie](service-updates-how-to.md) over installatie en ondersteuning van de update.


## <a name="updates-september-2019"></a>Updates (september 2019)

### <a name="update-rollup-40"></a>Update pakket 40

[Update pakket 40](h https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) bevat de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Updates voor Site Recovery agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)




### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM

Nieuwe functies voor herstel na nood gevallen voor Azure VM worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Opschonen na failback** | Na een failover naar de secundaire Azure en vervolgens een failback naar de primaire regio, Site Recovery automatisch machines in de secundaire regio opschonen. Het is niet nodig om VM'S en Nic's hand matig te verwijderen.
**IP-adres behouden door testfailover** | U kunt nu het IP-adres van de bron-VM bewaren tijdens een nood herstel analyse en een statisch IP-adres kiezen voor een testfailover.

### <a name="vmwarephysical-server-disaster-recovery"></a>Herstel na nood geval voor VMware/fysieke server

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
Nieuwe waarschuwingen voor proces server | Er zijn nieuwe waarschuwingen voor de proces server toegevoegd. [Meer informatie](vmware-physical-azure-monitor-process-server.md). 

### <a name="hyper-v-disaster-recovery"></a>Herstel na nood geval voor Hyper-V

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
Storage-account | Site Recovery ondersteunt nu het gebruik van opslag accounts waarvoor Firewall is ingeschakeld voor Hyper-V naar Azure-herstel na nood gevallen.  U kunt opslag accounts met firewall mogelijkheden selecteren als doel account of voor cache opslag. Als u gebruikmaakt van Firewall-account, moet u ervoor zorgen dat u de optie voor het toestaan van vertrouwde micro soft-Services hebt ingeschakeld.


## <a name="updates-august-2019"></a>Updates (augustus 2019)

### <a name="update-rollup-39"></a>Update pakket 39

[Update pakket 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) bevat de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Updates voor Site Recovery agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)


### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM

Nieuwe functies voor herstel na nood gevallen voor Azure VM worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Versleuteling zonder Azure AD** | Versleuteling zonder Azure AD-app wordt nu ondersteund voor Azure VM-replicatie naar Managed disks met Windows.
**Netwerk bronnen voor failover** | Wanneer een failover wordt uitgevoerd naar een andere regio, kunt u nu netwerk bron instellingen (Nsg's, taak verdeling, openbaar IP-adres) koppelen aan een virtuele machine. 

## <a name="updates-july-2019"></a>Updates (juli 2019)

### <a name="update-rollup-38"></a>Updatepakket 38

[Update pakket 38](https://support.microsoft.com/help/4513507/) bevat de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Updates voor Site Recovery agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)


### <a name="general"></a>Algemeen

Site Recovery ondersteunt nu het gebruik van opslag accounts voor algemeen gebruik v2 voor cache opslag of doel opslag. Voorheen werd alleen v1 ondersteund.

### <a name="vmware-to-azure-disaster-recovery"></a>Herstel na noodgevallen van VMware naar Azure

U kunt schijven nu tot 8 TB repliceren, wanneer u naar een Azure-VM met beheerde schijven repliceert.


## <a name="updates-june-2019"></a>Updates (juni 2019)

### <a name="update-rollup-37"></a>Update pakket 37

[Update pakket 37](https://support.microsoft.com/help/4508614/) bevat de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Updates voor Site Recovery agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)


### <a name="vmwarephysical-server-disaster-recovery"></a>Herstel na nood geval voor VMware/fysieke server

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**GPT-partities** | Vanaf update pakket 37 en hoger (Mobility Service versie 9.25.5241.1) worden Maxi maal vijf GPT-partities ondersteund in UEFI. Vóór deze update werden vier ondersteund.



## <a name="updates-may-2019"></a>Updates (mei 2019)

### <a name="update-rollup-36"></a>Updatepakket 36

[Update pakket 36](https://support.microsoft.com/help/4503156) bevat de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)

### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Toegevoegde schijven repliceren** | Schakel replicatie in voor gegevens schijven die zijn toegevoegd aan een virtuele machine van Azure die al is ingeschakeld voor herstel na nood gevallen. [Meer informatie](azure-to-azure-enable-replication-added-disk.md).
**Automatische updates** | Bij het configureren van automatische updates voor de extensie van de Mobility-service die wordt uitgevoerd op Azure-Vm's die zijn ingeschakeld voor nood herstel, kunt u nu een bestaand Automation-account selecteren dat u wilt gebruiken, in plaats van het standaard account te gebruiken dat is gemaakt door Site Recovery. [Meer informatie](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>Herstel na nood geval voor VMware/fysieke server

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Bewaking proces server** | Voor herstel na nood gevallen van on-premises virtuele VMware-machines en fysieke servers kunt u problemen met de proces server controleren en oplossen met verbeterde rapporten en waarschuwingen voor de server status. [Meer informatie](vmware-physical-azure-monitor-process-server.md). 





## <a name="updates-march-2019"></a>Updates (maart 2019)

### <a name="update-rollup-35"></a>Update pakket 35

[Update pakket 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) bevat de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)

### <a name="vmwarephysical-server-disaster-recovery"></a>Herstel na nood geval voor VMware/fysieke server

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Azure Managed Disks** | De replicatie van on-premises virtuele VMware-machines en fysieke servers is nu rechtstreeks voor beheerde schijven in Azure. On-premises gegevens worden verzonden naar een cache-opslag account in Azure en er worden herstel punten gemaakt op beheerde schijven op de doel locatie. Dit zorgt ervoor dat u niet meerdere doel opslag accounts hoeft te beheren.
**Configuratieserver** | Site Recovery ondersteunt nu configuratie servers met meerdere Nic's. Voeg extra adapters toe aan de configuratie Server-VM voordat u de configuratie server in de kluis registreert. Als u later toevoegt, moet u de server opnieuw registreren in de kluis.


## <a name="updates-february-2019"></a>Updates (februari 2019)

### <a name="update-rollup-34"></a>Update pakket 34 

[Update pakket 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) bevat de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).


### <a name="update-rollup-33"></a>Update pakket 33 

[Update pakket 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) bevat de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).


### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM 
De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Netwerk toewijzing** | Voor herstel na nood gevallen van Azure VM kunt u nu elk beschik bare doelnet werken gebruiken wanneer u replicatie inschakelt. 
**Standard - SSD** | U kunt nu herstel na nood gevallen instellen voor Azure-Vm's met behulp van [Standard-SSD-schijven](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Opslagruimten Direct** | U kunt herstel na nood gevallen instellen voor apps die worden uitgevoerd op Azure VM-apps met behulp van [opslagruimten direct](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) voor hoge Beschik baarheid.  Het gebruik van Opslagruimten Direct (S2D) in combi natie met Site Recovery biedt uitgebreide beveiliging van Azure VM-workloads. Met S2D kunt u een gast cluster hosten in Azure. Dit is vooral handig wanneer een virtuele machine als host fungeert voor een kritieke toepassing, zoals SAP ASCS Layer, SQL Server of scale-out Bestands server.


### <a name="vmwarephysical-server-disaster-recovery"></a>Herstel na nood geval voor VMware/fysieke server
De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux BRTFS-bestands systeem** | Site Recovery ondersteunt nu replicatie van virtuele VMware-machines met het BRTFS-bestands systeem. Replicatie wordt niet ondersteund als:<br/><br/>-Het subvolume van het BTRFS-bestands systeem wordt gewijzigd nadat de replicatie is ingeschakeld.<br/><br/>-Het bestands systeem is verdeeld over meerdere schijven.<br/><br/>-Het BTRFS-bestands systeem ondersteunt RAID.
**Windows Server 2019** | Ondersteuning toegevoegd voor computers met Windows Server 2019.


## <a name="updates-january-2019"></a>Updates (januari 2019)


### <a name="accelerated-networking-azure-vms"></a>Versneld netwerken (Azure Vm's)

Versneld netwerken maken gebruik van I/O-virtualisatie met één hoofdmap (SR-IOV) naar een VM, waardoor de netwerk prestaties worden verbeterd. Wanneer u replicatie inschakelt voor een virtuele machine van Azure, Site Recovery detecteert of versneld netwerken zijn ingeschakeld. Als dit het geval is, wordt na een failover-Site Recovery automatisch een versneld netwerk geconfigureerd op de doel replica van Azure VM, voor zowel [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) als [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Meer informatie](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Update pakket 32 

[Update pakket 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) bevat de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).

### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux-ondersteuning** | Er is ondersteuning toegevoegd voor RedHat-werk Station 6/7, en voor nieuwe kernel-versies voor Ubuntu, Debian en SUSE.
**Opslagruimten Direct** | Site Recovery ondersteunt Azure Vm's met behulp van Opslagruimten Direct (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>Herstel na nood geval voor virtuele VMware-machines/fysieke servers

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.
 
**Functie** | **Details**
--- | ---
**Linux-ondersteuning** | Er is ondersteuning toegevoegd voor redhat Enter prise Linux 7,6, RedHat Workstation 6/7, Oracle Linux 6.10/7,6 en nieuwe kernel-versies voor Ubuntu, Debian en SUSE.


### <a name="update-rollup-31"></a>Update pakket 31 

[Update pakket 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) bevat de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).

### <a name="vmware-vmsphysical-servers-replication"></a>Replicatie van virtuele VMware-machines/fysieke servers 
De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.
**Functie** | **Details**
--- | ---
**Linux-ondersteuning** | Er is ondersteuning toegevoegd voor Oracle Linux 6,8 en 6,9/7.0 en voor de UEK5-kernel.
**LVM** | Ondersteuning toegevoegd voor LVM-en LVM2-volumes.<br/><br/> De/boot-map op een schijf partitie en op LVM-volumes wordt nu ondersteund.
**Adreslijsten** | Er is ondersteuning toegevoegd voor deze mappen die zijn ingesteld als afzonderlijke partities of bestands systemen die zich niet op dezelfde systeem schijf bevinden:<br/><br/> /(root),/boot,/usr,/usr/local,/var,/etc.
**Windows Server 2008** | Ondersteuning voor dynamische schijven is toegevoegd.
**Failover** | Verbeterde failover-tijd voor VMware-Vm's waarbij storvsc en vsbus geen stuur Programma's worden opgestart.
**UEFI-ondersteuning** | Azure-Vm's ondersteunen boot type UEFI niet. U kunt nu on-premises fysieke servers met UEFI migreren naar Azure met Site Recovery. Site Recovery migreert de server door het opstart type te converteren naar BIOS vóór de migratie. Site Recovery eerder deze conversie alleen voor Vm's ondersteund. Ondersteuning is beschikbaar voor fysieke servers met Windows Server 2012 of hoger.

### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM
De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux-ondersteuning** | Ondersteund voor Oracle Linux 6,8 en 6,9/7.0; en voor de UEK5-kernel.
**Linux BRTFS-bestands systeem** | Ondersteund voor virtuele Azure-machines.
**Azure-Vm's in beschikbaarheids zones** | U kunt replicatie naar een andere regio inschakelen voor virtuele Azure-machines die zijn geïmplementeerd in beschikbaarheids zones. U kunt nu replicatie inschakelen voor een virtuele Azure-machine en het doel voor failover instellen op een enkele VM-instantie, een virtuele machine in een beschikbaarheidsset of een virtuele machine in een beschikbaarheids zone. De instelling heeft geen invloed op de replicatie. [Lees](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) de aankondiging.
**Firewall-ingeschakelde opslag (Portal/Power shell)** | Ondersteuning voor [opslag accounts met ingeschakelde firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> U kunt Azure-Vm's met niet-beheerde schijven op opslag accounts met ingeschakelde firewalls repliceren naar een andere Azure-regio voor herstel na nood gevallen.<br/><br/> U kunt opslag accounts met ingeschakelde firewall gebruiken als doel opslag accounts voor niet-beheerde schijven.<br/><br/> Ondersteund in de portal en met behulp van Power shell.

## <a name="updates-december-2018"></a>Updates (december 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Automatische updates voor de Mobility-service (Azure Vm's)

Site Recovery heeft een optie voor automatische updates toegevoegd aan de Mobility service-extensie. De Mobility service-extensie wordt geïnstalleerd op elke virtuele machine van Azure die wordt gerepliceerd door Site Recovery. Wanneer u replicatie inschakelt, selecteert u of Site Recovery updates voor de uitbrei ding wilt laten beheren.

Voor updates is het opnieuw opstarten van de VM niet vereist en de replicatie wordt niet beïnvloed. [Meer informatie](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Prijs calculator voor herstel na nood gevallen voor Azure VM

Herstel na nood gevallen van virtuele Azure-machines, en netwerk-en opslag kosten. Azure biedt een [prijs calculator](https://aka.ms/a2a-cost-estimator) om u te helpen deze kosten te berekenen. Site Recovery biedt nu een [voor beeld van een prijs schatting](https://aka.ms/a2a-cost-estimator) waarmee een voorbeeld implementatie wordt geprijsd op basis van een app met drie lagen, met zes virtuele machines met 12 Standard-HDD schijven en 6 Premium-SSD schijven.

- In het voor beeld wordt ervan uitgegaan dat de gegevens worden gewijzigd van 10 GB per dag voor Standard en 20 GB voor Premium.
- Voor uw specifieke implementatie kunt u de variabelen wijzigen om de kosten te schatten.
- U kunt het aantal Vm's, het aantal en het type beheerde schijven, en de verwachte totale wijzigings frequentie voor gegevens op de virtuele machines opgeven.
- Daarnaast kunt u een compressie factor Toep assen om de bandbreedte kosten te schatten.

[Lees](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) de aankondiging.


## <a name="updates-october-2018"></a>Updates (oktober 2018)

### <a name="update-rollup-30"></a>Update pakket 30 

[Update pakket 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).

### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM
De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Regio ondersteuning** | Site Recovery ondersteuning toegevoegd voor Australië-centraal 1 en Australië-centraal 2.
**Ondersteuning voor schijf versleuteling** | Ondersteuning toegevoegd voor herstel na nood gevallen van Azure-Vm's die zijn versleuteld met Azure Disk Encryption (ADE) met de Azure AD-app. [Meer informatie](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Schijf uitsluiting** | Niet-geïnitialiseerde schijven worden nu automatisch uitgesloten tijdens de replicatie van virtuele Azure-machines.
**Firewall-ingeschakelde opslag (Power shell)** | Ondersteuning voor [opslag accounts met ingeschakelde firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> U kunt Azure-Vm's met niet-beheerde schijven op opslag accounts met ingeschakelde firewalls repliceren naar een andere Azure-regio voor herstel na nood gevallen.<br/><br/> U kunt opslag accounts met ingeschakelde firewall gebruiken als doel opslag accounts voor niet-beheerde schijven.<br/><br/> Alleen ondersteund met Power shell.


### <a name="update-rollup-29"></a>Update pakket 29 

[Update pakket 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) biedt de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).


## <a name="updates-august-2018"></a>Updates (augustus 2018)

### <a name="update-rollup-28"></a>Update pakket 28 

[Update pakket 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) bevat de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).

### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM 
De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux-ondersteuning** | Toegevoegd ondersteund voor RedHat Enter prise Linux 6,10; CentOS 6,10.<br/><br/>
**Cloud ondersteuning** | Ondersteunde nood herstel voor virtuele Azure-machines in de Duitse Cloud.
**Herstel na nood geval voor meerdere abonnementen** | Ondersteuning voor het repliceren van virtuele Azure-machines in één regio naar een andere regio in een ander abonnement, binnen dezelfde Azure Active Directory Tenant. [Meer informatie](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Herstel na nood geval voor VMware VM/fysieke server 
De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux-ondersteuning** | Ondersteuning toegevoegd voor RedHat Enter prise Linux 6,10, CentOS 6,10.<br/><br/> Op Linux gebaseerde virtuele machines die gebruikmaken van de partitie stijl GPT (GUID-partitie tabel) in verouderde BIOS-compatibiliteits modus, worden nu ondersteund. Raadpleeg de [Veelgestelde vragen over Azure VM](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) voor meer informatie. 
**Herstel na nood geval voor Vm's na migratie** | Ondersteuning voor het inschakelen van herstel na nood gevallen naar een secundaire regio voor een on-premises virtuele VMware-machine die is gemigreerd naar Azure, zonder dat u de Mobility-service op de virtuele machine hoeft te verwijderen voordat u replicatie inschakelt.
**Windows Server 2008** | Ondersteuning voor de migratie van computers met Windows Server 2008 R2/2008 64-bits en 32-bits.<br/><br/> Alleen migratie (replicatie en failover). Failback wordt niet ondersteund.

## <a name="updates-july-2018"></a>Updates (juli 2018)

### <a name="update-rollup-27-july-2018"></a>Update pakket 27 (juli 2018)

[Update pakket 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) bevat de volgende updates.

**Update** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).

### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM 

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux-ondersteuning** | Ondersteuning toegevoegd voor Red Hat Enterprise Linux 7,5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Herstel na nood geval voor VMware VM/fysieke server 

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux-ondersteuning** | Ondersteuning toegevoegd voor Red Hat Enterprise Linux 7,5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Volgende stappen

Blijf op de hoogte van de updates op de pagina [Azure updates](https://azure.microsoft.com/updates/?product=site-recovery) .
