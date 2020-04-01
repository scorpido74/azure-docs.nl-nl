---
title: Ondersteuning voor noodherstel van Hyper V VM's naar Azure met Azure Site Recovery
description: Vat de ondersteunde componenten en vereisten voor hyper-V VM-noodherstel samen naar Azure met Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 1/27/2020
ms.author: raynew
ms.openlocfilehash: fd10468e823201bfa67aaf7c570071bd075ec4ac
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420834"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Ondersteuningsmatrix voor noodherstel van on-premises Hyper-V VM's naar Azure


In dit artikel worden de ondersteunde componenten en instellingen voor noodherstel van on-premises Hyper-V VM's naar Azure samengevat met behulp van [Azure Site Recovery](site-recovery-overview.md).



## <a name="supported-scenarios"></a>Ondersteunde scenario's

**Scenario** | **Details**
--- | ---
Hyper-V met Virtual Machine Manager <br> <br>| U disaster recovery uitvoeren naar Azure voor VM's die worden uitgevoerd op Hyper-V-hosts die worden beheerd in de Virtual Machine Manager-fabric van het Systeemcentrum.<br/><br/> U dit scenario implementeren in de Azure-portal of met PowerShell.<br/><br/> Wanneer Hyper-V-hosts worden beheerd door Virtual Machine Manager, u ook disaster recovery uitvoeren naar een secundaire on-premises site. Lees [deze zelfstudie voor](hyper-v-vmm-disaster-recovery.md)meer informatie over dit scenario.
Hyper-V zonder Virtual Machine Manager | U disaster recovery uitvoeren naar Azure voor VM's die worden uitgevoerd op Hyper-V-hosts die niet worden beheerd door Virtual Machine Manager.<br/><br/> U dit scenario implementeren in de Azure-portal of met PowerShell.

## <a name="on-premises-servers"></a>On-premises servers

**Server** | **Vereisten** | **Details**
--- | --- | ---
Hyper-V (zonder Virtual Machine Manager) |  Windows Server 2019, Windows Server 2016 (inclusief servercore-installatie), Windows Server 2012 R2 met de nieuwste updates | Als u Windows Server 2012 R2 met/of SCVMM 2012 R2 al hebt geconfigureerd met Azure Site Recovery en van plan bent het besturingssysteem te upgraden, volgt u de [richtlijnen.](upgrade-2012R2-to-2016.md) 
Hyper-V (uitgevoerd met Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Als Virtual Machine Manager wordt gebruikt, moeten Windows Server 2019-hosts worden beheerd in Virtual Machine Manager 2019. Op dezelfde manier moeten Windows Server 2016-hosts worden beheerd in Virtual Machine Manager 2016.<br/><br/> Opmerking: Failback naar alternatieve locatie wordt niet ondersteund voor Windows Server 2019-hosts.


## <a name="replicated-vms"></a>Gerepliceerde VM's


In de volgende tabel worden vm-ondersteuning samengevat. Site Recovery ondersteunt alle workloads die op een ondersteund besturingssysteem worden uitgevoerd.

 **Component** | **Details**
--- | ---
VM-configuratie | VM's die worden gerepliceerd naar Azure moeten voldoen aan [azure-vereisten.](#azure-vm-requirements)
Gastbesturingssysteem | Elk gastbesturingssysteem [dat wordt ondersteund voor Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)..<br/><br/> Windows Server 2016 Nano Server wordt niet ondersteund.


## <a name="vmdisk-management"></a>VM/Disk-beheer

**Actie** | **Details**
--- | ---
Formaat schijf wijzigen op gerepliceerde Hyper-V VM | Wordt niet ondersteund. Schakel replicatie uit, breng de wijziging aan en schakel replicatie voor de vm opnieuw in.
Schijf toevoegen op gerepliceerde Hyper-V VM | Wordt niet ondersteund. Schakel replicatie uit, breng de wijziging aan en schakel replicatie voor de vm opnieuw in.

## <a name="hyper-v-network-configuration"></a>Hyper-V-netwerkconfiguratie

**Component** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
Hostnetwerk: NIC Teaming | Ja | Ja
Hostnetwerk: VLAN | Ja | Ja
Hostnetwerk: IPv4 | Ja | Ja
Hostnetwerk: IPv6 | Nee | Nee
Gast VM-netwerk: NIC Teaming | Nee | Nee
Vm-netwerk voor gasten: IPv4 | Ja | Ja
Vm-netwerk voor gasten: IPv6 | Nee | Ja
GastVM-netwerk: statisch IP (Windows) | Ja | Ja
Gast VM-netwerk: Statische IP (Linux) | Nee | Nee
Vm-netwerk voor gasten: Multi-NIC | Ja | Ja
Https-proxy | Nee | Nee



## <a name="azure-vm-network-configuration-after-failover"></a>Azure VM-netwerkconfiguratie (na failover)

**Component** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Ja | Ja
ILB (ILB) | Ja | Ja
Elb | Ja | Ja
Azure Traffic Manager | Ja | Ja
Multi-NIC | Ja | Ja
Gereserveerd IP | Ja | Ja
IPv4 | Ja | Ja
Ip-adres van bron behouden | Ja | Ja
Eindpunten azure Virtual Network-service<br/> (zonder Azure Storage-firewalls) | Ja | Ja
Versneld netwerken | Nee | Nee


## <a name="hyper-v-host-storage"></a>Hyper-V-hostopslag

**Opslag** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | --- 
NFS | N.v.t. | N.v.t.
SMB 3.0 | Ja | Ja
SAN (ISCSI) | Ja | Ja
Multi-path (MPIO). Getest met:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM voor CLARiiON | Ja | Ja

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V VM-gastopslag

**Opslag** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
Vmdk | N.v.t. | N.v.t.
VHD/VHDX | Ja | Ja
Generatie 2 VM | Ja | Ja
EFI/UEFI<br></br>De gemigreerde VM in Azure wordt automatisch geconverteerd naar een BIOS-opstartVM. De VM moet alleen Windows Server 2012 en later draaien. De OS-schijf moet maximaal vijf partities of minder hebben en de grootte van de OS-schijf moet minder dan 300 GB zijn.| Ja | Ja
Gedeelde clusterschijf | Nee | Nee
Versleutelde schijf | Nee | Nee
NFS | N.v.t. | N.v.t.
SMB 3.0 | Nee | Nee
Rdm | N.v.t. | N.v.t.
Schijf >1 TB | Ja, tot 4.095 GB | Ja, tot 4.095 GB
Schijf: 4K logische en fysieke sector | Niet ondersteund: Gen 1/Gen 2 | Niet ondersteund: Gen 1/Gen 2
Schijf: 4K logische en 512-bytes fysieke sector | Ja |  Ja
Logisch volumebeheer (LVM). LVM wordt alleen ondersteund op dataschijven. Azure biedt slechts één osschijf. | Ja | Ja
Volume met gestreepte schijf >1 TB | Ja | Ja
Opslagruimten | Nee | Nee
Hot add/remove disk Hot add/remove disk Hot add/remove | Nee | Nee
Schijf uitsluiten | Ja | Ja
Multi-path (MPIO) | Ja | Ja

## <a name="azure-storage"></a>Azure Storage

**Component** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
Lokaal redundante opslag | Ja | Ja
Geografisch redundante opslag | Ja | Ja
Geografisch redundante opslag met leestoegang | Ja | Ja
Koele opslag | Nee | Nee
Hete opslag| Nee | Nee
Blok-blobs | Nee | Nee
Encryptie in rust (SSE)| Ja | Ja
Versleuteling in rust (CMK) <br></br> (Alleen voor failover naar beheerde schijven)| Ja (via PowerShell Az 3.3.0 module) | Ja (via PowerShell Az 3.3.0 module)
Premium Storage | Ja | Ja
Service importeren/exporteren | Nee | Nee
Azure Storage-accounts met firewall ingeschakeld | Ja. Voor doelopslag en cache. | Ja. Voor doelopslag en cache.
Opslagaccount wijzigen | Nee. Het doelAzure Storage-account kan niet worden gewijzigd nadat replicatie is ingemaakt. Als u noodherstel wilt wijzigen, uitschakelen en vervolgens opnieuw wilt inschakelen. | Nee


## <a name="azure-compute-features"></a>Azure-rekenfuncties

**Functie** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
Beschikbaarheidssets | Ja | Ja
Hub | Ja | Ja  
Managed Disks | Ja, voor failover.<br/><br/> Failback van beheerde schijven wordt niet ondersteund. | Ja, voor failover.<br/><br/> Failback van beheerde schijven wordt niet ondersteund.

## <a name="azure-vm-requirements"></a>Vereisten voor Azure-VM's

On-premises VM's die u in Azure repliceert, moeten voldoen aan de Azure VM-vereisten die in deze tabel zijn samengevat.

**Component** | **Vereisten** | **Details**
--- | --- | ---
Gastbesturingssysteem | Site Recovery ondersteunt alle besturingssystemen die [worden ondersteund door Azure.](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)  | Vereisten controleren mislukt als niet-ondersteund.
Gastbesturingssysteemarchitectuur | 32-bits (Windows Server 2008)/64-bits | Vereisten controleren mislukt als niet-ondersteund.
Grootte van de besturingssysteemschijf | Tot 2.048 GB voor generatie 1 VM's.<br/><br/> Tot 300 GB voor generatie 2 VM's.  | Vereisten controleren mislukt als niet-ondersteund.
Aantal besturingssysteemschijven | 1 | Vereisten controleren mislukt als niet-ondersteund.
Aantal gegevensschijven | 16 of minder  | Vereisten controleren mislukt als niet-ondersteund.
Grootte van VHD-gegevensschijf | Max. | Vereisten controleren mislukt als niet-ondersteund.
Netwerkadapters | Meerdere netwerkadapters worden ondersteund |
Gedeelde VHD | Niet ondersteund | Vereisten controleren mislukt als niet-ondersteund.
FC-schijf | Niet ondersteund | Vereisten controleren mislukt als niet-ondersteund.
Harde-schijfindeling | VHD <br/><br/> VHDX (VHDX) | Siteherstel converteert VHDX automatisch naar VHD wanneer u niet overgaat naar Azure. Wanneer u niet meer ter plaatse komt, blijven de virtuele machines het VHDX-formaat gebruiken.
BitLocker | Niet ondersteund | BitLocker moet zijn uitgeschakeld voordat u replicatie inschakelt voor een virtuele machine.
VM-naam | 1 tot 63 tekens. Alleen letters, cijfers en afbreekstreepjes. De VM-naam moet beginnen en eindigen met een letter of cijfer. | Werk de waarde in de VM-eigenschappen in Siteherstel bij.
VM-type | Eerste generatie<br/><br/> Generatie 2--Windows | Generatie 2 VM's met een type basistype van de OS-schijf (dat een of twee gegevensvolumes bevat die zijn opgemaakt als VHDX) en minder dan 300 GB schijfruimte worden ondersteund.<br></br>Linux Generation 2 VM's worden niet ondersteund. [Meer informatie](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Vault Actions recovery Services

**Actie** |  **Hyper-V met VMM** | **Hyper-V zonder VMM**
--- | --- | ---
Kluis verplaatsen tussen resourcegroepen<br/><br/> Binnen en tussen abonnementen | Nee | Nee
Opslag, netwerk, Azure VM's verplaatsen tussen resourcegroepen<br/><br/> Binnen en tussen abonnementen | Nee | Nee

> [!NOTE]
> Wanneer u Hyper-VM's van on-premises naar Azure repliceert, u repliceren naar slechts één AD-tenant vanuit één specifieke omgeving: Hyper-V-site of Hyper-V met VMM als van toepassing.


## <a name="provider-and-agent"></a>Provider en agent

Controleer of uw implementatie compatibel is met de instellingen in dit artikel, of u de nieuwste versies van de provider en agent gebruikt.

**Naam** | **Beschrijving** | **Details**
--- | --- | --- 
Azure-siteherstelprovider | Coördineert de communicatie tussen on-premises servers en Azure <br/><br/> Hyper-V met Virtual Machine Manager: geïnstalleerd op virtual machine manager-servers<br/><br/> Hyper-V zonder Virtual Machine Manager: geïnstalleerd op Hyper-V hosts| Nieuwste versie: 5.1.2700.1 (beschikbaar via de Azure-portal)<br/><br/> [Nieuwste functies en oplossingen](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure Recovery Services-agent | Coördineert replicatie tussen Hyper-V VM's en Azure<br/><br/> Geïnstalleerd op on-premises Hyper-V-servers (met of zonder Virtual Machine Manager) | Nieuwste agent beschikbaar via de portal






## <a name="next-steps"></a>Volgende stappen
Meer informatie over het voorbereiden van [Azure](tutorial-prepare-azure.md) op noodherstel van on-premises Hyper-VVm's.
