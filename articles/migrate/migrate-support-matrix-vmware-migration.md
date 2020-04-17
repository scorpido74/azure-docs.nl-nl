---
title: Ondersteuning voor VMware-migratie in Azure-migreren
description: Meer informatie over ondersteuning voor VMware VM-migratie in Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: eee16b244ae4f9d517bdd42a0b7f37b1494ac480
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538134"
---
# <a name="support-matrix-for-vmware-migration"></a>Ondersteuningsmatrix voor VMware-migratie

In dit artikel worden ondersteuningsinstellingen en -beperkingen voor het migreren van VMware VM's met [Azure Migrate: Servermigratie](migrate-services-overview.md#azure-migrate-server-migration-tool) samengevat. Als u op zoek bent naar informatie over het beoordelen van VMware VM's voor migratie naar Azure, controleert u de [matrix voor beoordelingsondersteuning](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Migratieopties

U VMware VM's op een aantal manieren migreren:

- Met agentless migratie: migreer VM's zonder er iets op te hoeven installeren. U implementeert het [Azure Migrate-toestel](migrate-appliance.md) voor agentloze migratie.
- Met op agent gebaseerde migratie: installeer een agent op de VM voor replicatie. Voor op agenten gebaseerde migratie moet u een [replicatietoestel](migrate-replication-appliance.md)implementeren.

Bekijk [dit artikel](server-migrate-overview.md) om erachter te komen welke methode u wilt gebruiken.

## <a name="migration-limitations"></a>Migratiebeperkingen

- U maximaal 10 VM's tegelijk selecteren voor replicatie. Als u meer machines wilt migreren, repliceer dan in groepen van 10.
- Voor VMware-agentloze migratie u tot 100 replicaties tegelijk uitvoeren.

## <a name="agentless-vmware-servers"></a>Agentless-VMware-servers

**Vmware** | **Details**
--- | ---
**VMware vCenter Server** | Versie 5.5, 6.0, 6.5 of 6.7.
**VMware vSphere ESXI-host** | Versie 5.5, 6.0, 6.5 of 6.7.
**vCenter Server-machtigingen** | Agentless migratie maakt gebruik van het [emigreren toestel](migrate-appliance.md). Het toestel heeft deze machtigingen nodig:<br/><br/> - **Datastore.Browse:** Laat browsen in VM-logboekbestanden het maken en verwijderen van momentopnamen oplossen.<br/><br/> - **Datastore.LowLevelFileOperations**: Sta bewerkingen voor lezen/schrijven/verwijderen/hernoemen toe in de datastore-browser om het maken en verwijderen van momentopnamen op te lossen.<br/><br/> - **VirtualMachine.Configuration.DiskChangeTracking:** Schakel het bijhouden van wijzigingen van VM-schijven in of uit om gewijzigde gegevensblokken tussen momentopnamen te trekken.<br/><br/> - **VirtualMachine.Configuration.DiskLease:** Sta schijfleasebewerkingen toe voor een VM, om de schijf te lezen met behulp van de VMware vSphere Virtual Disk Development Kit (VDDK).<br/><br/> - **VirtualMachine.Provisioning.AllowDiskAccess**: (specifiek voor vSphere 6.0 en hoger) Laat het openen van een schijf op een VM toe voor willekeurige leestoegang op de schijf met behulp van de VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowReadOnlyDiskAccess:** Laat het openen van een schijf op een VM toe om de schijf te lezen met de VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowDiskRandomAccess**: Laat het openen van een schijf op een VM, om de schijf te lezen met behulp van de VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowVirtualMachineDownload:** Hiermee u leesbewerkingen uitvoeren op bestanden die zijn gekoppeld aan een VM, de logboeken downloaden en oplossen als er een storing optreedt.<br/><br/> - **VirtualMachine.SnapshotManagement.***: Maak en beheer van VM-momentopnamen toe voor replicatie.<br/><br/> - **Virtual Machine.Interaction.Power Off:** Laat de VM worden uitgeschakeld tijdens de migratie naar Azure.



## <a name="agentless-vmware-vms"></a>Vm's zonder agent

**Ondersteuning** | **Details**
--- | ---
**Ondersteunde besturingssystemen** | [Windows-](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) en [Linux-besturingssystemen](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) die door Azure worden ondersteund, kunnen worden gemigreerd met behulp van agentloze migratie.
**Vereiste wijzigingen voor Azure** | Sommige VM's vereisen mogelijk wijzigingen, zodat ze in Azure kunnen worden uitgevoerd. Azure Migrate voert deze wijzigingen automatisch aan voor de volgende besturingssystemen:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - CentOS 6.5+, 7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8<br/><br/> Voor andere besturingssystemen moet u handmatig aanpassingen maken voordat u overstapt. De relevante artikelen bevatten instructies over hoe dit te doen.
**Linux opstarten** | Als /boot zich op een speciale partitie bevindt, moet deze zich op de osschijf bevinden en niet over meerdere schijven worden verspreid.<br/> Als /boot deel uitmaakt van de hoofdpartitie (/), moet de '/' partitie op de OS-schijf staan en geen andere schijven overspannen.
**UEFI-opstart** | VM's met UEFI-boot worden niet ondersteund voor migratie.
**Schijfgrootte** | 2 TB OS schijf; 8 TB voor dataschijven.
**Schijflimieten** |  Maximaal 60 schijven per vm.
**Versleutelde schijven/volumes** | VM's met versleutelde schijven/volumes worden niet ondersteund voor migratie.
**Gedeelde schijfcluster** | Wordt niet ondersteund.
**Onafhankelijke schijven** | Wordt niet ondersteund.
**RDM/passthrough-schijven** | Als VM's RDM- of passthrough-schijven hebben, worden deze schijven niet gerepliceerd naar Azure.
**NFS** | NFS-volumes die zijn gemonteerd als volumes op de VM's worden niet gerepliceerd.
**iSCSI-doelen** | VM's met iSCSI-doelen worden niet ondersteund voor agentloze migratie.
**Multipath IO** | Wordt niet ondersteund.
**Opslag vMotion** | Wordt niet ondersteund. Replicatie werkt niet als een VM opslagvMotion gebruikt.
**Samenwerkende NIC's** | Wordt niet ondersteund.
**IPv6** | Wordt niet ondersteund.
**Doelschijf** | VM's kunnen alleen worden gemigreerd naar beheerde schijven (standaard HDD, premium SSD) in Azure.
**Gelijktijdige replicatie** | 100 VM's per vCenter-server. Als u meer hebt, migreert u ze in batches van 100.


## <a name="agentless-azure-migrate-appliance"></a>Apparaat agentless-Azure migreren 

Agentless migration maakt gebruik van het [Azure Migrate-toestel](migrate-appliance.md). U het toestel implementeren als VMWare VM Met behulp van een OVA-sjabloon, geïmporteerd in vCenter Server of met behulp van een [PowerShell-script.](deploy-appliance-script.md)

- Meer informatie over [toestelvereisten](migrate-appliance.md#appliance---vmware) voor VMware.
- Meer informatie over URL's die het toestel moet openen in [openbare](migrate-appliance.md#public-cloud-urls) en [overheidsclouds.](migrate-appliance.md#government-cloud-urls)
- In Azure Government moet u het toestel implementeren met behulp van het script.

## <a name="agentless-ports"></a>Agentless-poorten

**Apparaat** | **Verbinding**
--- | ---
Toestel | Uitgaande verbindingen op poort 443 om gerepliceerde gegevens naar Azure te uploaden en te communiceren met Azure Migrate services die replicatie en migratie orkestreren.
vCenter-server | Binnenkomende verbindingen op poort 443 om het toestel in staat te stellen replicatie te orkestreren - snapshots maken, gegevens kopiëren, momentopnamen vrijgeven
vSphere/EXSI-host | Binnenkomend op TCP-poort 902 voor het toestel om gegevens van momentopnamen te repliceren.


## <a name="agent-based-vmware-servers"></a>VMware-servers op basis van agent
In deze tabel worden beoordelingsondersteuning en -beperkingen voor VMware-virtualisatieservers samengevat.

**VMware-vereisten** | **Details**
--- | ---
**VMware vCenter Server** | Versie 5.5, 6.0, 6.5 of 6.7.
**VMware vSphere ESXI-host** | Versie 5.5, 6.0, 6.5 of 6.7.
**vCenter Server-machtigingen** | Een alleen-lezen account voor vCenter Server.

## <a name="agent-based-vmware-vms"></a>VMware-VM's op basis van agent

De tabel bevat een overzicht van VMware VM-ondersteuning voor VMware VM's die u wilt migreren met behulp van op agent gebaseerde migratie.

**Ondersteuning** | **Details**
--- | ---
**Werkbelasting voor machines** | Azure Migrate ondersteunt migratie van elke workload (bijvoorbeeld Active Directory, SQL-server, enz.) die wordt uitgevoerd op een ondersteunde machine.
**Besturingssystemen** | Voor de meest recente informatie, controleer de ondersteuning van het [besturingssysteem](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) voor Site Recovery. Azure Migrate biedt identieke ondersteuning voor vm-besturingssysteem.
**Linux-bestandssysteem/gastopslag** | Voor de laatste informatie, controleer de [Linux-bestandssysteem ondersteuning](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) voor Site Recovery. Azure Migrate heeft identieke ondersteuning voor Linux-bestandssystemen.
**Netwerk/opslag** | Bekijk voor de meest recente informatie de vereisten voor [netwerk-](../site-recovery/vmware-physical-azure-support-matrix.md#network) en [opslag](../site-recovery/vmware-physical-azure-support-matrix.md#storage) voor siteherstel. Azure Migrate biedt identieke netwerk-/opslagvereisten.
**Azure-vereisten** | Controleer voor de meest recente informatie de [azure-netwerk-,](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover) [opslag-](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)en [rekenvereisten](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) voor siteherstel. Azure Migrate heeft identieke vereisten voor VMware-migratie.
**Mobiliteitsdienst** | De Mobiliteitsserviceagent moet zijn geïnstalleerd op elke vm die u wilt migreren.
**UEFI-opstart** | De gemigreerde VM in Azure wordt automatisch geconverteerd naar een BIOS-opstartVM.<br/><br/> De OS-schijf moet maximaal vier partities hebben en volumes moeten worden opgemaakt met NTFS.
**Doelschijf** | VM's kunnen alleen worden gemigreerd naar beheerde schijven (standaard HDD, premium SSD) in Azure.
**Schijfgrootte** | 2 TB OS schijf; 8 TB voor dataschijven.
**Schijflimieten** |  Maximaal 63 schijven per vm.
**Versleutelde schijven/volumes** | VM's met versleutelde schijven/volumes worden niet ondersteund voor migratie.
**Gedeelde schijfcluster** | Wordt niet ondersteund.
**Onafhankelijke schijven** | Ondersteund.
**Doorgeefschijven** | Ondersteund.
**NFS** | NFS-volumes die zijn gemonteerd als volumes op de VM's worden niet gerepliceerd.
**iSCSI-doelen** | VM's met iSCSI-doelen worden niet ondersteund voor agentloze migratie.
**Multipath IO** | Wordt niet ondersteund.
**Opslag vMotion** | Ondersteund
**Samenwerkende NIC's** | Wordt niet ondersteund.
**IPv6** | Wordt niet ondersteund.




## <a name="agent-based-replication-appliance"></a>Apparaat op basis van agent-replicatie 

Wanneer u het replicatietoestel instelt met de OVA-sjabloon in de Azure Migrate-hub, wordt Windows Server 2016 uitgevoerd en voldoet het aan de ondersteuningsvereisten. Als u het replicatietoestel handmatig instelt op een fysieke server, controleert u of het voldoet aan de vereisten.

- Meer informatie over vereisten voor [replicatieapparaten](migrate-replication-appliance.md#appliance-requirements) voor VMware.
- MySQL moet op het toestel worden geïnstalleerd. Meer informatie over [installatieopties](migrate-replication-appliance.md#mysql-installation).
- Meer informatie over URL's die het replicatietoestel moet openen in [openbare](migrate-replication-appliance.md#url-access) en [overheidsclouds.](migrate-replication-appliance.md#azure-government-url-access)
- Controleer de [poorten](migrate-replication-appliance.md#port-access) waartoe het replicatietoestel toegang nodig heeft.

## <a name="agent-based-ports"></a>Agent-poorten

**Apparaat** | **Verbinding**
--- | ---
VM's | De Mobiliteitsservice die op VM's wordt uitgevoerd, communiceert met het on-premises replicatietoestel (configuratieserver) op poort HTTPS 443 inbound, voor replicatiebeheer.<br/><br/> VM's verzenden replicatiegegevens naar de processerver (uitgevoerd op de configuratieservermachine) op poort HTTPS 9443 binnenkomend. Deze poort kan worden gewijzigd.
Replicatietoestel | Het replicatietoestel orkestreert replicatie met Azure via poort HTTPS 443 outbound.
Processerver | De processerver ontvangt replicatiegegevens, optimaliseert en versleutelt deze en stuurt deze naar Azure-opslag via poort 443-uitgaand.<br/> Standaard wordt de processerver uitgevoerd op het replicatietoestel.

## <a name="azure-vm-requirements"></a>Vereisten voor Azure-VM's

Alle on-premises VM's die zijn gerepliceerd naar Azure moeten voldoen aan de Azure VM-vereisten die in deze tabel zijn samengevat. Wanneer siteherstel een vereiste controle uitvoert voor replicatie, mislukt de controle als niet aan een aantal vereisten wordt voldaan.

**Component** | **Vereisten** | **Details**
--- | --- | ---
Gastbesturingssysteem | Verifieert ondersteunde VMware VM-besturingssystemen voor migratie.<br/> U elke werkbelasting migreren die wordt uitgevoerd op een ondersteund besturingssysteem. | Controleren mislukt als niet-ondersteund.
Gastbesturingssysteemarchitectuur | 64-bits. | Controleren mislukt als niet-ondersteund.
Grootte van de besturingssysteemschijf | Max. | Controleren mislukt als niet-ondersteund.
Aantal besturingssysteemschijven | 1 | Controleren mislukt als niet-ondersteund.
Aantal gegevensschijven | 64 of minder. | Controleren mislukt als niet-ondersteund.
Gegevensschijfgrootte | Max. | Controleren mislukt als niet-ondersteund.
Netwerkadapters | Meerdere adapters worden ondersteund. |
Gedeelde VHD | Wordt niet ondersteund. | Controleren mislukt als niet-ondersteund.
FC-schijf | Wordt niet ondersteund. | Controleren mislukt als niet-ondersteund.
BitLocker | Wordt niet ondersteund. | BitLocker moet zijn uitgeschakeld voordat u replicatie voor een machine inschakelt.
VM-naam | Van 1 tot 63 tekens.<br/> Alleen letters, cijfers en afbreekstreepjes.<br/><br/> De naam van de machine moet beginnen en eindigen met een letter of nummer. |  Werk de waarde in de machine-eigenschappen bij in Siteherstel.
Verbinding maken na migratie-Windows | Verbinding maken met Azure VM's met Windows na migratie:<br/> - Voordat migratie RDP op de on-premises VM inschakelt. Zorg dat TCP- en UDP-regels zijn toegevoegd voor het profiel **Openbaar** en dat RDP is toegestaan in **Windows Firewall** > **Toegestane apps** voor alle profielen.<br/> Schakel RDP in en sta RDP toe in **Windows Firewall** -> **Toegestane apps en functies** voor **domein- en privénetwerken** voor vpn-toegang. Controleer bovendien of het SAN-beleid van het besturingssysteem is ingesteld op **OnlineAll.** [Meer informatie](prepare-for-migration.md). |
Verbinding maken na migratie-Linux | Verbinding maken met Azure VM's na migratie met SSH:<br/> Controleer vóór de migratie op de on-premises machine of de Secure Shell-service is ingesteld op Start en dat firewallregels een SSH-verbinding toestaan.<br/> Na failover u op de Azure VM binnenkomende verbindingen met de SSH-poort toestaan voor de regels van de netwerkbeveiligingsgroep op de mislukte over-VM en voor het Azure-subnet waarmee deze is verbonden. Voeg bovendien een openbaar IP-adres voor de VM toe. |  


## <a name="next-steps"></a>Volgende stappen

[Selecteer](server-migrate-overview.md) een VMware-migratieoptie.
