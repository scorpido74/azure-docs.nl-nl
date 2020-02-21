---
title: Ev3-Series en Esv3-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de Ev3-en Esv3-serie.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 41b3997a9271a113a8d89f47d3a79d93bc13f92c
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493746"
---
# <a name="ev3-and-esv3-series"></a>Ev3 en Esv3-serie

De Ev3-en Esv3-serie functie van Intel® Xeon® 8171M 2,1 GHz (Skylake) of de Intel® Xeon® E5-2673 v4 2,3 GHz-processor (Broadwell) in een Hyper-Threaded configuratie, met een betere toegevoegde waarde voor de meeste werk belastingen voor algemeen gebruik en de Ev3 in uitlijning met de virtuele machines voor algemeen gebruik van de meeste andere Clouds.  Het geheugen is uitgevouwen (van 7 GiB/vCPU tot 8 GiB/vCPU), terwijl de schijf-en netwerk limieten per kern zijn aangepast om te worden uitgelijnd met de verplaatsing van hyperthreading. De Ev3 is de follow-up van de VM-grootten van het hoge geheugen van de D/dv2-families.

## <a name="ev3-series"></a>Ev3-serie

Instanties van de Ev3-serie zijn gebaseerd op de Intel® Xeon® 8171M 2,1 GHz (Skylake) of de Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell)-processor en Intel Turbo Boost-technologie 2,0. Ev3-exemplaren zijn ideaal voor geheugenintensieve bedrijfstoepassingen.

Gegevensschijfopslag wordt apart van virtuele machines in rekening gebracht. Als u Premium Storage-schijven wilt gebruiken, gebruik dan de ESv3-grootten. De prijs- en factureringsmeters voor de ESv3-grootten zijn gelijk aan die van de Ev3-serie.

De Ev3-serie van de virtuele machine met Intel® Hyper-Threading-technologie.

ACU: 160 - 190

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

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
| Standard_E64i_v3 <sup>1, 2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

Er zijn <sup>1</sup> beperkte core-grootten beschikbaar.

<sup>2</sup> exemplaar is geïsoleerd voor hardware, speciaal voor één klant.

## <a name="esv3-series"></a>Esv3-serie

Instanties van de Esv3-serie zijn gebaseerd op de Intel® Xeon® 8171M 2,1 GHz (Skylake) of de Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell)-processor, Intel Turbo Boost-technologie 2,0 en Premium Storage gebruiken. Instanties van de Esv3-serie zijn ideaal voor geheugenintensieve bedrijfs toepassingen.

De Esv3-serie van de virtuele machine met Intel® Hyper-Threading-technologie.

ACU: 160-190

Premium Storage: ondersteund

Premium Storage caching: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maxi maal cache geheugen en tijdelijke opslag doorvoer: IOPS/MBps (cache grootte in GiB) | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8/8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

Er zijn <sup>1</sup> beperkte core-grootten beschikbaar.

<sup>2</sup> exemplaar is geïsoleerd voor hardware, speciaal voor één klant.

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