---
title: Eav4-serie en Easv4-serie - Azure Virtual Machines
description: Specificaties voor de Eav4- en Easv4-serie VM's.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: ab45298dbcb8988da7d0211fcbde3087d0099038
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437079"
---
# <a name="eav4-and-easv4-series"></a>Eav4- en Easv4-serie

De Eav4-serie en Easv4-serie maken gebruik van AMD's 2,35 Ghz EPYC<sup>TM</sup> 7452-processor in een multi-threaded configuratie met maximaal 256 MB L3-cache, waardoor de opties voor het uitvoeren van de meeste geheugengeoptimaliseerde workloads toenemen. De Eav4-serie en Easv4-serie hebben dezelfde geheugen- en schijfconfiguraties als de Ev3-& Esv3-serie.

## <a name="eav4-series"></a>Eav4-serie

ACU: 230 - 260

Premium opslag: niet ondersteund

Premium Storage caching: niet ondersteund

Live migratie: ondersteund

Updates voor geheugenbehoud: ondersteund

Eav4-serie maten zijn gebaseerd op de 2,35 Ghz AMD EPYC<sup>TM</sup> 7452 processor die een verhoogde maximale frequentie van 3,35 GHz kan bereiken en premium SSD kunnen gebruiken. De Eav4-serie maten zijn ideaal voor geheugen-intensieve enterprise toepassingen. Gegevensschijfopslag wordt apart van virtuele machines in rekening gebracht. Als u premium SSD wilt gebruiken, gebruikt u de maten uit de Easv4-serie. De prijs- en factureringsmeters voor Easv4-maten zijn hetzelfde als de Eav3-serie.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Max NIC's / Verwachte netwerkbandbreedte (MBps) |
| -----|-----|-----|-----|-----|-----|-----|
| Standaard\_E2a\_v4|2|16|50|4|3000 / 46 / 23|2 / 1000 |
| Standaard\_E4a\_v4|4|32|100|8|6000 / 93 / 46|2 / 2000 |
| Standaard\_E8a\_v4|8|64|200|16|12.000 / 187 / 93|4 / 4000 |
| Standaard\_E16a\_v4|16|128|400|32|24.000 / 375 / 187|8 / 8000 |
| Standaard\_E20a\_v4|20|160|500|32|30000 / 468 / 234|8 / 10000 |
| Standaard\_E32a\_v4|32|256|800|32|48.000 / 750 / 375|8 / 16000 |
| Standaard\_E48a\_v4|48|384|1200|32|96000 / 1000 (500)|8 / 24000 |
| Standaard\_E64a\_v4|64|512|1600|32|96000 / 1000 (500)|8 / 30000 |
| Standaard\_E96a\_v4|96|672|2400|32|96000 / 1000 (500)|8 / 30000 |

## <a name="easv4-series"></a>Easv4-serie

ACU: 230 - 260

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

Live migratie: ondersteund

Updates voor geheugenbehoud: ondersteund

Easv4-serie maten zijn gebaseerd op de 2,35 Ghz AMD EPYC<sup>TM</sup> 7452 processor die een verhoogde maximale frequentie van 3,35 GHz kan bereiken en premium SSD kunnen gebruiken. De easv4-serie maten zijn ideaal voor geheugen-intensieve enterprise toepassingen.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Max NIC's / Verwachte netwerkbandbreedte (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000 / 32 (50)|3200 / 48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400 / 96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)|32000 / 480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000 / 510 (800)|51200 / 768|8 / 16000 |
| Standard_E48as_v4|48|384|768|32|96000 / 1020 (1200)|76800 / 1148|8 / 24000 |
| Standard_E64as_v4|64|512|1024|32|128000 / 1020 (1600)|80000 / 1200|8 / 30000 |
| Standard_E96as_v4|96|672|1344|32|192000 / 1020 (2400)|80000 / 1200|8 / 30000 |

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
