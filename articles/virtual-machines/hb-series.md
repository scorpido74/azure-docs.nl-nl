---
title: HB-serie - Azure Virtual Machines
description: Specificaties voor de HB-serie VM's.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 600f10e81742e9bb66c800b747fd7b2dc062754d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164828"
---
# <a name="hb-series"></a>HB-serie

VM's uit de HB-serie zijn geoptimaliseerd voor toepassingen die worden aangedreven door geheugenbandbreedte, zoals vloeistofdynamica, expliciete eindige elementenanalyse en weermodellering. HB VM's zijn voorzien van 60 AMD EPYC 7551-processorcores, 4 GB RAM per CPU-kern en geen gelijktijdige multithreading. Een HB VM biedt tot 260 GB/sec geheugenbandbreedte.

ACU: 199-216

Premium opslag: ondersteund

Premium Storage Caching: ondersteund

Live migratie: niet ondersteund

Updates voor geheugenbehoud: niet ondersteund

| Grootte | vCPU | Processor | Geheugen (GB) | Geheugenbandbreedte GB/s | Cpu-frequentie baseren (GHz) | All-cores frequentie (GHz, piek) | Single-core frequentie (GHz, piek) | RDMA-prestaties (Gb/s) | ONDERSTEUNING VOOR MPI | Tijdelijke opslag (GB) | Max. aantal gegevensschijven | Max Ethernet-NIC's |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Alle | 700 | 4 | 1 |

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