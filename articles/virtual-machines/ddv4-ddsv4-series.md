---
title: Ddv4 en Ddsv4-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de Dv4-, Ddv4-, Dsv4-en Ddsv4-serie.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 7a5951d15806cd2969d61c52ef929472da023386
ms.sourcegitcommit: 20e246e86e25d63bcd521a4b4d5864fbc7bad1b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/08/2020
ms.locfileid: "84488717"
---
# <a name="ddv4-and-ddsv4-series"></a>Ddv4- en Ddsv4-serie

De Ddv4-en Ddsv4-serie worden uitgevoerd op de Intel &reg; Xeon &reg; Platinum 8272CL-processors (Cascade Lake) in een configuratie met Hyper-Threading, met een betere toegevoegde waarde voor de meeste werk belastingen voor algemeen gebruik. De IT-service heeft een zeer hoge Turbo klok snelheid van 3,4 GHz, [Intel &reg; Turbo Boost-technologie 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel &reg; Hyper-Threading-technologie](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) en [intel &reg; Advanced vector-uitbrei dingen 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Ze ondersteunen ook [Intel &reg; diepe leer versterking](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Deze nieuwe VM-grootten hebben 50% grotere lokale opslag, evenals een betere lokale schijf-IOPS voor zowel lees-als schrijf bewerkingen in vergelijking met de [Dv3/Dsv3-](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) grootten met [Gen2-vm's](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2).

Gebruiks voorbeelden van de D-serie zijn onder andere zakelijke toepassingen, relationele data bases, caching in het geheugen en analyse.

## <a name="ddv4-series"></a>Ddv4-serie

Ddv4-serie-grootten worden uitgevoerd op Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). De Ddv4-serie biedt een combi natie van vCPU, geheugen en tijdelijke schijven voor de meeste productiewerk belastingen.

De nieuwe Ddv4 VM-grootten omvatten snelle, grotere lokale SSD-opslag (Maxi maal 2.400 GiB) en zijn ontworpen voor toepassingen die profiteren van lage latentie, snelle lokale opslag, zoals toepassingen waarvoor snelle lees-en schrijf bewerkingen zijn vereist voor tijdelijke opslag of waarvoor tijdelijke opslag nodig is voor caches of tijdelijke bestanden. U kunt standaard Ssd's en standaard Hdd's-opslag toevoegen aan de Ddv4-Vm's. Opslag van externe gegevens schijven wordt afzonderlijk van virtuele machines gefactureerd.

ACU: 195-210

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

Livemigratie: ondersteund

Updates voor geheugen behoud: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Max. door Voer in cache en tijdelijke opslag: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2/1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2/2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4/4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8/8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8/16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8/24000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8/30000 |

## <a name="ddsv4-series"></a>Ddsv4-serie

Ddsv4-serie wordt uitgevoerd op de Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). De Ddsv4-serie biedt een combi natie van vCPU, geheugen en tijdelijke schijven voor de meeste productiewerk belastingen.

De nieuwe Ddsv4 VM-grootten omvatten snelle, grotere lokale SSD-opslag (Maxi maal 2.400 GiB) en zijn ontworpen voor toepassingen die profiteren van lage latentie, snelle lokale opslag, zoals toepassingen waarvoor snelle lees-en schrijf bewerkingen zijn vereist voor tijdelijke opslag of waarvoor tijdelijke opslag nodig is voor caches of tijdelijke bestanden. 

 > [!NOTE]
 >De prijs-en facturerings meters voor Ddsv4-grootten zijn gelijk aan die van de Ddv4-serie.

ACU: 195-210

Premium Storage: ondersteund

Premium Storage caching: ondersteund

Livemigratie: ondersteund

Updates voor geheugen behoud: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maxi maal cache geheugen en tijdelijke opslag doorvoer: IOPS/MBps (cache grootte in GiB) | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120 (50) | 3000/48 | 2/1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242 (100) | 6400/96 | 2/2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485 (200) | 12800/192 | 4/4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968 (400) | 25600/384 | 8/8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936 (800) | 51200/768 | 8/16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8/24000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872 (1.600) | 80000/1200 | 8/30000 |

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
