---
title: Vereisten voor de configuratie server voor VMware nood herstel naar Azure met Azure Site Recovery | Microsoft Docs
description: In dit artikel worden de ondersteuning en vereisten beschreven voor het implementeren van de configuratie server voor VMware nood herstel op Azure met Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 86fa817128dc89eb97bee18f4f8a6de1f650c265
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814297"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Vereisten voor de configuratie server voor VMware nood herstel naar Azure

U implementeert een on-premises configuratie server wanneer u [Azure site Recovery](site-recovery-overview.md) gebruikt voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers naar Azure.

- De configuratie server coördineert de communicatie tussen on-premises VMware en Azure. Het beheert ook de gegevens replicatie.
- Meer [informatie](vmware-azure-architecture.md) over de onderdelen en processen van de configuratie server.

## <a name="configuration-server-deployment"></a>Implementatie van de configuratie server

Voor herstel na nood gevallen van virtuele VMware-machines naar Azure, implementeert u de configuratie server als een virtuele VMware-machine.

- Site Recovery biedt een eicellen-sjabloon die u downloadt van de Azure Portal en die u importeert in vCenter Server voor het instellen van de VM van de configuratie server.
- Wanneer u de configuratie server implementeert met behulp van de sjabloon van de eicellen, voldoet de virtuele machine automatisch aan de vereisten die in dit artikel worden vermeld.
- We raden u ten zeerste aan de configuratie server in te stellen met behulp van de eicellen-sjabloon. Als u echter nood herstel instelt voor VMware-Vm's en de eicellen-sjabloon niet kunt gebruiken, kunt u de configuratie server implementeren met behulp [van deze instructies](physical-azure-set-up-source.md).
- Als u de configuratie server implementeert voor herstel na nood gevallen van on-premises fysieke machines naar Azure, volgt u de instructies in [dit artikel](physical-azure-set-up-source.md). 


## <a name="hardware-requirements"></a>Hardwarevereisten

**Onderdeel** | **Vereiste** 
--- | ---
CPU-kernen | 8 
RAM | 16 GB
Aantal schijven | 3, met inbegrip van de besturingssysteem schijf, de cache schijf van de proces server en het Bewaar station voor failback 
Vrije schijf ruimte (cache van proces server) | 600 GB
Vrije schijf ruimte (Bewaar schijf) | 600 GB

## <a name="software-requirements"></a>Softwarevereisten

**Onderdeel** | **Vereiste** 
--- | ---
Besturingssysteem | Windows Server 2012 R2 <br> Windows Server 2016
Landinstelling van het besturingssysteem | Engels (en-us)
Windows Server-functies | Deze rollen niet inschakelen: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Groeps beleid | Dit groeps beleid niet inschakelen: <br> -Toegang tot de opdracht prompt voor komen. <br> -Toegang tot register bewerkings Programma's verhinderen. <br> -Logica vertrouwen voor bestands bijlagen. <br> -Schakel uitvoering van script in. <br> [Meer informatie](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Geen vooraf bestaande standaard website <br> -Geen bestaande website/toepassing die luistert op poort 443 <br>- [Anonieme verificatie](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) inschakelen <br> - [Fastcgi](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) -instelling inschakelen 

## <a name="network-requirements"></a>Netwerkvereisten

**Onderdeel** | **Vereiste** 
--- | --- 
Type IP-adres | Statisch 
Toegang tot het internet | De server moet toegang hebben tot deze Url's (rechtstreeks of via proxy): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> -https:\//management.Azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF moet ook toegang hebben tot de volgende Url's: <br> -https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> -https:\//login.live.com  <br> -https:\//auth.gfx.MS <br> -https:\//Graph.Windows.net <br> -https:\//login.Windows.net <br> -https:\//www.live.com <br> -https:\//www.Microsoft.com <br> - https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Poorten | 443 (Orchestration-besturingselement)<br>9443 (Gegevenstransport) 
NIC-type | VMXNET3 (als de configuratie server een VMware-VM is)

## <a name="required-software"></a>Vereiste software

**Onderdeel** | **Vereiste** 
--- | ---
VMware vSphere PowerCLI | [PowerCLI versie 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) moet worden geïnstalleerd als de configuratie server wordt uitgevoerd op een virtuele VMWare-machine.
MYSQL | MySQL moet worden geïnstalleerd. U kunt hand matig installeren of Site Recovery het installeren.

## <a name="sizing-and-capacity-requirements"></a>Grootte-en capaciteits vereisten

De volgende tabel bevat een overzicht van de capaciteits vereisten voor de configuratie server. Als u meerdere virtuele VMware-machines repliceert, controleert u de [overwegingen voor capaciteits planning](site-recovery-plan-capacity-vmware.md)en voert u het hulp programma [Azure site Recovery Deployment planner](site-recovery-deployment-planner.md) voor VMware-replicatie uit. 

**Onderdeel** | **Vereiste** 
--- | ---

| **CPU** | **Geheugenmetabase** | **Cache schijf** | **Gegevens wijzigings frequentie** | **Gerepliceerde machines** |
| --- | --- | --- | --- | --- |
| 8 Vcpu's<br/><br/> 2 sockets * 4 kernen \@ 2,5 GHz | 16 GB | 300 GB | 500 GB of minder | Les dan 100 machines |
| 12 Vcpu's<br/><br/> 2 SOCKS * 6 kernen \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 tot 150 computers |
| 16 Vcpu's<br/><br/> 2 SOCKS * 8 kernen \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 machines | 



## <a name="next-steps"></a>Volgende stappen
Stel herstel na nood gevallen van [virtuele VMware-machines](vmware-azure-tutorial.md) in op Azure.
