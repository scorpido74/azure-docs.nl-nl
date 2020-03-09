---
title: Machines voorbereiden voor migratie met Azure Migrate
description: Meer informatie over het voorbereiden van on-premises machines voor migratie met Azure Migrate.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927474"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>On-premises machines voorbereiden voor migratie naar Azure

In dit artikel wordt beschreven hoe u on-premises computers voorbereidt voordat u deze naar Azure migreert met behulp van [Azure migrate: Server migratie](migrate-services-overview.md#azure-migrate-server-migration-tool).

In dit artikel leert u het volgende:
> [!div class="checklist"]
> * Migratie beperkingen controleren.
> * Controleer de vereisten van het besturings systeem en de ondersteunings beperkingen.
> * Controleer de URL en poort toegang voor de machines die u wilt migreren.
> * Bekijk wijzigingen die u mogelijk moet maken voordat u begint met de migratie.
> * Instellingen configureren, zodat de stationsletters na de migratie behouden blijven.
> * Bereid machines voor, zodat u na de migratie verbinding kunt maken met de virtuele Azure-machines.

## <a name="verify-migration-limitations"></a>Migratie beperkingen controleren

- U kunt Maxi maal 35.000 VMware-Vm's/Hyper-V-Vm's evalueren in één Azure Migrate project met behulp van Azure Migrate server migratie. Een project kan VMware-Vm's en virtuele Hyper-V-machines combi neren, tot aan de limieten voor elke.
- U kunt Maxi maal tien Vm's tegelijk selecteren voor de migratie. Als u meer wilt repliceren, repliceert u in groepen van 10.
- Voor VMware-agentloze migratie kunt u Maxi maal 100 replicaties tegelijk uitvoeren.

## <a name="verify-operating-system-requirements"></a>Vereisten voor het besturings systeem controleren

- Controleer of uw [Windows-besturings systemen](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) worden ondersteund in Azure.
- Controleer of uw [Linux-distributies](../virtual-machines/linux/endorsed-distros.md) worden ondersteund in Azure.

## <a name="see-whats-supported"></a>Bekijk wat wordt ondersteund

Voor virtuele VMware-machines ondersteunt server migratie zonder [agents of migratie op basis van een agent](server-migrate-overview.md).

- **VMware-vm's**: Controleer de [migratie vereisten en ondersteuning](migrate-support-matrix-vmware-migration.md) voor VMware-vm's.
- **Virtuele Hyper-v-machines**: Controleer de [migratie vereisten en ondersteuning](migrate-support-matrix-hyper-v-migration.md) voor virtuele Hyper-v-machines.
- **Fysieke machines**: Controleer de [migratie vereisten en ondersteuning](migrate-support-matrix-physical-migration.md) voor on-premises fysieke machines en andere gevirtualiseerde servers. 

## <a name="review-url-and-port-access"></a>De toegang tot de URL en poort controleren

Computers hebben mogelijk Internet toegang nodig tijdens de migratie.

- **Azure migrate apparaat**: Bekijk de [url's](migrate-appliance.md#url-access) en [poorten](migrate-support-matrix-vmware-migration.md#agentless-ports) die door het Azure migrate-apparaat moeten worden geopend tijdens de migratie zonder agent.
- **Migratie op basis van VMware VM-agent**: [url's](migrate-replication-appliance.md#url-access) en [poorten](migrate-replication-appliance.md#port-access) controleren het replicatie apparaat wordt gebruikt tijdens de migratie van een VMware VM-agent. 
- **Hyper-v-hosts**: Controleer [url's en poorten](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) die de hyper-v-hosts moeten openen tijdens de migratie. 
- **Fysieke servers**: Bekijk [url's](migrate-replication-appliance.md#url-access) en [poorten](migrate-replication-appliance.md#port-access) die door het replicatie apparaat worden gebruikt tijdens de migratie van de fysieke server.

## <a name="verify-required-changes-before-migrating"></a>Controleer de vereiste wijzigingen voordat u de migratie uitvoert

Sommige Vm's kunnen wijzigingen vereisen zodat ze kunnen worden uitgevoerd in Azure. Azure Migrate worden deze wijzigingen automatisch aangebracht voor virtuele machines waarop deze besturings systemen worden uitgevoerd:

- Red Hat Enterprise Linux 7.0 +, 6.5 +
- CentOS 7.0 +, 6.5 +
- SUSE Linux Enterprise Server 12 SP1 +
- Ubuntu 18.04 LTS, 16.04 LTS, 14.04 LTS
- Debian 8, 7

Voor andere besturings systemen moet u computers hand matig voorbereiden vóór de migratie. 

### <a name="prepare-windows-machines"></a>Windows-computers voorbereiden

Als u een Windows-machine migreert, moet u vóór de migratie de volgende wijzigingen aanbrengen. Als u de virtuele machine migreert voordat u de wijzigingen aanbrengt, wordt de VM mogelijk niet opgestart in Azure.

1. [Schakel Azure Serial console in](../virtual-machines/troubleshooting/serial-console-windows.md) voor de Azure-VM. Het inschakelen van de-console helpt u bij het oplossen van problemen. U hoeft de virtuele machine niet opnieuw op te starten. De virtuele machine van Azure wordt opgestart met behulp van de schijf installatie kopie. Het opstarten van de schijf installatie kopie is gelijk aan het opnieuw opstarten voor de nieuwe virtuele machine. 
2. Als u computers met Windows Server 2003 wilt migreren, installeert u Hyper-V-gast integratie Services op het VM-besturings systeem. [Meer informatie](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Linux-machines voorbereiden

1. Installeer de Hyper-V Linux-integratie Services. De meeste nieuwe versies van Linux-distributies zijn standaard Hyper-V Linux-integratie Services.
2. Bouw de Linux init-installatie kopie opnieuw op zodat deze de benodigde Hyper-V-Stuur Programma's bevat. Als u de init-installatie kopie opnieuw bouwt, zorgt u ervoor dat de virtuele machine wordt opgestart in azure (alleen vereist voor bepaalde distributies).
3. [Logboek registratie van de Azure-seriële console inschakelen](../virtual-machines/troubleshooting/serial-console-linux.md). Het inschakelen van console logboek registratie helpt u bij het oplossen van problemen. U hoeft de virtuele machine niet opnieuw op te starten. De virtuele machine van Azure wordt opgestart met behulp van de schijf installatie kopie. Het opstarten van de schijf installatie kopie is gelijk aan het opnieuw opstarten voor de nieuwe virtuele machine.
4. Werk het apparaat toewijzings bestand bij met de apparaatnaam-naar-volume koppelingen, zodat u permanente apparaat-id's gebruikt.
5. Update vermeldingen in het fstab-bestand om permanente volume-id's te gebruiken.
6. Verwijder alle udev-regels waarmee interface namen worden gereserveerd op basis van een MAC-adres, enzovoort.
7. Netwerk interfaces bijwerken om een IP-adres van DHCP te ontvangen.

Meer informatie over de [stappen voor het uitvoeren van een virtuele Linux-machine in azure](../virtual-machines/linux/create-upload-generic.md)en het verkrijgen van instructies voor een aantal populaire Linux-distributies.

## <a name="preserve-drive-letters-after-migration"></a>Stationsletters na migratie behouden

Wanneer u een on-premises machine migreert naar Microsoft Azure, kunnen de stationsletters van de andere gegevens schijven van de oorspronkelijke waarden worden gewijzigd. 

Standaard wordt aan Azure-Vm's station D toegewezen om te gebruiken als tijdelijke opslag. Deze schijf toewijzing zorgt ervoor dat alle andere gekoppelde opslag stationstoewijzingen met één letter worden verhoogd. Als uw on-premises installatie bijvoorbeeld gebruikmaakt van een gegevens schijf die is toegewezen aan station D voor het installeren van toepassingen, wordt de toewijzing voor dit station verhoogd naar station E nadat u de VM naar Azure hebt gemigreerd. Als u deze automatische toewijzing wilt voor komen en ervoor wilt zorgen dat Azure de volgende gratis stationsletter aan het tijdelijke volume toewijst, stelt u het Storage Area Network (SAN)-beleid in op **OnlineAll**:

1. Open een opdracht prompt met verhoogde bevoegdheden op de on-premises machine (niet op de hostserver).
2. Voer **Disk Part**in.
3. Voer **San**in. Als de stationsletter van het gast besturingssysteem niet wordt onderhouden, wordt **offline alle** of **offline gedeelde** gegevens weer gegeven.
4. Voer bij de **Disk Part** -prompt **San POLICY = OnlineAll**in. Met deze instelling zorgt u ervoor dat schijven online worden gebracht en zorgt u ervoor dat u op beide schijven kunt lezen en schrijven.
5. Tijdens de test migratie kunt u controleren of de stationsletters behouden blijven.

## <a name="check-azure-vm-requirements"></a>Azure VM-vereisten controleren

On-premises machines die u naar Azure repliceert, moeten voldoen aan de vereisten van de Azure-VM voor het besturings systeem en de architectuur, de schijven, netwerk instellingen en de naam van de virtuele machine. Controleer de vereisten voor [VMware-vm's en fysieke servers](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)en [virtuele Hyper-V-machines](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) voordat u de migratie uitvoert.

## <a name="prepare-to-connect-after-migration"></a>Voorbereiden om verbinding te maken na de migratie

Virtuele Azure-machines worden tijdens de migratie naar Azure gemaakt. Na de migratie moet u verbinding kunnen maken met de nieuwe virtuele machines van Azure. Er zijn meerdere stappen nodig om verbinding te kunnen maken.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Voorbereiden om verbinding te maken met Azure Windows-Vm's

On-premises Windows-computers:

1. Windows-instellingen configureren. Instellingen zijn onder andere het verwijderen van statische permanente routes of WinHTTP-proxy.
2. Zorg ervoor dat de [vereiste services](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) worden uitgevoerd.
3. Schakel extern bureau blad (RDP) in om externe verbindingen naar de on-premises machine toe te staan. Meer informatie over het [gebruik van Power shell om RDP in te scha kelen](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Als u na de migratie toegang wilt krijgen tot een Azure-VM via internet, kunt u in Windows Firewall op de on-premises computer TCP en UDP toestaan in het open bare profiel en RDP als een toegestane app instellen voor alle profielen.
5. Als u na de migratie toegang wilt krijgen tot een Azure-VM via een site-naar-site-VPN, moet u in Windows Firewall op de on-premises computer RDP toestaan voor het domein en het persoonlijke profiel. Meer informatie over het [toestaan van RDP-verkeer](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Zorg ervoor dat er geen Windows-updates in behandeling zijn op de on-premises VM wanneer u migreert. Als dat het geval is, kunnen updates na de migratie worden geïnstalleerd op de virtuele machine van Azure en kunt u zich pas aanmelden bij de VM nadat de updates zijn voltooid.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Voorbereiden om verbinding te maken met virtuele Linux-machines

On-premises Linux-machines:

1. Controleer of de Secure shell-service is ingesteld om automatisch te starten bij het opstarten van het systeem.
2. Controleer of SSH-verbindingen zijn toegestaan door de firewallregels.

### <a name="configure-azure-vms-after-migration"></a>Virtuele Azure-machines configureren na migratie

Na de migratie voert u de volgende stappen uit op de virtuele Azure-machines die zijn gemaakt:

1. Wijs een openbaar IP-adres toe aan de virtuele machine om via Internet verbinding te maken met de virtuele machine. U moet een ander openbaar IP-adres voor de virtuele Azure-machine gebruiken dan u hebt gebruikt voor uw on-premises computer. [Meer informatie](../virtual-network/virtual-network-public-ip-address.md).
2. Controleer of de NSG-regels (netwerk beveiligings groep) op de virtuele machine binnenkomende verbindingen met de RDP-of SSH-poort toestaan.
3. Controleer de [Diagnostische gegevens over opstarten](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) om de virtuele machine weer te geven.

> [!NOTE]
> De Azure Bastion-service biedt persoonlijke RDP-en SSH-toegang tot Azure-Vm's. Meer [informatie](../bastion/bastion-overview.md) over deze service.

## <a name="next-steps"></a>Volgende stappen

Bepaal welke methode u wilt gebruiken voor het [migreren van virtuele VMware-machines](server-migrate-overview.md) naar Azure, of begin met het migreren van virtuele [Hyper-V-machines](tutorial-migrate-hyper-v.md) of [fysieke servers of gevirtualiseerde of Cloud-vm's](tutorial-migrate-physical-virtual-machines.md).
