---
title: Ondersteunings matrix voor nood herstel van virtuele VMware-machines of fysieke servers naar een secundaire VMware-site met Azure Site Recovery | Microsoft Docs
description: Geeft een samen vatting van de ondersteuning voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers naar een secundaire site met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 08/22/2019
ms.author: raynew
ms.openlocfilehash: c330afb2c5d315b3d386d1477669f1aab2f6e6f9
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972085"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Ondersteunings matrix voor nood herstel van virtuele VMware-machines en fysieke servers naar een secundaire site

In dit artikel wordt een overzicht gegeven van wat er wordt ondersteund wanneer u de [Azure site Recovery](site-recovery-overview.md) -service gebruikt voor herstel na nood gevallen van virtuele VMware-machines of fysieke Windows/Linux-servers naar een secundaire VMware-site.

- Als u virtuele VMware-machines of fysieke servers naar Azure wilt repliceren, moet u [deze ondersteunings matrix](vmware-physical-azure-support-matrix.md)raadplegen.
- Als u virtuele Hyper-V-machines naar een secundaire site wilt repliceren, raadpleegt u [deze ondersteunings matrix](hyper-v-azure-support-matrix.md).

> [!NOTE]
> De replicatie van on-premises virtuele VMware-machines en fysieke servers wordt verschaft door inmage Scout. Inmage Scout is opgenomen in Azure Site Recovery service-abonnement.


## <a name="host-servers"></a>Hostservers

**Besturingssysteem** | **Details**
--- | ---
vCenter-server | vCenter 5,5, 6,0 en 6,5<br/><br/> Als u 6,0 of 6,5 uitvoert, moet u er rekening mee houden dat er slechts 5,5 functies worden ondersteund.


## <a name="replicated-vm-support"></a>Ondersteuning voor gerepliceerde VM'S

De volgende tabel bevat een overzicht van de ondersteuning van besturings systemen voor computers die met Site Recovery worden gerepliceerd. Elke werk belasting kan worden uitgevoerd op het ondersteunde besturings systeem.

**Besturingssysteem** | **Details**
--- | ---
Windows Server | 64-bits Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 met ten minste SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enter prise Linux 6,4, 6,5, 6,8 met de Red Hat compatible kernel of een onherstelbare versie van de Enter prise kernel 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Linux-machine opslag

Alleen Linux-machines met de volgende opslag kunnen worden gerepliceerd:

- Bestands systeem (EXT3, ETX4, ReiserFS, XFS).
- Multipath-software-apparaattoewijzing.
- Volume manager (LVM2).
- Fysieke servers met HP CCISS-controller opslag worden niet ondersteund.
- Het ReiserFS-bestands systeem wordt alleen ondersteund op SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Netwerk configuratie-host/gast-VM

**Configuratie** | **Ondersteund**  
--- | --- 
Host-NIC-koppeling | Ja 
Host-VLAN | Ja 
Host - IPv4 | Ja 
Host-IPv6 | Nee 
Gast-VM-NIC-koppeling | Nee
Gast-VM-IPv4 | Ja
Gast-VM-IPv6 | Nee
Gast-VM-Windows/Linux-statisch IP-adres | Ja
Gast-VM-multi-NIC | Ja


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Host Storage

**Opslag (host)** | **Ondersteund** 
--- | --- 
NFS | Ja 
SMB 3.0 | N/A 
SAN (ISCSI) | Ja 
Meerdere paden (MPIO) | Ja 

### <a name="guest-or-physical-server-storage"></a>Opslag van gast-of fysieke servers

**Configuratie** | **Ondersteund** 
--- | --- 
VMDK | Ja 
VHD/VHDX | N/A 
VM van generatie 2 | N/A 
Gedeelde cluster schijf | Ja 
Versleutelde schijf | Nee 
UEFI| Ja 
NFS | Nee 
SMB 3.0 | Nee 
RDM | Ja 
Schijf > 1 TB | Ja 
Volume met gestripte schijf > 1 TB<br/><br/> LVM | Ja 
Opslag ruimten | Nee 
Hot toevoegen/verwijderen schijf | Ja 
Schijf uitsluiten | Ja 
Meerdere paden (MPIO) | N/A 

## <a name="vaults"></a>Kluizen

**Actie** | **Ondersteund** 
--- | --- 
Kluizen verplaatsen tussen resource groepen (binnen of tussen abonnementen) | Nee 
Opslag, netwerk, Azure-Vm's verplaatsen tussen resource groepen (binnen of tussen verschillende abonnementen) | Nee 

## <a name="mobility-service-and-updates"></a>Mobility service en updates

De Mobility-service coördineert de replicatie tussen on-premises VMware-servers of fysieke servers en de secundaire site. Wanneer u de replicatie instelt, moet u ervoor zorgen dat u de nieuwste versie van de Mobility-service en andere onderdelen hebt.

| **Update** | **Details** |
| --- | --- |
|Scout-updates | Scout-updates zijn cumulatief. <br/><br/> [Meer informatie over en downloaden van](vmware-physical-secondary-disaster-recovery.md#updates) de nieuwste updates voor Scout |
|Onderdeel updates | Scout-updates bevatten updates voor alle onderdelen, waaronder de RX-server, configuratie server, proces-en hoofddoel doel servers, vContinuum-servers en bron servers die u wilt beveiligen.<br/><br/> [Meer informatie](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Volgende stappen

De [Gebruikers handleiding voor Inmage Scout](https://aka.ms/asr-scout-user-guide) downloaden

- [Virtuele Hyper-V-machines in VMM-Clouds repliceren naar een secundaire site](tutorial-vmm-to-vmm.md)
- [Virtuele VMware-machines en fysieke servers repliceren naar een secundaire site](tutorial-vmware-to-vmware.md)
