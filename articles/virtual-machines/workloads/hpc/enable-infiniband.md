---
title: InifinBand inschakelen met SR-IOV - Azure Virtual Machines | Microsoft Documenten
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
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196769"
---
# <a name="enable-infiniband-with-sr-iov"></a>InfiniBand inschakelen met SR-IOV

De Azure NC-, ND- en H-serie VM's worden allemaal ondersteund door een toegewijd InfiniBand-netwerk. Alle rdma-formaten zijn in staat om gebruik te maken van dat netwerk met behulp van Intel MPI. Sommige VM-series hebben de ondersteuning voor alle MPI-implementaties en RDMA-werkwoorden uitgebreid via SR-IOV. VM's met RDMA-geschikt zijn [GPU-geoptimaliseerde](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) en [HPC-vm's (High-performance compute).](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc)

## <a name="choose-your-installation-path"></a>Kies uw installatiepad

Om aan de slag te gaan, is de eenvoudigste optie om een platformafbeelding te gebruiken die vooraf is geconfigureerd voor InfiniBand, indien beschikbaar:

- **HPC IaaS VM's** - Om aan de slag te gaan met IaaS VM's voor HPC, is de eenvoudigste oplossing het gebruik van de [CentOS-HPC 7.6 VM OS-afbeelding](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), die al is geconfigureerd met InfiniBand. Aangezien deze afbeelding al is geconfigureerd met InfiniBand, hoeft u deze niet handmatig te configureren. Zie Exemplaren met [Windows RDMA-geschikte exemplaren](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)voor compatibele Windows-versies .

- **GPU IaaS VM's** - Geen platformafbeeldingen zijn momenteel vooraf geconfigureerd voor GPU-geoptimaliseerde VM's, met uitzondering van [CentOS-HPC 7.6 VM OS-afbeelding](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Zie [Handmatig Mellanox OFED installeren](#manually-install-mellanox-ofed)om een aangepaste afbeelding met InfiniBand te configureren.

Als u een aangepaste VM-afbeelding of een [voor GPU geoptimaliseerde](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) VM gebruikt, moet u deze configureren met InfiniBand door de InfiniBandDriverLinux- of InfiniBandDriverWindows VM-extensie toe te voegen aan uw implementatie. Meer informatie over het gebruik van deze VM-extensies met [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) en [Windows.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

## <a name="manually-install-mellanox-ofed"></a>Handmatig installeren Mellanox OFED

Als u InfiniBand handmatig wilt configureren met SR-IOV, gebruikt u de volgende stappen. In het voorbeeld in deze stappen wordt syntaxis weergegeven voor RHEL/CentOS, maar de stappen zijn algemeen en kunnen worden gebruikt voor elk compatibel besturingssysteem zoals Ubuntu (16.04, 18.04 19.04) en SLES (12 SP4 en 15). De inbox drivers werken ook, maar de Mellanox OpenFabrics drivers bieden meer functies.

Voor meer informatie over de ondersteunde distributies voor de Mellanox driver, zie de nieuwste [Mellanox OpenFabrics drivers.](https://www.mellanox.com/page/products_dyn?product_family=26) Zie de [Mellanox-gebruikershandleiding](https://docs.mellanox.com/category/mlnxofedib)voor meer informatie over de Mellanox OpenFabrics-driver.

Zie het volgende voorbeeld voor het configureren van InfiniBand op Linux:

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

Download en installeer voor Windows de [Mellanox OFED voor Windows-stuurprogramma's.](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ip-over-infiniband"></a>IP inschakelen via InfiniBand

Gebruik de volgende opdrachten om IP over InfiniBand in te schakelen.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) op Azure.
