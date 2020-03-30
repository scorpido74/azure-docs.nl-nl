---
title: Versnelde netwerken inschakelen voor Azure VM-noodherstel met Azure Site Recovery
description: Beschrijft hoe u versneld netwerken inschakelt met Azure Site Recovery voor azure virtual machine disaster recovery
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 27691d8fab3e7c8ccd60351dc0be83898ff984ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73622436"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Versnelde netwerken met azure virtual machine disaster recovery

Accelerated Networking maakt single root I/O virtualisatie (SR-IOV) mogelijk voor een VM, waardoor de netwerkprestaties aanzienlijk worden verbeterd. Dit krachtige pad omzeilt de host vanaf het gegevenspad en vermindert latentie, jitter en CPU-gebruik, voor gebruik met de meest veeleisende netwerkworkloads op ondersteunde VM-typen. De volgende foto toont communicatie tussen twee VM's met en zonder versnelde netwerken:

![Vergelijking](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Azure Site Recovery stelt u in staat om de voordelen van Accelerated Networking te gebruiken voor virtuele Azure-machines die niet zijn overgegaan naar een andere Azure-regio. In dit artikel wordt beschreven hoe u Versnelde netwerken voor virtuele Azure-machines inschakelen die zijn gerepliceerd met Azure Site Recovery.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u het volgende begrijpt:
-   [Azure-replicatiearchitectuur voor](azure-to-azure-architecture.md) virtuele machines
-   [Replicatie instellen](azure-to-azure-tutorial-enable-replication.md) voor virtuele Azure-machines
-   [Niet voorbij](azure-to-azure-tutorial-failover-failback.md) Virtuele Azure-machines

## <a name="accelerated-networking-with-windows-vms"></a>Versneld netwerken met Windows VM's

Azure Site Recovery ondersteunt het inschakelen van Versnelde netwerken voor gerepliceerde virtuele machines alleen als de virtuele bronmachine Accelerated Networking heeft ingeschakeld. Als uw virtuele bronmachine geen Accelerated Networking heeft ingeschakeld, u hier leren hoe u Accelerated Networking voor virtuele Windows-machines [inschakelt.](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
De volgende distributies worden uit het vak ondersteund vanuit de Azure Gallery:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Ondersteunde VM-exemplaren
Accelerated Networking wordt ondersteund op de meeste algemene en voor compute geoptimaliseerde instantieformaten met 2 of meer vCPU's.  Deze ondersteunde series zijn: D/DSv2 en F/Fs

Op gevallen die hyperthreading ondersteunen, wordt Accelerated Networking ondersteund op VM-exemplaren met 4 of meer vCPU's. Ondersteunde series zijn: D/DSv3, E/ESv3, Fsv2 en Ms/Mms

Zie [Windows VM-formaten](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over VM-exemplaren .

## <a name="accelerated-networking-with-linux-vms"></a>Versneld netwerken met Linux VM's

Azure Site Recovery ondersteunt het inschakelen van Versnelde netwerken voor gerepliceerde virtuele machines alleen als de virtuele bronmachine Accelerated Networking heeft ingeschakeld. Als uw bron virtuele machine niet heeft Accelerated Networking ingeschakeld, u leren hoe u accelerated networking in te schakelen voor Linux virtuele machines [hier](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
De volgende distributies worden uit het vak ondersteund vanuit de Azure Gallery:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch" met backports kernel**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>Ondersteunde VM-exemplaren
Accelerated Networking wordt ondersteund op de meeste algemene en voor compute geoptimaliseerde instantieformaten met 2 of meer vCPU's.  Deze ondersteunde series zijn: D/DSv2 en F/Fs

Op gevallen die hyperthreading ondersteunen, wordt Accelerated Networking ondersteund op VM-exemplaren met 4 of meer vCPU's. Ondersteunde series zijn: D/DSv3, E/ESv3, Fsv2 en Ms/Mms.

Zie Linux VM-formaten voor meer informatie over [VM-exemplaren.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Versnelde netwerken inschakelen voor gerepliceerde VM's

Wanneer u [replicatie inschakelt](azure-to-azure-tutorial-enable-replication.md) voor virtuele Azure-machines, detecteert Site Recovery automatisch of de netwerkinterfaces voor virtuele machines Accelerated Networking hebben ingeschakeld. Als Accelerated Networking al is ingeschakeld, configureert Site Recovery accelerated networking automatisch op de netwerkinterfaces van de gerepliceerde virtuele machine.

De status van Accelerated Networking kan worden geverifieerd onder de sectie **Netwerkinterfaces** van de **compute- en netwerkinstellingen** voor de gerepliceerde virtuele machine.

![Versnelde netwerkinstelling](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Als u Accelerated Networking op de virtuele bronmachine hebt ingeschakeld nadat u replicatie hebt ingeschakeld, u versnelde netwerken voor de netwerkinterfaces van de gerepliceerde virtuele machine inschakelen volgens het volgende proces:
1. Compute- **en netwerkinstellingen** openen voor de gerepliceerde virtuele machine
2. Klik op de naam van de netwerkinterface onder de sectie **Netwerkinterfaces**
3. Selecteer **Ingeschakeld** in de vervolgkeuzelijst voor versnelde netwerken onder de kolom **Doel**

![Versneld netwerken inschakelen](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

Het bovenstaande proces moet ook worden gevolgd voor bestaande gerepliceerde virtuele machines, die niet eerder Accelerated Networking automatisch hebben ingeschakeld door Site Recovery.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [de voordelen van Accelerated Networking](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- Meer informatie over beperkingen en beperkingen van accelerated networking voor [virtuele Windows-machines](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) en [virtuele Linux-machines](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Meer informatie over [herstelplannen](site-recovery-create-recovery-plans.md) om failover van toepassingen te automatiseren.
