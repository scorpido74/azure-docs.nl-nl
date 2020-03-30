---
title: Matrix-Hyper-V-noodherstel ondersteunen op een secundaire VMM-site met Azure Site Recovery
description: Hiermee wordt de ondersteuning voor Hyper-V VM-replicatie in VMM-clouds samengevat naar een secundaire site met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 1126a85ed22ee17879767a93ca75dc76dd04b747
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132960"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Ondersteuningsmatrix voor herstel na noodgeval van virtuele Hyper-V-machines naar een secundaire site

In dit artikel wordt samengevat wat wordt ondersteund wanneer u de [Azure Site Recovery-service](site-recovery-overview.md) gebruikt om Hyper-V VM's die zijn beheerd in VMM-clouds (System Center Virtual Machine Manager) te repliceren naar een secundaire site. Als u Hyper-V VM's wilt repliceren naar Azure, controleert u [deze ondersteuningsmatrix](hyper-v-azure-support-matrix.md).

> [!NOTE]
> U alleen repliceren naar een secundaire site wanneer uw Hyper-V-hosts worden beheerd in VMM-clouds.


## <a name="host-servers"></a>Hostservers

**Besturingssysteem** | **Details**
--- | ---
Windows Server 2012 R2 | Servers moeten de nieuwste updates uitvoeren.
Windows Server 2016 |  VMM 2016-clouds met een mix van Windows Server 2016- en R2-hosts van 2012 worden momenteel niet ondersteund.<br/><br/> Implementaties die zijn geüpgraded van System Center 2012 R2 VMM 2012 R2 naar System Center 2016 worden momenteel niet ondersteund.


## <a name="replicated-vm-support"></a>Ondersteuning voor gerepliceerde VM

In de volgende tabel wordt een overzicht van de ondersteuning van het besturingssysteem voor machines die zijn gerepliceerd met Site recovery. Elke werkbelasting kan worden uitgevoerd op het ondersteunde besturingssysteem.

**Windows-versie** | **Hyper-V (met VMM)**
--- | ---
Windows Server 2016 | Elk gastbesturingssysteem [dat wordt ondersteund door Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) op Windows Server 2016 
Windows Server 2012 R2 | Elk gastbesturingssysteem [dat wordt ondersteund door Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) op Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Linux-machineopslag

Alleen Linux-machines met de volgende opslag kunnen worden gerepliceerd:

- Bestandssysteem (EXT3, ETX4, ReiserFS, XFS).
- Multipath software-apparaat Mapper.
- Volumemanager (LVM2).
- Fysieke servers met HP CCISS-controlleropslag worden niet ondersteund.
- Het ReiserFS-bestandssysteem wordt alleen ondersteund op SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Netwerkconfiguratie - Vm host/gast

**Configuratie** | **Ondersteund**  
--- | --- 
Gastheer - NIC teaming | Ja 
Gastheer - VLAN | Ja 
Host - IPv4 | Ja 
Host - IPv6 | Nee 
Gast VM - NIC teaming | Nee
GastVM - IPv4 | Ja
GastVM - IPv6 | Nee
Gast VM - Windows/Linux - Statisch IP-adres | Ja
Gast VM - Multi-NIC | Ja


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Hostopslag

**Opslag (host)** | **Ondersteund**
--- | --- 
NFS | N.v.t.
SMB 3.0 |  Ja
SAN (ISCSI) | Ja
Multi-path (MPIO) | Ja

### <a name="guest-or-physical-server-storage"></a>Opslag van gasten of fysieke servers

**Configuratie** | **Ondersteund**
--- | --- | 
Vmdk |  N.v.t.
VHD/VHDX | Ja (tot 16 schijven)
Gen 2 VM | Ja
Gedeelde clusterschijf | Nee
Versleutelde schijf | Nee
UEFI (UEFI)| N.v.t.
NFS | Nee
SMB 3.0 | Nee
Rdm | N.v.t.
Schijf > 1 TB | Ja
Volume met gestreepte schijf > 1 TB<br/><br/> Lvm | Ja
Opslagruimten | Ja
Hot add/remove disk Hot add/remove disk Hot add/remove | Nee
Schijf uitsluiten | Ja
Multi-path (MPIO) | Ja

## <a name="vaults"></a>Kluizen

**Actie** | **Ondersteund**
--- | --- 
Kluizen verplaatsen tussen resourcegroepen (binnen of tussen abonnementen) |  Nee
Opslag-, netwerk-, Azure-VM's verplaatsen tussen resourcegroepen (binnen of tussen abonnementen) | Nee

## <a name="azure-site-recovery-provider"></a>Azure-siteherstelprovider

De Provider coördineert de communicatie tussen VMM-servers. 

**Laatste** | **Updates**
--- | --- 
5.1.19 ([verkrijgbaar bij portal](https://aka.ms/downloaddra) | [Nieuwste functies en oplossingen](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Volgende stappen

[Hyper-V VM's in VMM-clouds repliceren naar een secundaire site](tutorial-vmm-to-vmm.md)

