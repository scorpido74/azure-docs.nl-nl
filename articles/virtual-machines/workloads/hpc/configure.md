---
title: High Performance Computing-Azure Virtual Machines | Microsoft Docs
description: Meer informatie over High Performance Computing in Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 723419b97dc024a700d860dd3fe61ff48073a587
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019995"
---
# <a name="optimization-for-linux"></a>Optimalisatie voor Linux

In dit artikel vindt u enkele belang rijke technieken voor het optimaliseren van uw installatie kopie van het besturings systeem. Meer informatie over het [inschakelen van Infiniband](enable-infiniband.md) en het optimaliseren van de installatie kopieën van het besturings systeem.

## <a name="update-lis"></a>LIS bijwerken

Als u implementeert met behulp van een aangepaste installatie kopie (bijvoorbeeld een ouder besturings systeem, zoals CentOS/RHEL 7,4 of 7,5), werkt u LIS bij op de VM.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Geheugen vrijmaken

Verbeter de efficiëntie door geheugen automatisch vrij te maken om externe geheugen toegang te voor komen.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Dit permanent maken na het opnieuw opstarten van de VM:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Firewall-en SELinux uitschakelen

Schakel Firewall en SELinux uit.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Cpupower uitschakelen

Schakel cpupower uit.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [inschakelen van Infiniband](enable-infiniband.md) en het optimaliseren van installatie kopieën van besturings systemen.

* Meer informatie over [HPC](/azure/architecture/topics/high-performance-computing/) op Azure.
