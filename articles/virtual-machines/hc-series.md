---
title: HC-serie - Azure Virtual Machines
description: Specificaties voor de HC-serie VM's.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cc25fb9b21d535ef07bcfae673be48216427b370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164777"
---
# <a name="hc-series"></a>HC-serie

HC-serie VM's zijn geoptimaliseerd voor toepassingen die worden aangedreven door dichte berekeningen, zoals impliciete eindige elementenanalyse, moleculaire dynamica en computationele chemie. HC VM's zijn voorzien van 44 Intel Xeon Platinum 8168-processorcores, 8 GB RAM per CPU-kern en geen hyperthreading. Het Intel Xeon Platinum-platform ondersteunt Intel's rijke ecosysteem van softwaretools zoals de Intel Math Kernel Library.

ACU: 297-315

Premium opslag: ondersteund

Premium Storage Caching: ondersteund

Live migratie: niet ondersteund

Updates voor geheugenbehoud: niet ondersteund

| Grootte | vCPU | Processor | Geheugen (GB) | Geheugenbandbreedte GB/s | Cpu-frequentie baseren (GHz) | All-cores frequentie (GHz, piek) | Single-core frequentie (GHz, piek) | RDMA-prestaties (Gb/s) | ONDERSTEUNING VOOR MPI | Tijdelijke opslag (GB) | Max. aantal gegevensschijven | Max Ethernet-NIC's |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Alle | 700 | 4 | 1 |

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