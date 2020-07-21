---
title: InifinBand inschakelen met SR-IOV-Azure Virtual Machines | Microsoft Docs
description: Meer informatie over het inschakelen van InfiniBand met SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: de61403b62f80bea7872d5ab3561567ae2109590
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500065"
---
# <a name="enable-infiniband-with-sr-iov"></a>InfiniBand inschakelen met SR-IOV

De Azure NC-, ND-en H-serie Vm's worden allemaal ondersteund door een speciaal InfiniBand-netwerk. Alle RDMA-ingeschakelde grootten kunnen gebruikmaken van het netwerk met behulp van Intel MPI. Sommige VM-reeksen bieden uitgebreide ondersteuning voor alle MPI-implementaties en RDMA-werk woorden via SR-IOV. Virtuele machines die geschikt zijn voor RDMA, zijn [geoptimaliseerd voor GPU](../../sizes-gpu.md) en [HPC-vm's (High-Performance Compute)](../../sizes-hpc.md) .

## <a name="choose-your-installation-path"></a>Kies uw installatiepad

Om aan de slag te gaan, is het gebruik van een platform installatie kopie vooraf geconfigureerd voor InfiniBand, waar beschikbaar:

- **HPC IaaS vm's** : om aan de slag te gaan met IaaS VM'S voor HPC, is de eenvoudigste oplossing het gebruik van de [CentOS-HPC 7,6 VM OS-installatie kopie](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), die al is geconfigureerd met Infiniband. Omdat deze installatie kopie al is geconfigureerd met InfiniBand, hoeft u deze niet hand matig te configureren. Zie voor compatibele Windows-versies [Windows RDMA-compatibele exemplaren](../../sizes-hpc.md#rdma-capable-instances).

- **GPU IaaS vm's** : er zijn momenteel geen platform installatie kopieën geconfigureerd voor door GPU geoptimaliseerde vm's, met uitzonde ring van [CentOS-HPC 7,6 VM-installatie kopie van het besturings systeem](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Als u een aangepaste installatie kopie met InfiniBand wilt configureren, raadpleegt u [MELLANOX OFED hand matig installeren](#manually-install-mellanox-ofed).

Als u een aangepaste VM-installatie kopie of een door [GPU geoptimaliseerde](../../sizes-gpu.md) virtuele machine gebruikt, moet u deze configureren met InfiniBand door de VM-extensie InfiniBandDriverLinux of InfiniBandDriverWindows toe te voegen aan uw implementatie. Meer informatie over het gebruik van deze VM-extensies met [Linux](../../sizes-hpc.md#rdma-capable-instances) en [Windows](../../sizes-hpc.md#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Mellanox OFED hand matig installeren

Als u InfiniBand met SR-IOV hand matig wilt configureren, gebruikt u de volgende stappen. In het voor beeld in deze stappen ziet u de syntaxis voor RHEL/CentOS, maar de stappen zijn algemeen en kunnen worden gebruikt voor elk compatibel besturings systeem zoals Ubuntu (16,04, 18,04 19,04) en SLES (12 SP4 en 15). De Stuur Programma's voor het postvak in werken ook, maar de Mellanox-Stuur Programma's voor openfabrics bieden meer functies.

Voor meer informatie over de ondersteunde distributies voor het Mellanox-stuur programma raadpleegt u de meest recente [Mellanox-Stuur Programma's](https://www.mellanox.com/page/products_dyn?product_family=26)voor een open Fabric-apparaat. Zie de [Mellanox-gebruikers handleiding](https://docs.mellanox.com/category/mlnxofedib)voor meer informatie over het Mellanox-stuur programma van de gebruiker.

Zie het volgende voor beeld voor het configureren van InfiniBand op Linux:

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

Voor Windows downloadt en installeert u de [MELLANOX OFED voor Windows-Stuur Programma's](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>IP via InfiniBand inschakelen

Gebruik de volgende opdrachten om IP via InfiniBand in te scha kelen.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [HPC](/azure/architecture/topics/high-performance-computing/) op Azure.
