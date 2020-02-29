---
title: Mv2-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de Mv2-serie.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 6fb0f93d3ac124b21bbc52ddc57bc720de6406e6
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163893"
---
# <a name="mv2-series"></a>Mv2-serie

De Mv2-serie biedt een platform met hoge door Voer en een laag latentie dat wordt uitgevoerd op een Hyper-Threaded Intel® Xeon® Platinum 8180M 2.5 GHz (Skylake)-processor met een basis frequentie van 2,5 GHz en een maximale Turbo frequentie van 3,8 GHz. Alle grootten voor virtuele machines uit de Mv2-serie kunnen zowel standaard als permanente schijven gebruiken. Instanties van de Mv2-serie zijn geoptimaliseerd voor geheugen die ongeëvenaarde reken prestaties bieden ter ondersteuning van grote in-memory data bases en workloads, met een hoge geheugen-naar-CPU-verhouding die ideaal is voor relationele database servers, grote caches en in het geheugen analyse.

Mv2-serie VM-functie Intel® Hyper-Threading-technologie

Premium Storage: ondersteund

Premium Storage caching: ondersteund

Livemigratie: niet ondersteund

Updates voor het behouden van geheugen: niet ondersteund

Write Accelerator: [ondersteund](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000/1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000/1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1, 2</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1, 2</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 / 32000 |

<sup>1</sup> virtuele machines uit de Mv2-serie zijn alleen generatie 2. Als u Linux gebruikt, raadpleegt u [ondersteuning voor virtuele machines van de tweede generatie op Azure](./linux/generation-2.md) voor instructies over het zoeken en selecteren van een installatie kopie.

<sup>2</sup> voor de M416ms_v2 en M416s_v2 grootten ziet u dat er voor de volgende installatie kopie alleen een eerste ondersteuning wordt geboden: "GEN2: SuSE Linux Enterprise Server (SLES) 12 SP4 voor SAP-toepassingen."

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
