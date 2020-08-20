---
title: Mv2-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de Mv2-serie.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: c91e9832e4b415a59c12b71964a0da580e9fda21
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651128"
---
# <a name="mv2-series"></a>Mv2-serie

De Mv2-serie biedt een platform met hoge door Voer en een laag latentie dat wordt uitgevoerd op een Hyper-Threaded Intel® Xeon® Platinum 8180M 2.5 GHz (Skylake)-processor met een basis frequentie van 2,5 GHz en een maximale Turbo frequentie van 3,8 GHz. Alle grootten voor virtuele machines uit de Mv2-serie kunnen zowel standaard als permanente schijven gebruiken. Instanties van de Mv2-serie zijn geoptimaliseerd voor geheugen die ongeëvenaarde reken prestaties bieden ter ondersteuning van grote data bases in het geheugen en workloads, met een hoge geheugen-naar-CPU-verhouding die ideaal is voor relationele database servers, grote caches en analyse in het geheugen.

Mv2-serie VM-functie Intel® Hyper-Threading-technologie

Premium Storage: ondersteund

Premium Storage caching: ondersteund

Livemigratie: niet ondersteund

Updates voor het behouden van geheugen: niet ondersteund

Write Accelerator: [ondersteund](./how-to-enable-write-accelerator.md)

|Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Max. aantal NIC's | Verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 | 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 | 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 | 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 | 32000 |

<sup>1</sup> virtuele machines uit de Mv2-serie zijn alleen van de tweede generatie en ondersteunen een subset van met generatie 2 ondersteunde installatie kopieën. Zie hieronder voor de volledige lijst met ondersteunde installatie kopieën voor Mv2-Series. Als u Linux gebruikt, raadpleegt u [ondersteuning voor virtuele machines van de tweede generatie op Azure](./linux/generation-2.md) voor instructies over het zoeken en selecteren van een installatie kopie. Als u Windows gebruikt, raadpleegt u [ondersteuning voor virtuele machines van de 2e generatie op Azure](./windows/generation-2.md) voor instructies over het zoeken en selecteren van een installatie kopie. 

- Windows Server 2019 of hoger
- SUSE Linux Enterprise Server 12 SP4 en hoger of SUSE Linux Enterprise Server 15 SP1 en hoger
- Red Hat Enterprise Linux 7,6, 7,7, 8,1 of hoger 
- Oracle Enter prise Linux 7,7 of hoger



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Andere grootten en informatie

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

Prijs calculator: [prijs calculator](https://azure.microsoft.com/pricing/calculator/)

Meer informatie over schijven typen: [schijf typen](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.