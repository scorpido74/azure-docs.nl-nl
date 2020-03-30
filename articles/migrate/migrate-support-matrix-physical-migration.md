---
title: Ondersteuning voor fysieke servermigratie in Azure-migreren
description: Meer informatie over ondersteuning voor fysieke servermigratie in Azure Migrate.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e55cf6dddbc8dafd33b444e4a0dbe378d807aea1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269547"
---
# <a name="support-matrix-for-physical-server-migration"></a>Ondersteuningsmatrix voor fysieke servermigratie

In dit artikel worden ondersteuningsinstellingen en -beperkingen voor het migreren van fysieke servers met [Azure Migrate: Servermigratie](migrate-services-overview.md#azure-migrate-server-migration-tool) samengevat. Als u op zoek bent naar informatie over het beoordelen van fysieke servers voor migratie naar Azure, controleert u de [matrix voor beoordelingsondersteuning](migrate-support-matrix-physical.md).


## <a name="overview"></a>Overzicht

U on-premises machines migreren als fysieke servers, met behulp van op agent gebaseerde replicatie. Met deze tool u een breed scala aan machines migreren naar Azure:

- On-premises fysieke servers.
- VM's gevirtualiseerd door platforms zoals Xen, KVM.
- Hyper-V VM's of VMware VM's als u om de een of andere reden de standaard [Hyper-V-](tutorial-migrate-hyper-v.md) of [VMware-stromen](server-migrate-overview.md) niet wilt gebruiken.
- VM's die in privéwolken worden uitgevoerd.
- VM's die worden uitgevoerd in openbare clouds zoals Amazon Web Services (AWS) of Google Cloud Platform (GCP).


## <a name="migration-limitations"></a>Migratiebeperkingen

U maximaal 10 machines tegelijk selecteren voor replicatie. Als u meer machines wilt migreren, repliceer dan in groepen van 10.


## <a name="physical-server-requirements"></a>Vereisten voor fysieke servers

De tabel geeft een overzicht van de ondersteuning voor fysieke servers die u wilt migreren met behulp van op agenten gebaseerde migratie.

**Ondersteuning** | **Details**
--- | ---
**Werkbelasting voor machines** | Azure Migrate ondersteunt migratie van elke workload (bijvoorbeeld Active Directory, SQL-server, enz.) die wordt uitgevoerd op een ondersteunde machine.
**Besturingssystemen** | Voor de meest recente informatie, controleer de ondersteuning van het [besturingssysteem](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) voor Site Recovery. Azure Migrate biedt identieke ondersteuning voor het besturingssysteem.
**Linux-bestandssysteem/gastopslag** | Voor de laatste informatie, controleer de [Linux-bestandssysteem ondersteuning](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) voor Site Recovery. Azure Migrate biedt identieke ondersteuning voor Linux-bestandssystemen.
**Netwerk/opslag** | Bekijk voor de meest recente informatie de vereisten voor [netwerk-](../site-recovery/vmware-physical-azure-support-matrix.md#network) en [opslag](../site-recovery/vmware-physical-azure-support-matrix.md#storage) voor siteherstel. Azure Migrate biedt identieke netwerk-/opslagvereisten.
**Azure-vereisten** | Controleer voor de meest recente informatie de [azure-netwerk-,](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover) [opslag-](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)en [rekenvereisten](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) voor siteherstel. Azure Migrate heeft identieke vereisten voor fysieke servermigratie.
**Mobiliteitsdienst** | De Mobiliteitsserviceagent moet op elke machine die u wilt migreren, worden geïnstalleerd.
**UEFI-opstart** | De gemigreerde machine in Azure wordt automatisch geconverteerd naar een BIOS-boot Azure VM.<br/><br/> De OS-schijf moet maximaal vier partities hebben en volumes moeten worden opgemaakt met NTFS.
**Doelschijf** | Machines kunnen alleen worden gemigreerd naar beheerde schijven (standaard HDD, premium SSD) in Azure.
**Schijfgrootte** | 2 TB OS schijf; 8 TB voor dataschijven.
**Schijflimieten** |  Maximaal 63 schijven per machine.
**Versleutelde schijven/volumes** |  Machines met versleutelde schijven/volumes worden niet ondersteund voor migratie.
**Gedeelde schijfcluster** | Wordt niet ondersteund.
**Onafhankelijke schijven** | Ondersteund.
**Doorgeefschijven** | Ondersteund.
**NFS** | NFS-volumes die zijn gemonteerd als volumes op de machines worden niet gerepliceerd.
**iSCSI-doelen** | Machines met iSCSI-doelen worden niet ondersteund voor agentloze migratie.
**Multipath IO** | Wordt niet ondersteund.
**Opslag vMotion** | Ondersteund
**Samenwerkende NIC's** | Wordt niet ondersteund.
**IPv6** | Wordt niet ondersteund.



## <a name="replication-appliance-requirements"></a>Replicatie-apparaatvereisten

Als u het replicatietoestel handmatig instelt op een fysieke server, controleert u of het voldoet aan de vereisten die in de tabel zijn samengevat. Wanneer u het Azure Migrate-replicatietoestel instelt als vmware-vm met behulp van de OVA-sjabloon in de Azure Migrate-hub, is het toestel ingesteld met Windows Server 2016 en voldoet het aan de ondersteuningsvereisten. 

- Meer informatie over [vereisten voor replicatietoestel](migrate-replication-appliance.md#appliance-requirements).
- MySQL moet op het toestel worden geïnstalleerd. Meer informatie over [installatieopties](migrate-replication-appliance.md#mysql-installation).
- Meer informatie over [URL's](migrate-replication-appliance.md#url-access) waartoe het replicatietoestel moet worden geopend.

## <a name="azure-vm-requirements"></a>Vereisten voor Azure-VM's

Alle on-premises VM's die zijn gerepliceerd naar Azure moeten voldoen aan de Azure VM-vereisten die in deze tabel zijn samengevat. Wanneer siteherstel een vereiste controle uitvoert voor replicatie, mislukt de controle als niet aan een aantal vereisten wordt voldaan.

**Component** | **Vereisten** | **Details**
--- | --- | ---
Gastbesturingssysteem | Verifieert ondersteunde besturingssystemen.<br/> U elke werkbelasting migreren die wordt uitgevoerd op een ondersteund besturingssysteem. | Controleren mislukt als niet-ondersteund.
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

Fysieke servers [migreren.](tutorial-migrate-physical-virtual-machines.md)
