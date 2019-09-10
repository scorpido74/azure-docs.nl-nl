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
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 7218fceae71969f204c6c25ba4793a7c94341693
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858482"
---
# <a name="enable-infiniband-with-sr-iov"></a>InfiniBand inschakelen met SR-IOV

De eenvoudigste en aanbevolen manier om aan de slag te gaan met IaaS Vm's voor HPC is het gebruik van de CentOS-HPC 7,6 VM-installatie kopie van het besturings systeem. Als u uw aangepaste VM-installatie kopie gebruikt, kunt u deze het beste configureren met InfiniBand (IB) door de InfiniBandDriverLinux-of InfiniBandDriverWindows-VM-extensie toe te voegen aan uw implementatie.
Meer informatie over het gebruik van deze VM-extensies met [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) en [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

Volg de onderstaande stappen om InfiniBand hand matig te configureren op Vm's met SR-IOV ingeschakeld (huidige HB-en HC-serie). Deze stappen gelden alleen voor RHEL/CentOS. Voor Ubuntu (16,04 en 18,04) en SLES (12 SP4 en 15) werken de Stuur Programma's voor het postvak in goed.

## <a name="manually-install-ofed"></a>OFED hand matig installeren

Installeer de meest recente MLNX_OFED-Stuur Programma's voor Connectx-5 van [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26).

Voor RHEL/CentOS (voor beeld hieronder voor 7,6):

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Voor Windows downloadt en installeert u de WinOF-2-Stuur Programma's voor Connectx-5 van [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ipoib"></a>IPoIB inschakelen

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>Een IP-adres toewijzen

Wijs een IP-adres toe aan de ib0-interface, met behulp van:

- Wijs het IP-adres hand matig toe aan de ib0-interface (als basis).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

OR

- Gebruik WALinuxAgent om IP-adres toe te wijzen en het persistent te maken.

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) op Azure.
