---
title: Dv2- en Dsv2-serie - Azure Virtual Machines
description: Specificaties voor de Vm's uit de Dv2- en Dsv2-serie.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 808b14f118e842cb9e52d110075f92ba25a343c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164420"
---
# <a name="dv2-and-dsv2-series"></a>Dv2- en DSv2-serie

De Dv2- en Dsv2-serie, een vervolg op de originele D-serie, zijn voorzien van een krachtigere CPU en een optimale CPU-to-memory configuratie waardoor ze geschikt zijn voor de meeste productieworkloads. De Dv2-serie is ongeveer 35% sneller dan de D-serie. De Dv2-serie draait op de Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), of de Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processors met de Intel Turbo Boost Technology 2.0. De Dv2-serie heeft dezelfde geheugen- en schijfconfiguraties als de D-serie.

## <a name="dv2-series"></a>Dv2-serie

Dv2-serie maten draaien op de Intel® Xeon® 8171M 2.1GHz (Skylake) of de Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) of de Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processors met Intel Turbo Boost Technology 2.0.

ACU: 210-250

Premium opslag: niet ondersteund

Premium Storage caching: niet ondersteund

Live migratie: ondersteund

Updates voor geheugenbehoud: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale temp opslagdoorvoer: IOPS/Read MBps/Write MBps | Max. aantal gegevensschijven | Doorvoer: IOPS | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3,5 | 50  | 3000/46/23    | 4  | 4 x 500  | 2/750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8 x 500  | 2/1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16 x 500 | 4/3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32 x 500 | 8/6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8/12000 |

## <a name="dsv2-series"></a>DSv2-serie

DSv2-serie maten draaien op de Intel® Xeon® 8171M 2.1GHz (Skylake) of de Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) of de Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processors met Intel Turbo Boost Technology 2.0 en gebruik maken van premium opslag.

ACU: 210-250

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

Live migratie: ondersteund

Updates voor geheugenbehoud: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer in cache en tijdelijke opslag: IOPS/MBps (cachegrootte in GiB) | Maximale schijfdoorvoer zonder cache: IOPS/MBps | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3,5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2/750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2/1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4/3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8/6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8/12000 |

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
