---
title: Mv2-serie - Azure Virtual Machines
description: Specificaties voor de Mv2-serie VM's.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 04/07/2020
ms.author: lahugh
ms.openlocfilehash: 764dc93608ae3b8882b7048a722c6d3415cbc644
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885783"
---
# <a name="mv2-series"></a>Mv2-serie

De Mv2-serie beschikt over een hoge doorvoer, lage latentie platform draait op een hyper-threaded Intel® Xeon® Platinum 8180M 2.5GHz (Skylake) processor met een alle core base frequentie van 2,5 GHz en een maximale turbo frequentie van 3,8 GHz. Alle virtuele machineformaten uit de Mv2-serie kunnen zowel standaard als premium persistente schijven gebruiken. Mv2-serie exemplaren zijn geheugengeoptimaliseerde VM-formaten die ongeëvenaarde rekenprestaties bieden ter ondersteuning van grote in-memory databases en workloads, met een hoge geheugen-cpu-ratio die ideaal is voor relationele databaseservers, grote caches en in-memory analytics.

Mv2-serie VM's functie Intel® Hyper-Threading Technology

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

Live migratie: niet ondersteund

Updates voor geheugenbehoud: niet ondersteund

Accelerator schrijven: [ondersteund](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Max NIC's / Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1.</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1.</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1.</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1.</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> Mv2-serie VM's zijn generatie 2 alleen. Als u Linux gebruikt, raadpleegt u [Ondersteuning voor generatie 2 VM's op Azure](./linux/generation-2.md) voor instructies over het zoeken en selecteren van een afbeelding. Als u Windows gebruikt, raadpleegt u [Ondersteuning voor generatie 2 VM's op Azure](./windows/generation-2.md) voor instructies over het zoeken en selecteren van een afbeelding. Minimale besturingssysteem releases vereist voor alle vier de verschillende Mv2-serie VM-types lijst als:

- Windows Server 2019 of hoger
- SUSE Linux Enterprise Server 12 SP4 en hoger of SUSE Linux Enterprise Server 15 SP1 en hoger
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 of hoger 
- Oracle Enterprise Linux 7.7 of hoger



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
