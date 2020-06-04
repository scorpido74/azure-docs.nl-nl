---
title: Ondersteuning voor Hyper-V-migratie in Azure Migrate
description: Meer informatie over ondersteuning voor Hyper-V-migratie met Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 1e1fa7e9c2e2105bef02f833e40dadf113dea4b5
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84323770"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Ondersteunings matrix voor Hyper-V-migratie

In dit artikel vindt u een overzicht van de ondersteunings instellingen en beperkingen voor het migreren van virtuele Hyper-V-machines met [Azure migrate: Server migratie](migrate-services-overview.md#azure-migrate-server-migration-tool) . Als u op zoek bent naar informatie over het evalueren van Hyper-V-Vm's voor migratie naar Azure, raadpleegt u de [ondersteunings matrix voor evaluatie](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Migratiebeperkingen

U kunt Maxi maal 10 Vm's tegelijk selecteren voor replicatie. Als u meer computers wilt migreren, repliceert u in groepen van 10.


## <a name="hyper-v-hosts"></a>Hyper-V-hosts

| **Ondersteuning**                | **Details**               
| :-------------------       | :------------------- |
| **Implementatie**       | De Hyper-V-host kan zelfstandig of in een cluster worden geïmplementeerd. <br/>Azure Migrate replicatie software (Hyper-V-replicatie provider) is geïnstalleerd op de Hyper-V-hosts.|
| **Machtigingen**           | U hebt beheerders machtigingen nodig op de Hyper-V-host. |
| **Host het besturingssysteem** | Windows Server 2019, Windows Server 2016 of Windows Server 2012 R2. |
| **Poort toegang** |  Uitgaande verbindingen op HTTPS-poort 443 voor het verzenden van VM-replicatie gegevens.

### <a name="url-access-public-cloud"></a>URL-toegang (open bare Cloud)

De replicatie provider software op de Hyper-V-hosts heeft toegang tot deze Url's nodig.

**URL** | **Details**
--- | ---
login.microsoftonline.com | Toegangs beheer en identiteits beheer met behulp van Active Directory.
backup.windowsazure.com | Replicatie gegevens overdragen en coördinatie.
*.hypervrecoverymanager.windowsazure.com | Gebruikt voor replicatie beheer.
*.blob.core.windows.net | Gegevens uploaden naar opslag accounts. 
dc.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne bewaking.
time.windows.com | Hiermee wordt de tijd synchronisatie tussen systeem en Global time gecontroleerd.

### <a name="url-access-azure-government"></a>URL-toegang (Azure Government)

De replicatie provider software op de Hyper-V-hosts heeft toegang tot deze Url's nodig.

**URL** | **Details**
--- | ---
login.microsoftonline.us | Toegangs beheer en identiteits beheer met behulp van Active Directory.
backup.windowsazure.us | Replicatie gegevens overdragen en coördinatie.
*. hypervrecoverymanager.windowsazure.us | Gebruikt voor replicatie beheer.
*. blob.core.usgovcloudapi.net | Gegevens uploaden naar opslag accounts.
dc.services.visualstudio.com | App-logboeken uploaden die worden gebruikt voor interne bewaking.
time.nist.gov | Hiermee wordt de tijd synchronisatie tussen systeem en Global time gecontroleerd.


## <a name="hyper-v-vms"></a>Virtuele Hyper-V-machines

| **Ondersteuning**                  | **Details**               
| :----------------------------- | :------------------- |
| **Besturingssysteem** | Alle [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) -en [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -besturings systemen die worden ondersteund door Azure. |
| **Vereiste wijzigingen voor Azure** | Voor sommige Vm's zijn mogelijk wijzigingen vereist zodat ze kunnen worden uitgevoerd in Azure. Pas aanpassingen hand matig aan voordat u de migratie uitvoert. De relevante artikelen bevatten instructies over hoe u dit doet. |
| **Linux-opstart**                 | Als/boot zich op een toegewezen partitie bevindt, moet deze zich op de besturingssysteem schijf bevinden en niet over meerdere schijven worden verspreid.<br/> Als/boot deel uitmaakt van de hoofd partitie (/), moet de partitie '/' zich op de besturingssysteem schijf bevindt en niet over andere schijven beschikken. |
| **UEFI-opstart**                  | De gemigreerde VM in azure wordt automatisch geconverteerd naar een BIOS-opstart-VM. Op de VM moet Windows Server 2012 en hoger worden uitgevoerd. De besturingssysteem schijf moet Maxi maal vijf partities of minder hebben en de grootte van de besturingssysteem schijf moet kleiner zijn dan 300 GB.|
| **Schijf grootte**                  | 2 TB voor de besturingssysteem schijf 4 TB voor gegevens schijven.|
| **Schijf nummer** | Maxi maal 16 schijven per VM.|
| **Versleutelde schijven/volumes**    | Niet ondersteund voor migratie.|
| **RDM/passthrough-schijven**      | Niet ondersteund voor migratie.|
| **Gedeelde schijf** | Vm's die gebruikmaken van gedeelde schijven, worden niet ondersteund voor migratie.|
| **NFS**                        | NFS-volumes die zijn gekoppeld als volumes op de Vm's, worden niet gerepliceerd.|
| **SCSI**                      | Vm's met iSCSI-doelen worden niet ondersteund voor migratie.
| **Doel schijf**                | U kunt alleen migreren naar Azure-Vm's met Managed disks. |
| **IPv6** | Niet ondersteund.|
| **NIC-koppeling** | Niet ondersteund.|
| **Azure Site Recovery** | U kunt niet repliceren met Azure Migrate server migratie als de virtuele machine is ingeschakeld voor replicatie met Azure Site Recovery.|
| **Poorten** | Uitgaande verbindingen op HTTPS-poort 443 voor het verzenden van VM-replicatie gegevens.|

## <a name="azure-vm-requirements"></a>Vereisten voor Azure-VM's

Alle on-premises Vm's die naar Azure worden gerepliceerd, moeten voldoen aan de vereisten voor de Azure-VM die in deze tabel worden samenvatten.

**Onderdeel** | **Vereisten** | **Details**
--- | --- | ---
Grootte van de besturingssysteemschijf | Maxi maal 2.048 GB. | De controle is mislukt als dit niet wordt ondersteund.
Aantal besturingssysteemschijven | 1 | De controle is mislukt als dit niet wordt ondersteund.
Aantal gegevensschijven | 16 of minder. | De controle is mislukt als dit niet wordt ondersteund.
Grootte van de gegevens schijf | Maxi maal 4.095 GB | De controle is mislukt als dit niet wordt ondersteund.
Netwerkadapters | Meerdere adapters worden ondersteund. |
Gedeelde VHD | Niet ondersteund. | De controle is mislukt als dit niet wordt ondersteund.
FC-schijf | Niet ondersteund. | De controle is mislukt als dit niet wordt ondersteund.
BitLocker | Niet ondersteund. | BitLocker moet worden uitgeschakeld voordat u replicatie voor een machine inschakelt.
VM-naam | Van 1 tot 63 tekens.<br/> Alleen letters, cijfers en afbreekstreepjes.<br/><br/> De naam van de computer moet beginnen en eindigen met een letter of cijfer. |  Werk de waarde in de computer eigenschappen in Site Recovery bij.
Verbinding maken na migratie-Windows | Verbinding maken met virtuele Azure-machines na de migratie:<br/> -Voordat de migratie RDP op de on-premises VM maakt. Zorg dat TCP- en UDP-regels zijn toegevoegd voor het profiel **Openbaar** en dat RDP is toegestaan in **Windows Firewall** > **Toegestane apps** voor alle profielen.<br/> Schakel voor site-naar-site-VPN-toegang RDP in en sta RDP toe in **Windows Firewall**  ->  **toegestane apps en functies** voor **domein-en particuliere** netwerken. Controleer bovendien of het SAN-beleid van het besturings systeem is ingesteld op **OnlineAll**. [Meer informatie](prepare-for-migration.md). |
Verbinding maken na migratie-Linux | Verbinding maken met virtuele Azure-machines na de migratie met SSH:<br/> Controleer voordat u de migratie op de on-premises computer controleert of de Secure shell-service is ingesteld op Start en of de firewall regels een SSH-verbinding toestaan.<br/> Nadat de failover is uitgevoerd op de Azure-VM, moet u binnenkomende verbindingen met de SSH-poort toestaan voor de regels voor de netwerk beveiligings groep op de virtuele machine die is mislukt en voor het Azure-subnet waarmee deze is verbonden. Voeg bovendien een openbaar IP-adres voor de virtuele machine toe. |  

## <a name="next-steps"></a>Volgende stappen

[Virtuele Hyper-V-machines migreren](tutorial-migrate-hyper-v.md) voor migratie.
