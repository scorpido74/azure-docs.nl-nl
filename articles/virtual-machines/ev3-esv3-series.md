---
title: Ev3-serie en Esv3-serie - Azure Virtual Machines
description: Specificaties voor de EV3- en Esv3-serie VM's.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: aad4610a44c59ed95cf1ad9777329097886c5bed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164369"
---
# <a name="ev3-and-esv3-series"></a>Ev3- en Esv3-serie

De Ev3- en Esv3-serie zijn voorzien van de Intel® Xeon® 8171M 2,1 GHz (Skylake) of de Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) processor in een hyper-threaded configuratie, het verstrekken van een betere waarde propositie voor de meeste algemene doeleinden workloads, en het brengen van de Ev3 in overeenstemming met het algemene doel VM's van de meeste andere wolken.  Het geheugen is uitgebreid (van 7 GiB/vCPU naar 8 GiB/vCPU), terwijl schijf- en netwerklimieten per core zijn aangepast om af te stemmen op de overgang naar hyperthreading. De Ev3 is de opvolger van de vm-formaten met een hoog geheugen van de D/Dv2-families.

## <a name="ev3-series"></a>Ev3-serie

Ev3-serie exemplaren zijn gebaseerd op de Functie intel® Xeon® 8171M 2.1 GHz (Skylake) of de Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) processor en Intel Turbo Boost Technology 2.0. Ev3-exemplaren zijn ideaal voor geheugenintensieve bedrijfstoepassingen.

Gegevensschijfopslag wordt apart van virtuele machines in rekening gebracht. Als u Premium Storage-schijven wilt gebruiken, gebruik dan de ESv3-grootten. De prijs- en factureringsmeters voor de ESv3-grootten zijn gelijk aan die van de Ev3-serie.

Ev3-serie VM's zijn voorzien van Intel® Hyper-Threading Technology.

ACU: 160 - 190

Premium opslag: niet ondersteund

Premium Storage caching: niet ondersteund

Live migratie: ondersteund

Updates voor geheugenbehoud: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Max. aantal NIC's/netwerkbandbreedte |
|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8/10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |
| Standard_E64i_v3 <sup>1,2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

<sup>1</sup> Beperkte kerngroottes beschikbaar.

<sup>2</sup> Instance is geïsoleerd voor hardware die is gewijd aan één klant.

## <a name="esv3-series"></a>Esv3-serie

Esv3-serie exemplaren zijn gebaseerd op de functie van de Intel® Xeon® 8171M 2.1 GHz (Skylake) of de Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) processor, Intel Turbo Boost Technology 2.0, en gebruik maken van premium opslag. Esv3-serie exemplaren zijn ideaal voor geheugenintensieve bedrijfstoepassingen.

Esv3-serie VM's zijn voorzien van Intel® Hyper-Threading Technology.

ACU: 160-190

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

Live migratie: ondersteund

Updates voor geheugenbehoud: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer in cache en tijdelijke opslag: IOPS/MBps (cachegrootte in GiB) | Maximale schijfdoorvoer zonder cache: IOPS/MBps | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1.</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2/2000 |
| Standard_E8s_v3 <sup>1.</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4/4000 |
| Standard_E16s_v3 <sup>1.</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8/8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1.</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |
| Standard_E64is_v3 <sup>2.</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

<sup>1</sup> Beperkte kerngroottes beschikbaar.

<sup>2</sup> Instance is geïsoleerd voor hardware die is gewijd aan één klant.

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
