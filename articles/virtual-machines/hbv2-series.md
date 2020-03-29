---
title: HBv2-serie - Azure Virtual Machines
description: Specificaties voor de HBv2-serie VM's.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: eea649610ca53ccbb98b5ca361555280dcd3dafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164794"
---
# <a name="hbv2-series"></a>HBv2-serie

HBv2-serie VM's zijn geoptimaliseerd voor toepassingen die worden aangedreven door geheugenbandbreedte, zoals vloeistofdynamica, eindige elementenanalyse en reservoirsimulatie. HBv2 VM's zijn voorzien van 120 AMD EPYC 7742-processorkernen, 4 GB RAM per CPU-kern en geen gelijktijdige multithreading. Elke HBv2 VM biedt tot 340 GB/sec geheugenbandbreedte en tot 4 teraFLOPS aan FP64-compute.

Premium opslag: ondersteund

Live migratie: niet ondersteund

Updates voor geheugenbehoud: niet ondersteund

| Grootte | vCPU | Processor | Geheugen (GB) | Geheugenbandbreedte GB/s | Cpu-frequentie baseren (GHz) | All-cores frequentie (GHz, piek) | Single-core frequentie (GHz, piek) | RDMA-prestaties (Gb/s) | ONDERSTEUNING VOOR MPI | Tijdelijke opslag (GB) | Max. aantal gegevensschijven | Max Ethernet-NIC's |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3,3 | 200 | Alle | 480 + 960 | 8 | 1 |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Andere maten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.