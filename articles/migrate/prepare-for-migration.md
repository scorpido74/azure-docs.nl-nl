---
title: Machines voorbereiden op migratie met Azure Migrate
description: Meer informatie over het voorbereiden van on-premises machines op migratie met Azure Migrate.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78927474"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>On-premises machines voorbereiden op migratie naar Azure

In dit artikel wordt beschreven hoe u on-premises machines voorbereiden voordat u ze naar Azure migreert met [Azure Migrate: Servermigratie](migrate-services-overview.md#azure-migrate-server-migration-tool).

In dit artikel leert u het volgende:
> [!div class="checklist"]
> * Migratiebeperkingen verifiëren.
> * Controleer de vereisten en ondersteuningsbeperkingen van het besturingssysteem.
> * Controleer url- en poorttoegang voor machines die u wilt migreren.
> * Bekijk wijzigingen die u mogelijk moet aanbrengen voordat u met de migratie begint.
> * Configureer instellingen zodat de stationsletters na de migratie behouden blijven.
> * Bereid machines zo voor dat u na migratie verbinding maken met de Azure VM's.

## <a name="verify-migration-limitations"></a>Migratiebeperkingen verifiëren

- U maximaal 35.000 VMware VM's/Hyper-V VM's beoordelen in één Azure Migrate-project met Azure Migrate Server Migration. Een project kan VMware VM's en Hyper-V VM's combineren, tot aan de limieten voor elk project.
- U maximaal 10 VM's tegelijk selecteren voor migratie. Als u meer wilt repliceren, repliceer dan in groepen van 10.
- Voor VMware-agentloze migratie u tot 100 replicaties tegelijk uitvoeren.

## <a name="verify-operating-system-requirements"></a>Vereisten van het besturingssysteem controleren

- Controleer of uw [Windows-besturingssystemen](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) worden ondersteund in Azure.
- Controleer of uw [Linux-distributies](../virtual-machines/linux/endorsed-distros.md) worden ondersteund in Azure.

## <a name="see-whats-supported"></a>Bekijk wat er wordt ondersteund

Voor VMware VM's ondersteunt Servermigratie [agentloze of agentmatige migratie.](server-migrate-overview.md)

- **VMware VM's:** Controleer [migratievereisten en ondersteuning](migrate-support-matrix-vmware-migration.md) voor VMware VM's.
- **Hyper-V VM's:** Controleer [migratievereisten en ondersteuning](migrate-support-matrix-hyper-v-migration.md) voor Hyper-V VM's.
- **Fysieke machines:** controleer [de migratievereisten en ondersteuning](migrate-support-matrix-physical-migration.md) voor on-premises fysieke machines en andere gevirtualiseerde servers. 

## <a name="review-url-and-port-access"></a>URL- en poorttoegang controleren

Machines hebben mogelijk toegang tot internet nodig tijdens de migratie.

- **Azure Migrate appliance**: Bekijk [URL's](migrate-appliance.md#url-access) en [poorten](migrate-support-matrix-vmware-migration.md#agentless-ports) die het Azure Migrate-toestel nodig heeft om toegang te krijgen tijdens de agentloze migratie.
- **VMware VM-agentgebaseerde migratie:** Bekijk [URL's](migrate-replication-appliance.md#url-access) en [poorten die](migrate-replication-appliance.md#port-access) het replicatietoestel gebruikt tijdens VMware VM-agentgebaseerde migratie. 
- **Hyper-V-hosts:** bekijk [URL's en poorten](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) waar de Hyper-V-hosts toegang toe moeten hebben tijdens de migratie. 
- **Fysieke servers:** controleer [URL's](migrate-replication-appliance.md#url-access) en [poorten die](migrate-replication-appliance.md#port-access) het replicatietoestel gebruikt tijdens fysieke servermigratie.

## <a name="verify-required-changes-before-migrating"></a>Vereiste wijzigingen verifiëren voordat u migreert

Sommige VM's vereisen mogelijk wijzigingen, zodat ze kunnen worden uitgevoerd in Azure. Azure Migrate voert deze wijzigingen automatisch aan voor VM's waarop deze besturingssystemen worden uitgevoerd:

- Red Hat Enterprise Linux 7.0+, 6.5+
- CentOS 7.0+, 6.5+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 18.04LTS, 16.04LTS, 14.04LTS
- Debian 8.

Voor andere besturingssystemen moet u machines handmatig voorbereiden voordat u overstapt. 

### <a name="prepare-windows-machines"></a>Windows-machines voorbereiden

Als u een Windows-machine migreert, voert u de volgende wijzigingen aan voordat u de migratie migreert. Als u de vm migreert voordat u de wijzigingen aanbrengt, wordt de VM mogelijk niet opgestart in Azure.

1. [Azure Serial Console inschakelen](../virtual-machines/troubleshooting/serial-console-windows.md) voor de Azure VM. Als u de console inschakelt, u problemen oplossen. U hoeft de VM niet opnieuw op te starten. De Azure VM wordt opgestart met behulp van de schijfafbeelding. De schijfafbeeldingwordt opnieuw opstarten voor de nieuwe virtuele machine. 
2. Als u machines migreert waarop Windows Server 2003 wordt uitgevoerd, installeert u Hyper-V Guest Integration Services op het VM-besturingssysteem. [Meer informatie](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Linux-machines voorbereiden

1. Installeer Hyper-V Linux Integration Services. De meeste nieuwe versies van Linux-distributies bevatten standaard Hyper-V Linux Integration Services.
2. Herbouw de Linux init afbeelding, zodat het bevat de nodige Hyper-V drivers. Als u de init-afbeelding opnieuw opbouwt, zorgt u ervoor dat de VM wordt opgestart in Azure (alleen vereist voor sommige distributies).
3. [Azure Serial Console-logboekregistratie inschakelen](../virtual-machines/troubleshooting/serial-console-linux.md). Als u consolelogboekregistratie inschakelt, u problemen oplossen. U hoeft de VM niet opnieuw op te starten. De Azure VM wordt opgestart met behulp van de schijfafbeelding. De schijfafbeeldingwordt opnieuw opstarten voor de nieuwe virtuele machine.
4. Werk het apparaatkaartbestand bij met de associaties tussen de naam van het apparaat en het volume, zodat u permanente apparaat-id's gebruikt.
5. Items in het fstab-bestand bijwerken om permanente volume-id's te gebruiken.
6. Verwijder udev-regels die interfacenamen reserveren op basis van MAC-adres, enzovoort.
7. Netwerkinterfaces bijwerken om een IP-adres van DHCP te ontvangen.

Meer informatie over de [stappen om een Linux-vm op Azure uit te voeren](../virtual-machines/linux/create-upload-generic.md)en instructies te krijgen voor enkele van de populaire Linux-distributies.

## <a name="preserve-drive-letters-after-migration"></a>Drive-letters behouden na migratie

Wanneer u een on-premises machine migreert naar Microsoft Azure, kunnen de stationsletters van extra gegevensschijven veranderen ten opzichte van hun oorspronkelijke waarden. 

Azure VM's krijgen standaard schijf D toegewezen om te gebruiken als tijdelijke opslag. Deze drive-toewijzing zorgt ervoor dat alle andere gekoppelde opslagstationstoewijzingen met één letter worden verhoogd. Als uw on-premises installatie bijvoorbeeld een gegevensschijf gebruikt die is toegewezen aan station D voor toepassingsinstallaties, wordt de toewijzing voor dit station verhoogd om E te stimuleren nadat u de VM naar Azure hebt gemigreerd. Als u deze automatische toewijzing wilt voorkomen en ervoor wilt zorgen dat Azure de volgende brief met een gratis station aan het tijdelijke volume toewijst, stelt u het SAN-beleid (Storage Area Network) in op **OnlineAll:**

1. Open op de on-premises machine (niet de hostserver) een opdrachtprompt met verhoogde bevoegdheid.
2. Voer **schijfonderdeel in**.
3. Voer **SAN**in . Als de stationsletter van het gastbesturingssysteem niet wordt bijgehouden, wordt **Offline Alles** of **Offline Gedeeld** geretourneerd.
4. Voer op de **SCHIJFPART-prompt** **SAN Policy=OnlineAll**in. Deze instelling zorgt ervoor dat schijven online worden gebracht en zorgt ervoor dat u lezen en schrijven naar beide schijven.
5. Tijdens de testmigratie u controleren of de stationsletters behouden blijven.

## <a name="check-azure-vm-requirements"></a>Azure VM-vereisten controleren

On-premises machines die u aan Azure repliceert, moeten voldoen aan de Azure VM-vereisten voor het besturingssysteem en de architectuur, de schijven, netwerkinstellingen en VM-naamgeving. Controleer de vereisten voor [Vm's en fysieke servers van VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)en [Hyper-V V's](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) voordat u migreert.

## <a name="prepare-to-connect-after-migration"></a>Voorbereiden op verbinding na migratie

Azure VM's worden gemaakt tijdens de migratie naar Azure. Na migratie moet u verbinding kunnen maken met de nieuwe Azure VM's. Er zijn meerdere stappen nodig om verbinding te maken.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Voorbereiden op verbinding maken met Azure Windows VM's

Op on-premises Windows-machines:

1. Windows-instellingen configureren. Instellingen omvatten het verwijderen van statische permanente routes of WinHTTP proxy.
2. Controleer of [de vereiste services](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) worden uitgevoerd.
3. Extern bureaublad (RDP) inschakelen om externe verbindingen met de on-premises machine toe te staan. Meer informatie over het [gebruik van PowerShell om RDP in te schakelen.](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)
4. Als u na migratie toegang wilt krijgen tot een Azure VM via internet, u in Windows Firewall op de on-premises machine TCP en UDP toestaan in het openbaar profiel en RDP instellen als een toegestane app voor alle profielen.
5. Als u na migratie toegang wilt krijgen tot een Azure VM via een site-naar-site VPN, u in Windows Firewall op de on-premises machine RDP toestaan voor de domein- en privéprofielen. Meer informatie over het [toestaan van RDP-verkeer](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Zorg ervoor dat er geen Windows-updates in behandeling zijn op de on-premises VM wanneer u migreert. Als dat het geval is, kunnen updates na migratie worden geïnstalleerd op de Azure VM en u zich pas aanmelden bij de VM als de updates zijn voltooid.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Voorbereiden om verbinding te maken met Linux Azure VM's

Op on-premises Linux machines:

1. Controleer of de Secure Shell-service is ingesteld om automatisch te starten bij het opstarten van het systeem.
2. Controleer of SSH-verbindingen zijn toegestaan door de firewallregels.

### <a name="configure-azure-vms-after-migration"></a>Azure VM's configureren na migratie

Voer na migratie de volgende stappen uit op de Azure VM's die zijn gemaakt:

1. Als u via internet verbinding wilt maken met de virtuele machine, wijst u een openbaar IP-adres toe aan de virtuele machine. U moet een ander openbaar IP-adres voor de Azure VM gebruiken dan u voor uw on-premises machine hebt gebruikt. [Meer informatie](../virtual-network/virtual-network-public-ip-address.md).
2. Controleer of de REGELS van de netwerkbeveiligingsgroep (NSG) op de VM binnenkomende verbindingen met de RDP- of SSH-poort toestaan.
3. Controleer [de opstartdiagnose](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) om de VM weer te geven.

> [!NOTE]
> De Azure Bastion-service biedt privé-RDP- en SSH-toegang tot Azure VM's. [Meer informatie](../bastion/bastion-overview.md) over deze service.

## <a name="next-steps"></a>Volgende stappen

Bepaal welke methode u wilt gebruiken om [Vm's vMware](server-migrate-overview.md) naar Azure te migreren of begin met het migreren van [Hyper-V VM's](tutorial-migrate-hyper-v.md) of [fysieke servers of gevirtualiseerde of cloudVM's.](tutorial-migrate-physical-virtual-machines.md)
