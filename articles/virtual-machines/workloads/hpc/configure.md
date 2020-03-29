---
title: High Performance Computing - Azure Virtual Machines | Microsoft Documenten
description: Meer informatie over High Performance Computing op Azure.
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
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707828"
---
# <a name="optimization-for-linux"></a>Optimalisatie voor Linux

Dit artikel toont een paar belangrijke technieken om uw OS-afbeelding te optimaliseren. Meer informatie over [het inschakelen van InfiniBand](enable-infiniband.md) en het optimaliseren van de OS-afbeeldingen.

## <a name="update-lis"></a>LIS bijwerken

Als u een aangepaste afbeelding implementeert (bijvoorbeeld een ouder besturingssysteem zoals CentOS/RHEL 7.4 of 7.5), werkt u LIS op de VM bij.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Geheugen terugwinnen

Verbeter de efficiëntie door automatisch geheugen terug te winnen om toegang tot extern geheugen te voorkomen.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Ga als volgt te werk om dit te laten aanhouden nadat VM opnieuw is opgestart:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Firewall en SELinux uitschakelen

Firewall en SELinux uitschakelen.

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

* Meer informatie over [het inschakelen van InfiniBand](enable-infiniband.md) en het optimaliseren van OS-afbeeldingen.

* Meer informatie over [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) op Azure.
