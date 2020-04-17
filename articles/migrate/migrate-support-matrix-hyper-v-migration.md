---
title: Ondersteuning voor Hyper-V-migratie in Azure Migreren
description: Meer informatie over ondersteuning voor Hyper-V-migratie met Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 8ec0b72cac75518ac938faa202b28d055409e8dc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538185"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Ondersteuningsmatrix voor Hyper-V-migratie

In dit artikel worden ondersteuningsinstellingen en -beperkingen voor het migreren van Hyper V VM's met [Azure Migrate: Servermigratie](migrate-services-overview.md#azure-migrate-server-migration-tool) samengevat. Als u op zoek bent naar informatie over het beoordelen van Hyper-V VM's voor migratie naar Azure, controleert u de [matrix voor beoordelingsondersteuning](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Migratiebeperkingen

U maximaal 10 VM's tegelijk selecteren voor replicatie. Als u meer machines wilt migreren, u repliceren in groepen van 10.


## <a name="hyper-v-hosts"></a>Hyper-V-hosts

| **Ondersteuning**                | **Details**               
| :-------------------       | :------------------- |
| **Implementatie**       | De Hyper-V-host kan zelfstandig of geïmplementeerd worden in een cluster. <br/>Azure Migrate replicatiesoftware (Hyper-V Replicatieprovider) is geïnstalleerd op de Hyper-V hosts.|
| **Machtigingen**           | U hebt beheerdersmachtigingen nodig voor de Hyper-V-host. |
| **Host het besturingssysteem** | Windows Server 2019, Windows Server 2016 of Windows Server 2012 R2. |
| **Poorttoegang** |  Uitgaande verbindingen op HTTPS-poort 443 om VM-replicatiegegevens te verzenden.

### <a name="url-access-public-cloud"></a>URL-toegang (public cloud)

De replicatieprovidersoftware op de Hyper-V-hosts heeft toegang tot deze URL's nodig.

**Url** | **Details**
--- | ---
login.microsoftonline.com | Toegangsbeheer en identiteitsbeheer met Active Directory.
backup.windowsazure.com | Replicatiegegevensoverdracht en -coördinatie.
*.hypervrecoverymanager.windowsazure.com | Gebruikt voor migratie.
*.blob.core.windows.net | Gegevens uploaden naar opslagaccounts. 
dc.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne controle.
time.windows.com | Controleert tijdsynchronisatie tussen systeem- en globale tijd.

### <a name="url-access-azure-government"></a>URL-toegang (Azure-overheid)

De replicatieprovidersoftware op de Hyper-V-hosts heeft toegang tot deze URL's nodig.

**Url** | **Details**
--- | ---
login.microsoftonline.us | Toegangsbeheer en identiteitsbeheer met Active Directory.
backup.windowsazure.us | Replicatiegegevensoverdracht en -coördinatie.
*.hypervrecoverymanager.windowsazure.us | Gebruikt voor migratie.
*.blob.core.usgovcloudapi.net | Gegevens uploaden naar opslagaccounts.
dc.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne controle.
time.nist.gov | Controleert tijdsynchronisatie tussen systeem- en globale tijd.


## <a name="hyper-v-vms"></a>Virtuele Hyper-V-machines

| **Ondersteuning**                  | **Details**               
| :----------------------------- | :------------------- |
| **Besturingssysteem** | Alle [Windows-](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) en [Linux-besturingssystemen](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) die worden ondersteund door Azure. |
| **Vereiste wijzigingen voor Azure** | Sommige VM's vereisen mogelijk wijzigingen, zodat ze in Azure kunnen worden uitgevoerd. Breng handmatig aanpassingen aan voor de migratie. De relevante artikelen bevatten instructies over hoe dit te doen. |
| **Linux opstarten**                 | Als /boot zich op een speciale partitie bevindt, moet deze zich op de osschijf bevinden en niet over meerdere schijven worden verspreid.<br/> Als /boot deel uitmaakt van de hoofdpartitie (/), moet de '/' partitie op de OS-schijf staan en geen andere schijven overspannen. |
| **UEFI-opstart**                  | De gemigreerde VM in Azure wordt automatisch geconverteerd naar een BIOS-opstartVM. De VM moet alleen Windows Server 2012 en later draaien. De OS-schijf moet maximaal vijf partities of minder hebben en de grootte van de OS-schijf moet minder dan 300 GB zijn.
  |
| **Schijfgrootte**                  | 2 TB voor de OS-schijf, 4 TB voor dataschijven.
| **Schijfnummer** | Maximaal 16 schijven per VM.
| **Versleutelde schijven/volumes**    | Niet ondersteund voor migratie. |
| **RDM/passthrough-schijven**      | Niet ondersteund voor migratie. |
| **Gedeelde schijf** | VM's met gedeelde schijven worden niet ondersteund voor migratie.
| **NFS**                        | NFS-volumes die zijn gemonteerd als volumes op de VM's worden niet gerepliceerd. |
| **Iscsi**                      | VM's met iSCSI-doelen worden niet ondersteund voor migratie.
| **Doelschijf**                | U migreren naar Azure VM's met alleen beheerde schijven. |
| **IPv6** | Wordt niet ondersteund.
| **NIC teaming** | Wordt niet ondersteund.
| **Azure Site Recovery** | U niet repliceren met Azure Migrate Server Migration als de VM is ingeschakeld voor replicatie met Azure Site Recovery.
| **Poorten** | Uitgaande verbindingen op HTTPS-poort 443 om VM-replicatiegegevens te verzenden.

## <a name="azure-vm-requirements"></a>Vereisten voor Azure-VM's

Alle on-premises VM's die zijn gerepliceerd naar Azure moeten voldoen aan de Azure VM-vereisten die in deze tabel zijn samengevat.

**Component** | **Vereisten** | **Details**
--- | --- | ---
Grootte van de besturingssysteemschijf | Max. | Controleren mislukt als niet-ondersteund.
Aantal besturingssysteemschijven | 1 | Controleren mislukt als niet-ondersteund.
Aantal gegevensschijven | 16 of minder. | Controleren mislukt als niet-ondersteund.
Gegevensschijfgrootte | Max. | Controleren mislukt als niet-ondersteund.
Netwerkadapters | Meerdere adapters worden ondersteund. |
Gedeelde VHD | Wordt niet ondersteund. | Controleren mislukt als niet-ondersteund.
FC-schijf | Wordt niet ondersteund. | Controleren mislukt als niet-ondersteund.
BitLocker | Wordt niet ondersteund. | BitLocker moet zijn uitgeschakeld voordat u replicatie voor een machine inschakelt.
VM-naam | Van 1 tot 63 tekens.<br/> Alleen letters, cijfers en afbreekstreepjes.<br/><br/> De naam van de machine moet beginnen en eindigen met een letter of nummer. |  Werk de waarde in de machine-eigenschappen bij in Siteherstel.
Verbinding maken na migratie-Windows | Verbinding maken met Azure VM's met Windows na migratie:<br/> - Voordat migratie RDP op de on-premises VM inschakelt. Zorg dat TCP- en UDP-regels zijn toegevoegd voor het profiel **Openbaar** en dat RDP is toegestaan in **Windows Firewall** > **Toegestane apps** voor alle profielen.<br/> Schakel RDP in en sta RDP toe in **Windows Firewall** -> **Toegestane apps en functies** voor **domein- en privénetwerken** voor vpn-toegang. Controleer bovendien of het SAN-beleid van het besturingssysteem is ingesteld op **OnlineAll.** [Meer informatie](prepare-for-migration.md). |
Verbinding maken na migratie-Linux | Verbinding maken met Azure VM's na migratie met SSH:<br/> Controleer vóór de migratie op de on-premises machine of de Secure Shell-service is ingesteld op Start en dat firewallregels een SSH-verbinding toestaan.<br/> Na failover u op de Azure VM binnenkomende verbindingen met de SSH-poort toestaan voor de regels van de netwerkbeveiligingsgroep op de mislukte over-VM en voor het Azure-subnet waarmee deze is verbonden. Voeg bovendien een openbaar IP-adres voor de VM toe. |  

## <a name="next-steps"></a>Volgende stappen

[Migreer Hyper-V VM's](tutorial-migrate-hyper-v.md) voor migratie.
