---
title: Geheugengeoptimaliseerde VM's uit de Dv2- en DSv2-serie - Azure Virtual Machines
description: Specificaties voor de Vm's uit de Dv2- en DSv2-serie.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 66e415070e60676df5602078aff50c7b68920f1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914038"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>Geheugen geoptimaliseerde Dv2- en Dsv2-serie

De Dv2- en Dsv2-serie, een opvolger van de originele D-serie, hebben een krachtigere CPU. DSv2-serie maten draaien op de Intel® Xeon® 8171M 2.1 GHz (Skylake) of de Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) of de Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processors. De Dv2-serie heeft dezelfde geheugen- en schijfconfiguraties als de D-serie.

## <a name="dv2-series-11-15"></a>Dv2-serie 11-15

Dv2-serie maten draaien op de Intel® Xeon® 8171M 2.1 GHz (Skylake) of de Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) of de Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processors.

ACU: 210 - 250

Premium opslag: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale temp opslagdoorvoer: IOPS/Read MBps/Write MBps | Maximale gegevensschijven/doorvoer: IOPS | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/12000 |
| Standard_D15_v2 <sup>1.</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8/25000 <sup>2</sup> |

<sup>1</sup> Instantie is geïsoleerd voor hardware die is gewijd aan één klant.
<sup>2</sup> 25000 Mbps met Accelerated Networking.

## <a name="dsv2-series-11-15"></a>DSv2-serie 11-15

DSv2-serie maten draaien op de Intel® Xeon® 8171M 2.1 GHz (Skylake) of de Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) of de Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processors.

ACU: 210 - 250 <sup>1</sup>

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer in cache en tijdelijke opslag: IOPS/MBps (cachegrootte in GiB) | Maximale schijfdoorvoer zonder cache: IOPS/MBps | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2 <sup>3.</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2/1500  |
| Standard_DS12_v2 <sup>3.</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4/3000  |
| Standard_DS13_v2 <sup>3.</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8/6000  |
| Standard_DS14_v2 <sup>3.</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8/12000 |
| Standard_DS15_v2 <sup>2.</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8/25000 <sup>4</sup> |

<sup>1</sup> De maximale schijfdoorvoer (IOPS of MBps) mogelijk met een DSv2-serie VM kan worden beperkt door het aantal, de grootte en de striping van de aangesloten schijf(en).  Zie Ontwerpen [voor hoge prestaties voor](./windows/premium-storage-performance.md)meer informatie.
<sup>2</sup> Instance is geïsoleerd om de Intel Haswell gebaseerde hardware en gewijd aan een enkele klant.  
<sup>3</sup> Beperkte kerngroottes beschikbaar.  
<sup>4</sup> 25000 Mbps met Accelerated Networking.

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
