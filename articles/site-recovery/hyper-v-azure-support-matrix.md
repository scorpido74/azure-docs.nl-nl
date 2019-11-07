---
title: Ondersteuning voor nood herstel van virtuele Hyper-V-machines naar Azure met Azure Site Recovery
description: Een overzicht van de ondersteunde onderdelen en vereisten voor herstel na nood gevallen voor Hyper-V-VM'S naar Azure met Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 9af85d8d9b181d619d8895542f142708626649d1
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620839"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Ondersteunings matrix voor herstel na nood gevallen van on-premises virtuele Hyper-V-machines naar Azure


In dit artikel vindt u een overzicht van de ondersteunde onderdelen en instellingen voor herstel na nood gevallen van on-premises virtuele Hyper-V-machines naar Azure met behulp van [Azure site Recovery](site-recovery-overview.md).

> [!WARNING]
> ASR-ondersteuning voor het gebruik van SCVMM-configuratie in account wordt binnenkort afgeschaft en daarom raden we u aan de details van de [afschaffing](scvmm-site-recovery-deprecation.md) te lezen voordat u doorgaat.


## <a name="supported-scenarios"></a>Ondersteunde scenario's

**Scenario** | **Details**
--- | ---
Hyper-V met Virtual Machine Manager <br> **Dit scenario bevindt zich in het pad van de afschaffing.** <br>| U kunt herstel na nood geval uitvoeren op Azure voor virtuele machines die worden uitgevoerd op Hyper-V-hosts die worden beheerd in de System Center Virtual Machine Manager infrastructuur resources.<br/><br/> U kunt dit scenario implementeren in de Azure Portal of met behulp van Power shell.<br/><br/> Wanneer Hyper-V-hosts door Virtual Machine Manager worden beheerd, kunt u herstel na nood gevallen ook uitvoeren op een secundaire on-premises site. Lees [deze zelf studie](hyper-v-vmm-disaster-recovery.md)voor meer informatie over dit scenario.
Hyper-V zonder Virtual Machine Manager | U kunt herstel na nood geval uitvoeren op Azure voor virtuele machines die worden uitgevoerd op Hyper-V-hosts die niet worden beheerd door Virtual Machine Manager.<br/><br/> U kunt dit scenario implementeren in de Azure Portal of met behulp van Power shell.

## <a name="on-premises-servers"></a>On-premises servers

**Server** | **Vereisten** | **Details**
--- | --- | ---
Hyper-V (wordt uitgevoerd zonder Virtual Machine Manager) |  Windows Server 2019, Windows Server 2016 (inclusief Server Core-installatie), Windows Server 2012 R2 met de nieuwste updates | Als u Windows Server 2012 R2 al hebt geconfigureerd met/of SCVMM 2012 R2 met Azure Site Recovery en een upgrade van het besturings systeem wilt uitvoeren, volgt u de [documentatie bij](upgrade-2012R2-to-2016.md) de richt lijnen. 
Hyper-V (uitgevoerd met Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Als Virtual Machine Manager wordt gebruikt, moeten hosts met Windows Server 2019 worden beheerd in Virtual Machine Manager 2019. Op dezelfde manier moeten hosts met Windows Server 2016 worden beheerd in Virtual Machine Manager 2016.<br/><br/>


## <a name="replicated-vms"></a>Gerepliceerde Vm's


De volgende tabel bevat een overzicht van de VM-ondersteuning. Site Recovery ondersteunt alle workloads die worden uitgevoerd op een ondersteund besturings systeem.

 **Onderdeel** | **Details**
--- | ---
VM-configuratie | Vm's die naar Azure repliceren, moeten voldoen aan de [vereisten van Azure](#azure-vm-requirements).
Gast besturingssysteem | Elk gast besturingssysteem dat wordt [ondersteund voor Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)..<br/><br/> Windows Server 2016 nano server wordt niet ondersteund.


## <a name="vmdisk-management"></a>VM/schijf beheer

**Actie** | **Details**
--- | ---
Grootte van schijf op gerepliceerde Hyper-V-VM wijzigen | Wordt niet ondersteund. Schakel replicatie uit, breng de wijziging aan en schakel de replicatie voor de virtuele machine opnieuw in.
Een schijf toevoegen aan een gerepliceerde Hyper-V-VM | Wordt niet ondersteund. Schakel replicatie uit, breng de wijziging aan en schakel de replicatie voor de virtuele machine opnieuw in.

## <a name="hyper-v-network-configuration"></a>Configuratie van Hyper-V-netwerk

**Onderdeel** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
Hostnetwerkadapter: NIC-koppeling | Ja | Ja
Hostnetwerkadapter: VLAN | Ja | Ja
Hostcluster: IPv4 | Ja | Ja
Hostnetwerkadapter: IPv6 | Nee | Nee
VM-netwerk van de gast: NIC-koppeling | Nee | Nee
VM-netwerk van gast: IPv4 | Ja | Ja
VM-netwerk van de gast: IPv6 | Nee | Ja
VM-netwerk van de gast: statisch IP-adres (Windows) | Ja | Ja
VM-netwerk van de gast: statisch IP-adres (Linux) | Nee | Nee
VM-netwerk van de gast: meerdere NIC'S | Ja | Ja



## <a name="azure-vm-network-configuration-after-failover"></a>Azure VM-netwerk configuratie (na failover)

**Onderdeel** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
ExpressRoute van Azure | Ja | Ja
ILB | Ja | Ja
ELB | Ja | Ja
Azure Traffic Manager | Ja | Ja
Multi-NIC | Ja | Ja
Reserved IP | Ja | Ja
IPv6 | Ja | Ja
Bron-IP-adres behouden | Ja | Ja
Azure Virtual Network Service-eind punten<br/> (zonder Azure Storage firewalls) | Ja | Ja
Versneld netwerken | Nee | Nee


## <a name="hyper-v-host-storage"></a>Opslag voor Hyper-V-host

**Opslag** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | --- 
NFS | N.v.t. | N.v.t.
SMB 3.0 | Ja | Ja
SAN (ISCSI) | Ja | Ja
Meerdere paden (MPIO). Getest met:<br></br> Micro soft DSM, EMC PowerPath 5,7 SP4<br/><br/> EMC PowerPath-DSM voor CLARiiON | Ja | Ja

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V VM-gast opslag

**Opslag** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
VMDK | N.v.t. | N.v.t.
VHD/VHDX | Ja | Ja
VM van de 2e generatie | Ja | Ja
EFI/UEFI| Ja | Ja
Gedeelde cluster schijf | Nee | Nee
Versleutelde schijf | Nee | Nee
NFS | N.v.t. | N.v.t.
SMB 3.0 | Nee | Nee
RDM | N.v.t. | N.v.t.
Schijf > 1 TB | Ja, Maxi maal 4.095 GB | Ja, Maxi maal 4.095 GB
Schijf: logische en fysieke sector van 4 KB | Niet ondersteund: gen 1/gen 2 | Niet ondersteund: gen 1/gen 2
Schijf: 4 KB logische en 512 bytes fysieke sector | Ja |  Ja
Beheer van logische volumes (LVM). LVM wordt alleen ondersteund op gegevens schijven. Azure biedt slechts één besturingssysteem schijf. | Ja | Ja
Volume met gestripte schijf > 1 TB | Ja | Ja
Opslag ruimten | Nee | Nee
Hot toevoegen/verwijderen schijf | Nee | Nee
Schijf uitsluiten | Ja | Ja
Meerdere paden (MPIO) | Ja | Ja

## <a name="azure-storage"></a>Azure Storage

**Onderdeel** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
Lokaal redundante opslag | Ja | Ja
Geografisch redundante opslag | Ja | Ja
Geografisch redundante opslag met lees toegang | Ja | Ja
Cool Storage | Nee | Nee
Hot Storage| Nee | Nee
Blok-blobs | Nee | Nee
Versleuteling op rest (SSE)| Ja | Ja
Premium Storage | Ja | Ja
Import/export-service | Nee | Nee
Azure Storage-accounts waarvoor Firewall is ingeschakeld | Ja. Voor doel opslag en cache. | Ja. Voor doel opslag en cache.
Opslag account wijzigen | Nee. Het Azure Storage-doel account kan niet worden gewijzigd nadat de replicatie is ingeschakeld. Schakel herstel na nood gevallen uit en vervolgens opnieuw in. | Nee


## <a name="azure-compute-features"></a>Azure Compute-functies

**Functie** | **Hyper-V met Virtual Machine Manager** | **Hyper-V zonder Virtual Machine Manager**
--- | --- | ---
Beschikbaarheidssets | Ja | Ja
HUB | Ja | Ja  
Managed Disks | Ja, voor failover.<br/><br/> Failback van beheerde schijven wordt niet ondersteund. | Ja, voor failover.<br/><br/> Failback van beheerde schijven wordt niet ondersteund.

## <a name="azure-vm-requirements"></a>Vereisten voor Azure VM

On-premises Vm's die u naar Azure repliceert, moeten voldoen aan de vereisten van de Azure-VM die in deze tabel worden samenvatten.

**Onderdeel** | **Vereisten** | **Details**
--- | --- | ---
Gast besturingssysteem | Site Recovery ondersteunt alle besturings systemen die worden [ondersteund door Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | De controle van vereisten mislukt als dit niet wordt ondersteund.
Architectuur van gast besturingssysteem | 32-bits (Windows Server 2008)/64-bit | De controle van vereisten mislukt als dit niet wordt ondersteund.
Schijf grootte van het besturings systeem | Maxi maal 2.048 GB voor virtuele machines van de eerste generatie.<br/><br/> Maxi maal 300 GB voor virtuele machines van de tweede generatie.  | De controle van vereisten mislukt als dit niet wordt ondersteund.
Aantal besturingssysteem schijven | 1 | De controle van vereisten mislukt als dit niet wordt ondersteund.
Aantal gegevens schijven | 16 of minder  | De controle van vereisten mislukt als dit niet wordt ondersteund.
VHD-grootte van gegevens schijf | Maxi maal 4.095 GB | De controle van vereisten mislukt als dit niet wordt ondersteund.
Netwerk adapters | Meerdere netwerkadapters worden ondersteund |
Gedeelde VHD | Niet ondersteund | De controle van vereisten mislukt als dit niet wordt ondersteund.
FC-schijf | Niet ondersteund | De controle van vereisten mislukt als dit niet wordt ondersteund.
Indeling van harde schijf | SCHIJVEN <br/><br/> INDELING | Site Recovery converteert VHDX automatisch naar VHD wanneer u een failover naar Azure voert. Wanneer u een failover naar on-premises doorvoert, blijven de virtuele machines de VHDX-indeling gebruiken.
BitLocker | Niet ondersteund | BitLocker moet worden uitgeschakeld voordat u replicatie voor een virtuele machine inschakelt.
VM-naam | 1 tot 63 tekens. Alleen letters, cijfers en afbreekstreepjes. De VM-naam moet beginnen en eindigen met een letter of cijfer. | Werk de waarde in de VM-eigenschappen in Site Recovery bij.
VM-type | Generatie 1<br/><br/> Generatie 2: Windows | Virtuele machines van de tweede generatie met het type basis van het besturings systeem (met een of twee gegevens volumes, geformatteerd als VHDX) en minder dan 300 GB schijf ruimte worden ondersteund.<br></br>Vm's van de Linux-generatie 2 worden niet ondersteund. [Meer informatie](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Recovery Services kluis acties

**Actie** |  **Hyper-V met VMM** | **Hyper-V zonder VMM**
--- | --- | ---
De kluis verplaatsen tussen resource groepen<br/><br/> Binnen en tussen abonnementen | Nee | Nee
Opslag, netwerk, Azure-Vm's verplaatsen tussen resource groepen<br/><br/> Binnen en tussen abonnementen | Nee | Nee

> [!NOTE]
> Wanneer u Hyper-Vm's van on-premises repliceert naar Azure, kunt u naar slechts één AD-Tenant repliceren vanaf één specifieke omgeving-Hyper-V-site of Hyper-V met VMM, indien van toepassing.


## <a name="provider-and-agent"></a>Provider en agent

Om ervoor te zorgen dat uw implementatie compatibel is met de instellingen in dit artikel, moet u ervoor zorgen dat u de nieuwste versie van de provider en de agent uitvoert.

**Naam** | **Beschrijving** | **Details**
--- | --- | --- 
Azure Site Recovery provider | Coördineert de communicatie tussen on-premises servers en Azure <br/><br/> Hyper-V met Virtual Machine Manager: geïnstalleerd op Virtual Machine Manager-servers<br/><br/> Hyper-V zonder Virtual Machine Manager: geïnstalleerd op Hyper-V-hosts| Nieuwste versie: 5.1.2700.1 (beschikbaar via de Azure Portal)<br/><br/> [Nieuwste functies en oplossingen](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure Recovery Services-agent | Coördinaten van replicatie tussen Hyper-V-Vm's en Azure<br/><br/> Geïnstalleerd op on-premises Hyper-V-servers (met of zonder Virtual Machine Manager) | Nieuwste agent die beschikbaar is via de portal






## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [voorbereiden van Azure](tutorial-prepare-azure.md) voor herstel na nood gevallen van on-premises virtuele Hyper-V-machines.
