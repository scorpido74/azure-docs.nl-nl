---
title: Bekende problemen met HB-serie en HC-serie VM's - Azure Virtual Machines | Microsoft Documenten
description: Meer informatie over bekende problemen met VM-formaten uit de HB-serie in Azure.
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
ms.openlocfilehash: 8d4b57fb2fee3849e102868c86fe3cab465fc70d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707783"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Bekende problemen met VM's uit de HB-serie en HC-serie

Dit artikel biedt de meest voorkomende problemen en oplossingen bij het gebruik van HB-serie en HC-serie VM's.

## <a name="dram-on-hb-series"></a>DRAM op HB-serie

VM's uit de HB-serie kunnen op dit moment slechts 228 GB RAM blootstellen aan gastVM's. Dit is te wijten aan een bekende beperking van Azure hypervisor om te voorkomen dat pagina's worden toegewezen aan de lokale DRAM van AMD CCX's (NUMA-domeinen) gereserveerd voor de gast-VM.

## <a name="accelerated-networking"></a>Versneld netwerken

Azure Accelerated Networking is op dit moment niet ingeschakeld, maar wel naarmate we verder komen in de preview-periode. We zullen klanten op de hoogte stellen wanneer deze functie wordt ondersteund.

## <a name="qp0-access-restriction"></a>Qp0 Toegangsbeperking

Om hardwaretoegang op laag niveau te voorkomen die kan leiden tot beveiligingsproblemen, is Queue Pair 0 niet toegankelijk voor vm's van gasten. Dit mag alleen gevolgen hebben voor acties die doorgaans worden geassocieerd met het beheer van de ConnectX-5 NIC en het uitvoeren van sommige InfiniBand-diagnostiek zoals ibdiagnet, maar niet op toepassingen van eindgebruikers zelf.

## <a name="ud-transport"></a>UD Transport

Bij de lancering ondersteunen de HB- en HC-serie Dynamically Connected Transport (DCT) niet. Ondersteuning voor DCT zal in de loop van de tijd worden geïmplementeerd. Reliable Connection (RC) en Unreliable Datagram (UD) transporten worden ondersteund.

## <a name="gss-proxy"></a>GSS-proxy

GSS Proxy heeft een bekende bug in CentOS/RHEL 7.5 die zich kan manifesteren als een aanzienlijke prestatie- en reactiestraf wanneer deze wordt gebruikt met NFS. Dit kan worden beperkt met:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Cachereiniging

Op HPC-systemen is het vaak handig om het geheugen op te schonen nadat een taak is voltooid voordat de volgende gebruiker hetzelfde knooppunt krijgt toegewezen. Na het uitvoeren van applicaties in Linux u merken dat uw beschikbare geheugen vermindert terwijl uw buffergeheugen toeneemt, ondanks het niet uitvoeren van toepassingen.

![Schermafbeelding van opdrachtprompt](./media/known-issues/cache-cleaning-1.png)

Met `numactl -H` behulp van zal laten zien welke NUMAnode (s) het geheugen is gebufferd met (eventueel alle). In Linux kunnen gebruikers de caches op drie manieren reinigen om gebufferd of in de cache opgeslagen geheugen terug te brengen naar 'gratis'. Je moet wortel zijn of sudo-machtigingen hebben.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Schermafbeelding van opdrachtprompt](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Kernelwaarschuwingen

Mogelijk ziet u de volgende kernelwaarschuwingsberichten bij het opstarten van een VM uit de HB-serie onder Linux.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

U deze waarschuwing negeren. Dit is te wijten aan een bekende beperking van de Azure-hypervisor die in de loop van de tijd zal worden aangepakt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [high-performance computing](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
