---
title: Ondersteuning voor VMware/fysiek herstel van rampen naar een secundaire site met Azure Site Recovery
description: Hiermee wordt de ondersteuning voor noodherstel van Vm's en fysieke servers naar een secundaire site samengevat met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cabd3f7693c6b6b86bf0324bdafdfe1377d1ece8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256794"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Ondersteuningsmatrix voor noodherstel van Vm's en fysieke servers naar een secundaire site

In dit artikel wordt samengevat wat wordt ondersteund wanneer u de [Azure Site Recovery-service](site-recovery-overview.md) gebruikt voor noodherstel van VMware VM's of Windows/Linux-fysieke servers op een secundaire VMware-site.

- Als u VMware VM's of fysieke servers wilt repliceren naar Azure, controleert u [deze ondersteuningsmatrix](vmware-physical-azure-support-matrix.md).
- Als u Hyper-V VM's wilt repliceren naar een secundaire site, controleert u [deze ondersteuningsmatrix](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replicatie van on-premises Vm's vmware en fysieke servers wordt geleverd door InMage Scout. InMage Scout is opgenomen in het Azure Site Recovery-serviceabonnement.

## <a name="end-of-support-announcement"></a>Aankondiging van aflopen van ondersteuning
Het scenario siteherstel voor replicatie tussen on-premises VMware of fysieke datacenters bereikt het einde van de ondersteuning.

- Vanaf augustus 2018 kan het scenario niet worden geconfigureerd in de vault van Recovery Services en kan de InMage Scout-software niet meer uit de kluis worden gedownload. Bestaande implementaties worden ondersteund.
- - Vanaf 31 december 2020 wordt het scenario niet meer ondersteund.
Bestaande partners kunnen nieuwe klanten aan het scenario aan boord nemen totdat de ondersteuning is beëindigd.
- In de loop van 2018 en 2019 worden twee updates uitgebracht:

    - Update 7: Lost problemen met de netwerkconfiguratie en naleving op en biedt TLS 1.2-ondersteuning.
    - Update 8: Voegt ondersteuning toe voor Linux-besturingssystemen RHEL/CentOS 7.3/7.4/7.5 en voor SUSE 12
    - Na update 8 worden er geen verdere updates meer uitgebracht. Er zal beperkte hotfix-ondersteuning zijn voor de besturingssystemen die zijn toegevoegd in update 8 en bugfixes op basis van de beste inspanning.

## <a name="host-servers"></a>Hostservers

**Besturingssysteem** | **Details**
--- | ---
vCenter-server | vCenter 5.5, 6.0 en 6.5<br/><br/> Als u 6.0 of 6.5 uitvoert, moet u er rekening mee houden dat slechts 5,5 functies worden ondersteund.


## <a name="replicated-vm-support"></a>Ondersteuning voor gerepliceerde VM

In de volgende tabel wordt een overzicht van de ondersteuning van het besturingssysteem voor machines die zijn gerepliceerd met Site recovery. Elke werkbelasting kan worden uitgevoerd op het ondersteunde besturingssysteem.

**Besturingssysteem** | **Details**
--- | ---
Windows Server | 64-bits Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 met ten minste SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6,5, 6,6, 6,7, 6,8, 6,9, 7,0, 7,1, 7,2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 met de Red Hat-compatibele kernel of Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


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
NFS | Ja 
SMB 3.0 | N.v.t. 
SAN (ISCSI) | Ja 
Multi-path (MPIO) | Ja 

### <a name="guest-or-physical-server-storage"></a>Opslag van gasten of fysieke servers

**Configuratie** | **Ondersteund** 
--- | --- 
Vmdk | Ja 
VHD/VHDX | N.v.t. 
Gen 2 VM | N.v.t. 
Gedeelde clusterschijf | Ja 
Versleutelde schijf | Nee 
UEFI (UEFI)| Ja 
NFS | Nee 
SMB 3.0 | Nee 
Rdm | Ja 
Schijf > 1 TB | Ja 
Volume met gestreepte schijf > 1 TB<br/><br/> Lvm | Ja 
Opslagruimten | Nee 
Hot add/remove disk Hot add/remove disk Hot add/remove | Ja 
Schijf uitsluiten | Ja 
Multi-path (MPIO) | N.v.t. 

## <a name="vaults"></a>Kluizen

**Actie** | **Ondersteund** 
--- | --- 
Kluizen verplaatsen tussen resourcegroepen (binnen of tussen abonnementen) | Nee 
Opslag-, netwerk-, Azure-VM's verplaatsen tussen resourcegroepen (binnen of tussen abonnementen) | Nee 

## <a name="mobility-service-and-updates"></a>Mobiliteitsservice en updates

De Mobiliteitsservice coördineert replicatie tussen on-premises VMware-servers of fysieke servers en de secundaire site. Wanneer u replicatie instelt, moet u ervoor zorgen dat u over de nieuwste versie van de Mobiliteitsservice beschikt en van andere componenten.

| **Update** | **Details** |
| --- | --- |
|Scout updates | Scout-updates zijn cumulatief. <br/><br/> [Meer informatie over en download](vmware-physical-secondary-disaster-recovery.md#updates) de nieuwste Scout-updates |
|Componentupdates | Scout-updates bevatten updates voor alle componenten, waaronder de RX-server, configuratieserver, proces- en masterdoelservers, vContinuum-servers en bronservers die u wilt beveiligen.<br/><br/> [Meer informatie](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Volgende stappen

Download de [InMage Scout-gebruikershandleiding](https://aka.ms/asr-scout-user-guide)

- [Hyper-V VM's in VMM-clouds repliceren naar een secundaire site](tutorial-vmm-to-vmm.md)
- [Virtuele VMware-machines en fysieke servers repliceren naar een secundaire site](tutorial-vmware-to-vmware.md)
