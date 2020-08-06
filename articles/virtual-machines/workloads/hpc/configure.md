---
title: Configuratie en optimalisatie van met InfiniBand ingeschakelde H-Series en N-Series Azure Virtual Machines
description: Meer informatie over het configureren en optimaliseren van de met InfiniBand ingeschakelde H-Series en N-Series Vm's voor HPC.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/01/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: dfa1c790dc0f2e229b3bfa19616e5760c3d3d02e
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87825137"
---
# <a name="configure-and-optimize-vms"></a>VM's configureren en optimaliseren

In dit artikel worden bekende technieken gedeeld voor het configureren en optimaliseren van de met InfiniBand ingeschakelde [H-Series](../../sizes-hpc.md) en [N-Series](../../sizes-gpu.md) vm's voor HPC.

## <a name="vm-images"></a>VM-installatiekopieën
Op InfiniBand ingeschakelde Vm's zijn de juiste Stuur Programma's vereist om RDMA in te scha kelen. Op Linux worden de CentOS-HPC-VM-installatie kopieën in de Marketplace vooraf geconfigureerd met de juiste Stuur Programma's. De Ubuntu-VM-installatie kopieën kunnen worden geconfigureerd met de juiste Stuur Programma's met behulp van de [instructies hier](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351). Het is ook raadzaam om [aangepaste VM-installatie kopieën](../../linux/tutorial-custom-images.md) te maken met de juiste Stuur Programma's en configuratie en deze herhaaldelijk te hergebruiken.

### <a name="centos-hpc-vm-images"></a>CentOS-HPC VM-installatie kopieën
Voor [RDMA-compatibele](../../sizes-hpc.md#rdma-capable-instances), op SR-IOV geschikte vm's, CENTOS-HPC-versie 6,5 of hoger, is er een geschikte versie van 7,5 in Marketplace. Een voor beeld: voor [vm's uit de H16-serie](../../h-series.md)wordt versie 7,1 tot 7,5 aanbevolen. Deze VM-installatie kopieën worden vooraf geladen met de netwerk directe Stuur Programma's voor RDMA en Intel MPI versie 5,1.

  Voor met SR-IOV ingeschakelde [RDMA-compatibele vm's](../../sizes-hpc.md#rdma-capable-instances), [CentOS-HPC-versie 7,6 of een latere](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) versie van VM-installatie kopieën in de Marketplace, zijn geschikt. Deze VM-installatie kopieën worden geoptimaliseerd en vooraf geladen met de OFED-Stuur Programma's voor RDMA en verschillende veelgebruikte MPI-bibliotheken en weten schappelijke computing pakketten. Dit is de eenvoudigste manier om aan de slag te gaan.

  Voor beeld van scripts die worden gebruikt bij het maken van de VM-installatie kopieën CentOS-HPC versie 7,6 en hoger van een basis installatie kopie voor CentOS Marketplace, vindt u op de [azhpc-images opslag plaats](https://github.com/Azure/azhpc-images/tree/master/centos).

### <a name="rhelcentos-vm-images"></a>VM-installatie kopieën van RHEL/CentOS
Installatie kopieën op basis van niet-HPC-RHEL of CentOS op de Marketplace kunnen worden geconfigureerd voor gebruik op [virtuele machines met RDMA-functionaliteit](../../sizes-hpc.md#rdma-capable-instances)voor SR-IOV. Meer informatie over het [inschakelen van Infiniband](enable-infiniband.md) en het instellen van [mpi](setup-mpi.md) op de vm's.

  Voor beeld van scripts die worden gebruikt bij het maken van de VM-installatie kopieën CentOS-HPC versie 7,6 en hoger van een basis installatie kopie voor CentOS Marketplace, vindt u op de [azhpc-images opslag plaats](https://github.com/Azure/azhpc-images/tree/master/centos).

### <a name="ubuntu-vm-images"></a>Ubuntu VM-installatie kopieën
Ubuntu Server 16,04 LTS, 18,04 LTS en 20,04 LTS VM-installatie kopieën in de Marketplace worden ondersteund voor [virtuele machines](../../sizes-hpc.md#rdma-capable-instances)met SR-IOV en niet-SR-IOV-ondersteuning. Meer informatie over het [inschakelen van Infiniband](enable-infiniband.md) en het instellen van [mpi](setup-mpi.md) op de vm's.

  Voor beeld van scripts die kunnen worden gebruikt bij het maken van de Ubuntu 18,04 LTS op basis van HPC VM-installatie kopieën bevinden zich op de [azhpc-installatie kopieën opslag plaats](https://github.com/Azure/azhpc-images/tree/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc).

### <a name="suse-linux-enterprise-server-vm-images"></a>VM-installatie kopieën SUSE Linux Enterprise Server
SLES 12 SP3 voor HPC, SLES 12 SP3 voor HPC (Premium), SLES 12 SP1 voor HPC, SLES 12 SP1 voor HPC (Premium), SLES 12 SP4 en SLES 15 VM-installatie kopieën in de Marketplace worden ondersteund. Deze VM-installatie kopieën worden vooraf geladen met de netwerk directe Stuur Programma's voor RDMA en Intel MPI versie 5,1. Meer informatie over het [instellen van MPI](setup-mpi.md) op de vm's.

## <a name="optimize-vms"></a>Vm's optimaliseren

Hier volgen enkele optionele optimalisatie-instellingen voor verbeterde prestaties van de virtuele machine.

### <a name="update-lis"></a>LIS bijwerken

Indien nodig voor functionaliteit of prestaties, kunnen [Lis-Stuur programma's (Linux Integration Services)](../../linux/endorsed-distros.md) worden geïnstalleerd of bijgewerkt op ondersteunde OS distributies, met name voor de implementatie met behulp van een aangepaste installatie kopie of een oudere versie van het besturings systeem, zoals CENTOS/RHEL 6. x of een eerdere versie van 7. x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Geheugen vrijmaken

Verbeter de prestaties door geheugen automatisch vrij te maken om externe geheugen toegang te voor komen.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Dit permanent maken na het opnieuw opstarten van de VM:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Firewall-en SELinux uitschakelen

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Cpupower uitschakelen

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>WALinuxAgent configureren

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
U kunt de WALinuxAgent eventueel uitschakelen als een voorbereidende taak en een achterwaartse post-taak inschakelen voor een maximale Beschik baarheid van de VM-resource voor de HPC-workload.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [inschakelen van Infiniband](enable-infiniband.md) op de vm's met de [H-Series](../../sizes-hpc.md) en [N-serie](../../sizes-gpu.md) van Infiniband.
- Meer informatie over het installeren van verschillende [ondersteunde mpi-bibliotheken](setup-mpi.md) en hun optimale configuratie op de vm's.
- Bekijk het [overzicht van de HB-serie](hb-series-overview.md) en het [HC-serie-overzicht](hc-series-overview.md) voor meer informatie over het optimaal configureren van workloads voor prestaties en schaal baarheid.
- Meer informatie over de laatste aankondigingen en enkele HPC-voor beelden en-resultaten vindt u in de blogs van de [technische community van Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Zie [High Performance Computing (HPC) in azure](/azure/architecture/topics/high-performance-computing/)voor een architectuur weergave op een hoger niveau voor het uitvoeren van HPC-workloads.