---
title: Fsv2-serie - Azure Virtual Machines
description: Specificaties voor de FSV2-serie VM's.
services: virtual-machines
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f28c6b61aee3c8cbc078db1c2cfb48ed1fba4554
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164845"
---
# <a name="fsv2-series"></a>Fsv2-serie

De Fsv2-serie is gebaseerd op de Intel® Xeon® Platinum 8168 processor. Het beschikt over een duurzame all core Turbo kloksnelheid van 3,4 GHz en een maximale single-core turbo frequentie van 3,7 GHz. Intel® AVX-512 instructies zijn nieuw op Intel Scalable Processors. Deze instructies bieden tot een 2x prestatieverbetering voor vectorverwerkingsworkloads op zowel enkele als dubbele precisie floating point-bewerkingen. Met andere woorden, ze zijn echt snel voor elke computationele werkbelasting.

Fsv2-serie VM's zijn voorzien van Intel® Hyper-Threading Technology.

ACU: 195 - 210

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

Live migratie: ondersteund

Updates voor geheugenbehoud: ondersteund

| Grootte | vCPU's | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer in cache en tijdelijke opslag: IOPS/MBps (cachegrootte in GiB) | Maximale schijfdoorvoer zonder cache: IOPS/MBps | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2/875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2/1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4/3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4/7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8/14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8/21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8/28000 |
| Standard_F72s_v2<sup>1.</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8/30000 |

<sup>1</sup> Het gebruik van meer dan 64 vCPU vereisen een van deze ondersteunde gast besturingssystemen:

- Windows Server 2016 of hoger
- Ubuntu 16.04 LTS of hoger, met Azure tuned kernel (4.15 kernel of hoger)
- SLES 12 SP2 of hoger
- RHEL- of CentOS-versie 6.7 tot en met 6.10, met door Microsoft geleverd LIS-pakket 4.3.1 (of hoger) geïnstalleerd
- RHEL- of CentOS-versie 7.3, met door Microsoft geleverd LIS-pakket 4.2.1 (of hoger) geïnstalleerd
- RHEL- of CentOS-versie 7.6 of hoger
- Oracle Linux met UEK4 of hoger
- Debian 9 met de backports kernel, Debian 10 of hoger
- CoreOS met een 4.14 kernel of hoger

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
