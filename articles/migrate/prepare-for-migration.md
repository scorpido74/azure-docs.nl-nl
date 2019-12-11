---
title: Machines voorbereiden voor migratie met Azure Migrate
description: Meer informatie over het voorbereiden van on-premises machines voor migratie met Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6f5535a57fae847c8a376b8b39e43955675da739
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974781"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>On-premises machines voorbereiden voor migratie naar Azure

In dit artikel wordt beschreven hoe u on-premises computers voorbereidt voordat u deze naar Azure migreert met [Azure migrate server migratie](migrate-services-overview.md#azure-migrate-server-migration-tool).


In dit artikel leert u het volgende:
> [!div class="checklist"]
> * Migratie beperkingen controleren.
> * Controleer de vereisten van het besturings systeem en de ondersteunings beperkingen.
> * Controleer de URL/poort toegang voor de machines die u wilt migreren.
> * Bekijk wijzigingen die u mogelijk moet maken voordat u begint met de migratie.
> * Instellingen configureren zodat de stationsletters na de migratie behouden blijven.
> * Bereid machines voor, zodat u na de migratie verbinding kunt maken met de virtuele Azure-machines.


## <a name="verify-migration-limitations"></a>Migratie beperkingen controleren

- U kunt Maxi maal 35.000 VMware-Vm's/Hyper-V-Vm's evalueren in één Azure Migrate project met Azure Migrate server migratie. Een project kan zowel virtuele VMware-machines als virtuele Hyper-V-machines combi neren tot de limieten voor elke.
- U kunt Maxi maal 10 Vm's tegelijk selecteren voor de migratie. Als u meer wilt repliceren, repliceert u in groepen van 10.
- Voor VMware-agentloze migratie kunt u Maxi maal 100 replicaties tegelijk uitvoeren.

## <a name="verify-operating-system-requirements"></a>Vereisten voor het besturings systeem controleren

- Controleer of uw [Windows-besturings systemen](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) worden ondersteund in Azure.
- Controleer of uw [Linux-distributies](../virtual-machines/linux/endorsed-distros.md) worden ondersteund in Azure.


## <a name="check-whats-supported"></a>Controleren wat er wordt ondersteund

- Voor virtuele VMware-machines ondersteunt Azure Migrate server migratie zonder [agents of migratie op basis van een agent](server-migrate-overview.md). Controleer de vereisten voor VMware-VM'S en ondersteuning voor migraties zonder [agent](migrate-support-matrix-vmware.md#migration---limitations) en [op basis van agents](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) .
- Controleer de [migratie vereisten en ondersteuning](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) voor virtuele Hyper-V-machines.
- Controleer de [migratie vereisten en ondersteuning](migrate-support-matrix-physical.md) voor on-premises fysieke machines of andere gevirtualiseerde servers. 




## <a name="review-urlport-access"></a>URL/poort toegang controleren

Computers hebben mogelijk Internet toegang nodig tijdens de migratie.

- Bekijk Url's die VMware-Vm's nodig hebben tijdens zonder [agents](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) of [op agents gebaseerde](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) migraties.
- Bekijk Url's die Hyper-V-hosts moeten openen tijdens de migratie. Virtuele Hyper-V-machines hebben geen toegang tot internet nodig.
- [Bekijk url's](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) die fysieke machines of andere gevirtualiseerde servers nodig hebben tijdens de migratie.
- Bij migratie op basis van een agent van virtuele VMware-machines/fysieke servers moet de Mobility-service die op de computers wordt uitgevoerd, toegang hebben tot Azure Migrate-onderdelen. Voor replicatie beheer communiceert de service die op de computer wordt uitgevoerd, met het on-premises Azure Migrate replicatie apparaat op poort HTTPS 443 binnenkomend. De computers verzenden replicatie gegevens naar de Azure Migrate-proces server op poort HTTPS 9443 binnenkomend. Deze poort kan worden gewijzigd.


## <a name="verify-required-changes-before-migration"></a>Controleer de vereiste wijzigingen vóór de migratie

Voor sommige Vm's zijn mogelijk wijzigingen vereist zodat ze kunnen worden uitgevoerd in Azure. Azure Migrate worden deze wijzigingen automatisch aangebracht voor virtuele machines waarop deze besturings systemen worden uitgevoerd:
- Red Hat Enterprise Linux 6.5+, 7.0+
- CentOS 6.5+, 7.0+
- SUSE Linux Enterprise Server 12 SP1 +
- Ubuntu 14.04LTS, 16.04LTS, 18.04LTS
- Debian 7, 8

Voor andere besturings systemen moet u de computers hand matig voorbereiden vóór de migratie. 

### <a name="prepare-windows-machines"></a>Windows-computers voorbereiden

Als u een Windows-computer wilt migreren, moet u deze wijzigingen vóór de migratie aanbrengen. Als u de virtuele machine migreert voordat u de wijzigingen aanbrengt, wordt de VM mogelijk niet opgestart in Azure.

1. [Schakel Azure Serial Access-console in](../virtual-machines/troubleshooting/serial-console-windows.md) voor de Azure-VM. Dit helpt bij het oplossen van problemen. U hoeft de virtuele machine niet opnieuw op te starten. De virtuele machine van Azure wordt opgestart met behulp van de schijf installatie kopie. Dit komt overeen met het opnieuw opstarten voor de nieuwe virtuele machine. 
2. Als u computers met Windows Server 2003 wilt migreren, installeert u Hyper-V-gast integratie Services op het VM-besturings systeem. [Meer informatie](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Linux-machines voorbereiden

1. Installeer de Hyper-V Linux-integratie Services. De meeste nieuwe versies van Linux-distributies zijn dit standaard.
2. Bouw de Linux init-installatie kopie opnieuw op zodat deze de benodigde Hyper-V-Stuur Programma's bevat. Dit zorgt ervoor dat de virtuele machine wordt opgestart in Azure en alleen is vereist voor bepaalde distributies.
3. [Logboek registratie van de Azure-seriële console inschakelen](../virtual-machines/troubleshooting/serial-console-linux.md). Dit helpt bij het oplossen van problemen. U hoeft de virtuele machine niet opnieuw op te starten. De virtuele machine van Azure wordt opgestart met behulp van de schijf installatie kopie. Dit komt overeen met het opnieuw opstarten voor de nieuwe virtuele machine.
4. Werk het kaart bestand met de apparaatnaam bij naar volume koppelingen, zodat u permanente apparaat-id's gebruikt.
5. Update fstab-vermeldingen om permanente volume-id's te gebruiken.
6. Verwijder alle udev-regels waarmee interface namen worden gereserveerd op basis van een MAC-adres, enzovoort.
7. Netwerk interfaces bijwerken om een IP-adres van DHCP te ontvangen.
8. Meer [informatie](../virtual-machines/linux/create-upload-generic.md) over de stappen die nodig zijn voor het uitvoeren van een virtuele Linux-machine in Azure en over het verkrijgen van instructies voor een aantal populaire Linux-distributies.

## <a name="preserve-drive-letters-after-migration"></a>Stationsletters na migratie behouden

Wanneer u een on-premises machine migreert naar Microsoft Azure, kunnen de stationsletters van de andere gegevens schijven van de vorige waarden worden gewijzigd. Standaard wordt aan Azure-Vm's station D toegewezen voor gebruik als tijdelijke opslag. Deze schijf toewijzing zorgt ervoor dat alle andere gekoppelde opslag stationstoewijzingen met één letter worden verhoogd.

Als uw on-premises installatie bijvoorbeeld gebruikmaakt van een gegevens schijf die is toegewezen aan station D voor het installeren van toepassingen, wordt de toewijzing voor dit station verhoogd naar station E nadat u de VM naar Azure hebt gemigreerd. Als u deze automatische toewijzing wilt voor komen en ervoor wilt zorgen dat Azure de volgende gratis stationsletter aan het tijdelijke volume toewijst, stelt u het Storage Area Network (SAN)-beleid als volgt in op OnlineAll:

1. Open een opdracht prompt met verhoogde bevoegdheden op de on-premises machine (niet op de hostserver).
2. Typ **DiskPart**.
3. Typ **San**. Als de stationsletter van het gast besturingssysteem niet wordt onderhouden, wordt **offline alle** of **offline gedeelde** gegevens weer gegeven.
4. Typ **San POLICY = OnlineAll**bij de **Disk Part** -prompt. Deze instelling zorgt ervoor dat schijven online worden gebracht en zijn zowel leesbaar als beschrijfbaar.
5. Tijdens de test migratie kunt u controleren of de stationsletters behouden blijven.


## <a name="check-azure-vm-requirements"></a>Azure VM-vereisten controleren

On-premises machines die u naar Azure repliceert, moeten voldoen aan de vereisten van de Azure-VM voor het besturings systeem en de architectuur, schijven, netwerk instellingen en VM-naamgeving. Controleer de vereisten voor [VMware-vm's/fysieke servers](migrate-support-matrix-vmware.md#azure-vm-requirements)en [virtuele Hyper-V-machines](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) vóór de migratie.


## <a name="prepare-to-connect-after-migration"></a>Voorbereiden om verbinding te maken na de migratie

Virtuele Azure-machines worden tijdens de migratie naar Azure gemaakt. Na de migratie moet u verbinding kunnen maken met de nieuwe virtuele machines van Azure. Er zijn een aantal stappen nodig om verbinding te kunnen maken.

### <a name="prepare-to-connect-to-windows-azure-vms"></a>Voorbereiden om verbinding te maken met virtuele Windows Azure-machines

Ga als volgt te werk op on-premises Windows-computers:

1. Windows-instellingen configureren. Dit geldt ook voor het verwijderen van statische permanente routes of WinHTTP-proxy.
2. Zorg ervoor dat [deze services](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) worden uitgevoerd.
3. Schakel extern bureau blad (RDP) in om externe verbindingen naar de on-premises machine toe te staan. [Meer informatie over het](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) inschakelen van RDP met Power shell.
4. Als u na de migratie toegang wilt krijgen tot een Azure-VM via internet, kunt u in Windows Firewall op de on-premises computer TCP en UDP toestaan in het open bare profiel en RDP als een toegestane app instellen voor alle profielen.
5. Als u na de migratie toegang wilt krijgen tot een Azure-VM via een site-naar-site-VPN, moet u in Windows Firewall op de on-premises computer RDP toestaan voor het domein en het persoonlijke profiel. [Meer informatie](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) over het toestaan van RDP-verkeer. 
6. Zorg ervoor dat er geen Windows-updates in behandeling zijn op de on-premises VM wanneer u migreert. Als dat het geval is, kunnen updates na de migratie worden geïnstalleerd op de virtuele machine van Azure en kunt u zich pas aanmelden bij de VM nadat de updates zijn voltooid.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Voorbereiden om verbinding te maken met virtuele Linux-machines

Ga als volgt te werk op on-premises Linux-machines:

1. Controleer of de Secure shell-service is ingesteld om automatisch te starten bij het opstarten van het systeem.
2. Controleer of SSH-verbindingen zijn toegestaan door de firewallregels.

### <a name="configure-azure-vms-after-migration"></a>Virtuele Azure-machines configureren na migratie

Na de migratie voert u de volgende stappen uit op de virtuele Azure-machines die worden gemaakt.

1. Wijs een openbaar IP-adres toe aan de virtuele machine om via Internet verbinding te maken met de virtuele machine. U kunt niet hetzelfde open bare IP-adres gebruiken voor de Azure-VM die u hebt gebruikt voor uw on-premises machine. [Meer informatie](../virtual-network/virtual-network-public-ip-address.md).
2. Controleer of de NSG-regels (netwerk beveiligings groep) op de virtuele machine binnenkomende verbindingen met de RDP-of SSH-poort toestaan.
3. Controleer de [Diagnostische gegevens over opstarten](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) om de virtuele machine weer te geven.

> [!NOTE]
> De Azure Bastion-service biedt persoonlijke RDP-en SSH-toegang tot Azure-Vm's. Meer [informatie](../bastion/bastion-overview.md) over deze service.



## <a name="next-steps"></a>Volgende stappen

Bepaal welke methode u wilt gebruiken voor het [migreren van virtuele VMware-machines](server-migrate-overview.md) naar Azure, of begin met het migreren van virtuele [Hyper-V-machines](tutorial-migrate-hyper-v.md) of [fysieke servers of gevirtualiseerde/Cloud-vm's](tutorial-migrate-physical-virtual-machines.md).
