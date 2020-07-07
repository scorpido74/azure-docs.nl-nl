---
title: Bekende problemen met Vm's uit de HB-serie en HC-serie-Azure Virtual Machines | Microsoft Docs
description: Meer informatie over bekende problemen met VM-grootten van de HB-serie in Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67707783"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Bekende problemen met VM's uit de HB-serie en HC-serie

In dit artikel vindt u de meest voorkomende problemen en oplossingen bij het gebruik van Vm's uit de HB-serie en HC-serie.

## <a name="dram-on-hb-series"></a>DRAM on HB-serie

Vm's uit de HB-serie kunnen op dit moment slechts 228 GB RAM-geheugen beschikbaar maken voor gast-vm's. Dit wordt veroorzaakt door een bekende beperking van Azure Hyper Visor om te voor komen dat pagina's worden toegewezen aan de lokale DRAM van AMD CCX (NUMA-domeinen) die zijn gereserveerd voor de gast-VM.

## <a name="accelerated-networking"></a>Versneld netwerken

Versneld netwerken van Azure is op dit moment niet ingeschakeld, maar tijdens het uitvoeren van de preview-periode. Klanten worden op de hoogte gesteld wanneer deze functie wordt ondersteund.

## <a name="qp0-access-restriction"></a>qp0-toegangs beperking

Om te voor komen dat hardware-toegang op laag niveau kan leiden tot beveiligings problemen, is de wachtrij koppeling 0 niet toegankelijk voor gast-Vm's. Dit geldt alleen voor acties die doorgaans zijn gekoppeld aan het beheer van de verbinding met de Connectx-5-NIC, en voor het uitvoeren van een paar InfiniBand Diagnostics zoals ibdiagnet, maar niet voor eindgebruikers toepassingen zelf.

## <a name="ud-transport"></a>UD transport

Bij het starten biedt de HB-en HC-serie geen ondersteuning voor dynamisch verbonden Trans Port (DCT). Ondersteuning voor DCT zal na verloop van tijd worden geïmplementeerd. De trans porten betrouw bare verbinding (RC) en onbetrouwbare data gram (UD) worden ondersteund.

## <a name="gss-proxy"></a>GSS-proxy

GSS-proxy heeft een bekende fout in CentOS/RHEL 7,5 die kan worden opgedeeld als aanzienlijke prestaties en reactie snelheid bij NFS. Dit kan worden verholpen:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Cache reiniging

Op HPC-systemen is het vaak handig het geheugen op te schonen nadat een taak is voltooid voordat aan de volgende gebruiker hetzelfde knoop punt is toegewezen. Nadat u toepassingen in Linux hebt uitgevoerd, is het mogelijk dat uw beschik bare geheugen vermindert terwijl het buffer geheugen toeneemt, ondanks dat er geen toepassingen worden uitgevoerd.

![Scherm opname van opdracht prompt](./media/known-issues/cache-cleaning-1.png)

Met `numactl -H` worden de NUMAnode (s) weer gegeven waarin het geheugen wordt gebufferd (mogelijk alle). In Linux kunnen gebruikers de caches op drie manieren opschonen om gebufferde of in de cache geplaatste geheugen te retour neren naar Free. U moet hoofd zijn of over sudo-machtigingen beschikken.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Scherm opname van opdracht prompt](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Kernel-waarschuwingen

Mogelijk worden de volgende kernel-waarschuwings berichten weer gegeven wanneer u een VM van de HB-serie opstart onder Linux.

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

U kunt deze waarschuwing negeren. Dit wordt veroorzaakt door een bekende beperking van de Azure-Hyper Visor die na verloop van tijd wordt opgelost.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [High-Performance Computing](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
