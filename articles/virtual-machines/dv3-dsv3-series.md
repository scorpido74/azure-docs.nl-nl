---
title: Dv3- en Dsv3-serie - Azure Virtual Machines
description: Specificaties voor de Vm's uit de Dv3- en Dsv3-serie.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ea5811fa20ea4866655de74d79ff3905ba03f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164403"
---
# <a name="dv3-and-dsv3-series"></a>Dv3- en DSv3-serie

De Dv3-serie draait op de Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), of de Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processors in een hyper-threaded configuratie, het verstrekken van een betere waarde propositie voor de meeste algemene doelworkloads. Het geheugen is uitgebreid (van ~ 3,5 GiB / vCPU tot 4 GiB / vCPU), terwijl schijf- en netwerklimieten per core zijn aangepast om af te stemmen op de overgang naar hyperthreading. De Dv3-serie heeft niet langer het hoge geheugen VM maten van de D / Dv2-serie, die zijn verplaatst naar het geheugen [geoptimaliseerdev3 en Esv3-serie](ev3-esv3-series.md).

Voorbeelden van use cases uit de D-serie zijn enterprise-grade applicaties, relationele databases, in-memory caching en analytics.

## <a name="dv3-series"></a>Dv3-serie

Dv3-serie maten draaien op de Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), of de Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processors met Intel Turbo Boost Technology 2.0. De Dv3-serie biedt een combinatie van vCPU, geheugen en tijdelijke opslag voor de meeste productieworkloads.

Gegevensschijfopslag wordt apart van virtuele machines in rekening gebracht. Als u Premium Storage-schijven wilt gebruiken, gebruik dan de Dsv3-grootten. De prijs- en factureringsmeters voor de Dsv3-grootten zijn gelijk aan die van de Dv3-serie.

Vm's uit de DV3-serie zijn voorzien van Intel® Hyper Threading-technologie.

ACU: 160-190

Premium opslag: niet ondersteund

Premium Storage caching: niet ondersteund

Live migratie: ondersteund

Updates voor geheugenbehoud: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale temp opslagdoorvoer: IOPS/Read MBps/Write MBps | Maximale NIC's/netwerkbandbreedte |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3-serie

De maten van de Dsv3-serie draaien op de Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), of de Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processors met Intel Turbo Boost Technology 2.0 en gebruik premium storage. De Dsv3-serie biedt een combinatie van vCPU, geheugen en tijdelijke opslag voor de meeste productieworkloads.

Vm's uit de Dsv3-serie zijn voorzien van Intel® Hyper Threading-technologie.

ACU: 160-190

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

Live migratie: ondersteund

Updates voor geheugenbehoud: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer in cache en tijdelijke opslag: IOPS/MBps (cachegrootte in GiB) | Maximale schijfdoorvoer zonder cache: IOPS/MBps | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 3200/48    | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 6400/96    | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 12800/192  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 25600/384  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

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
