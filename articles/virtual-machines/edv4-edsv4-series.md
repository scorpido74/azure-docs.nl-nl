---
title: Edv4 en Edsv4-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de Ev4-, Edv4-, Esv4-en Edsv4-serie.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 795891f56985504a3584089d7377f753605ba4dd
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84263037"
---
# <a name="edv4-and-edsv4-series"></a>Edv4 en Edsv4-serie

De Edv4-en Edsv4-serie worden uitgevoerd op de Intel &reg; Xeon &reg; Platinum 8272CL-processors (Cascade Lake) in een configuratie met Hyper-Threading en zijn ideaal voor diverse geheugenintensieve bedrijfs toepassingen en-functies tot 504 RAM-geheugen, [Intel &reg; Turbo Boost-technologie 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel &reg; Hyper-Threading-technologie](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) en [Intel &reg; Advanced Vector-uitbrei dingen 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Deze nieuwe VM-grootten hebben 50% grotere lokale opslag, evenals een betere lokale schijf-IOPS voor zowel lees-als schrijf bewerkingen in vergelijking met de [Ev3/Esv3-](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) grootten met [Gen2 vm's](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)


> [!IMPORTANT]
> Als u een nieuwe virtuele machine implementeert met behulp van de Edv4-of Edsv4-serie en een Linux-installatie kopie wilt gebruiken, moet u RHEL 8. x, CentOS 8. x of Oracle 7. x of hoger gebruiken. Als u RHEL 7. x, CentOS 7. x of Orcale 6. x kiest, wordt er een fout in de kernel voor de kernelmodus weer. Micro soft implementeert een oplossing actief. Alleen RHEL, CentOS en Oracle worden beïnvloed.

## <a name="edv4-series"></a>Edv4-serie

Edv4-serie-grootten worden uitgevoerd op de Intel &reg; Xeon &reg; Platinum 8272CL-processors (Cascade Lake). De Edv4-grootten van virtuele machines bieden Maxi maal 504 GiB aan RAM-geheugen, naast snelle en grote lokale SSD-opslag (Maxi maal 2.400 GiB). Deze virtuele machines zijn ideaal voor geheugenintensieve bedrijfs toepassingen en-toepassingen die profiteren van lage latentie en hoge snelheid voor lokale opslag. U kunt standaard Ssd's en standaard Hdd's-schijf opslag koppelen aan de virtuele machines van Edv4. 

ACU: 195-210

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

Livemigratie: ondersteund

Updates voor geheugen behoud: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Max. door Voer in cache en tijdelijke opslag: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2/1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2/2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4/4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8/8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8/10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8/16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8/24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8/30000 |


## <a name="edsv4-series"></a>Edsv4-serie

Edsv4-serie-grootten worden uitgevoerd op de Intel &reg; Xeon &reg; Platinum 8272CL-processors (Cascade Lake). De Edsv4-grootten van virtuele machines bieden Maxi maal 504 GiB aan RAM-geheugen, naast snelle en grote lokale SSD-opslag (Maxi maal 2.400 GiB). Deze virtuele machines zijn ideaal voor geheugenintensieve bedrijfs toepassingen en-toepassingen die profiteren van lage latentie en hoge snelheid voor lokale opslag.

ACU: 195-210

Premium Storage: ondersteund

Premium Storage caching: ondersteund

Livemigratie: ondersteund

Updates voor geheugen behoud: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Max. door Voer in cache en tijdelijke opslag: IOPS/MBps | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120 (50) | 3200/48 | 2/1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242 (100) | 6400/96 | 2/2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485 (200) | 12800/192 | 4/4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968 (400) | 25600/384 | 8/8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211 (500)  | 32000/480  | 8/10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936 (800) | 51200/768  | 8/16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8/24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872 (1.600) | 80000/1200 | 8/30000 |

Er zijn <sup>1</sup> [beperkte core-grootten beschikbaar](https://docs.microsoft.com/azure/virtual-machines/windows/constrained-vcpu).


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
